# 描述

DRBD 是一种无共享、同步复制的块设备。它被设计用作高可用性集群的构建块，在这种情况下，它是共享存储的“即插即用”替代品。简单来说，你可以将其视为网络 RAID 1。请访问 http://www.drbd.org 以了解更多信息。

这里包含的文件旨在帮助理解实现：

DRBD-8.3-data-packets.svg、DRBD-data-packets.svg：涉及一些功能和写入数据包。

conn-states-8.dot、disk-states-8.dot、node-states-8.dot：DRBD 状态转换的子图。