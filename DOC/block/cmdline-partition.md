# 嵌入式设备命令行分区解析

=====================================================================

支持从命令行读取块设备分区表。通常用于固定块（eMMC）嵌入式设备。它没有 MBR，因此节省了存储空间。引导加载程序可以通过块设备上数据的绝对地址轻松访问。用户可以轻松更改分区。

命令行的格式类似于 mtdparts：

blkdevparts=<blkdev-def>[;<blkdev-def>]

  <blkdev-def> := <blkdev-id>:<partdef>[,<partdef>]

    <partdef> := <size>[@<offset>](part-name)

<blkdev-id>

    块设备磁盘名称，嵌入式设备使用固定块设备，其磁盘名称也是固定的。例如：mmcblk0、mmcblk1、mmcblk0boot0。

<size>

    分区大小，以字节为单位，例如：512、1m、1G。

<offset>

    分区起始地址，以字节为单位。

(part-name)

    分区名称，内核通过“PARTNAME”发送 uevent。应用程序可以创建一个指向具有名称“PARTNAME”的块设备分区的链接。用户空间应用程序可以通过分区名称访问分区。

# 示例：

    eMMC 磁盘名称为“mmcblk0”和“mmcblk0boot0”

  bootargs：

    'blkdevparts=mmcblk0:1G(data0),1G(data1),-;mmcblk0boot0:1m(boot),-(kernel)'

  dmesg：

    mmcblk0: p1(data0) p2(data1) p3()

    mmcblk0boot0: p1(boot) p2(kernel)