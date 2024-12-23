**What**：`/sys/class/uwb_rc`

**Date**：2008 年 7 月

**KernelVersion**：2.6.27

**Contact**：linux-usb@vger.kernel.org

**Description**：

WiMedia 超宽带通用无线电平台（UWB）无线电控制器的接口。

假定熟悉 ECMA-368“高速超宽带 MAC 和 PHY 规范”。

**What**：`/sys/class/uwb_rc/beacon_timeout_ms`

**Date**：2008 年 7 月

**KernelVersion**：2.6.27

**Description**：

如果在至少这段时间内没有从设备接收到信标，则该设备将被视为已消失并将被移除。默认值为 3 个超帧（约 197 毫秒），这是规范所要求的。

**What**：`/sys/class/uwb_rc/uwbN/`

**Date**：2008 年 7 月

**KernelVersion**：2.6.27

**Contact**：linux-usb@vger.kernel.org

**Description**：

单个 UWB 无线电控制器。

**What**：`/sys/class/uwb_rc/uwbN/beacon`

**Date**：2008 年 7 月

**KernelVersion**：2.6.27

**Contact**：linux-usb@vger.kernel.org

**Description**：

写入：

`<channel>`

以在信标时强制使用特定通道，或者，如果`<channel>`为 -1，则禁止信标。如果`<channel>`为 0，则将使用默认的通道选择算法。有效通道取决于无线电控制器支持的频段组。

读取返回当前活动的通道，如果无线电控制器未信标，则返回 -1。

**What**：`/sys/class/uwb_rc/uwbN/ASIE`

**Date**：2014 年 8 月

**KernelVersion**：3.18

**Contact**：linux-usb@vger.kernel.org

**Description**：

此设备信标中包含的应用特定信息元素（ASIE），以空格分隔的十六进制八位字节表示。

读取返回当前的 ASIE。写入将用写入的 ASIE 替换当前的 ASIE。

**What**：`/sys/class/uwb_rc/uwbN/scan`

**Date**：2008 年 7 月

**KernelVersion**：2.6.27

**Contact**：linux-usb@vger.kernel.org

**Description**：

写入：

`<channel> <type> [<bpst offset>]`

以在某个通道上开始（或停止）扫描。`<type>`为以下之一：

0 - 扫描

1 - 在 BP 之外扫描

2 - 在不活动时扫描

3 - 禁用扫描

4 - 扫描（`<bpst offset>`为起始时间）

**What**：`/sys/class/uwb_rc/uwbN/mac_address`

**Date**：2008 年 7 月

**KernelVersion**：2.6.27

**Contact**：linux-usb@vger.kernel.org

**Description**：

此无线电控制器的 EUI-48，以冒号分隔的十六进制八位字节表示。写入将更改无线电控制器的 EUI-48，但仅在设备未信标或扫描时进行。

**What**：`/sys/class/uwb_rc/uwbN/wusbhc`

**Date**：2008 年 7 月

**KernelVersion**：2.6.27

**Contact**：linux-usb@vger.kernel.org

**Description**：

指向使用此无线电控制器的 WUSB 主机控制器 PAL 设备（如果有）的符号链接。

**What**：`/sys/class/uwb_rc/uwbN/<EUI-48>/`

**Date**：2008 年 7 月

**KernelVersion**：2.6.27

**Contact**：linux-usb@vger.kernel.org

**Description**：

已通过扫描检测到或作为无线电控制器信标组成员的邻居 UWB 设备。

**What**：`/sys/class/uwb_rc/uwbN/<EUI-48>/BPST`

**Date**：2008 年 7 月

**KernelVersion**：2.6.27

**Contact**：linux-usb@vger.kernel.org

**Description**：

收到此设备最后一个信标的时间（使用无线电控制器内部的 1 毫秒间隔超帧定时器）。

**What**：`/sys/class/uwb_rc/uwbN/<EUI-48>/DevAddr`

**Date**：2008 年 7 月

**KernelVersion**：2.6.27

**Contact**：linux-usb@vger.kernel.org

**Description**：

此设备当前的 DevAddr，以冒号分隔的十六进制八位字节表示。

**What**：`/sys/class/uwb_rc/uwbN/<EUI-48>/EUI_48`

**Date**：2008 年 7 月

**KernelVersion**：2.6.27

**Contact**：linux-usb@vger.kernel.org

**Description**：

此设备的 EUI-48，以冒号分隔的十六进制八位字节表示。

**What**：`/sys/class/uwb_rc/uwbN/<EUI-48>/IEs`

**Date**：2008 年 7 月

**KernelVersion**：2.6.27

**Contact**：linux-usb@vger.kernel.org

**Description**：

此设备信标中包含的最新 IEs，以空格分隔的十六进制八位字节表示，每行一个 IE。

**What**：`/sys/class/uwb_rc/uwbN/<EUI-48>/LQE`

**Date**：2008 年 7 月

**KernelVersion**：2.6.27

**Contact**：linux-usb@vger.kernel.org

**Description**：

链路质量估计 - 从此设备接收的所有数据包的信噪比（SNR），以 dB 为单位。这为合适的 PHY 速率提供了估计。有关更多详细信息，请参考 [ECMA-368] 第 13.3 节。

**What**：`/sys/class/uwb_rc/uwbN/<EUI-48>/RSSI`

**Date**：2008 年 7 月

**KernelVersion**：2.6.27

**Contact**：linux-usb@vger.kernel.org

**Description**：

接收信号强度指示 - 接收信号的强度，以 dB 为单位。LQE 是对无线电链路质量更有用的度量。