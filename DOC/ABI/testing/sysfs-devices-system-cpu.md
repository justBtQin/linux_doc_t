**What**：`/sys/devices/system/cpu/`

**Date**：git 之前的历史

**Contact**：Linux 内核邮件列表 <linux-kernel@vger.kernel.org>

**Description**：

    一组全局和单个 CPU 的属性集合。

    单个 CPU 的属性包含在以内核逻辑 CPU 编号命名的子目录中，例如：

    `/sys/devices/system/cpu/cpu#/`

**What**：`/sys/devices/system/cpu/kernel_max` 

`/sys/devices/system/cpu/offline` 

`/sys/devices/system/cpu/online` 

`/sys/devices/system/cpu/possible` 

`/sys/devices/system/cpu/present`

**Date**：2008 年 12 月

**Contact**：Linux 内核邮件列表 <linux-kernel@vger.kernel.org>

**Description**：描述与热插拔相关的内核限制的 CPU 拓扑文件。简要说明：

    `kernel_max`：内核配置所允许的最大 CPU 索引。

    `offline`：因热插拔关闭或超过内核配置允许的 CPU 数量限制（上述的 `kernel_max`）而未在线的 CPU。

    `online`：在线并正在被调度的 CPU。

    `possible`：已分配资源且如果存在就可以上线的 CPU。

    `present`：已被识别为存在于系统中的 CPU。

    更多信息请参阅 Documentation/cputopology.txt 。

**What**：`/sys/devices/system/cpu/probe` 

`/sys/devices/system/cpu/release`

**Date**：2009 年 11 月

**Contact**：Linux 内核邮件列表 <linux-kernel@vger.kernel.org>

**Description**：CPU 的动态添加和移除。这并非热插拔移除，而是指从系统中完全移除/添加 CPU。

    `probe`：向此文件写入将动态向系统添加一个 CPU。写入文件以添加 CPU 的信息是特定于架构的。

    `release`：向此文件写入将动态从系统中移除一个 CPU。写入文件以移除 CPU 的信息是特定于架构的。

**What**：`/sys/devices/system/cpu/cpu#/node`

**Date**：2009 年 10 月

**Contact**：Linux 内存管理邮件列表 <linux-mm@kvack.org>

**Description**：发现 CPU 所属的 NUMA 节点

    当 CONFIG_NUMA 启用时，会有一个指向相应 NUMA 节点目录的符号链接。

    例如，对于 NUMA 节点 2 中的 cpu42，会创建以下符号链接：

    `/sys/devices/system/cpu/cpu42/node2 -> ../../node/node2`

**What**：`/sys/devices/system/cpu/cpu#/topology/core_id` 

`/sys/devices/system/cpu/cpu#/topology/core_siblings` 

`/sys/devices/system/cpu/cpu#/topology/core_siblings_list` 

`/sys/devices/system/cpu/cpu#/topology/physical_package_id` 

`/sys/devices/system/cpu/cpu#/topology/thread_siblings` 

`/sys/devices/system/cpu/cpu#/topology/thread_siblings_list`

**Date**：2008 年 12 月

**Contact**：Linux 内核邮件列表 <linux-kernel@vger.kernel.org>

**Description**：描述逻辑 CPU 与同一物理封装中其他核心和线程关系的 CPU 拓扑文件。

    系统中的每个逻辑 CPU 都会创建一个 cpu# 目录，例如 `/sys/devices/system/cpu/cpu42/`。

    简要说明，上述文件如下：

    `core_id`：cpu# 的 CPU 核心 ID。通常是硬件平台的标识符（而非内核的）。实际值取决于架构和平台。

    `core_siblings`：内核内部的 cpu# 在同一物理封装内的硬件线程映射。

    `core_siblings_list`：与 cpu# 在同一物理封装内的逻辑 CPU 编号的人类可读列表。

    `physical_package_id`：cpu# 的物理封装 ID。通常对应于物理插座编号，但实际值取决于架构和平台。

    `thread_siblings`：内核内部的与 cpu# 在同一核心内的硬件线程映射。

    `thread_siblings_list`：与 cpu# 在同一核心内的硬件线程的人类可读列表。

    更多信息请参阅 Documentation/cputopology.txt 。

**What**：`/sys/devices/system/cpu/cpuidle/current_driver` 

`/sys/devices/system/cpu/cpuidle/current_governer_ro`

**Date**：2007 年 9 月

**Contact**：Linux 内核邮件列表 <linux-kernel@vger.kernel.org>

**Description**：发现 cpuidle 策略和机制

    如今的各种 CPU 支持多个通过不同的退出延迟和空闲期间功耗来区分的空闲级别。

    空闲策略（调控器）与空闲机制（驱动程序）不同

    `current_driver`：显示当前的空闲机制。

    `current_governer_ro`：显示当前的空闲策略。

    更多信息请参阅 Documentation/cpuidle/ 中的文件。

**What**：`/sys/devices/system/cpu/cpu#/cpufreq/*`

**Date**：git 之前的历史

**Contact**：linux-pm@vger.kernel.org

**Description**：发现并更改 CPU 的时钟速度

    时钟缩放允许您动态更改 CPU 的时钟速度。这是节省电池电量的好方法，因为时钟速度越低，CPU 消耗的功率越少。

    此目录中有许多可调整的参数。

    更多信息请参阅 Documentation/cpu-freq/ 中的文件。

    特别是，请阅读 Documentation/cpu-freq/user-guide.txt 以了解如何控制这些参数。

**What**：`/sys/devices/system/cpu/cpu#/cpufreq/freqdomain_cpus`

**Date**：2013 年 6 月

**Contact**：linux-pm@vger.kernel.org

**Description**：发现同一 CPU 频率协调域中的 CPU

    `freqdomain_cpus` 是共享相同时钟/频率域（可能在硬件级别）的 CPU（在线 + 离线）列表。该信息可能对 cpufreq 核心隐藏，并且 `related_cpus` 的值可能与 `freqdomain_cpus` 不同。此属性对于使用 acpi-cpufreq 的平台上的用户空间 DVFS 控制器获取更好的功率/性能结果很有用。

    仅当使用 acpi-cpufreq 驱动程序时，此文件才存在。

**What**：`/sys/devices/system/cpu/cpu*/cache/index3/cache_disable_{0,1}`

**Date**：2008 年 8 月

**KernelVersion**：2.6.27

**Contact**：Linux 内核邮件列表 <linux-kernel@vger.kernel.org>

**Description**：禁用 L3 缓存索引

    这些文件存在于每个 CPU 的 `cache/index3` 目录中。每个 `cache_disable_{0,1}` 文件对应一个禁用插槽，可用于禁用一个缓存索引。在具有此功能的处理器上从此类文件读取将返回该节点当前禁用的索引。每个节点或 MCM 机器上的每个内部节点都有一个 L3 结构。向这些文件之一写入有效的索引将导致指定的缓存索引被禁用。

    所有具有 L3 缓存的 AMD 处理器都提供此功能。有关详细信息，请参阅 http://developer.amd.com/documentation/guides/Pages/default.aspx 上的 BKDG 。

**What**：`/sys/devices/system/cpu/cpufreq/boost`

**Date**：2012 年 8 月

**Contact**：Linux 内核邮件列表 <linux-kernel@vger.kernel.org>

**Description**：处理器频率提升控制

    此开关控制整个系统的提升设置。提升允许 CPU 和固件以超过其标称限制的频率运行。

    更多细节可以在 Documentation/cpu-freq/boost.txt 中找到。

**What**：`/sys/devices/system/cpu/cpu#/crash_notes` 

`/sys/devices/system/cpu/cpu#/crash_notes_size`

**Date**：2013 年 4 月

**Contact**：kexec@lists.infradead.org

**Description**：每个 CPU 注释的地址和大小。

    `crash_notes`：保存 cpu# 注释的内存的物理地址。

    `crash_notes_size`：cpu# 注释的大小。

**What**：`/sys/devices/system/cpu/intel_pstate/max_perf_pct` 

`/sys/devices/system/cpu/intel_pstate/min_perf_pct` 

`/sys/devices/system/cpu/intel_pstate/no_turbo`

**Date**：2013 年 2 月

**Contact**：linux-pm@vger.kernel.org

**Description**：Intel P-state 驱动程序的参数

    用于在 Intel Sandybridge+ 处理器中选择当前 P-state 的逻辑。这三个旋钮控制驱动程序将请求的 P-state 的限制。

    `max_perf_pct`：限制驱动程序将请求的最大 P 状态，表示为可用性能的百分比。

    `min_perf_pct`：限制驱动程序将请求的最小 P 状态，表示为可用性能的百分比。

    `no_turbo`：限制驱动程序选择低于涡轮频率范围的 P 状态。

    更多细节可以在 Documentation/cpu-freq/intel-pstate.txt 中找到。

**What**：`/sys/devices/system/cpu/cpu*/cache/index*/<set_of_attributes_mentioned_below>`

**Date**：2014 年 7 月（有记录，在 2008 年 8 月之前就存在）

**Contact**：Sudeep Holla <sudeep.holla@arm.com> 、Linux 内核邮件列表 <linux-kernel@vger.kernel.org>

**Description**：CPU 缓存属性的参数

    `allocation_policy`：
        - `WriteAllocate`：由于写入导致缓存未命中时，为缓存行分配内存位置
        - `ReadAllocate`：由于读取导致缓存未命中时，为缓存行分配内存位置
        - `ReadWriteAllocate`：同时进行写入分配和读取分配

    `attributes`：LEGACY 仅在 IA64 上使用，与 `write_policy` 相同

    `coherency_line_size`：从内存传输到缓存的最小数据量（以字节为单位）

    `level`：多级缓存配置中的缓存层级

    `number_of_sets`：缓存中的集合总数，集合是具有相同缓存索引的缓存行的集合

    `physical_line_partition`：每个缓存标记的物理缓存行数

    `shared_cpu_list`：共享缓存的逻辑 CPU 列表

    `shared_cpu_map`：包含共享缓存的 CPU 列表的逻辑 CPU 掩码

    `size`：总缓存大小（以 kB 为单位）

    `type`：
        - `Instruction`：仅保存指令的缓存
        - `Data`：仅缓存数据的缓存
        - `Unified`：同时保存数据和指令的缓存

    `ways_of_associativity`：在缓存中放置特定内存块的自由度

    `write_policy`：
        - `WriteThrough`：数据同时写入缓存行和较低级别内存中的块
        - `WriteBack`：数据仅写入缓存行，修改后的缓存行仅在被替换时写入主内存