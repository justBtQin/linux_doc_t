**/sys/block/<disk>/stat**

日期：2008 年 2 月

联系人：Jerome Marchand <jmarchan@redhat.com>

描述：

/sys/block/<disk>/stat 文件显示磁盘 <disk> 的 I/O 统计信息。它们包含 11 个字段：

```
1 - 成功完成的读取次数
2 - 合并的读取次数
3 - 读取的扇区数
4 - 花费在读取上的时间（毫秒）
5 - 完成的写入次数
6 - 合并的写入次数
7 - 写入的扇区数
8 - 花费在写入上的时间（毫秒）
9 - 正在进行的 I/O 数量
10 - 花费在执行 I/O 上的时间（毫秒）
11 - 执行 I/O 所花费的加权时间（毫秒）
```

更多详细信息请参考 Documentation/iostats.txt

**/sys/block/<disk>/<part>/stat**

日期：2008 年 2 月

联系人：Jerome Marchand <jmarchan@redhat.com>

描述：

/sys/block/<disk>/<part>/stat 文件显示分区 <part> 的 I/O 统计信息。格式与上述的 /sys/block/<disk>/stat 格式相同。

**/sys/block/<disk>/integrity/format**

日期：2008 年 6 月

联系人：Martin K. Petersen <martin.petersen@oracle.com>

描述：

用于支持完整性的块设备的元数据格式。例如 T10-DIF-TYPE1-CRC。

**/sys/block/<disk>/integrity/read_verify**

日期：2008 年 6 月

联系人：Martin K. Petersen <martin.petersen@oracle.com>

描述：

指示块层是否应验证由支持发送完整性元数据的设备处理的读取请求的完整性。

**/sys/block/<disk>/integrity/tag_size**

日期：2008 年 6 月

联系人：Martin K. Petersen <martin.petersen@oracle.com>

描述：

每 512 字节数据可用的完整性标签空间的字节数。

**/sys/block/<disk>/integrity/device_is_integrity_capable**

日期：2014 年 7 月

联系人：Martin K. Petersen <martin.petersen@oracle.com>

描述：

指示存储设备是否能够存储完整性元数据。如果设备具有 T10 PI 能力，则设置。

**/sys/block/<disk>/integrity/write_generate**

日期：2008 年 6 月

联系人：Martin K. Petersen <martin.petersen@oracle.com>

描述：

指示块层是否应自动为绑定到支持接收完整性元数据的设备的写入请求生成校验和。

**/sys/block/<disk>/alignment_offset**

日期：2009 年 4 月

联系人：Martin K. Petersen <martin.petersen@oracle.com>

描述：

存储设备可能报告的物理块大小大于逻辑块大小（例如，具有 4KB 物理扇区并向操作系统公开 512 字节逻辑块的驱动器）。此参数指示设备的开头与磁盘的自然对齐偏移了多少字节。

**/sys/block/<disk>/<partition>/alignment_offset**

日期：2009 年 4 月

联系人：Martin K. Petersen <martin.petersen@oracle.com>

描述：

存储设备可能报告的物理块大小大于逻辑块大小（例如，具有 4KB 物理扇区并向操作系统公开 512 字节逻辑块的驱动器）。此参数指示分区的开头与磁盘的自然对齐偏移了多少字节。

**/sys/block/<disk>/queue/logical_block_size**

日期：2009 年 5 月

联系人：Martin K. Petersen <martin.petersen@oracle.com>

描述：

这是存储设备可以寻址的最小单位。通常为 512 字节。

**/sys/block/<disk>/queue/physical_block_size**

日期：2009 年 5 月

联系人：Martin K. Petersen <martin.petersen@oracle.com>

描述：

这是物理存储设备可以原子写入的最小单位。通常与逻辑块大小相同，但可能更大。一个例子是具有 4KB 扇区并向操作系统公开 512 字节逻辑块大小的 SATA 驱动器。对于堆叠的块设备，physical_block_size 变量包含组件设备的最大 physical_block_size。

**/sys/block/<disk>/queue/minimum_io_size**

日期：2009 年 4 月

联系人：Martin K. Petersen <martin.petersen@oracle.com>

描述：

存储设备可能报告其粒度或首选最小 I/O 大小，这是设备可以执行而不会产生性能损失的最小请求。对于磁盘驱动器，这通常是物理块大小。对于 RAID 阵列，这通常是条带块大小。适当对齐的 minimum_io_size 的倍数是希望进行大量 I/O 操作的工作负载的首选请求大小。

**/sys/block/<disk>/queue/optimal_io_size**

日期：2009 年 4 月

联系人：Martin K. Petersen <martin.petersen@oracle.com>

描述：

存储设备可能报告最佳 I/O 大小，这是设备用于持续 I/O 的首选单位。磁盘驱动器很少报告此信息。对于 RAID 阵列，通常是条带宽度或内部磁道大小。适当对齐的 optimal_io_size 的倍数是希望获得持续吞吐量的工作负载的首选请求大小。如果未报告最佳 I/O 大小，则此文件包含 0。

**/sys/block/<disk>/queue/nomerges**

日期：2010 年 1 月

联系人：

描述：

标准 I/O 电梯操作包括尝试合并连续的 I/O。对于已知的随机 I/O 负载，这些尝试将始终失败，并导致在内核中花费额外的周期。这允许通过以下两种方式之一关闭此行为：设置为 1 时，禁用复杂的合并检查，但启用与前一个 I/O 请求的单次合并。设置为 2 时，禁用所有合并尝试。默认值为 0 - 启用所有类型的合并尝试。

**/sys/block/<disk>/discard_alignment**

日期：2011 年 5 月

联系人：Martin K. Petersen <martin.petersen@oracle.com>

描述：

支持丢弃功能的设备可能在内部以大于导出逻辑块大小的单位分配空间。discard_alignment 参数指示设备的开头与内部分配单元的自然对齐偏移了多少字节。

**/sys/block/<disk>/<partition>/discard_alignment**

日期：2011 年 5 月

联系人：Martin K. Petersen <martin.petersen@oracle.com>

描述：

支持丢弃功能的设备可能在内部以大于导出逻辑块大小的单位分配空间。discard_alignment 参数指示分区的开头与内部分配单元的自然对齐偏移了多少字节。

**/sys/block/<disk>/queue/discard_granularity**

日期：2011 年 5 月

联系人：Martin K. Petersen <martin.petersen@oracle.com>

描述：

支持丢弃功能的设备可能在内部使用大于逻辑块大小的单位分配空间。discard_granularity 参数指示设备报告的内部分配单元的大小（以字节为单位）。否则，discard_granularity 将设置为与设备的物理块大小匹配。discard_granularity 为 0 表示设备不支持丢弃功能。

**/sys/block/<disk>/queue/discard_max_bytes**

日期：2011 年 5 月

联系人：Martin K. Petersen <martin.petersen@oracle.com>

描述：

支持丢弃功能的设备可能对单个操作中可以修剪或取消映射的字节数有内部限制。某些存储协议也对单个命令中可以描述的块数量有固有限制。discard_max_bytes 参数由设备驱动程序设置为单个操作中可以丢弃的最大字节数。发送到设备的丢弃请求不得超过此限制。discard_max_bytes 值为 0 表示设备不支持丢弃功能。

**/sys/block/<disk>/queue/discard_zeroes_data**

日期：2011 年 5 月

联系人：Martin K. Petersen <martin.petersen@oracle.com>

描述：

支持丢弃功能的设备在读取先前丢弃的块时可能会返回陈旧或随机数据。如果文件系统期望被丢弃的块被显式清除，这可能会导致问题。如果设备报告在读取丢弃区域时确定性地返回零，则`discard_zeroes_data`参数将被设置为 1。否则，它将为 0，读取丢弃区域的结果是未定义的。

什么：`/sys/block/<disk>/queue/write_same_max_bytes`

日期：2012 年 1 月

联系人：Martin K. Petersen <martin.petersen@oracle.com>

描述：

一些设备支持写相同操作，其中单个数据块可以被写入存储上的一系列连续块。这可用于擦除磁盘上的区域或在 RAID 配置中初始化驱动器。`write_same_max_bytes`表示在单个写相同命令中可以写入多少字节。如果`write_same_max_bytes`为 0，则设备不支持写相同。