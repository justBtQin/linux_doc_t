**What**: /sys/class/pktcdvd/

**Date**: 十月 2006

**KernelVersion**: 2.6.20

**Contact**: Thomas Maier <balagi@justmail.de>

**Description**:

sysfs 接口

---------------

pktcdvd 模块（数据包写入驱动程序）在 sysfs 中创建以下文件：

（<devid> 格式为 主设备号:次设备号 ）

/sys/class/pktcdvd/

    add            (0200) 写入一个块设备 ID（主设备号:次设备号）

                           以创建一个新的 pktcdvd 设备并将其映射

                           到块设备。

    remove         (0200) 写入 pktcdvd 设备 ID（主设备号:次设备号）

                           以删除 pktcdvd 设备。

    device_map     (0444) 以以下格式显示设备映射：

                             pktcdvd[0-7] <pktdevid> <blkdevid>

/sys/class/pktcdvd/pktcdvd[0-7]/

    dev                   (0444) 设备 ID

    uevent                (0200) 用于发送 uevent。

/sys/class/pktcdvd/pktcdvd[0-7]/stat/

    packets_started       (0444) 已启动数据包的数量。

    packets_finished      (0444) 已完成数据包的数量。

    kb_written            (0444) 写入的千字节数。

    kb_read               (0444) 读取的千字节数。

    kb_read_gather        (0444) 为填充写入数据包而读取的千字节数。

    reset                 (0200) 写入任何值以重置

                                 pktcdvd 设备统计值，如

                                 读取/写入的字节数。

/sys/class/pktcdvd/pktcdvd[0-7]/write_queue/

    size                  (0444) 包含 bio 写入队列的大小。

    congestion_off        (0644) 如果 bio 写入队列大小低于

                                 此标记，则接受来自块层的新 bio 请求。

    congestion_on         (0644) 如果 bio 写入队列大小高于

                                 此标记，则不再接受来自块

                                 层的 bio 写入请求，并等待直到 pktcdvd

                                 设备处理了足够的 bio，以便 bio 写入队列大小

                                 低于 congestion off 标记。

                                  值 <= 0 禁用拥塞控制。

示例：

--------

要直接使用 pktcdvd sysfs 接口，可以执行以下操作：

# 创建一个映射到 /dev/hdc 的新 pktcdvd 设备

echo "22:0" >/sys/class/pktcdvd/add

cat /sys/class/pktcdvd/device_map

# 假设创建了设备 pktcdvd0，查看 stat 的值

cat /sys/class/pktcdvd/pktcdvd0/stat/kb_written

# 打印映射块设备的设备 ID

fgrep pktcdvd0 /sys/class/pktcdvd/device_map

# 使用 pktcdvd0 设备 ID 253:0 删除设备

echo "253:0" >/sys/class/pktcdvd/remove