**What**：`/sys/kernel/mm/ksm`

**Date**：2009 年 9 月

**KernelVersion**：2.6.32

**Contact**：Linux 内存管理邮件列表 <linux-mm@kvack.org>

**Description**：内核同页合并（KSM）的接口

**What**：`/sys/kernel/mm/ksm/full_scans`

**What**：`/sys/kernel/mm/ksm/pages_shared`

**What**：`/sys/kernel/mm/ksm/pages_sharing`

**What**：`/sys/kernel/mm/ksm/pages_to_scan`

**What**：`/sys/kernel/mm/ksm/pages_unshared`

**What**：`/sys/kernel/mm/ksm/pages_volatile`

**What**：`/sys/kernel/mm/ksm/run`

**What**：`/sys/kernel/mm/ksm/sleep_millisecs`

**Date**：2009 年 9 月

**Contact**：Linux 内存管理邮件列表 <linux-mm@kvack.org>

**Description**：内核同页合并守护进程的 sysfs 接口

    `full_scans`：对所有可合并区域进行扫描的次数。

    `pages_shared`：正在使用的共享页面数量。

    `pages_sharing`：共享这些页面的更多站点数量，即节省的数量。

    `pages_to_scan`：在 `ksmd` 进入睡眠之前要扫描的当前页面数量。

    `pages_unshared`：唯一但反复检查以进行合并的页面数量。

    `pages_volatile`：变化太快而无法放入树中的页面数量。

    `run`：写入 0 以禁用 `ksm`，读取 0 表示 `ksm` 已禁用。写入 1 以运行 `ksm`，读取 1 表示 `ksm` 正在运行。写入 2 以禁用 `ksm` 并取消合并其所有页面。

    `sleep_millisecs`：`ksm` 在两次扫描之间应睡眠的毫秒数。

    更多信息请参阅 Documentation/vm/ksm.txt 。

**What**：`/sys/kernel/mm/ksm/merge_across_nodes`

**Date**：2013 年 1 月

**KernelVersion**：3.9

**Contact**：Linux 内存管理邮件列表 <linux-mm@kvack.org>

**Description**：控制跨不同 NUMA 节点合并页面。

    当设置为 0 时，仅合并来自同一节点的页面，否则可以合并来自所有节点的页面（默认）。