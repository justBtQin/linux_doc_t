**What**: /sys/devices/system/node/possible

**Date**: 十月 2002

**Contact**: Linux 内存管理列表 <linux-mm@kvack.org>

**Description**: 可能在某个时刻变为在线的节点。

**What**: /sys/devices/system/node/online

**Date**: 十月 2002

**Contact**: Linux 内存管理列表 <linux-mm@kvack.org>

**Description**: 处于在线状态的节点。

**What**: /sys/devices/system/node/has_normal_memory

**Date**: 十月 2002

**Contact**: Linux 内存管理列表 <linux-mm@kvack.org>

**Description**: 具有常规内存的节点。

**What**: /sys/devices/system/node/has_cpu

**Date**: 十月 2002

**Contact**: Linux 内存管理列表 <linux-mm@kvack.org>

**Description**: 具有一个或多个 CPU 的节点。

**What**: /sys/devices/system/node/has_high_memory

**Date**: 十月 2002

**Contact**: Linux 内存管理列表 <linux-mm@kvack.org>

**Description**: 具有常规或高内存的节点。取决于 CONFIG_HIGHMEM。

**What**: /sys/devices/system/node/nodeX

**Date**: 十月 2002

**Contact**: Linux 内存管理列表 <linux-mm@kvack.org>

**Description**: 当 CONFIG_NUMA 启用时，这是一个包含关于节点 X 信息的目录，例如哪些 CPU 是该节点的本地 CPU。每个文件的详细信息如下。

**What**: /sys/devices/system/node/nodeX/cpumap

**Date**: 十月 2002

**Contact**: Linux 内存管理列表 <linux-mm@kvack.org>

**Description**: 节点的 cpumap。

**What**: /sys/devices/system/node/nodeX/cpulist

**Date**: 十月 2002

**Contact**: Linux 内存管理列表 <linux-mm@kvack.org>

**Description**: 与该节点关联的 CPU。

**What**: /sys/devices/system/node/nodeX/meminfo

**Date**: 十月 2002

**Contact**: Linux 内存管理列表 <linux-mm@kvack.org>

**Description**: 提供关于节点的内存分布和使用情况的信息。类似于 /proc/meminfo，请参阅 Documentation/filesystems/proc.txt。

**What**: /sys/devices/system/node/nodeX/numastat

**Date**: 十月 2002

**Contact**: Linux 内存管理列表 <linux-mm@kvack.org>

**Description**: 节点的命中/未命中统计信息，以页为单位。请参阅 Documentation/numastat.txt。

**What**: /sys/devices/system/node/nodeX/distance

**Date**: 十月 2002

**Contact**: Linux 内存管理列表 <linux-mm@kvack.org>

**Description**: 节点与系统中所有其他节点之间的距离。

**What**: /sys/devices/system/node/nodeX/vmstat

**Date**: 十月 2002

**Contact**: Linux 内存管理列表 <linux-mm@kvack.org>

**Description**: 节点的分区虚拟内存统计信息。这是 numastat 的超集。

**What**: /sys/devices/system/node/nodeX/compact

**Date**: 二月 2010

**Contact**: Mel Gorman <mel@csn.ul.ie>

**Description**: 当写入此文件时，该节点内的所有内存将被压缩。完成后，内存将被释放为尽可能多的连续页块。

**What**: /sys/devices/system/node/nodeX/hugepages/hugepages-<size>/

**Date**: 十二月 2009

**Contact**: Lee Schermerhorn <lee.schermerhorn@hp.com>

**Description**: 节点的大页大小控制/查询属性。请参阅 Documentation/vm/hugetlbpage.txt。