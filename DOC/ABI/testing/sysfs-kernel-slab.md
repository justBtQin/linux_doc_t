**What**：`/sys/kernel/slab`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `/sys/kernel/slab` 目录包含每个缓存的 SLUB 分配器内部状态的快照。某些文件可以修改以更改缓存的行为（以及任何与之合并的缓存，如果有的话）。

**Users**：内核内存调优工具

**What**：`/sys/kernel/slab/cache/aliases`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `aliases` 文件为只读，指定有多少个缓存合并到了此缓存中。

**What**：`/sys/kernel/slab/cache/align`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `align` 文件为只读，指定缓存对象的字节对齐方式。

**What**：`/sys/kernel/slab/cache/alloc_calls`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `alloc_calls` 文件为只读，列出了从此缓存进行分配的内核代码位置。只有在为该缓存启用调试时，`alloc_calls` 文件才包含信息（请参阅 Documentation/vm/slub.txt）。

**What**：`/sys/kernel/slab/cache/alloc_fastpath`

**Date**：2008 年 2 月

**KernelVersion**：2.6.25

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `alloc_fastpath` 文件显示通过快速路径分配的对象数量。可以写入以清除当前计数。

    当 CONFIG_SLUB_STATS 启用时可用。

**What**：`/sys/kernel/slab/cache/alloc_from_partial`

**Date**：2008 年 2 月

**KernelVersion**：2.6.25

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `alloc_from_partial` 文件显示 CPU 缓存已满并通过使用部分使用的缓存列表中的缓存进行重新填充的次数。可以写入以清除当前计数。

    当 CONFIG_SLUB_STATS 启用时可用。

**What**：`/sys/kernel/slab/cache/alloc_refill`

**Date**：2008 年 2 月

**KernelVersion**：2.6.25

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `alloc_refill` 文件显示每次 CPU 空闲列表为空但由于远程 CPU 释放而有可用对象的次数。可以写入以清除当前计数。

    当 CONFIG_SLUB_STATS 启用时可用。

**What**：`/sys/kernel/slab/cache/alloc_slab`

**Date**：2008 年 2 月

**KernelVersion**：2.6.25

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `alloc_slab` 文件显示从页面分配器分配新缓存的次数。可以写入以清除当前计数。

    当 CONFIG_SLUB_STATS 启用时可用。

**What**：`/sys/kernel/slab/cache/alloc_slowpath`

**Date**：2008 年 2 月

**KernelVersion**：2.6.25

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `alloc_slowpath` 文件显示由于重新填充或从部分或新缓存分配而使用慢速路径分配的对象数量。可以写入以清除当前计数。

    当 CONFIG_SLUB_STATS 启用时可用。

**What**：`/sys/kernel/slab/cache/cache_dma`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `cache_dma` 文件为只读，指定对象是否来自 ZONE_DMA。

    当 CONFIG_ZONE_DMA 启用时可用。

**What**：`/sys/kernel/slab/cache/cpu_slabs`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `cpu_slabs` 文件为只读，显示有多少个 CPU 缓存处于活动状态及其 NUMA 位置。

**What**：`/sys/kernel/slab/cache/cpuslab_flush`

**Date**：2009 年 4 月

**KernelVersion**：2.6.31

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `cpuslab_flush` 文件显示由于销毁或缩小缓存、CPU 离线或强制从特定节点分配而导致缓存的 CPU 缓存被刷新的次数。可以写入以清除当前计数。

    当 CONFIG_SLUB_STATS 启用时可用。

**What**：`/sys/kernel/slab/cache/ctor`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `ctor` 文件为只读，指定缓存的对象构造函数，在为新缓存分配时为每个对象调用。

**What**：`/sys/kernel/slab/cache/deactivate_empty`

**Date**：2008 年 2 月

**KernelVersion**：2.6.25

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `deactivate_empty` 文件显示空的 CPU 缓存被停用的次数。可以写入以清除当前计数。

    当 CONFIG_SLUB_STATS 启用时可用。

**What**：`/sys/kernel/slab/cache/deactivate_full`

**Date**：2008 年 2 月

**KernelVersion**：2.6.25

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `deactivate_full` 文件显示满的 CPU 缓存被停用的次数。可以写入以清除当前计数。

    当 CONFIG_SLUB_STATS 启用时可用。

**What**：`/sys/kernel/slab/cache/deactivate_remote_frees`

**Date**：2008 年 2 月

**KernelVersion**：2.6.25

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `deactivate_remote_frees` 文件显示 CPU 缓存被停用且包含远程释放的空闲对象的次数。可以写入以清除当前计数。

    当 CONFIG_SLUB_STATS 启用时可用。

**What**：`/sys/kernel/slab/cache/deactivate_to_head`

**Date**：2008 年 2 月

**KernelVersion**：2.6.25

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `deactivate_to_head` 文件显示部分 CPU 缓存被停用并添加到其节点的部分列表头部的次数。可以写入以清除当前计数。

    当 CONFIG_SLUB_STATS 启用时可用。

**What**：`/sys/kernel/slab/cache/deactivate_to_tail`

**Date**：2008 年 2 月

**KernelVersion**：2.6.25

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `deactivate_to_tail` 文件显示部分 CPU 缓存被停用并添加到其节点的部分列表尾部的次数。可以写入以清除当前计数。

    当 CONFIG_SLUB_STATS 启用时可用。

**What**：`/sys/kernel/slab/cache/destroy_by_rcu`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `destroy_by_rcu` 文件为只读，指定是否通过 rcu 释放缓存（而不是对象）。

**What**：`/sys/kernel/slab/cache/free_add_partial`

**Date**：2008 年 2 月

**KernelVersion**：2.6.25

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `free_add_partial` 文件显示在满的缓存中释放对象以至于必须将其添加到其节点的部分列表的次数。可以写入以清除当前计数。

    当 CONFIG_SLUB_STATS 启用时可用。

**What**：`/sys/kernel/slab/cache/free_calls`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `free_calls` 文件为只读，如果启用了缓存调试，则列出对象释放的位置（请参阅 Documentation/vm/slub.txt）。

**What**：`/sys/kernel/slab/cache/free_fastpath`

**Date**：2008 年 2 月

**KernelVersion**：2.6.25

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `free_fastpath` 文件显示通过快速路径释放的对象数量，因为它是来自 CPU 缓存的对象。可以写入以清除当前计数。

    当 CONFIG_SLUB_STATS 启用时可用。

**What**：`/sys/kernel/slab/cache/free_frozen`

**Date**：2008 年 2 月

**KernelVersion**：2.6.25

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `free_frozen` 文件显示释放到冻结缓存（即远程 CPU 缓存）的对象数量。可以写入以清除当前计数。

    当 CONFIG_SLUB_STATS 启用时可用。

**What**：`/sys/kernel/slab/cache/free_remove_partial`

**Date**：2008 年 2 月

**KernelVersion**：2.6.25

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `free_remove_partial` 文件显示在释放对象到现在为空的缓存以至于必须从其节点的部分列表中删除的次数。可以写入以清除当前计数。

    当 CONFIG_SLUB_STATS 启用时可用。

**What**：`/sys/kernel/slab/cache/free_slab`

**Date**：2008 年 2 月

**KernelVersion**：2.6.25

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `free_slab` 文件显示释放空缓存回页面分配器的次数。可以写入以清除当前计数。

    当 CONFIG_SLUB_STATS 启用时可用。

**What**：`/sys/kernel/slab/cache/free_slowpath`

**Date**：2008 年 2 月

**KernelVersion**：2.6.25

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `free_slowpath` 文件显示使用慢速路径（即释放到满或部分缓存）释放的对象数量。可以写入以清除当前计数。

    当 CONFIG_SLUB_STATS 启用时可用。

**What**：`/sys/kernel/slab/cache/hwcache_align`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `hwcache_align` 文件为只读，指定对象是否在缓存行上对齐。

**What**：`/sys/kernel/slab/cache/min_partial`

**Date**：2009 年 2 月

**KernelVersion**：2.6.30

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、David Rientjes <rientjes@google.com>

**Description**：

    `min_partial` 文件指定节点的部分列表上应保留的空缓存数量，以避免分配新缓存的开销。此类缓存可以通过使用 `shrink` 文件回收。

**What**：`/sys/kernel/slab/cache/object_size`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `object_size` 文件为只读，指定缓存的对象大小。

**What**：`/sys/kernel/slab/cache/objects`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> 、Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `objects` 文件为只读，显示活动对象的数量以及它们来自哪些节点。

**What**：`/sys/kernel/slab/cache/objects_partial`

**Date**：2008 年 4 月

**KernelVersion**：2.6.26

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> ，Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `objects_partial` 文件为只读，展示部分缓存上有多少对象以及它们来自哪些节点。

**What**：`/sys/kernel/slab/cache/objs_per_slab`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> ，Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `objs_per_slab` 文件为只读，指定在`/sys/kernel/slab/cache/order`中指定的顺序下，单个缓存可分配的对象数量。

**What**：`/sys/kernel/slab/cache/order`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> ，Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `order`文件指定分配新缓存的页面顺序。它是可写的，可更改以增加每个缓存的对象数量。若因碎片导致无法分配缓存，SLUB 将根据其特征以最小可能的顺序重试。

    当指定`debug_guardpage_minorder=N`（N > 0）参数（见 Documentation/kernel-parameters.txt）时，使用最小可能的顺序，此 sysfs 条目在运行时不可用于更改顺序。

**What**：`/sys/kernel/slab/cache/order_fallback`

**Date**：2008 年 4 月

**KernelVersion**：2.6.26

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> ，Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `order_fallback`文件显示在缓存的顺序下无法分配新缓存而回退到最小可能顺序的次数。可写入以清除当前计数。

    当 CONFIG_SLUB_STATS 启用时可用。

**What**：`/sys/kernel/slab/cache/partial`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> ，Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `partial`文件为只读，展示有多少个部分缓存以及每个节点的列表长度。

**What**：`/sys/kernel/slab/cache/poison`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> ，Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `poison`文件指定分配新缓存时是否应给对象“下毒”。

**What**：`/sys/kernel/slab/cache/reclaim_account`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> ，Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `reclaim_account`文件指定缓存的对象是否可回收（并按其移动性分组）。

**What**：`/sys/kernel/slab/cache/red_zone`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> ，Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `red_zone`文件指定缓存的对象是否有红色区域。

**What**：`/sys/kernel/slab/cache/remote_node_defrag_ratio`

**Date**：2008 年 1 月

**KernelVersion**：2.6.25

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> ，Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `remote_node_defrag_ratio`文件指定 SLUB 尝试用远程节点的部分缓存来填充 CPU 缓存而非在本地节点分配新缓存的次数百分比。这减少了整个系统的内存浪费量，但可能代价高昂。

    当 CONFIG_NUMA 启用时可用。

**What**：`/sys/kernel/slab/cache/sanity_checks`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> ，Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `sanity_checks`文件指定是否应对释放执行昂贵的检查，至少启用双重释放检查。启用`sanity_checks`的缓存不能与未启用的缓存合并。

**What**：`/sys/kernel/slab/cache/shrink`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> ，Christoph Lameter <cl@linux-foundation.org>

**Description**：

    当应从缓存回收内存时写入`shrink`文件。空闲的部分缓存被释放，部分列表被排序，以便首先使用可用对象最少的缓存。

**What**：`/sys/kernel/slab/cache/slab_size`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> ，Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `slab_size`文件为只读，以字节为单位指定带有元数据（调试信息和对齐）的对象大小。

**What**：`/sys/kernel/slab/cache/slabs`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> ，Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `slabs`文件为只读，展示有多少个缓存（包括 CPU 缓存和部分缓存）以及它们来自哪些节点。

**What**：`/sys/kernel/slab/cache/store_user`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> ，Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `store_user`文件指定是否应跟踪缓存的分配或释放位置。

**What**：`/sys/kernel/slab/cache/total_objects`

**Date**：2008 年 4 月

**KernelVersion**：2.6.26

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> ，Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `total_objects`文件为只读，显示一个缓存拥有的总对象数量以及它们来自哪些节点。

**What**：`/sys/kernel/slab/cache/trace`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> ，Christoph Lameter <cl@linux-foundation.org>

**Description**：

    `trace`文件指定是否应跟踪对象的分配和释放。

**What**：`/sys/kernel/slab/cache/validate`

**Date**：2007 年 5 月

**KernelVersion**：2.6.22

**Contact**：Pekka Enberg <penberg@cs.helsinki.fi> ，Christoph Lameter <cl@linux-foundation.org>

**Description**：

    写入`validate`文件会导致 SLUB 遍历其所有缓存的对象并检查元数据的有效性。
