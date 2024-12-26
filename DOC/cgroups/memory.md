<markdown_document>

# Memory Resource Controller

注意：此文档已过时，需要进行全面重写。但其中仍包含一些有用信息，因此我们将其保留在此处，但在需要更深入理解时，请务必查看当前代码。

注意：在本文档中，内存资源控制器通常被称为内存控制器。请勿将此处的内存控制器与硬件中使用的内存控制器混淆。

（对于编辑者）

在本文档中：

当我们提及带有内存控制器的 cgroup（cgroupfs 的目录）时，我们称之为“内存 cgroup”。当你查看 git-log 和源代码时，你会看到补丁的标题和函数名倾向于使用“memcg”。在本文档中，我们避免使用它。

内存控制器的好处和目的

内存控制器将一组任务的内存行为与系统的其余部分隔离开来。LWN 上的文章[12]提到了内存控制器的一些可能用途。内存控制器可用于：

a. 隔离一个应用程序或一组应用程序

内存密集型应用程序可以被隔离并限制在较小的内存量中。

b. 创建具有有限内存量的 cgroup；这可以用作以 mem=XXXX 启动的良好替代方案。

c. 虚拟化解决方案可以控制它们希望分配给虚拟机实例的内存量。

d. CD/DVD 刻录机可以控制系统其余部分使用的内存量，以确保刻录不会因缺乏可用内存而失败。

e. 还有其他几个用例；找到一个或仅为了好玩而使用控制器（学习和在 VM 子系统上进行黑客攻击）。

当前状态：linux-2.6.34-mmotm（2010 年 4 月的开发版本）

功能：

- 统计匿名页面、文件缓存、交换缓存的使用情况并对其进行限制。

- 页面专门链接到每个 memcg 的 LRU，并且没有全局 LRU。

- 可选地，可以统计和限制内存+交换的使用情况。

- 分层统计

- 软限制

- 在移动任务时选择移动（充电）账户。

- 使用阈值通知程序

- 内存压力通知程序

- oom-killer 禁用旋钮和 oom-notifier

- 根 cgroup 没有限制控制。

内核内存支持仍在进行中，当前版本提供了基本功能。（见第 2.7 节）

控制文件简要摘要。

```
tasks # 附加一个任务（线程）并显示线程列表
cgroup.procs # 显示进程列表
cgroup.event_control # 用于 event_fd()的接口
memory.usage_in_bytes # 显示当前内存使用情况
（详情见 5.5）
memory.memsw.usage_in_bytes # 显示当前内存+交换使用情况
（详情见 5.5）
memory.limit_in_bytes # 设置/显示内存使用限制
memory.memsw.limit_in_bytes # 设置/显示内存+交换使用限制
memory.failcnt # 显示内存使用达到限制的次数
memory.memsw.failcnt # 显示内存+交换达到限制的次数
memory.max_usage_in_bytes # 显示记录的最大内存使用情况
memory.memsw.max_usage_in_bytes # 显示记录的最大内存+交换使用情况
memory.soft_limit_in_bytes # 设置/显示内存使用的软限制
memory.stat # 显示各种统计信息
memory.use_hierarchy # 设置/显示启用分层账户
memory.force_empty # 触发强制移动到父级的充电
memory.pressure_level # 设置内存压力通知
memory.swappiness # 设置/显示 vmscan 的交换性参数
（见 sysctl 的 vm.swappiness）
memory.move_charge_at_immigrate # 设置/显示移动充电的控制
memory.oom_control # 设置/显示 oom 控制。
memory.numa_stat # 显示每个 numa 节点的内存使用次数
memory.kmem.limit_in_bytes # 设置/显示内核内存的硬限制
memory.kmem.usage_in_bytes # 显示当前内核内存分配
memory.kmem.failcnt # 显示内核内存使用达到限制的次数
memory.kmem.max_usage_in_bytes # 显示记录的最大内核内存使用情况
memory.kmem.tcp.limit_in_bytes # 设置/显示 tcp 缓冲区内存的硬限制
memory.kmem.tcp.usage_in_bytes # 显示当前 tcp 缓冲区内存分配
memory.kmem.tcp.failcnt # 显示 tcp 缓冲区内存使用达到限制的次数
memory.kmem.tcp.max_usage_in_bytes # 显示记录的最大 tcp 缓冲区内存使用情况
```

# 1. 历史

内存控制器有着悠久的历史。Balbir Singh[1]发布了关于内存控制器的评论请求。在发布 RFC 时，已经有几种内存控制的实现。RFC 的目标是为内存控制所需的最小功能建立共识和协议。第一个 RSS 控制器由 Balbir Singh[2]于 2007 年 2 月发布。Pavel Emelianov[3][4][5]此后发布了三个版本的 RSS 控制器。在 OLS 的资源管理 BoF 上，每个人都建议我们同时处理页面缓存和 RSS。还提出了允许用户空间处理 OOM 的请求。当前的内存控制器处于版本 6；它结合了映射（RSS）和未映射的页面缓存控制[11]。

# 2. 内存控制

内存是一种独特的资源，因为它的数量是有限的。如果一个任务需要大量的 CPU 处理，该任务可以在数小时、数天、数月或数年内分散其处理，但对于内存，需要重复使用相同的物理内存来完成任务。

内存控制器的实现已分为几个阶段。这些阶段是：

1. 内存控制器

2. mlock(2)控制器

3. 内核用户内存统计和 slab 控制

4. 用户映射长度控制器

内存控制器是第一个开发的控制器。

## 2.1. 设计

设计的核心是一个称为 page_counter 的计数器。page_counter 跟踪与控制器相关的进程组的当前内存使用情况和限制。每个 cgroup 都有一个与之关联的内存控制器特定数据结构（mem_cgroup）。

## 2.2. 统计

```
        +--------------------+
        |  mem_cgroup        |
        |  (page_counter)    |
        +--------------------+
         /            ^      \
        /             |       \
           +---------------+  |        +---------------+
           | mm_struct     |  |....    | mm_struct     |
           |               |  |        |               |
           +---------------+  |        +---------------+
                          |
                          + --------------+
                                      |
           +---------------+           +------+--------+
           | page          +---------->  page_cgroup|
           |               |           |               |
           +---------------+           +---------------+
```

（图 1：统计层次结构）

图 1 展示了控制器的重要方面：

1. 按 cgroup 进行统计。

2. 每个 mm_struct 知道它属于哪个 cgroup。

3. 每个页面都有一个指向 page_cgroup 的指针，而 page_cgroup 又知道它所属的 cgroup。

统计过程如下：调用 mem_cgroup_charge_common()来设置必要的数据结构，并检查正在计费的 cgroup 是否超过其限制。如果是，则在该 cgroup 上调用回收。更多细节可以在本文档的回收部分找到。如果一切顺利，将更新一个称为 page_cgroup 的页面元数据结构。page_cgroup 在 cgroup 上有自己的 LRU。（*）page_cgroup 结构在引导/内存热插拔时分配。

### 2.2.1 统计细节

所有映射的匿名页面（RSS）和缓存页面（页面缓存）都被统计。一些永远不可回收且不在 LRU 上的页面不被统计。我们仅在通常的 VM 管理下统计页面。

RSS 页面在页面错误时进行统计，除非它们之前已经被统计过。当文件页面插入到 inode（基数树）中时，将作为页面缓存进行统计。当它映射到进程的页表中时，会小心避免重复统计。

当 RSS 页面完全取消映射时，将不再统计。当从基数树中删除 PageCache 页面时，将不再统计。即使 RSS 页面完全取消映射（由 kswapd 进行），它们在系统中可能仍作为交换缓存存在，直到真正被释放。这种交换缓存也会被统计。交换进来的页面在映射之前不会被统计。

注意：内核会进行交换预读并一次读取多个交换。这意味着交换进来的页面可能包含导致页面错误的任务之外的其他任务的页面。因此，我们避免在交换输入 I/O 时进行统计。

在页面迁移时，统计信息会被保留。

注意：我们仅统计 LRU 上的页面，因为我们的目的是控制使用的页面数量；不在 LRU 上的页面从 VM 角度来看往往是失控的。

## 2.3 共享页面统计

共享页面基于首次触摸方法进行统计。首次触摸页面的 cgroup 将为该页面计费。这种方法背后的原则是，积极使用共享页面的 cgroup 最终将为其计费（一旦从引入它的 cgroup 中取消计费 - 这将在内存压力下发生）。

但请参阅第 8.2 节：当将一个任务移动到另一个 cgroup 时，如果选择了 move_charge_at_immigrate，则其页面可能会重新计费到新的 cgroup。

例外情况：如果未使用 CONFIG_MEMCG_SWAP。当你执行 swapoff 并强制将 shmem（tmpfs）的交换出页面换入内存时，页面的计费将针对 swapoff 的调用者，而不是 shmem 的用户。

## 2.4 交换扩展（CONFIG_MEMCG_SWAP）

交换扩展允许你记录交换的计费。如果可能，交换进来的页面将被计费回原始页面分配器。

当统计交换时，会添加以下文件：

- memory.memsw.usage_in_bytes。

- memory.memsw.limit_in_bytes。

memsw 表示内存+交换。内存+交换的使用受到 memsw.limit_in_bytes 的限制。

示例：假设一个系统有 4G 的交换空间。一个任务在 2G 内存限制下分配了 6G 的内存（错误地），将使用所有的交换空间。在这种情况下，设置 memsw.limit_in_bytes = 3G 将防止交换的滥用。通过使用 memsw 限制，你可以避免由于交换短缺而导致的系统 OOM。

* 为什么是“内存+交换”而不是交换。全局 LRU（kswapd）可以任意交换出页面。交换出意味着将账户从内存移动到交换中...内存+交换的使用没有变化。换句话说，当我们希望在不影响全局 LRU 的情况下限制交换的使用时，从操作系统的角度来看，内存+交换限制比仅限制交换更好。

* 当 cgroup 达到 memory.memsw.limit_in_bytes 时会发生什么

当 cgroup 达到 memory.memsw.limit_in_bytes 时，在该 cgroup 中进行交换是无用的。然后，cgroup 例程将不会进行交换，并且文件缓存将被删除。但如前所述，全局 LRU 可以为了系统内存管理状态的合理性而从其中交换出内存。你不能通过 cgroup 禁止它。

## 2.5 回收

每个 cgroup 维护一个每个 cgroup 的 LRU，其结构与全局 VM 相同。当一个 cgroup 超过其限制时，我们首先尝试从该 cgroup 回收内存，以便为该 cgroup 触摸的新页面腾出空间。如果回收不成功，将调用 OOM 例程来选择并杀死该 cgroup 中最大的任务。（见下文 10. OOM 控制）

回收算法未针对 cgroup 进行修改，除了选择回收的页面来自每个 cgroup 的 LRU 列表。

注意：回收不适用于根 cgroup，因为我们不能对根 cgroup 设置任何限制。

注意 2：当 panic_on_oom 设置为“2”时，整个系统将崩溃。

当注册 oom 事件通知程序时，将发送事件。（见 oom_control 部分）

## 2.6 锁定

lock_page_cgroup()/unlock_page_cgroup()不应在 mapping->tree_lock 下调用。

其他锁定顺序如下：

PG_locked.    mm->page_table_lock

    zone->lru_lock

   lock_page_cgroup. 在许多情况下，仅调用 lock_page_cgroup()。

每个区域每个 cgroup 的 LRU（cgroup 的私有 LRU）仅由 zone->lru_lock 保护，它没有自己的锁。

## 2.7 内核内存扩展（CONFIG_MEMCG_KMEM）

通过内核内存扩展，内存控制器能够限制系统使用的内核内存量。内核内存与用户内存根本不同，因为它不能被交换出去，这使得通过消耗过多这种宝贵资源来 DoS 系统成为可能。

在为一个组设置限制之前，内核内存将不会被统计。这允许现有设置继续工作而不会受到干扰。

如果 cgroup 有子级，或者该 cgroup 中已经有任务，则不能设置限制。在这些条件下尝试设置限制将返回 -EBUSY。当 use_hierarchy == 1 且一个组被统计时，其子女将自动被统计，而不管其限制值如何。

在一个组首次被限制后，它将一直被统计，直到被删除。内存限制本身当然可以通过将 -1 写入 memory.kmem.limit_in_bytes 来删除。在这种情况下，kmem 将被统计，但不受限制。

根 cgroup 不施加内核内存限制。根 cgroup 的使用情况可能会被统计，也可能不会被统计。使用的内存将累积到 memory.kmem.usage_in_bytes 中，或者在有意义时累积到单独的计数器中。（目前仅针对 tcp）。主要的“kmem”计数器被馈送到主计数器中，因此 kmem 费用也将在用户计数器中可见。

目前尚未为内核内存实现软限制。在达到这些限制时触发 slab 回收是未来的工作。

### 2.7.1 当前统计的内核内存资源

* 栈页面：每个进程都会消耗一些栈页面。通过将其统计到内核内存中，我们可以防止在内核内存使用过高时创建新进程。

* slab 页面：由 SLAB 或 SLUB 分配器分配的页面将被跟踪。每次从 memcg 内部首次触摸缓存时，都会创建每个 kmem_cache 的副本。创建是延迟进行的，因此在缓存创建期间，一些对象可能仍会被跳过。slab 页面中的所有对象都应该属于同一个 memcg。只有在缓存分配页面期间任务迁移到不同的 memcg 时，此规则才会失败。

* 套接字内存压力：一些套接字协议具有内存压力阈值。内存控制器允许它们在每个 cgroup 中单独控制，而不是全局控制。

* tcp 内存压力：tcp 协议的套接字内存压力。

### 2.7.2 常见用例

由于“kmem”计数器被馈送到主用户计数器中，内核内存永远不能完全独立于用户内存进行限制。假设“U”是用户限制，“K”是内核限制。有三种可能的设置限制的方式：

    U!= 0，K = 无限制：

    这是在 kmem 统计之前已经存在的标准 memcg 限制机制。内核内存将被完全忽略。

</markdown_document><markdown_document>

U 不等于 0，K 小于 U：

内核内存是用户内存的一个子集。这种设置在每个 cgroup 的总内存超配的部署中很有用。

超配内核内存限制绝对不被推荐，因为系统仍然可能耗尽不可回收的内存。

在这种情况下，管理员可以设置 K，使得所有组的总和永远不大于总内存，并以牺牲其服务质量为代价自由地设置 U。

警告：在当前的实现中，当 cgroup 达到 K 但仍低于 U 时，内存回收不会被触发，这使得这种设置不实用。

U 不等于 0，K 大于等于 U：

由于 kmem 计费也将被计入用户计数器，并且对于两种类型的内存，cgroup 都将触发回收。这种设置为管理员提供了一个统一的内存视图，对于只想跟踪内核内存使用情况的人也很有用。

# 3. 用户界面

## 3.0. 配置

a. 启用 CONFIG_CGROUPS

b. 启用 CONFIG_MEMCG

c. 启用 CONFIG_MEMCG_SWAP（以使用交换扩展）

d. 启用 CONFIG_MEMCG_KMEM（以使用 kmem 扩展）

## 3.1. 准备 cgroups（请参阅 cgroups.txt，为什么需要 cgroups？）

    # mount -t tmpfs none /sys/fs/cgroup
    # mkdir /sys/fs/cgroup/memory
    # mount -t cgroup none /sys/fs/cgroup/memory -o memory

## 3.2. 创建新组并将 bash 移入其中

    # mkdir /sys/fs/cgroup/memory/0
    # echo $$ > /sys/fs/cgroup/memory/0/tasks

因为现在我们在 0 cgroup 中，我们可以更改内存限制：

    # echo 4M > /sys/fs/cgroup/memory/0/memory.limit_in_bytes

注意：我们可以使用后缀（k、K、m、M、g 或 G）来表示千、兆或千兆字节的值。（这里，千、兆、吉是 kibibytes、mebibytes、gibibytes。）

注意：我们可以写入“-1”来重置*.limit_in_bytes（无限制）。

注意：我们不能再设置根 cgroup 的限制。

    # cat /sys/fs/cgroup/memory/0/memory.limit_in_bytes
    4194304

我们可以检查使用情况：

    # cat /sys/fs/cgroup/memory/0/memory.usage_in_bytes
    1216512

对这个文件的成功写入并不能保证将这个限制设置为写入文件的值。这可能是由于多种因素，例如向上舍入到页边界或系统上的总可用内存。用户需要在写入后重新读取这个文件，以确保内核提交的值。

    # echo 1 > memory.limit_in_bytes
    # cat memory.limit_in_bytes
    4096

memory.failcnt 字段给出了 cgroup 限制被超过的次数。

memory.stat 文件提供了记账信息。现在，缓存、RSS 和活动页/非活动页的数量都被显示出来。

# 4. 测试

对于测试功能和实现，请参阅 memcg_test.txt。

性能测试也很重要。要查看纯内存控制器的开销，在 tmpfs 上进行测试将为你提供小开销的良好数字。例如：在 tmpfs 上进行内核编译。

页错误可扩展性也很重要。在测量并行页错误测试时，多进程测试可能比多线程测试更好，因为它有共享对象/状态的噪声。

但上述两种情况都是在极端情况下进行测试。在内存控制器下尝试常规测试总是有帮助的。

## 4.1 故障排除

有时用户可能会发现 cgroup 下的应用程序被 OOM 杀手终止。有以下几个原因：

1. cgroup 限制太低（低到无法做任何有用的事情）

2. 用户正在使用匿名内存，并且交换被关闭或太低

一个同步操作，然后 echo 1 > /proc/sys/vm/drop_caches 将有助于清除 cgroup 中缓存的一些页面（页缓存页面）。

要了解发生了什么，根据“10. OOM 控制”（下面）禁用 OOM_Kill 并观察发生的情况将是有帮助的。

## 4.2 任务迁移

当一个任务从一个 cgroup 迁移到另一个 cgroup 时，其计费默认情况下不会被转发。从原始 cgroup 分配的页面仍然会被计费，只有当页面被释放或回收时，计费才会被删除。

你可以在任务迁移时移动任务的计费。请参阅 8. “在任务迁移时移动计费”

## 4.3 删除 cgroup

可以通过 rmdir 删除一个 cgroup，但如 4.1 和 4.2 节中所讨论的，即使所有任务都已迁移出 cgroup，它可能仍有一些计费与之相关。（因为我们是根据页面而不是任务计费的。）

我们将统计信息移动到根（如果 use_hierarchy==0）或父级（如果 use_hierarchy==1），除了从子级取消计费外，计费没有变化。

在删除 cgroup 时，交换信息中记录的计费不会更新。记录的信息将被丢弃，使用交换（swapcache）的 cgroup 将被视为其新所有者并计费。

关于 use_hierarchy，请参阅第 6 节。

# 5. 其他接口。

## 5.1 force_empty

提供了 memory.force_empty 接口，用于使 cgroup 的内存使用量为空。

当向此接口写入任何内容时：

    # echo 0 > memory.force_empty

cgroup 将被回收，并且将尽可能多地回收页面。

此接口的典型用例是在调用 rmdir()之前。

因为 rmdir()将所有页面移动到父级，一些未使用的页缓存可能会被移动到父级。如果你想避免这种情况，force_empty 将很有用。

此外，请注意，当设置 memory.kmem.limit_in_bytes 时，由于内核页面而产生的计费仍将被看到。这不被视为失败，写入仍将返回成功。在这种情况下，预期 memory.kmem.usage_in_bytes == memory.usage_in_bytes。

关于 use_hierarchy，请参阅第 6 节。

## 5.2 stat 文件

memory.stat 文件包括以下统计信息：

每个内存 cgroup 的本地状态
```
cache - 页缓存内存的字节数。
rss - 匿名和交换缓存内存的字节数（包括透明大页）。
rss_huge - 匿名透明大页的字节数。
mapped_file - 映射文件的字节数（包括 tmpfs/shmem）
pgpgin - 内存 cgroup 的计费事件数。每次将页面计为 cgroup 的映射匿名页（RSS）或缓存页（页缓存）时，就会发生计费事件。
pgpgout - 内存 cgroup 的取消计费事件数。每次从 cgroup 中取消计费页面时，就会发生取消计费事件。
swap - 交换使用的字节数
writeback - 排队等待同步到磁盘的文件/匿名缓存的字节数。
inactive_anon - 非活动 LRU 列表中的匿名和交换缓存内存的字节数。
active_anon - 活动 LRU 列表中的匿名和交换缓存内存的字节数。
inactive_file - 非活动 LRU 列表中的文件支持内存的字节数。
active_file - 活动 LRU 列表中的文件支持内存的字节数。
unevictable - 无法回收的内存的字节数（mlocked 等）。
```

考虑层次结构的状态（请参阅 memory.use_hierarchy 设置）
```
hierarchical_memory_limit - 考虑到层次结构的内存限制的字节数，内存 cgroup 位于该层次结构之下。
hierarchical_memsw_limit - 考虑到层次结构的内存+交换限制的字节数，内存 cgroup 位于该层次结构之下。
total_<counter> - <counter>的层次结构版本，除了 cgroup 自己的值之外，还包括所有层次结构子级的<counter>值的总和，即 total_cache。
```
以下附加统计信息依赖于 CONFIG_DEBUG_VM。
```
recent_rotated_anon - VM 内部参数。（请参阅 mm/vmscan.c）
recent_rotated_file - VM 内部参数。（请参阅 mm/vmscan.c）
recent_scanned_anon - VM 内部参数。（请参阅 mm/vmscan.c）
recent_scanned_file - VM 内部参数。（请参阅 mm/vmscan.c）
```
备注：

recent_rotated 表示 LRU 旋转的最近频率。

recent_scanned 表示最近对 LRU 的扫描次数。

为了更好的调试，请参阅代码以了解其含义。

注意：

仅将匿名和交换缓存内存列为“rss”统计的一部分。

这不应与真实的“驻留集大小”或 cgroup 使用的物理内存量相混淆。

“rss + file_mapped”将为你提供 cgroup 的驻留集大小。

（注意：文件和 shmem 可能在其他 cgroup 之间共享。在这种情况下，只有当内存 cgroup 是页缓存的所有者时，才会计算 file_mapped。）

## 5.3 swappiness

覆盖特定组的 /proc/sys/vm/swappiness。根 cgroup 中的可调参数对应于全局 swappiness 设置。

请注意，与全局回收期间不同，限制回收强制 0 swappiness 即使有交换存储空间可用也确实防止任何交换。如果没有文件页面可回收，这可能会导致 memcg OOM 杀手。

## 5.4 failcnt

内存 cgroup 提供 memory.failcnt 和 memory.memsw.failcnt 文件。这个 failcnt（== 失败计数）显示使用计数器达到其限制的次数。当内存 cgroup 达到限制时，failcnt 增加，其下的内存将被回收。

你可以通过将 0 写入 failcnt 文件来重置 failcnt。

    # echo 0 >.../memory.failcnt

## 5.5 usage_in_bytes

为了提高效率，与其他内核组件一样，内存 cgroup 使用一些优化来避免不必要的缓存行伪共享。usage_in_bytes 受到该方法的影响，并且不会显示内存（和交换）使用的“精确”值，它是一个用于高效访问的模糊值。（当然，必要时，它是同步的。）

如果你想知道更精确的内存使用情况，你应该使用 memory.stat 中的 RSS+CACHE（+SWAP）值（请参阅 5.2）。

## 5.6 numa_stat

这类似于 numa_maps，但基于每个 memcg 运行。

这对于在 memcg 内提供对 numa 局部性信息的可见性很有用，因为页面可以从任何物理节点分配。

一个用例是通过将此信息与应用程序的 CPU 分配相结合来评估应用程序性能。

每个 memcg 的 numa_stat 文件包括“total”、“file”、“anon”和“unevictable”每个节点的页面计数，包括“hierarchical_<counter>”，它除了 memcg 自己的值之外，还汇总了所有层次结构子级的值。

memory.numa_stat 的输出格式为：

total=<总页数> N0=<节点 0 页数> N1=<节点 1 页数>...

file=<总文件页数> N0=<节点 0 页数> N1=<节点 1 页数>...

anon=<总匿名页数> N0=<节点 0 页数> N1=<节点 1 页数>...

unevictable=<总匿名页数> N0=<节点 0 页数> N1=<节点 1 页数>...

hierarchical_<counter>=<计数器页数> N0=<节点 0 页数> N1=<节点 1 页数>...

“total”计数是 file + anon + unevictable 的总和。

# 6. 层次结构支持

内存控制器支持深度层次结构和层次结构记账。通过在 cgroup 文件系统中创建适当的 cgroup 来创建层次结构。例如，考虑以下 cgroup 文件系统层次结构：

       根
     /  |   \
            /    \
           a     b     c
                      | \
                      |  \
                      d   e

在上面的图中，启用层次结构记账后，e 的所有内存使用量都将计入其祖先，直到根（即 c 和根），如果 memory.use_hierarchy 已启用。如果祖先之一超过其限制，回收算法将从祖先及其子级的任务中回收内存。

## 6.1 启用层次结构记账和回收

默认情况下，内存 cgroup 禁用层次结构功能。可以通过将 1 写入根 cgroup 的 memory.use_hierarchy 文件来启用支持：

    # echo 1 > memory.use_hierarchy

可以通过以下方式禁用该功能：

    # echo 0 > memory.use_hierarchy

注意 1：如果 cgroup 已经在其下面创建了其他 cgroup，或者父 cgroup 已启用 use_hierarchy，则启用/禁用将失败。

注意 2：当 panic_on_oom 设置为“2”时，在任何 cgroup 中发生 OOM 事件时，整个系统将崩溃。

# 7. 软限制

软限制允许更大程度地共享内存。软限制背后的想法是允许控制组根据需要使用尽可能多的内存，前提是：

a. 没有内存争用

b. 它们不超过其硬限制

当系统检测到内存争用或低内存时，控制组将被推回到其软限制。如果每个控制组的软限制非常高，它们将尽可能地被推回，以确保一个控制组不会使其他控制组饿死内存。

请注意，软限制是一个尽力而为的功能；它没有任何保证，但它会尽力确保在内存严重争用时，根据软限制提示/设置分配内存。目前，基于软限制的回收设置为从 balance_pgdat（kswapd）调用。

## 7.1 接口

可以使用以下命令设置软限制（在本示例中，我们假设软限制为 256 MiB）：

    # echo 256M > memory.soft_limit_in_bytes

如果我们想将其更改为 1G，我们可以在任何时候使用：

    # echo 1G > memory.soft_limit_in_bytes

注意 1：由于软限制涉及回收内存以平衡内存 cgroup，因此它们需要很长时间才能生效。

注意 2：建议始终将软限制设置为低于硬限制，否则硬限制将优先。

# 8. 在任务迁移时移动计费

用户可以在任务迁移时移动与任务相关的计费，即从旧 cgroup 中取消任务的页面计费，并将其计入新 cgroup。由于缺少页表，此功能在!CONFIG_MMU 环境中不受支持。

## 8.1 接口

此功能默认是禁用的。可以通过写入目标 cgroup 的 memory.move_charge_at_immigrate 来启用（并再次禁用）。

如果要启用它：

    # echo (一些正值) > memory.move_charge_at_immigrate

注意：memory.move_charge_at_immigrate 的每个位都有其自己的含义，关于应该移动哪种类型的计费。请参阅 8.2 以获取详细信息。

注意：仅当你移动 mm->owner 时，即线程组的领导者时，才会移动计费。

注意：如果我们在目标 cgroup 中找不到足够的空间用于任务，我们将尝试通过回收内存来腾出空间。如果我们无法腾出足够的空间，任务迁移可能会失败。

注意：如果移动的计费很多，可能需要几秒钟。

并且如果要再次禁用它：

    # echo 0 > memory.move_charge_at_immigrate

## 8.2 可以移动的计费类型

memory.move_charge_at_immigrate 的每个位都有其自己的含义，关于应该移动哪种类型的计费。但在任何情况下，必须注意，只有当页面或交换的计费被计入任务的当前（旧）内存 cgroup 时，才能移动其计费。

      位 | 要移动哪种类型的计费？
      -----+------------------------------------------------------------------------
       0  | 目标任务使用的匿名页面的计费（或其交换）。
          | 你必须启用交换扩展（请参阅 2.4）才能启用交换计费的移动。
      -----+------------------------------------------------------------------------
       1  | 目标任务映射的文件页面（普通文件、tmpfs 文件（例如 ipc 共享内存）和 tmpfs 文件的交换）的计费。

与匿名页面的情况不同，被任务映射范围中的文件页面（和交换区）即使任务尚未发生页面错误也将被移动，即它们可能不是任务的“RSS”，而是映射相同文件的其他任务的“RSS”。并且页面的映射计数被忽略（即使`page_mapcount(page) > 1`，页面也可以被移动）。你必须启用交换扩展（见 2.4）才能启用交换区的移动。

## 8.3 TODO

- 所有的移动电荷操作都在`cgroup_mutex`下进行。长时间持有互斥锁不是好的行为，所以我们可能需要一些技巧。

# 9. 内存阈值

内存 cgroup 使用 cgroups 通知 API（见 cgroups.txt）来实现内存阈值。它允许注册多个内存和内存交换阈值，并在跨越阈值时获得通知。

要注册一个阈值，应用程序必须：

- 使用`eventfd(2)`创建一个`eventfd`；

- 打开`memory.usage_in_bytes`或`memory.memsw.usage_in_bytes`；

- 将类似“<event_fd> <fd of memory.usage_in_bytes> <threshold>”的字符串写入`cgroup.event_control`。

当内存使用在任何方向跨越阈值时，应用程序将通过`eventfd`被通知。它适用于根 cgroup 和非根 cgroup。

# 10. OOM 控制

`memory.oom_control`文件用于 OOM 通知和其他控制。

内存 cgroup 使用 cgroups 通知 API（见 cgroups.txt）实现 OOM 通知器。它允许注册多个 OOM 通知传递，并在 OOM 发生时获得通知。

要注册一个通知器，应用程序必须：

 - 使用`eventfd(2)`创建一个`eventfd`；

 - 打开`memory.oom_control`文件；

 - 将类似“<event_fd> <fd of memory.oom_control>”的字符串写入`cgroup.event_control`。

当 OOM 发生时，应用程序将通过`eventfd`被通知。OOM 通知不适用于根 cgroup。

你可以通过将“1”写入`memory.oom_control`文件来禁用 OOM 杀手，如下所示：

`#echo 1 > memory.oom_control`

如果禁用了 OOM 杀手，cgroup 下的任务在请求可计费内存时将在内存 cgroup 的 OOM-waitqueue 中挂起/睡眠。

要使它们运行，你必须通过以下方式放松内存 cgroup 的 OOM 状态：

 - 增大限制或减少使用量。要减少使用量，

 - 杀死一些任务。

 - 将一些任务移动到其他具有账户迁移的组。

 - 删除一些文件（在 tmpfs 上？）

然后，停止的任务将再次工作。

在读取时，显示 OOM 的当前状态。

`oom_kill_disable 0 或 1（如果为 1，OOM 杀手被禁用）`

`under_oom 0 或 1（如果为 1，内存 cgroup 处于 OOM 状态，任务可能被停止）`

# 11. 内存压力

压力级别通知可用于监视内存分配成本；根据压力，应用程序可以实现管理其内存资源的不同策略。压力级别定义如下：

“低”级别意味着系统正在为新分配回收内存。监视此回收活动可能对维持缓存级别有用。在收到通知后，程序（通常是“活动管理器”）可以分析`vmstat`并提前采取行动（即提前关闭不重要的服务）。

“中等”级别意味着系统正在经历中等内存压力，系统可能正在进行交换、将活动文件缓存换出等。在此事件发生时，应用程序可以决定进一步分析`vmstat`/`zoneinfo`/`memcg`或内部内存使用统计信息，并释放任何可以轻松从磁盘重建或重新读取的资源。

“临界”级别意味着系统正在积极颠簸，即将内存不足（OOM），甚至内核 OOM 杀手正在触发。应用程序应尽一切可能帮助系统。此时咨询`vmstat`或任何其他统计信息可能为时已晚，因此建议立即采取行动。

事件向上传播直到事件被处理，即事件不是传递式的。这意味着：例如，你有三个 cgroup：A->B->C。现在你在 cgroup A、B 和 C 上设置了一个事件监听器，并假设组 C 经历了一些压力。在这种情况下，只有组 C 将收到通知，即组 A 和 B 将不会收到通知。这样做是为了避免过多的“广播”消息，这会干扰系统，特别是在我们内存不足或颠簸时。因此，请明智地组织 cgroup，或手动传播事件（或者，要求我们实现传递式事件，并解释为什么你需要它们。）

`memory.pressure_level`文件仅用于设置一个`eventfd`。要注册一个通知，应用程序必须：

- 使用`eventfd(2)`创建一个`eventfd`；

- 打开`memory.pressure_level`；

- 将类似“<event_fd> <fd of memory.pressure_level> <level>”的字符串写入`cgroup.event_control`。

当内存压力达到特定级别（或更高）时，应用程序将通过`eventfd`被通知。对`memory.pressure_level`的读/写操作未实现。

测试：

这里有一个小脚本示例，它创建一个新的 cgroup，设置内存限制，在 cgroup 中设置一个通知，然后使子 cgroup 经历临界压力：

`# cd /sys/fs/cgroup/memory/`

`# mkdir foo`

`# cd foo`

`# cgroup_event_listener memory.pressure_level low &`

`# echo 8000000 > memory.limit_in_bytes`

`# echo 8000000 > memory.memsw.limit_in_bytes`

`# echo $$ > tasks`

`# dd if=/dev/zero | read x`

（预计会有一堆通知，最终，OOM 杀手将触发。）

# 12. TODO

1. 使每个 cgroup 扫描器首先回收非共享页面

2. 教导控制器考虑共享页面

3. 在限制尚未达到但使用量越来越接近时在后台开始回收

# 总结

总体而言，内存控制器一直是一个稳定的控制器，并在社区中进行了广泛的评论和讨论。

参考文献

1. Singh, Balbir. RFC: Memory Controller, http://lwn.net/Articles/206697/

2. Singh, Balbir. Memory Controller (RSS Control),

   http://lwn.net/Articles/222762/

3. Emelianov, Pavel. Resource controllers based on process cgroups

   http://lkml.org/lkml/2007/3/6/198

4. Emelianov, Pavel. RSS controller based on process cgroups (v2)

   http://lkml.org/lkml/2007/4/9/78

5. Emelianov, Pavel. RSS controller based on process cgroups (v3)

   http://lkml.org/lkml/2007/5/30/244

6. Menage, Paul. Control Groups v10, http://lwn.net/Articles/236032/

7. Vaidyanathan, Srinivasan, Control Groups: Pagecache accounting and control

   subsystem (v3), http://lwn.net/Articles/235534/

8. Singh, Balbir. RSS controller v2 test results (lmbench),

   http://lkml.org/lkml/2007/5/17/232

9. Singh, Balbir. RSS controller v2 AIM9 results

   http://lkml.org/lkml/2007/5/18/1

10. Singh, Balbir. Memory controller v6 test results,

    http://lkml.org/lkml/2007/8/19/36

11. Singh, Balbir. Memory controller introduction (v6),

    http://lkml.org/lkml/2007/8/17/69

12. Corbet, Jonathan, Controlling memory use in cgroups,

    http://lwn.net/Articles/243795/

</markdown_document>