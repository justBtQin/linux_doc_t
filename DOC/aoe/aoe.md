# ATA over Ethernet 是一种网络协议，可提供对 LAN 上块存储的简单访问。

[http://support.coraid.com/documents/AoEr11.txt](http://support.coraid.com/documents/AoEr11.txt)

2.6 和 3.x 内核的 EtherDrive(R)HOWTO 位于...

[http://support.coraid.com/support/linux/EtherDrive-2.6-HOWTO.html](http://support.coraid.com/support/linux/EtherDrive-2.6-HOWTO.html)

它有很多提示和技巧！请特别参阅虚拟内存的推荐调整：

[http://support.coraid.com/support/linux/EtherDrive-2.6-HOWTO-5.html#ss5.19](http://support.coraid.com/support/linux/EtherDrive-2.6-HOWTO-5.html#ss5.19)

aoetools 是用户空间程序，旨在与该驱动程序配合使用。aoetools 在 sourceforge 上。

[http://aoetools.sourceforge.net/](http://aoetools.sourceforge.net/)

此 Documentation/aoe 目录中的脚本旨在记录驱动程序的使用，如果安装了 aoetools，则不是必需的。

# 创建设备节点

使用 udev 的用户应自动找到创建的块设备节点，但要创建所有必要的设备节点，请使用此目录中的 udev 配置规则（udev.txt）。有一个 udev-install.sh 脚本，展示了如何在系统上安装这些规则。还有一个自动加载脚本，展示了如何编辑 /etc/modprobe.d/aoe.conf 以确保在必要时加载 aoe 模块。预先加载 aoe 模块比自动加载更可取，因为 AoE 发现需要几秒钟。第一次运行 a 命令时如果 AoE 设备不存在，但几秒钟后出现，可能会造成混淆。

# 使用设备节点

“cat /dev/etherd/err”会阻塞，等待错误诊断输出，如任何重传的数据包。

“echo eth2 eth4 > /dev/etherd/interfaces”告诉 aoe 驱动程序将 ATA over Ethernet 流量限制为 eth2 和 eth4。出于安全考虑，应忽略来自不受信任网络的 AoE 流量。另请参阅下面描述的 aoe_iflist 驱动程序选项。

“echo > /dev/etherd/discover”告诉驱动程序找出可用的 AoE 设备。

将来，这些字符设备可能会消失并被 sysfs 对应物取代。使用 aoetools 中的命令可使用户免受这些实现细节的影响。块设备的命名如下：

```
e{shelf}.{slot}
e{shelf}.{slot}p{part}
```

... 因此，“e0.2”是第一个架子（架子地址为零）中从左数第三个刀片（插槽 2）。这是整个磁盘。该磁盘上的第一个分区将是“e0.2p1”。

# 使用 SYSFS

/sys/block 中的每个 aoe 块设备都有额外的属性 state、mac 和 netif。当设备准备好进行 I/O 时，state 属性为“up”；如果检测到但不可用，则为“down”。“down,closewait”状态表示设备仍处于打开状态，在关闭之前无法再次启动。

mac 属性是远程 AoE 设备的以太网地址。netif 属性是本地主机上与远程 AoE 设备通信的网络接口。此目录中有一个脚本以方便的方式格式化此信息。使用 aoetools 的用户应使用 aoe-stat 命令。

```
root@makki root# sh Documentation/aoe/status.sh 
     e10.0            eth3              up
     e10.1            eth3              up
     e10.2            eth3              up
     e10.3            eth3              up
     e10.4            eth3              up
     e10.5            eth3              up
     e10.6            eth3              up
     e10.7            eth3              up
     e10.8            eth3              up
     e10.9            eth3              up
      e4.0            eth1              up
      e4.1            eth1              up
      e4.2            eth1              up
      e4.3            eth1              up
      e4.4            eth1              up
      e4.5            eth1              up
      e4.6            eth1              up
      e4.7            eth1              up
      e4.8            eth1              up
      e4.9            eth1              up
```

使用 /sys/module/aoe/parameters/aoe_iflist（或更好的是，下面讨论的驱动程序选项）而不是 /dev/etherd/interfaces 来将 AoE 流量限制为给定的以空格分隔的网络接口列表。与旧的字符设备不同，sysfs 条目既可以读取也可以写入。

在设置允许的接口列表后触发发现是有帮助的。aoetools 包为此提供了一个 aoe-discover 脚本。您也可以直接使用上面描述的 /dev/etherd/discover 特殊文件。

# 驱动程序选项

对于内置的 aoe 驱动程序有一个引导选项和相应的模块参数 aoe_iflist。没有此选项，所有网络接口都可用于 ATA over Ethernet。以下是模块参数的使用示例。

```
    modprobe aoe_iflist="eth1 eth3"
```

aoe_deadsecs 模块参数确定驱动程序等待 AoE 设备对 AoE 命令作出响应的最大秒数。经过 aoe_deadsecs 秒后，AoE 设备将被标记为“down”。出于测试目的，支持值为零，使 aoe 驱动程序永远尝试 AoE 命令。

aoe_maxout 模块参数的默认值为 128。这是一次将发送到 AoE 目标的未响应数据包的最大数量。

aoe_dyndevs 模块参数的默认值为 1，这意味着驱动程序将根据发现的顺序为发现的 AoE 目标分配块设备次要编号。使用动态次要设备编号时，可以支持更大范围的 AoE 架子和插槽地址。使用 udev 的用户永远不必考虑次要编号。使用 aoe_dyndevs=0 允许使用 aoetools 中的 aoe-mkshelf 脚本使用静态次要编号方案预先创建设备节点。