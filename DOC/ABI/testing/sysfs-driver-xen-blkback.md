**What**: /sys/module/xen_blkback/parameters/max_buffer_pages

**Date**: 2013 年 3 月

**KernelVersion**: 3.11

**Contact**: Roger Pau Monné <roger.pau@citrix.com>

**Description**: 每个块后端缓冲区中要保留的空闲页面的最大数量。

**What**: /sys/module/xen_blkback/parameters/max_persistent_grants

**Date**: 2013 年 3 月

**KernelVersion**: 3.11

**Contact**: Roger Pau Monné <roger.pau@citrix.com>

**Description**: 在 blkback 中可以持久映射的授权的最大数量。如果前端尝试使用超过 max_persistent_grants 的数量，LRU 就会启动并开始每 100 毫秒删除 max_persistent_grants 的 5%。