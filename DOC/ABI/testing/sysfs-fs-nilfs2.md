**/sys/fs/nilfs2/features/revision**

- **What**：显示 NILFS 文件系统驱动的当前版本。此值告知驱动准备支持的文件系统版本。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示 NILFS 文件系统驱动的当前版本。此值告知驱动准备支持的文件系统版本。

**/sys/fs/nilfs2/features/README**

- **What**：描述 /sys/fs/nilfs2/features 组的属性。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：描述 /sys/fs/nilfs2/features 组的属性。

**/sys/fs/nilfs2/<device>/revision**

- **What**：显示卷上的 NILFS 文件系统版本。此值告知挂载卷上的元数据结构版本。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示卷上的 NILFS 文件系统版本。此值告知挂载卷上的元数据结构版本。

**/sys/fs/nilfs2/<device>/blocksize**

- **What**：以字节为单位显示卷的块大小。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：以字节为单位显示卷的块大小。

**/sys/fs/nilfs2/<device>/device_size**

- **What**：以字节为单位显示卷大小。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：以字节为单位显示卷大小。

**/sys/fs/nilfs2/<device>/free_blocks**

- **What**：显示卷上的空闲块数量。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示卷上的空闲块数量。

**/sys/fs/nilfs2/<device>/uuid**

- **What**：显示卷的 UUID（通用唯一标识符）。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示卷的 UUID（通用唯一标识符）。

**/sys/fs/nilfs2/<device>/volume_name**

- **What**：显示卷的标签。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示卷的标签。

**/sys/fs/nilfs2/<device>/README**

- **What**：描述 /sys/fs/nilfs2/<device> 组的属性。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：描述 /sys/fs/nilfs2/<device> 组的属性。

**/sys/fs/nilfs2/<device>/superblock/sb_write_time**

- **What**：以人类可读格式显示超级块的最后写入时间。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：以人类可读格式显示超级块的最后写入时间。

**/sys/fs/nilfs2/<device>/superblock/sb_write_time_secs**

- **What**：以秒为单位显示超级块的最后写入时间。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：以秒为单位显示超级块的最后写入时间。

**/sys/fs/nilfs2/<device>/superblock/sb_write_count**

- **What**：显示超级块的当前写入计数。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示超级块的当前写入计数。

**/sys/fs/nilfs2/<device>/superblock/sb_update_frequency**

- **What**：显示/设置超级块定期更新的间隔（以秒为单位）。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示/设置超级块定期更新的间隔（以秒为单位）。

**/sys/fs/nilfs2/<device>/superblock/README**

- **What**：描述 /sys/fs/nilfs2/<device>/superblock 组的属性。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：描述 /sys/fs/nilfs2/<device>/superblock 组的属性。

**/sys/fs/nilfs2/<device>/segctor/last_pseg_block**

- **What**：显示最新段的起始块编号。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示最新段的起始块编号。

**/sys/fs/nilfs2/<device>/segctor/last_seg_sequence**

- **What**：显示最新段的序列值。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示最新段的序列值。

**/sys/fs/nilfs2/<device>/segctor/last_seg_checkpoint**

- **What**：显示最新段的检查点编号。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示最新段的检查点编号。

**/sys/fs/nilfs2/<device>/segctor/current_seg_sequence**

- **What**：显示段序列计数器。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示段序列计数器。

**/sys/fs/nilfs2/<device>/segctor/current_last_full_seg**

- **What**：显示最新完整段的索引编号。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示最新完整段的索引编号。

**/sys/fs/nilfs2/<device>/segctor/next_full_seg**

- **What**：显示下一个要使用的完整段的索引编号。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示下一个要使用的完整段的索引编号。

**/sys/fs/nilfs2/<device>/segctor/next_pseg_offset**

- **What**：显示当前完整段中下一个部分段的偏移量。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示当前完整段中下一个部分段的偏移量。

**/sys/fs/nilfs2/<device>/segctor/next_checkpoint**

- **What**：显示下一个检查点编号。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示下一个检查点编号。

**/sys/fs/nilfs2/<device>/segctor/last_seg_write_time**

- **What**：以人类可读格式显示最后一个段的写入时间。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：以人类可读格式显示最后一个段的写入时间。

**/sys/fs/nilfs2/<device>/segctor/last_seg_write_time_secs**

- **What**：以秒为单位显示最后一个段的写入时间。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：以秒为单位显示最后一个段的写入时间。

**/sys/fs/nilfs2/<device>/segctor/last_nongc_write_time**

- **What**：以人类可读格式显示最后一个未用于清理操作的段的写入时间。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：以人类可读格式显示最后一个未用于清理操作的段的写入时间。

**/sys/fs/nilfs2/<device>/segctor/last_nongc_write_time_secs**

- **What**：以秒为单位显示最后一个未用于清理操作的段的写入时间。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：以秒为单位显示最后一个未用于清理操作的段的写入时间。

**/sys/fs/nilfs2/<device>/segctor/dirty_data_blocks_count**

- **What**：显示脏数据块的数量。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示脏数据块的数量。

**/sys/fs/nilfs2/<device>/segctor/README**

- **What**：描述 /sys/fs/nilfs2/<device>/segctor 组的属性。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：描述 /sys/fs/nilfs2/<device>/segctor 组的属性。

**/sys/fs/nilfs2/<device>/segments/segments_number**

- **What**：显示卷上的段数量。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示卷上的段数量。

**/sys/fs/nilfs2/<device>/segments/blocks_per_segment**

- **What**：显示段中的块数量。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示段中的块数量。

**/sys/fs/nilfs2/<device>/segments/clean_segments**

- **What**：显示干净段的数量。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示干净段的数量。

**/sys/fs/nilfs2/<device>/segments/dirty_segments**

- **What**：显示脏段的数量。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示脏段的数量。

**/sys/fs/nilfs2/<device>/segments/README**

- **What**：描述 /sys/fs/nilfs2/<device>/segments 组的属性。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：描述 /sys/fs/nilfs2/<device>/segments 组的属性。

**/sys/fs/nilfs2/<device>/checkpoints/checkpoints_number**

- **What**：显示卷上的检查点数量。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示卷上的检查点数量。

**/sys/fs/nilfs2/<device>/checkpoints/snapshots_number**

- **What**：显示卷上的快照数量。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示卷上的快照数量。

**/sys/fs/nilfs2/<device>/checkpoints/last_seg_checkpoint**

- **What**：显示最新段的检查点编号。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示最新段的检查点编号。

**/sys/fs/nilfs2/<device>/checkpoints/next_checkpoint**

- **What**：显示下一个检查点编号。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示下一个检查点编号。

**/sys/fs/nilfs2/<device>/checkpoints/README**

- **What**：描述 /sys/fs/nilfs2/<device>/checkpoints 组的属性。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：描述 /sys/fs/nilfs2/<device>/checkpoints 组的属性。

**/sys/fs/nilfs2/<device>/mounted_snapshots/README**

- **What**：描述 /sys/fs/nilfs2/<device>/mounted_snapshots 组的内容。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：描述 /sys/fs/nilfs2/<device>/mounted_snapshots 组的内容。

**/sys/fs/nilfs2/<device>/mounted_snapshots/<id>/inodes_count**

- **What**：显示快照的 inode 数量。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示快照的 inode 数量。

**/sys/fs/nilfs2/<device>/mounted_snapshots/<id>/blocks_count**

- **What**：显示快照的块数量。

- **Date**：2014 年 4 月

- **Contact**："Vyacheslav Dubeyko" <slava@dubeyko.com>

- **Description**：显示快照的块数量。**What**: /sys/fs/nilfs2/<device>/mounted_snapshots/<id>/README

**Date**: 四月 2014

**Contact**: "Vyacheslav Dubeyko" <slava@dubeyko.com>

**Description**:

描述 /sys/fs/nilfs2/<device>/mounted_snapshots/<id> 组的属性。