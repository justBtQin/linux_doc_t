`zram`：基于压缩内存的块设备

----------------------------------------

# 介绍

`zram`模块创建基于内存的块设备，名为`/dev/zram<id>`（`<id>` = 0, 1,...）。写入这些磁盘的页面会被压缩并存储在内存本身中。这些磁盘允许非常快速的 I/O，并且压缩提供了大量的内存节省。一些用例包括`/tmp`存储、用作交换磁盘、`/var`下的各种缓存等等 :)

单个`zram`设备的统计信息通过`/sys/block/zram<id>/`下的`sysfs`节点导出。

# 使用方法

以下展示了使用`zram`的典型步骤序列。

1) 加载模块：

    `modprobe zram num_devices=4`

    这将创建 4 个设备：`/dev/zram{0,1,2,3}`

    （`num_devices`参数是可选的。默认：1）

2) 设置最大压缩流数量

    压缩后端可能使用多达`max_comp_streams`个压缩流，

    从而允许最多`max_comp_streams`个并发压缩操作。默认情况下，压缩后端使用单个压缩流。

    示例：

    # 显示最大压缩流数量

    `cat /sys/block/zram0/max_comp_streams`

    # 将最大压缩流数量设置为 3

    `echo 3 > /sys/block/zram0/max_comp_streams`

注意：

为了启用压缩后端的多流支持，必须在`ZRAM`设备初始化之前将`max_comp_streams`初始设置为所需的并发级别。一旦设备初始化为单流压缩后端（`max_comp_streams`等于 1），如果尝试更改`max_comp_streams`的值，将会看到错误，因为单流压缩后端是通过锁开销问题实现的特殊情况，不支持动态`max_comp_streams`。只有多流后端支持动态`max_comp_streams`调整。

3) 选择压缩算法

    使用`comp_algorithm`设备属性，可以看到可用的和当前选择的（在方括号中显示）压缩算法，

    更改所选的压缩算法（一旦设备初始化，就无法更改压缩算法）。

    示例：

    # 显示支持的压缩算法

    `cat /sys/block/zram0/comp_algorithm`

    `lzo [lz4]`

    # 选择`lzo`压缩算法

    `echo lzo > /sys/block/zram0/comp_algorithm`

4) 设置磁盘大小

    通过将值写入`sysfs`节点`disksize`来设置磁盘大小。

    该值可以是字节或使用内存后缀。

    示例：

    # 以 50MB 的磁盘大小初始化`/dev/zram0`

    `echo $((50*1024*1024)) > /sys/block/zram0/disksize`

    # 使用内存后缀

    `echo 256K > /sys/block/zram0/disksize`

    `echo 512M > /sys/block/zram0/disksize`

    `echo 1G > /sys/block/zram0/disksize`

注意：

创建大于内存两倍大小的`zram`没有什么意义，因为我们期望的压缩比是 2:1。请注意，`zram`在未使用时大约使用磁盘大小的 0.1%，因此一个巨大的`zram`是浪费的。

5) 设置内存限制：可选

    通过将值写入`sysfs`节点`mem_limit`来设置内存限制。

    该值可以是字节或使用内存后缀。

    此外，您可以在运行时更改该值。

    示例：

    # 用 50MB 内存限制`/dev/zram0`

    `echo $((50*1024*1024)) > /sys/block/zram0/mem_limit`

    # 使用内存后缀

    `echo 256K > /sys/block/zram0/mem_limit`

    `echo 512M > /sys/block/zram0/mem_limit`

    `echo 1G > /sys/block/zram0/mem_limit`

    # 禁用内存限制

    `echo 0 > /sys/block/zram0/mem_limit`

6) 激活：

    `mkswap /dev/zram0`

    `swapon /dev/zram0`

    `mkfs.ext4 /dev/zram1`

    `mount /dev/zram1 /tmp`

7) 统计信息：

每个设备的统计信息作为`/sys/block/zram<id>/`下的各种节点导出。

以下是导出的设备属性的简要说明。如需更多详细信息，请阅读`Documentation/ABI/testing/sysfs-block-zram`。

```
名称            访问权限            描述
----            ------            -----------
`disksize`          RW    显示和设置设备的磁盘大小
`initstate`         RO    显示设备的初始化状态
`reset`             WO    触发设备重置
`num_reads`         RO    读取的次数
`failed_reads`      RO    读取失败的次数
`num_write`         RO    写入的次数
`failed_writes`     RO    写入失败的次数
`invalid_io`        RO    非页大小对齐的 I/O 请求次数
`max_comp_streams`  RW    可能的并发压缩操作的数量
`comp_algorithm`    RW    显示和更改压缩算法
`notify_free`       RO    释放页面的通知次数（无论是插槽释放通知还是`REQ_DISCARD`请求）
`zero_pages`        RO    写入此磁盘的零填充页面的数量
`orig_data_size`    RO    存储在此磁盘中的数据的未压缩大小
`compr_data_size`   RO    存储在此磁盘中的数据的压缩大小
`mem_used_total`    RO    为此磁盘分配的内存量
`mem_used_max`      RW    `zram`存储压缩数据所使用的最大内存量
`mem_limit`         RW    `ZRAM`可用于存储压缩数据的最大内存量
`num_migrated`      RO    由压缩整理迁移的对象数量
```

# 警告

=======

每个统计`sysfs`属性都被认为已弃用。基本策略是：

-- 现有的`RW`节点将降级为`WO`节点（在 Linux 4.11 中）

-- 已弃用的`RO``sysfs`节点最终将被删除（在 Linux 4.11 中）

已弃用的属性列表可在此处找到：

`Documentation/ABI/obsolete/sysfs-block-zram`

基本上，每个具有自己可读`sysfs`节点的属性（例如`num_reads`）*并且*可通过其中一个统计文件（`zram<id>/stat`、`zram<id>/io_stat`或`zram<id>/mm_stat`）访问的属性都被认为已弃用。

建议用户空间使用以下文件来读取设备统计信息。

文件`/sys/block/zram<id>/stat`

表示块层统计信息。有关详细信息，请阅读`Documentation/block/stat.txt`。

文件`/sys/block/zram<id>/io_stat`

该统计文件表示设备的 I/O 统计信息，未由块层统计，因此在`zram<id>/stat`文件中不可用。它由一行文本组成，包含以下以空格分隔的统计信息：

    `failed_reads`

    `failed_writes`

    `invalid_io`

    `notify_free`

文件`/sys/block/zram<id>/mm_stat`

该统计文件表示设备的`mm`统计信息。它由一行文本组成，包含以下以空格分隔的统计信息：

    `orig_data_size`

    `compr_data_size`

    `mem_used_total`

    `mem_limit`

    `mem_used_max`

    `zero_pages`

    `num_migrated`

8) 停用：

    `swapoff /dev/zram0`

    `umount /dev/zram1`

9) 重置：

    将任何正值写入`reset``sysfs`节点

    `echo 1 > /sys/block/zram0/reset`

    `echo 1 > /sys/block/zram1/reset`

    这将释放为给定设备分配的所有内存，并将磁盘大小重置为零。在重新使用设备之前，必须再次设置磁盘大小。

尼廷·古普塔

`ngupta@vflare.org`