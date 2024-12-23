**What**：`/sys/class/<iface>/queues/rx-<queue>/rps_cpus`

**Date**：2010 年 3 月

**KernelVersion**：2.6.35

**Contact**：netdev@vger.kernel.org

**Description**：

当前启用参与此网络设备接收队列的接收数据包转向数据包处理流程的 CPU 的掩码。可能的值取决于系统中可用 CPU 的数量。

**What**：`/sys/class/<iface>/queues/rx-<queue>/rps_flow_cnt`

**Date**：2010 年 4 月

**KernelVersion**：2.6.35

**Contact**：netdev@vger.kernel.org

**Description**：

此特定网络设备接收队列当前正在处理的接收数据包转向流的数量。

**What**：`/sys/class/<iface>/queues/tx-<queue>/tx_timeout`

**Date**：2011 年 11 月

**KernelVersion**：3.3

**Contact**：netdev@vger.kernel.org

**Description**：

指示此网络接口传输队列看到的传输超时事件的数量。

**What**：`/sys/class/<iface>/queues/tx-<queue>/tx_maxrate`

**Date**：2015 年 3 月

**KernelVersion**：4.1

**Contact**：netdev@vger.kernel.org

**Description**：

为此队列设置的 Mbps 最大速率，值为零表示禁用，默认禁用。

**What**：`/sys/class/<iface>/queues/tx-<queue>/xps_cpus`

**Date**：2010 年 11 月

**KernelVersion**：2.6.38

**Contact**：netdev@vger.kernel.org

**Description**：

当前启用参与此网络设备传输队列的传输数据包转向数据包处理流程的 CPU 的掩码。可能的值取决于系统中可用 CPU 的数量。

**What**：`/sys/class/<iface>/queues/tx-<queue>/byte_queue_limits/hold_time`

**Date**：2011 年 11 月

**KernelVersion**：3.3

**Contact**：netdev@vger.kernel.org

**Description**：

指示此特定网络设备传输队列的保持时间（以毫秒为单位），用于测量松弛度。默认值为 1000。

**What**：`/sys/class/<iface>/queues/tx-<queue>/byte_queue_limits/inflight`

**Date**：2011 年 11 月

**KernelVersion**：3.3

**Contact**：netdev@vger.kernel.org

**Description**：

指示此网络设备传输队列中正在传输的字节（对象）数量。

**What**：`/sys/class/<iface>/queues/tx-<queue>/byte_queue_limits/limit`

**Date**：2011 年 11 月

**KernelVersion**：3.3

**Contact**：netdev@vger.kernel.org

**Description**：

指示此网络设备传输队列允许排队的当前字节限制。此值被限制在由 `limit_max` 和 `limit_min` 定义的范围内。

**What**：`/sys/class/<iface>/queues/tx-<queue>/byte_queue_limits/limit_max`

**Date**：2011 年 11 月

**KernelVersion**：3.3

**Contact**：netdev@vger.kernel.org

**Description**：

指示此网络设备传输队列允许排队的字节的绝对最大限制。有关默认值，请参阅 `include/linux/dynamic_queue_limits.h` 。

**What**：`/sys/class/<iface>/queues/tx-<queue>/byte_queue_limits/limit_min`

**Date**：2011 年 11 月

**KernelVersion**：3.3

**Contact**：netdev@vger.kernel.org

**Description**：

指示此网络设备传输队列允许排队的字节的绝对最小限制。默认值为 0。