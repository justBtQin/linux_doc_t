**What**: `/sys/class/uwb_rc/uwbN/wusbhc/wusb_chid`

**Date**: July 2008

**KernelVersion**: 2.6.27

**Contact**: David Vrabel <david.vrabel@csr.com>

**Description**: 写入此主机控制器的 CHID（16 个用空格分隔的十六进制八位字节）。这启动主机控制器，使其能够接受来自 WUSB 设备的连接。设置全零的 CHID 以停止主机控制器。

**What**: `/sys/class/uwb_rc/uwbN/wusbhc/wusb_trust_timeout`

**Date**: July 2008

**KernelVersion**: 2.6.27

**Contact**: David Vrabel <david.vrabel@csr.com>

**Description**: 在“wusb_trust_timeout”毫秒内未向主机发送 WUSB 数据包的设备被视为已断开连接并被移除。默认值 4000 毫秒是 WUSB 规范要求的值。由于这与安全性（特别是 PTK 和 GTK 的生命周期）有关，不应从默认值更改。

**What**: `/sys/class/uwb_rc/uwbN/wusbhc/wusb_phy_rate`

**Date**: August 2009

**KernelVersion**: 2.6.32

**Contact**: David Vrabel <david.vrabel@csr.com>

**Description**: 用于所有连接设备的最大 PHY 速率。这仅在测试和开发中有有限的用途，因为硬件的自动速率适应比此简单控制更好。请参考[ECMA-368]第 10.3.1.1 节以获取要使用的值。

**What**: `/sys/class/uwb_rc/uwbN/wusbhc/wusb_dnts`

**Date**: June 2013

**KernelVersion**: 3.11

**Contact**: Thomas Pugliese <thomas.pugliese@gmail.com>

**Description**: WUSB 主机应使用的设备通知时隙（DNTS）计数和毫秒间隔。这控制设备将有机会向主机发送通知的频率。

**What**: `/sys/class/uwb_rc/uwbN/wusbhc/wusb_retry_count`

**Date**: June 2013

**KernelVersion**: 3.11

**Contact**: Thomas Pugliese <thomas.pugliese@gmail.com>

**Description**: WUSB 主机在报告总线事务错误之前应尝试的重试次数。有效值的范围是[0..15]，其中 0 表示无限重试。