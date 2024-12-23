**what：** `/sys/class/<iface>/statistics/collisions`

**日期：** 2005 年 4 月

**内核版本：** 2.6.12

**联系人：** netdev@vger.kernel.org

**描述：** 表示此网络设备看到的冲突数量。此值可能与所有 MAC 层不相关。

**what：** `/sys/class/<iface>/statistics/multicast`

**日期：** 2005 年 4 月

**内核版本：** 2.6.12

**联系人：** netdev@vger.kernel.org

**描述：** 表示此网络设备接收的多播数据包数量。

**what：** `/sys/class/<iface>/statistics/rx_bytes`

**日期：** 2005 年 4 月

**内核版本：** 2.6.12

**联系人：** netdev@vger.kernel.org

**描述：** 表示此网络设备接收的字节数。有关此值何时递增的确切含义，请参阅网络驱动程序。

**what：** `/sys/class/<iface>/statistics/rx_compressed`

**日期：** 2005 年 4 月

**内核版本：** 2.6.12

**联系人：** netdev@vger.kernel.org

**描述：** 表示此网络设备接收的压缩数据包数量。此值可能仅与支持数据包压缩的接口（例如：PPP）相关。

**what：** `/sys/class/<iface>/statistics/rx_crc_errors`

**日期：** 2005 年 4 月

**内核版本：** 2.6.12

**联系人：** netdev@vger.kernel.org

**描述：** 表示此网络设备接收的带有 CRC（FCS）错误的数据包数量。请注意，具体含义可能取决于接口使用的 MAC 层。

**what：** `/sys/class/<iface>/statistics/rx_dropped`

**日期：** 2005 年 4 月

**内核版本：** 2.6.12

**联系人：** netdev@vger.kernel.org

**描述：** 表示网络设备接收但丢弃的数据包数量，这些数据包不会转发到上层进行数据包处理。有关此值的确切含义，请参阅网络驱动程序。

**what：** `/sys/class/<iface>/statistics/rx_fifo_errors`

**日期：** 2005 年 4 月

**内核版本：** 2.6.12

**联系人：** netdev@vger.kernel.org

**描述：** 表示此网络设备看到的接收 FIFO 错误数量。有关此值的确切含义，请参阅网络驱动程序。

**what：** `/sys/class/<iface>/statistics/rx_frame_errors`

**日期：** 2005 年 4 月

**内核版本：** 2.6.12

**联系人：** netdev@vger.kernel.org

**描述：** 表示接收的带有错误（例如对齐错误）的帧数。请注意，具体含义取决于所使用的 MAC 层协议。有关此值的确切含义，请参阅网络驱动程序。

**what：** `/sys/class/<iface>/statistics/rx_length_errors`

**日期：** 2005 年 4 月

**内核版本：** 2.6.12

**联系人：** netdev@vger.kernel.org

**描述：** 表示接收的长度错误（过大或过小）的错误数据包数量。有关此值的确切含义，请参阅网络驱动程序。

**what：** `/sys/class/<iface>/statistics/rx_missed_errors`

**日期：** 2005 年 4 月

**内核版本：** 2.6.12

**联系人：** netdev@vger.kernel.org

**描述：** 表示由于接收侧容量不足而错过的接收数据包数量。有关此值的确切含义，请参阅网络驱动程序。

**what：** `/sys/class/<iface>/statistics/rx_over_errors`

**日期：** 2005 年 4 月

**内核版本：** 2.6.12

**联系人：** netdev@vger.kernel.org

**描述：** 表示接收的数据包与网络设备配置的接收大小相比过大（例如：大于 MTU）的数量。有关此值的确切含义，请参阅网络驱动程序。

**what：** `/sys/class/<iface>/statistics/rx_packets`

**日期：** 2005 年 4 月

**内核版本：** 2.6.12

**联系人：** netdev@vger.kernel.org

**描述：** 表示此网络设备接收的良好数据包总数。

**what：** `/sys/class/<iface>/statistics/tx_aborted_errors`

**日期：** 2005 年 4 月

**内核版本：** 2.6.12

**联系人：** netdev@vger.kernel.org

**描述：** 表示网络设备在传输过程中中止的数据包数量（例如：因为介质冲突）。有关此值的确切含义，请参阅网络驱动程序。

**what：** `/sys/class/<iface>/statistics/tx_bytes`

**日期：** 2005 年 4 月

**内核版本：** 2.6.12

**联系人：** netdev@vger.kernel.org

**描述：** 表示网络设备传输的字节数。有关此值的确切含义，特别是此值是否计算所有成功传输的数据包或所有已排队等待传输的数据包，请参阅网络驱动程序。

**what：** `/sys/class/<iface>/statistics/tx_carrier_errors`

**日期：** 2005 年 4 月

**内核版本：** 2.6.12

**联系人：** netdev@vger.kernel.org

**描述：** 表示由于载波错误（例如：物理链路断开）而无法传输的数据包数量。有关此值的确切含义，请参阅网络驱动程序。

**what：** `/sys/class/<iface>/statistics/tx_compressed`

**日期：** 2005 年 4 月

**内核版本：** 2.6.12

**联系人：** netdev@vger.kernel.org

**描述：** 表示传输的压缩数据包数量。请注意，此值可能仅与支持压缩的设备（例如：PPP）相关。

**what：** `/sys/class/<iface>/statistics/tx_dropped`

**日期：** 2005 年 4 月

**内核版本：** 2.6.12

**联系人：** netdev@vger.kernel.org

**描述：** 表示传输过程中丢弃的数据包数量。有关数据包被丢弃的确切原因，请参阅驱动程序。

**what：** `/sys/class/<iface>/statistics/tx_errors`

**日期：** 2005 年 4 月

**内核版本：** 2.6.12

**联系人：** netdev@vger.kernel.org

**描述：** 表示网络设备在传输过程中出错的数据包数量。有关数据包被丢弃的确切原因，请参阅驱动程序。

**what：** `/sys/class/<iface>/statistics/tx_fifo_errors`

**日期：** 2005 年 4 月

**内核版本：** 2.6.12

**联系人：** netdev@vger.kernel.org

**描述：** 表示导致传输 FIFO 错误的数据包数量。有关数据包被丢弃的确切原因，请参阅驱动程序。

**what：** `/sys/class/<iface>/statistics/tx_heartbeat_errors`

**日期：** 2005 年 4 月

**内核版本：** 2.6.12

**联系人：** netdev@vger.kernel.org

**描述：** 表示传输的已报告为心跳错误的数据包数量。有关数据包被丢弃的确切原因，请参阅驱动程序。

**what：** `/sys/class/<iface>/statistics/tx_packets`

**日期：** 2005 年 4 月

**内核版本：** 2.6.12

**联系人：** netdev@vger.kernel.org

**描述：** 表示网络设备传输的数据包数量。请参阅驱动程序，了解此值是报告所有尝试传输还是成功传输的数量。

**what：** `/sys/class/<iface>/statistics/tx_window_errors`

**日期：** 2005 年 4 月

**内核版本：** 2.6.12

**联系人：** netdev@vger.kernel.org

**描述：** 表示由于窗口冲突而未成功传输的数据包数量。具体含义取决于所使用的 MAC 层。在以太网中，这通常用于报告晚期冲突错误。