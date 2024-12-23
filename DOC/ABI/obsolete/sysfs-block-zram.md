**What**：`/sys/block/zram<id>/num_reads`

**日期**：2015 年 8 月

**联系人**：Sergey Senozhatsky <sergey.senozhatsky@gmail.com>

**描述**：

    `num_reads` 文件为只读，指定在此设备上进行的读取（失败或成功）的数量。

    现在可通过 `zram<id>/stat` 节点访问。

**What**：`/sys/block/zram<id>/num_writes`

**日期**：2015 年 8 月

**联系人**：Sergey Senozhatsky <sergey.senozhatsky@gmail.com>

**描述**：

    `num_writes` 文件为只读，指定在此设备上进行的写入（失败或成功）的数量。

    现在可通过 `zram<id>/stat` 节点访问。

**What**：`/sys/block/zram<id>/invalid_io`

**日期**：2015 年 8 月

**联系人**：Sergey Senozhatsky <sergey.senozhatsky@gmail.com>

**描述**：

    `invalid_io` 文件为只读，指定发送到此设备的非页面大小对齐的 I/O 请求的数量。

    现在可通过 `zram<id>/io_stat` 节点访问。

**What**：`/sys/block/zram<id>/failed_reads`

**日期**：2015 年 8 月

**联系人**：Sergey Senozhatsky <sergey.senozhatsky@gmail.com>

**描述**：

    `failed_reads` 文件为只读，指定在此设备上发生的失败读取的数量。

    现在可通过 `zram<id>/io_stat` 节点访问。

**What**：`/sys/block/zram<id>/failed_writes`

**日期**：2015 年 8 月

**联系人**：Sergey Senozhatsky <sergey.senozhatsky@gmail.com>

**描述**：

    `failed_writes` 文件为只读，指定在此设备上发生的失败写入的数量。

    现在可通过 `zram<id>/io_stat` 节点访问。

**What**：`/sys/block/zram<id>/notify_free`

**日期**：2015 年 8 月

**联系人**：Sergey Senozhatsky <sergey.senozhatsky@gmail.com>

**描述**：

    `notify_free` 文件为只读。根据设备使用场景，它可能统计 a) 由于交换槽空闲通知而释放的页面数量，或者 b) 由于 bio 发送的 `REQ_DISCARD` 请求而释放的页面数量。前者在交换槽被释放时发送到交换块设备，这意味着该磁盘正被用作交换磁盘。后者由挂载了丢弃选项的文件系统发送，每当一些数据块被丢弃时。

    现在可通过 `zram<id>/io_stat` 节点访问。

**What**：`/sys/block/zram<id>/zero_pages`

**日期**：2015 年 8 月

**联系人**：Sergey Senozhatsky <sergey.senozhatsky@gmail.com>

**描述**：

    `zero_pages` 文件为只读，指定写入此磁盘的零填充页面的数量。此类页面不分配内存。

    现在可通过 `zram<id>/mm_stat` 节点访问。

**What**：`/sys/block/zram<id>/orig_data_size`

**日期**：2015 年 8 月

**联系人**：Sergey Senozhatsky <sergey.senozhatsky@gmail.com>

**描述**：

    `orig_data_size` 文件为只读，指定存储在此磁盘中的未压缩数据的大小。这不包括零填充页面（`zero_pages`），因为它们不分配内存。

    单位：字节

    现在可通过 `zram<id>/mm_stat` 节点访问。

**What**：`/sys/block/zram<id>/compr_data_size`

**日期**：2015 年 8 月

**联系人**：Sergey Senozhatsky <sergey.senozhatsky@gmail.com>

**描述**：

    `compr_data_size` 文件为只读，指定存储在此磁盘中的压缩数据的大小。因此，可以使用 `orig_data_size` 和此统计信息计算压缩比。

    单位：字节

    现在可通过 `zram<id>/mm_stat` 节点访问。

**What**：`/sys/block/zram<id>/mem_used_total`

**日期**：2015 年 8 月

**联系人**：Sergey Senozhatsky <sergey.senozhatsky@gmail.com>

**描述**：

    `mem_used_total` 文件为只读，指定为此磁盘分配的内存量，包括分配器碎片和元数据开销。因此，可以使用 `compr_data_size` 和此统计信息计算分配器空间效率。

    单位：字节

    现在可通过 `zram<id>/mm_stat` 节点访问。

**What**：`/sys/block/zram<id>/mem_used_max`

**日期**：2015 年 8 月

**联系人**：Sergey Senozhatsky <sergey.senozhatsky@gmail.com>

**描述**：

    `mem_used_max` 文件为读/写，指定 ZRAM 用于存储压缩数据所消耗的最大内存量。要重置该值，应写入“0”。否则，可能会看到 `-EINVAL`。

    单位：字节

    降级为只写节点：因此仅可以设置新值；其当前值存储在 `zram<id>/mm_stat` 节点中。

**What**：`/sys/block/zram<id>/mem_limit`

**日期**：2015 年 8 月

**联系人**：Sergey Senozhatsky <sergey.senozhatsky@gmail.com>

**描述**：

    `mem_limit` 文件为读/写，指定 ZRAM 可用于存储压缩数据的最大内存量。该限制可以在运行时更改，“0”表示禁用限制。初始状态无限制。

    单位：字节

    降级为只写节点：因此仅可以设置新值；其当前值存储在 `zram<id>/mm_stat` 节点中。