**What**: `/sys/block/<disk>/bcache/unregister`

**Date**:  November 2010

**Contact**: Kent Overstreet <kent.overstreet@gmail.com>

**Description**: 向此文件写入会导致后备设备或缓存被注销。如果后备设备在缓存中有脏数据，写入模式会自动禁用，并且在设备注销之前所有脏数据都会被刷新。缓存会在注销自身之前注销所有关联的后备设备。

**What**: `/sys/block/<disk>/bcache/clear_stats`

**Date**:  November 2010

**Contact**: Kent Overstreet <kent.overstreet@gmail.com>

**Description**: 向此文件写入会重置设备的所有统计信息。

**What**: `/sys/block/<disk>/bcache/cache`

**Date**:  November 2010

**Contact**: Kent Overstreet <kent.overstreet@gmail.com>

**Description**: 对于有缓存的后备设备，是该缓存的 bcache/ 目录的符号链接。

**What**: `/sys/block/<disk>/bcache/cache_hits`

**Date**:  November 2010

**Contact**: Kent Overstreet <kent.overstreet@gmail.com>

**Description**: 对于后备设备：完整缓存命中的整数次数，按 bio 计数。部分缓存命中计为未命中。

**What**: `/sys/block/<disk>/bcache/cache_misses`

**Date**:  November 2010

**Contact**: Kent Overstreet <kent.overstreet@gmail.com>

**Description**: 对于后备设备：缓存未命中的整数次数。

**What**: `/sys/block/<disk>/bcache/cache_hit_ratio`

**Date**:  November 2010

**Contact**: Kent Overstreet <kent.overstreet@gmail.com>

**Description**: 对于后备设备：缓存命中的百分比。

**What**: `/sys/block/<disk>/bcache/sequential_cutoff`

**Date**:  November 2010

**Contact**: Kent Overstreet <kent.overstreet@gmail.com>

**Description**: 对于后备设备：超过此阈值后，顺序 IO 将跳过缓存。以人类可读单位（即 echo 10M > sequntial_cutoff）读取和写入字节。

**What**: `/sys/block/<disk>/bcache/bypassed`

**Date**:  November 2010

**Contact**: Kent Overstreet <kent.overstreet@gmail.com>

**Description**: 所有绕过缓存（由于顺序截断）的读取和写入的总和。以人类可读单位表示字节。

**What**: `/sys/block/<disk>/bcache/writeback`

**Date**:  November 2010

**Contact**: Kent Overstreet <kent.overstreet@gmail.com>

**Description**: 对于后备设备：启用时，启用回写缓存，写入将在缓存中缓冲。关闭时，缓存处于直写模式；读取和写入将添加到缓存中，但不会进行写缓冲。

**What**: `/sys/block/<disk>/bcache/writeback_running`

**Date**:  November 2010

**Contact**: Kent Overstreet <kent.overstreet@gmail.com>

**Description**: 对于后备设备：关闭时，脏数据不会从缓存写入后备设备。缓存仍将用于缓冲写入，直到几乎满为止，此时写入会透明地恢复为直写模式。仅用于基准测试/测试。

**What**: `/sys/block/<disk>/bcache/writeback_delay`

**Date**:  November 2010

**Contact**: Kent Overstreet <kent.overstreet@gmail.com>

**Description**: 对于后备设备：在回写模式下，当脏数据写入缓存且缓存中没有该后备设备的脏数据时，从缓存到后备设备的回写将在此延迟后开始，以整数秒表示。

**What**: `/sys/block/<disk>/bcache/writeback_percent`

**Date**:  November 2010

**Contact**: Kent Overstreet <kent.overstreet@gmail.com>

**Description**: 对于后备设备：如果非零，仅当缓存使用超过此百分比时，才从缓存到后备设备进行回写，允许更多的写合并发生并减少发送到后备设备的总写入次数。整数介于 0 和 40 之间。

**What**: `/sys/block/<disk>/bcache/synchronous`

**Date**:  November 2010

**Contact**: Kent Overstreet <kent.overstreet@gmail.com>

**Description**: 对于缓存，一个允许切换同步模式开和关的布尔值。在同步模式下，所有写入都被排序，以便缓存可以从不干净的关闭中可靠地恢复；如果禁用，bcache 通常不会等待写入完成，但如果缓存未干净关闭，所有数据将从缓存中丢弃。在启用回写缓存时不应关闭。

**What**: `/sys/block/<disk>/bcache/discard`

**Date**:  November 2010

**Contact**: Kent Overstreet <kent.overstreet@gmail.com>

**Description**: 对于缓存，一个允许在设备支持的情况下关闭或打开 discard/TRIM 的布尔值。

**What**: `/sys/block/<disk>/bcache/bucket_size`

**Date**:  November 2010

**Contact**: Kent Overstreet <kent.overstreet@gmail.com>

**Description**: 对于缓存，在创建缓存时设置的人类可读单位的桶大小；应与 SSD 的擦除块大小匹配以获得最佳性能。

**What**: `/sys/block/<disk>/bcache/nbuckets`

**Date**:  November 2010

**Contact**: Kent Overstreet <kent.overstreet@gmail.com>

**Description**: 对于缓存，可用桶的数量。

**What**: `/sys/block/<disk>/bcache/tree_depth`

**Date**:  November 2010

**Contact**: Kent Overstreet <kent.overstreet@gmail.com>

**Description**: 对于缓存，不包括叶节点的 btree 高度（即一个节点的树深度为 0）。

**What**: `/sys/block/<disk>/bcache/btree_cache_size`

**Date**:  November 2010

**Contact**: Kent Overstreet <kent.overstreet@gmail.com>

**Description**: 当前在内存中缓存的 btree 桶/节点的数量；缓存根据系统其他部分的内存压力动态增长和收缩。

**What**: `/sys/block/<disk>/bcache/written`

**Date**:  November 2010

**Contact**: Kent Overstreet <kent.overstreet@gmail.com>

**Description**: 对于缓存，以人类可读单位写入缓存的总数据量，不包括所有元数据。

**What**: `/sys/block/<disk>/bcache/btree_written`

**Date**:  November 2010

**Contact**: Kent Overstreet <kent.overstreet@gmail.com>

**Description**: 对于缓存，所有 btree 写入的总和，以人类可读单位表示。