**What**: /sys/class/udc/<udc>/a_alt_hnp_support

**Date**: June 2011

**KernelVersion**: 3.1

**Contact**: Felipe Balbi <balbi@kernel.org>

**Description**: 指示 OTG A-Host 在备用端口是否支持 HNP。

**Users**: 

**What**: /sys/class/udc/<udc>/a_hnp_support

**Date**: June 2011

**KernelVersion**: 3.1

**Contact**: Felipe Balbi <balbi@kernel.org>

**Description**: 指示 OTG A-Host 在该端口是否支持 HNP。

**Users**: 

**What**: /sys/class/udc/<udc>/b_hnp_enable

**Date**: June 2011

**KernelVersion**: 3.1

**Contact**: Felipe Balbi <balbi@kernel.org>

**Description**: 指示 OTG A-Host 是否启用了 HNP 支持。

**Users**: 

**What**: /sys/class/udc/<udc>/current_speed

**Date**: June 2011

**KernelVersion**: 3.1

**Contact**: Felipe Balbi <balbi@kernel.org>

**Description**: 指示此端口的当前协商速度。

**Users**: 

**What**: /sys/class/udc/<udc>/is_a_peripheral

**Date**: June 2011

**KernelVersion**: 3.1

**Contact**: Felipe Balbi <balbi@kernel.org>

**Description**: 指示此端口是 OTG 会话中的默认主机，但使用 HNP 切换了角色。

**Users**: 

**What**: /sys/class/udc/<udc>/is_otg

**Date**: June 2011

**KernelVersion**: 3.1

**Contact**: Felipe Balbi <balbi@kernel.org>

**Description**: 指示此端口支持 OTG。

**Users**: 

**What**: /sys/class/udc/<udc>/maximum_speed

**Date**: June 2011

**KernelVersion**: 3.1

**Contact**: Felipe Balbi <balbi@kernel.org>

**Description**: 指示此端口支持的最大 USB 速度。

**Users**: 

**What**: /sys/class/udc/<udc>/maximum_speed

**Date**: June 2011

**KernelVersion**: 3.1

**Contact**: Felipe Balbi <balbi@kernel.org>

**Description**: 指示此端口支持的最大 USB 速度。

**Users**: 

**What**: /sys/class/udc/<udc>/soft_connect

**Date**: June 2011

**KernelVersion**: 3.1

**Contact**: Felipe Balbi <balbi@kernel.org>

**Description**: 允许用户断开数据上拉电阻，从而导致与 USB 主机的逻辑断开。

**Users**: 

**What**: /sys/class/udc/<udc>/srp

**Date**: June 2011

**KernelVersion**: 3.1

**Contact**: Felipe Balbi <balbi@kernel.org>

**Description**: 允许用户手动启动会话请求协议。

**Users**: 

**What**: /sys/class/udc/<udc>/state

**Date**: June 2011

**KernelVersion**: 3.1

**Contact**: Felipe Balbi <balbi@kernel.org>

**Description**: 指示 USB 设备控制器的当前状态。有效状态为：'not-attached'、'attached'、'powered'、'reconnecting'、'unauthenticated'、'default'、'addressed'、'configured' 和'suspended'；然而，并非所有 USB 设备控制器都支持报告所有状态。

**Users**: