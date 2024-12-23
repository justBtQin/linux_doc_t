**What**: `/sys/block/zram<id>/disksize`

**Date**: 2010 年 8 月

**Contact**: Nitin Gupta <ngupta@vflare.org>

**Description**:

`disksize`文件是可读可写的，指定了磁盘大小，代表此磁盘中可存储的*未压缩*数据的限制。

**单位**: 字节

**What**: `/sys/block/zram<id>/initstate`

**Date**: 2010 年 8 月

**Contact**: Nitin Gupta <ngupta@vflare.org>

**Description**:

`initstate`文件是只读的，显示设备的初始化状态。

**What**: `/sys/block/zram<id>/reset`

**Date**: 2010 年 8 月

**Contact**: Nitin Gupta <ngupta@vflare.org>

**Description**:

`reset`文件是只写的，允许重置设备。重置操作会释放与此设备相关的所有内存。

**What**: `/sys/block/zram<id>/num_reads`

**Date**: 2010 年 8 月

**Contact**: Nitin Gupta <ngupta@vflare.org>

**Description**:

`num_reads`文件是只读的，指定在此设备上完成的读操作（成功或失败）的数量。

**What**: `/sys/block/zram<id>/num_writes`

**Date**: 2010 年 8 月

**Contact**: Nitin Gupta <ngupta@vflare.org>

**Description**:

`num_writes`文件是只读的，指定在此设备上完成的写操作（成功或失败）的数量。

**What**: `/sys/block/zram<id>/invalid_io`

**Date**: 2010 年 8 月

**Contact**: Nitin Gupta <ngupta@vflare.org>

**Description**:

`invalid_io`文件是只读的，指定发送到此设备的非页面大小对齐的 I/O 请求的数量。

**What**: `/sys/block/zram<id>/failed_reads`

**Date**: 2014 年 2 月

**Contact**: Sergey Senozhatsky <sergey.senozhatsky@gmail.com>

**Description**:

`failed_reads`文件是只读的，指定在此设备上发生的失败读操作的数量。

**What**: `/sys/block/zram<id>/failed_writes`

**Date**: 2014 年 2 月

**Contact**: Sergey Senozhatsky <sergey.senozhatsky@gmail.com>

**Description**:

`failed_writes`文件是只读的，指定在此设备上发生的失败写操作的数量。

**What**: `/sys/block/zram<id>/max_comp_streams`

**Date**: 2014 年 2 月

**Contact**: Sergey Senozhatsky <sergey.senozhatsky@gmail.com>

**Description**:

`max_comp_streams`文件是可读可写的，指定后端的`zcomp_strm`压缩流（并发压缩操作的数量）。

**What**: `/sys/block/zram<id>/comp_algorithm`

**Date**: 2014 年 2 月

**Contact**: Sergey Senozhatsky <sergey.senozhatsky@gmail.com>

**Description**:

`comp_algorithm`文件是可读可写的，用于显示可用和选定的压缩算法，以及更改压缩算法的选择。

**What**: `/sys/block/zram<id>/notify_free`

**Date**: 2010 年 8 月

**Contact**: Nitin Gupta <ngupta@vflare.org>

**Description**:

`notify_free`文件是只读的。根据设备使用场景，它可能统计 a) 由于交换槽空闲通知而释放的页面数量，或者 b) 由于`bio`发送的`REQ_DISCARD`请求而释放的页面数量。前者在交换槽被释放时发送到交换块设备，这意味着该磁盘正被用作交换磁盘。后者由挂载了丢弃选项的文件系统发送，每当一些数据块被丢弃时。

**What**: `/sys/block/zram<id>/zero_pages`

**Date**: 2010 年 8 月

**Contact**: Nitin Gupta <ngupta@vflare.org>

**Description**:

`zero_pages`文件是只读的，指定写入此磁盘的零填充页面的数量。此类页面不分配内存。

**What**: `/sys/block/zram<id>/orig_data_size`

**Date**: 2010 年 8 月

**Contact**: Nitin Gupta <ngupta@vflare.org>

**Description**:

`orig_data_size`文件是只读的，指定存储在此磁盘中的未压缩数据的大小。这不包括零填充页面（`zero_pages`），因为它们不分配内存。

**单位**: 字节

**What**: `/sys/block/zram<id>/compr_data_size`

**Date**: 2010 年 8 月

**Contact**: Nitin Gupta <ngupta@vflare.org>

**Description**:

`compr_data_size`文件是只读的，指定存储在此磁盘中的压缩数据的大小。因此，可以使用`orig_data_size`和此统计信息计算压缩比。

**单位**: 字节

**What**: `/sys/block/zram<id>/mem_used_total`

**Date**: 2010 年 8 月

**Contact**: Nitin Gupta <ngupta@vflare.org>

**Description**:

`mem_used_total`文件是只读的，指定为此磁盘分配的内存量，包括分配器碎片和元数据开销。因此，可以使用`compr_data_size`和此统计信息计算分配器空间效率。

**单位**: 字节

**What**: `/sys/block/zram<id>/mem_used_max`

**Date**: 2014 年 8 月

**Contact**: Minchan Kim <minchan@kernel.org>

**Description**:

`mem_used_max`文件是读/写的，指定 ZRAM 用于存储压缩数据所消耗的最大内存量。

要重置该值，应写入“0”。否则，可能会看到 `-EINVAL`。

**单位**: 字节

**What**: `/sys/block/zram<id>/mem_limit`

**Date**: 2014 年 8 月

**Contact**: Minchan Kim <minchan@kernel.org>

**Description**:

`mem_limit`文件是读/写的，指定 ZRAM 可用于存储压缩数据的最大内存量。该限制可以在运行时更改，“0”表示禁用限制。初始状态无限制。

**单位**: 字节

**What**: `/sys/block/zram<id>/compact`

**Date**: 2015 年 8 月

**Contact**: Minchan Kim <minchan@kernel.org>

**Description**:

`compact`文件是只写的，触发 ZRM 分配器使用的压缩操作。分配器移动一些对象，以便释放碎片空间。

**What**: `/sys/block/zram<id>/io_stat`

**Date**: 2015 年 8 月

**Contact**: Sergey Senozhatsky <sergey.senozhatsky@gmail.com>

**Description**:

`io_stat`文件是只读的，累积块层未统计的设备的 I/O 统计信息。例如，`failed_reads`、`failed_writes` 等。文件格式类似于块层统计信息文件格式。

**What**: `/sys/block/zram<id>/mm_stat`

**Date**: 2015 年 8 月

**Contact**: Sergey Senozhatsky <sergey.senozhatsky@gmail.com>

**Description**:

`mm_stat`文件是只读的，以类似于块层统计信息文件格式的形式表示设备的内存管理（`mm`）统计信息（`orig_data_size`、`compr_data_size` 等）。