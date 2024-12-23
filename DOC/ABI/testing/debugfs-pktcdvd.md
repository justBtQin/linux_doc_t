What: `/sys/kernel/debug/pktcdvd/pktcdvd[0-7]`

Date: `Oct. 2006`

KernelVersion: `2.6.20`

Contact: `Thomas Maier <balagi@justmail.de>`

Description:

debugfs 接口

   `pktcdvd`模块（数据包写入驱动程序）在`debugfs`中创建以下文件：

   `/sys/kernel/debug/pktcdvd/pktcdvd[0-7]/`

    `info`（`0444`）：大量驱动程序统计信息和信息。 

示例:

   `cat /sys/kernel/debug/pktcdvd/pktcdvd0/info`