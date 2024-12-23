# 集群范围的加电/断电竞争避免算法

=========================================================

本文件记录了用于协调 CPU 和集群设置与拆除操作以及安全管理硬件一致性控制的算法。

“原理”部分解释了该算法的用途和必要性。“基本模型”使用系统的简化视图解释了一般概念。其他部分解释了实际使用的算法的细节。

# 原理

---------

在包含多个 CPU 的系统中，希望在系统空闲时能够关闭单个 CPU，以降低功耗和热耗散。

在包含多个 CPU 集群的系统中，也希望能够关闭整个集群。

关闭和打开整个集群是一项有风险的任务，因为它涉及到在操作系统继续运行的情况下执行可能具有破坏性的操作，影响一组独立运行的 CPU。这意味着我们需要一些协调，以确保仅在真正安全的情况下才执行关键的集群级操作。

简单的锁定可能不足以解决此问题，因为像 Linux 自旋锁这样的机制可能依赖于一致性机制，而这些机制在集群加电时不会立即启用。由于启用或禁用这些机制本身可能是非原子操作（例如写入一些硬件寄存器并使大型缓存无效），因此需要其他协调方法来保证集群级的安全断电和加电。

本文档中介绍的机制描述了一种基于一致性内存的协议，用于执行所需的协调。它旨在尽可能轻量级，同时提供所需的安全属性。

# 基本模型

-----------

每个集群和 CPU 都被分配一个状态，如下所示：

```
 DOWN
 COMING_UP
 UP
 GOING_DOWN
    +---------> UP ----------+
    |                        v
 COMING_UP                GOING_DOWN
    ^                        |
    +--------- DOWN <--------+
```

- DOWN：CPU 或集群不同步，要么关闭电源或挂起，要么准备关闭电源或挂起。

- COMING_UP：CPU 或集群已承诺进入 UP 状态。它可能正在初始化和启用一致性的过程中。

- UP：CPU 或集群在硬件级别处于活动和同步状态。处于此状态的 CPU 不一定被内核积极使用。

- GOING_DOWN：CPU 或集群已承诺进入 DOWN 状态。它可能正在拆除和一致性退出的过程中。

在任何时候，每个 CPU 都被分配其中一个状态。CPU 状态在下面的“CPU 状态”部分中进行了描述。

每个集群也被分配一个状态，但有必要将状态值分为两部分（“集群”状态和“入站”状态），并引入额外的状态，以避免集群中的不同 CPU 同时修改状态时出现竞争。集群级状态在“集群状态”部分中进行了描述。

为了在本讨论中区分 CPU 状态和集群状态，状态名称为 CPU 状态加上“CPU_”前缀，为集群状态加上“CLUSTER_”或“INBOUND_”前缀。

# CPU 状态

---------

在该算法中，多核处理器中的每个单独核心都被称为“CPU”。假设 CPU 是单线程的：因此，CPU 在单个时间点只能执行一件事情。

这意味着 CPU 非常符合基本模型。

该算法为系统中的每个 CPU 定义了以下状态：

```
 CPU_DOWN
 CPU_COMING_UP
 CPU_UP
 CPU_GOING_DOWN
 集群设置和
 CPU 设置完成          策略决策
      +-----------> CPU_UP ------------+
      |                                v
 CPU_COMING_UP                   CPU_GOING_DOWN
      ^                                |
      +----------- CPU_DOWN <----------+
 策略决策           CPU 拆除完成
 或硬件事件
```

这四个状态的定义与基本模型的状态密切对应。

状态之间的转换如下。

触发事件（自发的）意味着 CPU 可以仅通过本地进展转换到下一个状态，而不需要任何外部事件发生。

```
CPU_DOWN：
	当 CPU 准备好断电时，它进入 CPU_DOWN 状态。到达此状态后，CPU 通常会通过 WFI 指令或固件调用自行断电或挂起。
	下一个状态：CPU_COMING_UP
	条件：无
	触发事件：
		a) 由另一个 CPU 的策略决策导致的显式硬件加电操作；
		b) 硬件事件，如中断。

CPU_COMING_UP：
	在集群设置并同步之前，CPU 不能开始参与硬件一致性。如果集群未准备好，那么 CPU 将在 CPU_COMING_UP 状态下等待，直到集群已设置好。
	下一个状态：CPU_UP
	条件：CPU 的父集群必须处于 CLUSTER_UP 状态。
	触发事件：父集群转换到 CLUSTER_UP 状态。
	有关 CLUSTER_UP 状态的描述，请参考“集群状态”部分。

CPU_UP：
	当 CPU 到达 CPU_UP 状态时，CPU 可以安全地开始参与本地一致性。
	这是通过跳转到内核的 CPU 恢复代码来完成的。
	请注意，此状态的定义与基本模型的定义略有不同：CPU_UP 并不意味着 CPU 已经同步，但确实意味着可以安全地恢复内核。内核处理其余的恢复过程，因此剩余步骤不作为竞争避免算法的一部分可见。
	CPU 保持在该状态，直到做出明确的策略决策来关闭或挂起 CPU。
	下一个状态：CPU_GOING_DOWN
	条件：无
	触发事件：明确的策略决策

CPU_GOING_DOWN：
	在该状态下，CPU 退出一致性，包括实现此目的所需的任何操作（例如清理数据缓存）。
	下一个状态：CPU_DOWN
	条件：本地 CPU 拆除完成
	触发事件：（自发的）
```
	
# 集群状态

-------------

集群是一组具有一些公共资源的连接 CPU。由于集群包含多个 CPU，它可以同时执行多个操作。这有一些影响。特别是，一个 CPU 可以在另一个 CPU 拆除集群时启动。

在本讨论中，“出站侧”是拆除集群的 CPU 看到的集群状态视图。“入站侧”是设置 CPU 的 CPU 看到的集群状态视图。

为了在这种情况下实现安全协调，重要的是设置集群的 CPU 可以独立于拆除集群的 CPU 宣传其状态。因此，集群状态分为两部分：

```
“集群”状态：集群的全局状态；或出站侧的状态：
	CLUSTER_DOWN
	CLUSTER_UP
	CLUSTER_GOING_DOWN

“入站”状态：入站侧的集群状态。`INBOUND_NOT_COMING_UP`
	`INBOUND_COMING_UP`
	这些状态的不同配对导致集群整体有六种可能的状态：
		`CLUSTER_UP`
		`+==========> INBOUND_NOT_COMING_UP -------------+`
		`#`
		``
		`CLUSTER_UP     <----+                                |`
		`INBOUND_COMING_UP      |                                v`
		`^             CLUSTER_GOING_DOWN       CLUSTER_GOING_DOWN`
		`#              INBOUND_COMING_UP <=== INBOUND_NOT_COMING_UP`
		`CLUSTER_DOWN         |                                |`
		`INBOUND_COMING_UP <----+                                |`
		``
		`^                                               |`
		`+===========     CLUSTER_DOWN      <------------+`
		`                       INBOUND_NOT_COMING_UP`

	转换 -----> 只能由出站 CPU 进行，并且仅涉及“集群”状态的更改。
	转换 ===##> 只能由入站 CPU 进行，并且仅涉及“入站”状态的更改，除非出站侧没有进一步的转换可能（即出站 CPU 已将集群置于 CLUSTER_DOWN 状态）。
	争用避免算法不提供确定集群内哪些确切的 CPU 扮演这些角色的方法。这必须通过其他方式提前决定。请参阅“最后一人和第一人选择”部分以获取更多解释。
	`CLUSTER_DOWN/INBOUND_NOT_COMING_UP` 是集群实际可以关闭电源的唯一状态。
	入站和出站 CPU 的并行性通过从 CLUSTER_GOING_DOWN/INBOUND_NOT_COMING_UP（对应于基本模型中的 GOING_DOWN）到 CLUSTER_DOWN/INBOUND_COMING_UP（对应于基本模型中的 COMING_UP）的两条不同路径的存在来观察。第二条路径完全避免了集群拆除。
	`CLUSTER_UP/INBOUND_COMING_UP` 相当于基本模型中的 UP。到 CLUSTER_UP/INBOUND_NOT_COMING_UP 的最终转换是微不足道的，只是重置状态机以准备下一个周期。
	允许转换的详细信息如下。每种情况下的下一个状态表示为：
	`<集群状态>/<入站状态> (<转换者>)`
	其中 `<转换者>` 是转换可以发生的一侧；入站或出站。

`CLUSTER_DOWN/INBOUND_NOT_COMING_UP`：
	下一个状态：`CLUSTER_DOWN/INBOUND_COMING_UP`（入站）
	条件：无
	触发事件：
		`a) 来自另一个 CPU 的策略决策导致的显式硬件加电操作；`
		`b) 硬件事件，如中断。`
`CLUSTER_DOWN/INBOUND_COMING_UP`：
	在这种状态下，入站 CPU 设置集群，包括在集群级别启用硬件一致性以及为实现此目的所需的任何其他操作（例如缓存无效）。
	此状态的目的是进行足够的集群级设置，以使集群中的其他 CPU 能够安全地进入一致性。
	下一个状态：`CLUSTER_UP/INBOUND_COMING_UP`（入站）
	条件：集群级设置和硬件一致性完成
	触发事件：（自发）

`CLUSTER_UP/INBOUND_COMING_UP`：
	集群级设置完成，并且为集群启用了硬件一致性。集群中的其他 CPU 可以安全地进入一致性。
	这是一个瞬态状态，立即导致`CLUSTER_UP/INBOUND_NOT_COMING_UP`。集群上的所有其他 CPU 应将这两个状态视为等效。
	一个状态：`CLUSTER_UP/INBOUND_NOT_COMING_UP`（入站）
	条件：无
	触发事件：（自发）

`CLUSTER_UP/INBOUND_NOT_COMING_UP`：
	集群级设置完成，并且为集群启用了硬件一致性。集群中的其他 CPU 可以安全地进入一致性。
	集群将保持在这种状态，直到做出关闭集群电源的策略决策。
	下一个状态：`CLUSTER_GOING_DOWN/INBOUND_NOT_COMING_UP`（出站）
	条件：无
	触发事件：关闭集群的策略决策

`CLUSTER_GOING_DOWN/INBOUND_NOT_COMING_UP`：
	出站 CPU 正在拆除集群。所选 CPU 必须在此状态下等待，直到集群中的所有 CPU 都处于 CPU_DOWN 状态。
	当所有 CPU 都处于 CPU_DOWN 状态时，可以拆除集群，例如通过清除数据缓存并退出集群级一致性。
	避免不必要的浪费拆除操作，出站应检查入站集群状态是否异步转换为 INBOUND_COMING_UP。或者，可以检查单个 CPU 是否进入 CPU_COMING_UP 或 CPU_UP。
	下一个状态：

`CLUSTER_DOWN/INBOUND_NOT_COMING_UP`（出站）
	条件：集群已拆除并准备关闭电源
	触发事件：（自发）

`CLUSTER_GOING_DOWN/INBOUND_COMING_UP`（入站）
	条件：无
	触发事件：
		`a) 来自另一个 CPU 的策略决策导致的显式硬件加电操作；`
		`b) 硬件事件，如中断。`

`CLUSTER_GOING_DOWN/INBOUND_COMING_UP`：
	集群正在（或已经）被拆除，但在此期间另一个 CPU 已上线并试图再次设置集群。
	如果出站 CPU 观察到此状态，它有两种选择：
		`a) 退出拆除，将集群恢复到 CLUSTER_UP 状态；`
		`b) 完成拆除并将集群置于 CLUSTER_DOWN 状态；入站 CPU 将从那里再次设置集群。`
	选择（a）通过避免在集群实际上不会关闭电源的情况下不必要的拆除和设置操作来允许减少一些延迟。
	下一个状态：
	
	`CLUSTER_UP/INBOUND_COMING_UP`（出站）
		条件：集群级设置和硬件一致性完成
		触发事件：（自发）
	`CLUSTER_DOWN/INBOUND_COMING_UP`（出站）
		条件：集群已拆除并准备关闭电源
		触发事件：（自发）
```

# 最后一人和第一人选择

--------------------------------

在出站侧执行集群拆除操作的 CPU 通常称为“最后一人”。

在入站侧执行集群设置的 CPU 通常称为“第一人”。

上面记录的争用避免算法不提供选择哪些 CPU 应该扮演这些角色的机制。

- 最后一人：

在关闭集群时，所有涉及的 CPU 最初都在执行 Linux 并且是一致的。因此，在 CPU 变得不一致之前，可以使用普通的自旋锁安全地选择最后一人。

- 第一人：

由于 CPU 可能因外部唤醒事件而异步加电，需要一种动态机制来确保只有一个 CPU 尝试扮演第一人角色并进行集群级初始化：其他 CPU 必须在此完成之前等待。

集群级初始化可能涉及在总线结构中配置一致性控制等操作。

mcpm_head.S 中的当前实现使用单独的互斥机制来进行此仲裁。此机制在 vlocks.txt 中有详细记录。

# 功能和限制

------------------------

```
实现：
	当前基于 ARM 的实现分为`arch/arm/common/mcpm_head.S`（低级入站 CPU 操作）和`arch/arm/common/mcpm_entry.c`（其他所有内容）：
	`__mcpm_cpu_going_down()` 表示 CPU 到 CPU_GOING_DOWN 状态的转换。
	`__mcpm_cpu_down()` 表示 CPU 到 CPU_DOWN 状态的转换。

	CPU 通过 mcpm_head.S 中的底层上电代码转换到 CPU_COMING_UP，然后转换到 CPU_UP。这可能涉及特定于 CPU 的设置代码，但在当前的实现中并没有。

	`__mcpm_outbound_enter_critical()` 和 `__mcpm_outbound_leave_critical()` 处理从 CLUSTER_UP 到 CLUSTER_GOING_DOWN 的转换，以及从那里转换到 CLUSTER_DOWN 或回到 CLUSTER_UP（在集群电源关闭中止的情况下）。

	这些函数比 `__mcpm_cpu_*()` 函数更复杂，因为在集群级别进行安全转换需要额外的 CPU 间协调。

	集群通过 mcpm_head.S 中的底层上电代码从 CLUSTER_DOWN 转换回 CLUSTER_UP。这通常涉及特定于平台的设置代码，由通过 mcpm_sync_init 注册的特定于平台的 power_up_setup 函数提供。 

深度拓扑结构：
	如当前所描述和实现的，该算法不支持涉及超过两级的 CPU 拓扑（即，不支持集群的集群）。
	该算法可以通过为额外的拓扑级别复制集群级状态，并修改中间（非最外层）集群级别的转换规则来扩展。
```

# 尾注

--------

最初由 Dave Martin 为 Linaro Limited 创建和记录，与 Nicolas Pitre 和 Achin Gupta 合作。

版权 (C) 2012 - 2013 Linaro Limited

根据 GNU 通用公共许可证第 2 版的条款分发，如 linux/COPYING 中所定义。