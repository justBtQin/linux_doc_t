<markdown_document>

# Network classifier cgroup

Network classifier cgroup 提供了一个接口，用于用类标识符（classid）标记网络数据包。

流量控制器（tc）可用于为来自不同 cgroup 的数据包分配不同的优先级。此外，Netfilter（iptables）可以使用此标签对这些数据包执行操作。

创建一个 net_cls cgroups 实例会创建一个 net_cls.classid 文件。此 net_cls.classid 值初始化为 0。

可以将十六进制值写入 net_cls.classid；这些值的格式为 0xAAAABBBB；AAAA 是主句柄编号，BBBB 是次句柄编号。读取 net_cls.classid 会产生一个十进制结果。

示例：

mkdir /sys/fs/cgroup/net_cls

mount -t cgroup -onet_cls net_cls /sys/fs/cgroup/net_cls

mkdir /sys/fs/cgroup/net_cls/0

echo 0x100001 >  /sys/fs/cgroup/net_cls/0/net_cls.classid

    - 设置一个 10:1 句柄。

cat /sys/fs/cgroup/net_cls/0/net_cls.classid

1048577

配置 tc：

tc qdisc add dev eth0 root handle 10: htb

tc class add dev eth0 parent 10: classid 10:1 htb rate 40mbit

    - 创建流量类 10:1

tc filter add dev eth0 parent 10: protocol ip prio 10 handle 1: cgroup

配置 iptables，基本示例：

iptables -A OUTPUT -m cgroup! --cgroup 0x100001 -j DROP

</markdown_document>