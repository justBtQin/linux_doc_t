**/sys/fs/ext4/<disk>/mb_stats**

日期：2008 年 3 月

联系人：“Theodore Ts'o” <tytso@mit.edu>

描述：

控制多块分配器是否应收集统计信息，这些信息在卸载时显示。1 表示收集统计信息，0 表示不收集统计信息。

**/sys/fs/ext4/<disk>/mb_group_prealloc**

日期：2008 年 3 月

联系人：“Theodore Ts'o” <tytso@mit.edu>

描述：

如果在 ext4 超级块中未设置条带大小，多块分配器将把分配请求向上舍入到此调优参数的倍数。

**/sys/fs/ext4/<disk>/mb_max_to_scan**

日期：2008 年 3 月

联系人：“Theodore Ts'o” <tytso@mit.edu>

描述：

多块分配器将搜索的最大扩展数，以找到最佳扩展。

**/sys/fs/ext4/<disk>/mb_min_to_scan**

日期：2008 年 3 月

联系人：“Theodore Ts'o” <tytso@mit.edu>

描述：

多块分配器将搜索的最小扩展数，以找到最佳扩展。

**/sys/fs/ext4/<disk>/mb_order2_req**

日期：2008 年 3 月

联系人：“Theodore Ts'o” <tytso@mit.edu>

描述：

控制使用伙伴缓存的请求的最小大小（以 2 的幂为单位）的调优参数。

**/sys/fs/ext4/<disk>/mb_stream_req**

日期：2008 年 3 月

联系人：“Theodore Ts'o” <tytso@mit.edu>

描述：

具有少于此可调参数的块数的文件将从特定于块组的预分配池中分配其块，以便小文件紧密打包在一起。每个大文件将从其自己的唯一预分配池中分配其块。

**/sys/fs/ext4/<disk>/inode_readahead_blks**

日期：2008 年 3 月

联系人：“Theodore Ts'o” <tytso@mit.edu>

描述：

控制 ext4 的 inode 表预读算法将预读到缓冲区缓存中的 inode 表块的最大数量的调优参数。

**/sys/fs/ext4/<disk>/delayed_allocation_blocks**

日期：2008 年 3 月

联系人：“Theodore Ts'o” <tytso@mit.edu>

描述：

此文件为只读，显示页缓存中脏的块数，但这些块在文件系统中的位置尚未分配。

**/sys/fs/ext4/<disk>/lifetime_write_kbytes**

日期：2008 年 3 月

联系人：“Theodore Ts'o” <tytso@mit.edu>

描述：

此文件为只读，显示自创建此文件系统以来写入此文件系统的千字节数。

**/sys/fs/ext4/<disk>/session_write_kbytes**

日期：2008 年 3 月

联系人：“Theodore Ts'o” <tytso@mit.edu>

描述：

此文件为只读，显示自挂载此文件系统以来写入此文件系统的千字节数。

**/sys/fs/ext4/<disk>/inode_goal**

日期：2008 年 6 月

联系人：“Theodore Ts'o” <tytso@mit.edu>

描述：

调优参数（如果非零）控制 inode 分配器使用的目标 inode，优先于所有其他分配启发式方法。此仅用于调试，在生产系统中应为 0。

**/sys/fs/ext4/<disk>/max_writeback_mb_bump**

日期：2009 年 9 月

联系人：“Theodore Ts'o” <tytso@mit.edu>

描述：

回写代码在继续处理另一个 inode 之前将尝试写出的最大兆字节数。

**/sys/fs/ext4/<disk>/extent_max_zeroout_kb**

日期：2012 年 8 月

联系人：“Theodore Ts'o” <tytso@mit.edu>

描述：

在操作 inode 的扩展树时，将优先零清除的最大千字节数，而不是创建新的未初始化扩展。请注意，使用较大的值将增加完成随机写入操作所需时间的可变性（因为 4k 随机写入可能由于零清除操作而变为大得多的写入）。