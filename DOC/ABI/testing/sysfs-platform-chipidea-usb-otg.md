**What**: `/sys/bus/platform/devices/ci_hdrc.0/inputs/a_bus_req`

**Date**: Feb 2014

**Contact**: Li Jun <b47624@freescale.com>

**Description**:

可设置和读取。若 A 设备上运行的应用程序想要使用总线，则将 a_bus_req（A 设备总线请求）输入设置为 1；当应用程序不再使用总线（或想要作为外围设备工作）时，将其设置为 0。内核也可根据来自 B 设备的远程唤醒信号将 a_bus_req 设置为 1，此时 A 设备应决定恢复总线。有效值为“1”和“0”。

读取：如果 A 设备上运行的应用程序以主机角色使用总线，则返回 1，否则返回 0。

**What**: `/sys/bus/platform/devices/ci_hdrc.0/inputs/a_bus_drop`

**Date**: Feb 2014

**Contact**: Li Jun <b47624@freescale.com>

**Description**:

可设置和读取。

当 A 设备上运行的应用程序想要关闭总线时，a_bus_drop（A 设备总线关闭）输入为 1，否则为 0。当 a_bus_drop 为 1 时，a_bus_req 应为 0。

有效值为“1”和“0”。

读取：如果 A 设备已关闭总线（vbus 已关闭），则返回 1，否则返回 0。

**What**: `/sys/bus/platform/devices/ci_hdrc.0/inputs/b_bus_req`

**Date**: Feb 2014

**Contact**: Li Jun <b47624@freescale.com>

**Description**:

可设置和读取。

b_bus_req（B 设备总线请求）输入在 B 设备上运行的应用程序想要以主机角色使用总线时为 1，当应用程序不再想要作为主机工作并决定切换回作为外围设备时为 0。

有效值为“1”和“0”。

读取：如果 B 设备上运行的应用程序以主机角色使用总线，则返回 1，否则返回 0。

**What**: `/sys/bus/platform/devices/ci_hdrc.0/inputs/a_clr_err`

**Date**: Feb 2014

**Contact**: Li Jun <b47624@freescale.com>

**Description**:

仅可设置。

a_clr_err（A 设备 Vbus 错误清除）输入用于清除 vbus 错误，然后 A 设备将关闭总线。

有效值为“1”。