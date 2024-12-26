<markdown_document>

# Block IO Controller

## Overview

cgroup 子系统 "blkio" 实现了块 I/O 控制器。在存储层次结构的叶节点以及中间节点似乎都需要各种 I/O 控制策略（如比例带宽、最大带宽）。计划使用相同的基于 cgroup 的管理接口来管理 blkio 控制器，并根据用户选项在后台切换 I/O 策略。

目前实现了两种 I/O 控制策略。第一种是基于比例权重时间的磁盘策略划分。它在 CFQ 中实现。因此，此策略仅在使用 CFQ 时在叶节点上生效。第二种是限流策略，可用于指定设备上的上限 I/O 速率限制。此策略在通用块层中实现，可用于叶节点以及设备映射器等更高级别的逻辑设备。

## HOWTO

### 带宽的比例权重划分

- 启用块 I/O 控制器

    CONFIG_BLK_CGROUP=y

- 在 CFQ 中启用组调度

    CONFIG_CFQ_GROUP_IOSCHED=y

- 编译并引导内核并挂载 I/O 控制器（blkio）；请参阅 cgroups.txt，为什么需要 cgroups？

    mount -t tmpfs cgroup_root /sys/fs/cgroup

    mkdir /sys/fs/cgroup/blkio

    mount -t cgroup -o blkio none /sys/fs/cgroup/blkio

- 创建两个 cgroup

    mkdir -p /sys/fs/cgroup/blkio/test1/ /sys/fs/cgroup/blkio/test2

- 设置组 test1 和 test2 的权重

    echo 1000 > /sys/fs/cgroup/blkio/test1/blkio.weight

    echo 500 > /sys/fs/cgroup/blkio/test2/blkio.weight

- 在同一磁盘（file1，file2）上创建两个相同大小的文件（每个文件 512MB），并在不同的 cgroup 中启动两个 dd 线程来读取这些文件。

    sync

    echo 3 > /proc/sys/vm/drop_caches

    dd if=/mnt/sdb/zerofile1 of=/dev/null &

    echo $! > /sys/fs/cgroup/blkio/test1/tasks

    cat /sys/fs/cgroup/blkio/test1/tasks

    dd if=/mnt/sdb/zerofile2 of=/dev/null &

    echo $! > /sys/fs/cgroup/blkio/test2/tasks

    cat /sys/fs/cgroup/blkio/test2/tasks

- 在宏观层面，第一个 dd 应该首先完成。要获得更精确的数据，请继续查看（借助脚本）两个 test1 和 test2 组的 blkio.disk_time 和 blkio.disk_sectors 文件。这将告诉每个组获得了多少磁盘时间（以毫秒为单位），以及每个组向磁盘发送了多少扇区。我们在磁盘时间方面提供公平性，因此理想情况下，cgroup 的 io.disk_time 应与权重成比例。

### 限流/上限策略

- 启用块 I/O 控制器

    CONFIG_BLK_CGROUP=y

- 在块层中启用限流

    CONFIG_BLK_DEV_THROTTLING=y

- 挂载 blkio 控制器（请参阅 cgroups.txt，为什么需要 cgroups？）

        mount -t cgroup -o blkio none /sys/fs/cgroup/blkio

- 在根组的特定设备上指定带宽速率。策略的格式为 "<major>:<minor>  <bytes_per_second>"。

        echo "8:16  1048576" > /sys/fs/cgroup/blkio/blkio.throttle.read_bps_device

        上述操作将对主/从编号为 8:16 的设备上的根组的读取操作设置 1MB/秒的限制。

- 运行 dd 来读取文件并查看速率是否被限制为 1MB/s。

        # dd if=/mnt/common/zerofile of=/dev/null bs=4K count=1024

        # iflag=direct

        1024+0 records in

        1024+0 records out

        4194304 bytes (4.2 MB) copied, 4.0001 s, 1.0 MB/s

        可以使用 blkio.throttle.write_bps_device 文件设置写入限制。

## Hierarchical Cgroups

CFQ 和限流都实现了层次结构支持；然而，只有在从 cgroup 侧启用 "sane_behavior" 时，限流的层次结构支持才会启用，目前这是一个开发选项，尚未公开可用。

如果有人创建了如下层次结构：

            root

            /  \

		     test1 test2

			|

		     test3

CFQ 默认情况下和启用 "sane_behavior" 的限流将正确处理层次结构。有关 CFQ 层次结构支持的详细信息，请参考 Documentation/block/cfq-iosched.txt。对于限流，所有限制都适用于整个子树，而所有统计信息都仅适用于该 cgroup 中的任务直接生成的 I/O。

如果从 cgroup 侧未启用 "sane_behavior" 就启用限流，实际上将所有同级组视为如下情况：

                pivot

                /  /   \  \

            root  test1 test2  test3

## Various user visible config options

### CONFIG_BLK_CGROUP

    - 块 I/O 控制器。

### CONFIG_DEBUG_BLK_CGROUP

    - 调试帮助。目前，如果启用此选项，一些额外的统计文件将出现在 cgroup 中。

### CONFIG_CFQ_GROUP_IOSCHED

    - 在 CFQ 中启用组调度。目前仅允许创建 1 级组。

### CONFIG_BLK_DEV_THROTTLING

    - 在块层中启用块设备限流支持。

## Details of cgroup files

### Proportional weight policy files

- blkio.weight

    - 指定每个 cgroup 的权重。这是该组在所有设备上的默认权重，除非被每个设备的规则覆盖。（请参阅 blkio.weight_device）。目前允许的权重范围是 10 到 1000。

- blkio.weight_device

    - 可以使用此接口为每个 cgroup 中的每个设备指定规则。这些规则覆盖了 blkio.weight 指定的组权重的默认值。

    以下是格式。
```
    # echo dev_maj:dev_minor weight > blkio.weight_device
    在这个 cgroup 中为 /dev/sdb（8:16）配置权重=300
    # echo 8:16 300 > blkio.weight_device
    # cat blkio.weight_device
    dev     weight
    8:16    300

    在这个 cgroup 中为 /dev/sda（8:0）配置权重=500
    # echo 8:0 500 > blkio.weight_device
    # cat blkio.weight_device
    dev     weight
    8:0     500
    8:16    300

    在这个 cgroup 中删除 /dev/sda 的特定权重
    # echo 8:0 0 > blkio.weight_device
    # cat blkio.weight_device
    dev     weight
    8:16    300
```

- blkio.leaf_weight[_device]

    - 对于决定给定 cgroup 中的任务在与该 cgroup 的子 cgroup 竞争时具有多少权重的目的，是 blkio.weight[_device] 的等效项。有关详细信息，请参考 Documentation/block/cfq-iosched.txt。

- blkio.time

    - 每个设备分配给 cgroup 的磁盘时间（以毫秒为单位）。前两个字段指定设备的主设备号和次设备号，第三个字段指定分配给组的磁盘时间（以毫秒为单位）。

- blkio.sectors

    - 该组传输到/从磁盘的扇区数量。前两个字段指定设备的主设备号和次设备号，第三个字段指定该组传输到/从设备的扇区数量。

- blkio.io_service_bytes

    - 该组传输到/从磁盘的字节数。这些进一步按操作类型（读取或写入、同步或异步）划分。前两个字段指定设备的主设备号和次设备号，第三个字段指定操作类型，第四个字段指定字节数。

- blkio.io_serviced

    - 该组完成到/从磁盘的 I/O 数量。这些进一步按操作类型（读取或写入、同步或异步）划分。前两个字段指定设备的主设备号和次设备号，第三个字段指定操作类型，第四个字段指定 I/O 数量。

- blkio.io_service_time

    - 此 cgroup 完成的 I/O 从请求调度到请求完成的总时间。这以纳秒为单位，对于闪存设备也有意义。对于队列深度为 1 的设备，此时间表示实际服务时间。当队列深度>1 时，情况不再如此，因为请求可能会乱序服务。这可能导致给定 I/O 的服务时间包括乱序服务的多个 I/O 的服务时间，这可能导致 total io_service_time >实际经过的时间。

本次时间进一步根据操作类型（读或写、同步或异步）进行划分。前两个字段指定设备的主设备号和次设备号，第三个字段指定操作类型，第四个字段指定以纳秒为单位的 io_service_time。

- blkio.io_wait_time

    - 此 cgroup 的 I/O 在调度程序队列中等待服务所花费的总时间。由于它是所有 I/O 的累积 io_wait_time，所以可能大于自开始以来经过的总时间。这不是 cgroup 花费的总等待时间的度量，而是其各个 I/O 的等待时间的度量。对于队列深度 > 1 的设备，此指标不包括 I/O 被调度到设备后但实际得到服务之前等待服务的时间（由于设备对请求的重新排序，这里可能存在时间延迟）。以纳秒为单位，这对于闪存设备也有意义。此时间进一步根据操作类型（读或写、同步或异步）进行划分。前两个字段指定设备的主设备号和次设备号，第三个字段指定操作类型，第四个字段指定以纳秒为单位的 io_wait_time。

- blkio.io_merged

    - 合并到属于此 cgroup 的请求中的 bios/请求的总数。此数据进一步根据操作类型（读或写、同步或异步）进行划分。

- blkio.io_queued

    - 在此 cgroup 的任何给定时刻排队的请求总数。此数据进一步根据操作类型（读或写、同步或异步）进行划分。

- blkio.avg_queue_size

    - 仅在 CONFIG_DEBUG_BLK_CGROUP=y 时启用的调试辅助工具。此 cgroup 存在的整个时间内的平均队列大小。每次此 cgroup 的队列之一获得时间片时，都会采集队列大小样本。

- blkio.group_wait_time

    - 仅在 CONFIG_DEBUG_BLK_CGROUP=y 时启用的调试辅助工具。这是 cgroup 自变得忙碌（即从 0 个请求排队变为 1 个请求排队）以来为其一个队列获得时间片所需的等待时间。这与 io_wait_time 不同，io_wait_time 是该 cgroup 中的每个 I/O 在调度程序队列中等待的时间总和。以纳秒为单位。如果在 cgroup 处于等待（获取时间片）状态时读取此值，该统计信息将仅报告自上次获得时间片以来累积的 group_wait_time，而不包括当前增量。

- blkio.empty_time

    - 仅在 CONFIG_DEBUG_BLK_CGROUP=y 时启用的调试辅助工具。这是 cgroup 在未被服务时没有任何挂起请求所花费的时间，即它不包括 cgroup 的任何队列空闲所花费的时间。以纳秒为单位。如果在 cgroup 处于空闲状态时读取此值，该统计信息将仅报告自上次有挂起请求以来累积的 empty_time，而不包括当前增量。

- blkio.idle_time

    - 仅在 CONFIG_DEBUG_BLK_CGROUP=y 时启用的调试辅助工具。这是 IO 调度程序为给定 cgroup 空闲以等待比其他队列/ cgroup 中的现有请求更好的请求所花费的时间。以纳秒为单位。如果在 cgroup 处于空闲状态时读取此值，该统计信息将仅报告自上次空闲周期以来累积的 idle_time，而不包括当前增量。

- blkio.dequeue

    - 仅在 CONFIG_DEBUG_BLK_CGROUP=y 时启用的调试辅助工具。这提供了有关一个组从设备的服务树中出队的次数的统计信息。前两个字段指定设备的主设备号和次设备号，第三个字段指定一个组从特定设备出队的次数。

- blkio.*_recursive

    - 各种统计信息的递归版本。这些文件显示与非递归对应文件相同的信息，但包括所有后代 cgroup 的统计信息。

## 节流/上限策略文件

-------------------

- blkio.throttle.read_bps_device

    - 指定从设备读取的速率上限。IO 速率以每秒字节为单位指定。规则是针对每个设备的。以下是格式。

    echo "<major>:<minor>  <rate_bytes_per_second>" > /cgrp/blkio.throttle.read_bps_device

- blkio.throttle.write_bps_device

    - 指定写入设备的速率上限。IO 速率以每秒 IO 为单位指定。规则是针对每个设备的。以下是格式。

    echo "<major>:<minor>  <rate_bytes_per_second>" > /cgrp/blkio.throttle.write_bps_device

- blkio.throttle.read_iops_device

    - 指定从设备读取的速率上限。IO 速率以每秒 IO 为单位指定。规则是针对每个设备的。以下是格式。

    echo "<major>:<minor>  <rate_io_per_second>" > /cgrp/blkio.throttle.read_iops_device

- blkio.throttle.write_iops_device

    - 指定写入设备的速率上限。IO 速率以每秒 IO 为单位指定。规则是针对每个设备的。以下是格式。

    echo "<major>:<minor>  <rate_io_per_second>" > /cgrp/blkio.throttle.write_iops_device

注意：如果为一个设备同时指定了带宽和 IOPS 规则，则该 IO 将同时受到这两个约束。

- blkio.throttle.io_serviced

    - 该组（根据节流策略查看）完成到/从磁盘的 I/O（bio）数量。这些数据进一步根据操作类型（读或写、同步或异步）进行划分。前两个字段指定设备的主设备号和次设备号，第三个字段指定操作类型，第四个字段指定 I/O 的数量。

    blkio.io_serviced 按照 CFQ 进行计数，计数单位是请求数（struct request）。另一方面，blkio.throttle.io_serviced 按照节流策略计数的是 bios 的数量。这些 bios 稍后可能会被电梯合并，完成的请求总数可能会更少。

- blkio.throttle.io_service_bytes

    - 该组传输到/从磁盘的字节数。这些数据进一步根据操作类型（读或写、同步或异步）进行划分。前两个字段指定设备的主设备号和次设备号，第三个字段指定操作类型，第四个字段指定字节数。

    这些数字应大致与 CFQ 更新的 blkio.io_service_bytes 相同。两者的区别在于，如果 CFQ 不在请求队列上运行，则 blkio.io_service_bytes 将不会更新。

## 各种策略中的通用文件

-------------------

- blkio.reset_stats

    - 向此文件写入一个整数将导致重置该 cgroup 的所有统计信息。

## CFQ sysfs 可调参数

=================

/sys/block/<disk>/queue/iosched/slice_idle

------------------------------------------

在较快的硬件上，CFQ 可能会很慢，特别是对于顺序工作负载。这是因为 CFQ 在单个队列上空闲，而单个队列可能无法驱动更深的请求队列深度以保持存储繁忙。在这种情况下，人们可以尝试将 slice_idle 设置为 0，这将在支持 NCQ 的硬件上将 CFQ 切换到 IOPS（每秒 IO 操作）模式。

这意味着 CFQ 将不会在 cfq 组的 cfq 队列之间空闲，因此能够驱动更高的队列深度并实现更好的吞吐量。这也意味着 cfq 在 IOPS 方面而不是在磁盘时间方面提供组之间的公平性。

/sys/block/<disk>/queue/iosched/group_idle

------------------------------------------

如果通过设置 slice_idle=0 禁用单个 cfq 队列和 cfq 服务树的空闲，则 group_idle 开始生效。这意味着 CFQ 仍将在组上空闲，以尝试在组之间提供公平性。

默认情况下，group_idle 与 slice_idle 相同，如果 slice_idle 已启用，则不会执行任何操作。

如果创建了多个组，并将应用程序放入这些组中，但这些应用程序没有产生足够的 IO 来保持磁盘繁忙，那么整体吞吐量可能会下降。在这种情况下，设置 group_idle = 0，CFQ 将不会在各个组上闲置，吞吐量应该会提高。

## 什么有效

==========

- 当前仅支持同步 IO 队列。所有的缓冲写入仍然是系统范围的，而不是每个组的。因此，我们不会看到组之间缓冲写入的服务差异。

</markdown_document>