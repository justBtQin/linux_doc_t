**What**：`/sys/class/net/<iface>/cdc_ncm/min_tx_pkt`

**Date**：2014 年 5 月

**KernelVersion**：3.16

**Contact**：Bjørn Mork <bjorn@mork.no>

**Description**：驱动程序将填充长于此值的 NCM 传输块（NTBs）至`tx_max`，允许设备接收`tx_max`大小的帧且无终止短数据包。短于此限制的 NTBs 将按原样传输，无需任何填充，并以短 USB 数据包终止。

填充至`tx_max`允许驱动程序连续传输 NTBs 而无任何交错的短 USB 数据包。这减少了设备中的短数据包中断数量，并代表了 USB 总线带宽与设备 DMA 优化之间的权衡。设置为 0 以填充所有帧。设置大于`tx_max`以禁用所有填充。

**What**：`/sys/class/net/<iface>/cdc_ncm/rx_max`

**Date**：2014 年 5 月

**KernelVersion**：3.16

**Contact**：Bjørn Mork <bjorn@mork.no>

**Description**：接收的 NTB 大小的最大值。不能超过设备支持的最大值。必须至少允许一个最大大小的数据报加上报头。

实际限制取决于设备。请参阅`dwNtbInMaxSize`。

注意：某些设备将静默忽略此值的更改，导致 NTBs 过大和相应的帧错误。

**What**：`/sys/class/net/<iface>/cdc_ncm/tx_max`

**Date**：2014 年 5 月

**KernelVersion**：3.16

**Contact**：Bjørn Mork <bjorn@mork.no>

**Description**：传输的 NTB 大小的最大值。不能超过设备支持的最大值。必须至少允许一个最大大小的数据报加上报头。

实际限制取决于设备。请参阅`dwNtbOutMaxSize`。

**What**：`/sys/class/net/<iface>/cdc_ncm/tx_timer_usecs`

**Date**：2014 年 5 月

**KernelVersion**：3.16

**Contact**：Bjørn Mork <bjorn@mork.no>

**Description**：数据报聚合超时（以微秒为单位）。驱动程序将在传输 NTB 帧之前最多等待此超时的 3 倍以聚合更多数据报。

有效范围：5 至 4000000。

设置为 0 以禁用聚合。

以下只读属性均表示“通用串行总线网络控制模型设备的网络控制模型子类规范”（CDC NCM）第 6.2.1 节“获取 NTB 参数”中定义的结构的字段，修订版 1.0（勘误表 1），2010 年 11 月 24 日，来自 USB 实施者论坛公司。描述引自 CDC NCM 的表 6-3：“NTB 参数结构”。

**What**：`/sys/class/net/<iface>/cdc_ncm/bmNtbFormatsSupported`

**Date**：2014 年 5 月

**KernelVersion**：3.16

**Contact**：Bjørn Mork <bjorn@mork.no>

**Description**：位 0：支持 16 位 NTB（设置为 1）

位 1：支持 32 位 NTB

位 2 - 15：保留（重置为零；主机必须忽略）

**What**：`/sys/class/net/<iface>/cdc_ncm/dwNtbInMaxSize`

**Date**：2014 年 5 月

**KernelVersion**：3.16

**Contact**：Bjørn Mork <bjorn@mork.no>

**Description**：IN NTB 的最大大小（以字节为单位）

**What**：`/sys/class/net/<iface>/cdc_ncm/wNdpInDivisor`

**Date**：2014 年 5 月

**KernelVersion**：3.16

**Contact**：Bjørn Mork <bjorn@mork.no>

**Description**：用于 IN NTB 数据报有效载荷对齐的除数

**What**：`/sys/class/net/<iface>/cdc_ncm/wNdpInPayloadRemainder`

**Date**：2014 年 5 月

**KernelVersion**：3.16

**Contact**：Bjørn Mork <bjorn@mork.no>

**Description**：用于在 NTB 内对齐输入数据报有效载荷的余数：（有效载荷偏移量）mod（wNdpInDivisor）= wNdpInPayloadRemainder

**What**：`/sys/class/net/<iface>/cdc_ncm/wNdpInAlignment`

**Date**：2014 年 5 月

**KernelVersion**：3.16

**Contact**：Bjørn Mork <bjorn@mork.no>

**Description**：IN 管道上 NTB 的 NDP 对齐模数。应为 2 的幂，且至少为 4。

**What**：`/sys/class/net/<iface>/cdc_ncm/dwNtbOutMaxSize`

**Date**：2014 年 5 月

**KernelVersion**：3.16

**Contact**：Bjørn Mork <bjorn@mork.no>

**Description**：OUT NTB 的最大大小

**What**：`/sys/class/net/<iface>/cdc_ncm/wNdpOutDivisor`

**Date**：2014 年 5 月

**KernelVersion**：3.16

**Contact**：Bjørn Mork <bjorn@mork.no>

**Description**：OUT NTB 数据报对齐模数

**What**：`/sys/class/net/<iface>/cdc_ncm/wNdpOutPayloadRemainder`

**Date**：2014 年 5 月

**KernelVersion**：3.16

**Contact**：Bjørn Mork <bjorn@mork.no>

**Description**：用于在 NTB 内对齐输出数据报有效载荷偏移量的余数：填充，应由函数传输为零，并被主机忽略。（有效载荷偏移量）mod（wNdpOutDivisor）= wNdpOutPayloadRemainder

**What**：`/sys/class/net/<iface>/cdc_ncm/wNdpOutAlignment`

**Date**：2014 年 5 月

**KernelVersion**：3.16

**Contact**：Bjørn Mork <bjorn@mork.no>

**Description**：用于 OUT 管道上 NTB 的 NDP 对齐模数。应为 2 的幂，且至少为 4。

**What**：`/sys/class/net/<iface>/cdc_ncm/wNtbOutMaxDatagrams`

**Date**：2014 年 5 月

**KernelVersion**：3.16

**Contact**：Bjørn Mork <bjorn@mork.no>

**Description**：主机可打包到单个 OUT NTB 中的最大数据报数量。零表示设备不施加限制。