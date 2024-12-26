<markdown_document>

# Network priority cgroup

网络优先级 cgroup 提供了一个接口，允许管理员动态设置各种应用程序生成的网络流量的优先级。

通常，应用程序会通过 SO_PRIORITY 套接字选项设置其流量的优先级。然而，这并不总是可能的，因为：

1. 应用程序可能没有被编码为设置此值。

2. 应用程序流量的优先级通常是特定于站点的管理决策，而不是应用程序定义的。

此 cgroup 允许管理员将一个进程分配到一个组中，该组定义了给定接口上的出站流量的优先级。可以通过首先挂载 cgroup 文件系统来创建网络优先级组。

# mount -t cgroup -onet_prio none /sys/fs/cgroup/net_prio

通过上述步骤，作为父计费组的初始组在“/sys/fs/cgroup/net_prio”处可见。此组包括系统中的所有任务。“/sys/fs/cgroup/net_prio/tasks”列出了此 cgroup 中的任务。

每个 net_prio cgroup 包含两个特定于子系统的文件：

- net_prio.prioidx：此文件是只读的，只是提供信息。它包含一个唯一的整数值，内核将其用作此 cgroup 的内部表示。

- net_prio.ifpriomap：此文件包含从属于该组的进程生成并从系统出站的流量的优先级映射。它包含以<ifname priority>形式的元组列表。可以通过使用相同的元组格式将字符串回显到文件中来修改此文件的内容。例如：

echo "eth0 5" > /sys/fs/cgroups/net_prio/iscsi/net_prio.ifpriomap

此命令将强制属于 iscsi net_prio cgroup 并从接口 eth0 出站的任何流量的优先级设置为 5。父计费组也有一个可写的“net_prio.ifpriomap”文件，可用于设置系统默认优先级。

优先级在将帧排队到设备排队规则（qdisc）之前立即设置，因此在进行硬件队列选择之前将分配优先级。

net_prio cgroup 的一个用途是与 mqprio qdisc 一起使用，允许将应用程序流量引导到基于硬件/驱动程序的流量类。然后，这些映射可以由管理员或其他网络协议（如 DCBX）管理。

新的 net_prio cgroup 继承父级的配置。

</markdown_document>