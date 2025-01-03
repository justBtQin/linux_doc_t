# 空块设备驱动程序

================================================================================

# 一、概述

空块设备（/dev/nullb*）用于对各种块层实现进行基准测试。它模拟一个大小为 X 千兆字节的块设备。以下是可能的实例：

  单队列块层

    - 请求式。     - 每个设备一个提交队列。     - 实现 IO 调度算法（CFQ、Deadline、noop）。   多队列块层

    - 请求式。     - 每个设备可配置的提交队列。   无块层（称为基于 bio）

    - 基于 bio。IO 请求直接提交到设备驱动程序。     - 直接接受 bio 数据结构并返回它们。 

所有这些都有一个用于系统中每个核心的完成队列。 

# 二、适用于所有实例的模块参数：

queue_mode=[0-2]：默认：2-多队列

  选择模块应实例化的块层。 

  0：基于 bio。   1：单队列。   2：多队列。 

home_node=[0--nr_nodes]：默认：NUMA_NO_NODE

  选择数据结构分配的 CPU 节点。 

gb=[以 GB 为单位的大小]：默认：250GB

  报告给系统的设备大小。 

bs=[块大小（以字节为单位）]：默认：512 字节

  报告给系统的块大小。 

nr_devices=[设备数量]：默认：2

  实例化的块设备数量。它们被实例化为 /dev/nullb0 等。 

irqmode=[0-2]：默认：1-软中断

  用于完成到块层的 IO 的完成模式。 

  0：无。   1：软中断。使用 IPI 在 CPU 节点之间完成 IO。模拟当 IO 从设备连接的主节点以外的其他 CPU 节点发出时的开销。   2：定时器：在每个 IO 完成之前等待特定时间段（completion_nsec）。 

completion_nsec=[ns]：默认：10.000ns

  与 irqmode=2（定时器）结合使用。每个完成事件必须等待的时间。 

submit_queues=[0..nr_cpus]：

  附加到设备驱动程序的提交队列数量。如果未设置，在单队列和基于 bio 的实例中默认为 1。对于多队列，当 use_per_node_hctx 模块参数为 1 时将被忽略。 

hw_queue_depth=[0..qdepth]：默认：64

  设备的硬件队列深度。 

# 三：多队列特定参数

use_per_node_hctx=[0/1]：默认：0

  0：提交队列的数量设置为 submit_queues 参数的值。   1：多队列块层在系统中的每个 CPU 节点上实例化为一个硬件调度队列。