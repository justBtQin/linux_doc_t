**关于 /sys/class/backlight/<backlight>/bl_power**

- **What**：/sys/class/backlight/<backlight>/bl_power

- **Date**：April 2005

- **KernelVersion**：2.6.12

- **Contact**：Richard Purdie <rpurdie@rpsys.net>

- **Description**：控制 BACKLIGHT 电源，值为 fb.h 中的 FB_BLANK_*

    - FB_BLANK_UNBLANK (0) ：开启电源。

    - FB_BLANK_POWERDOWN (4) ：关闭电源

- **Users**：HAL

**关于 /sys/class/backlight/<backlight>/brightness**

- **What**：/sys/class/backlight/<backlight>/brightness

- **Date**：April 2005

- **KernelVersion**：2.6.12

- **Contact**：Richard Purdie <rpurdie@rpsys.net>

- **Description**：控制此 <backlight> 的亮度。值在 0 到 max_brightness 之间。此文件还将显示驱动程序中存储的亮度级别，该级别可能不是实际亮度（请参阅 actual_brightness）。

- **Users**：HAL

**关于 /sys/class/backlight/<backlight>/actual_brightness**

- **What**：/sys/class/backlight/<backlight>/actual_brightness

- **Date**：March 2006

- **KernelVersion**：2.6.17

- **Contact**：Richard Purdie <rpurdie@rpsys.net>

- **Description**：通过查询硬件显示实际亮度。

- **Users**：HAL

**关于 /sys/class/backlight/<backlight>/max_brightness**

- **What**：/sys/class/backlight/<backlight>/max_brightness

- **Date**：April 2005

- **KernelVersion**：2.6.12

- **Contact**：Richard Purdie <rpurdie@rpsys.net>

- **Description**：<backlight> 的最大亮度。

- **Users**：HAL

**关于 /sys/class/backlight/<backlight>/type**

- **What**：/sys/class/backlight/<backlight>/type

- **Date**：September 2010

- **KernelVersion**：2.6.37

- **Contact**：Matthew Garrett <mjg@redhat.com>

- **Description**：由 <backlight> 控制的接口类型。

    - "firmware"：驱动程序使用标准固件接口。

    - "platform"：驱动程序使用特定于平台的接口。

    - "raw"：驱动程序直接控制硬件寄存器。

一般情况下，当单个设备有多个背光接口可用时，应优先选择固件控制，其次是平台控制，最后是原始控制。使用固件接口可降低与硬件和操作系统独立更新背光状态混淆的可能性。平台接口主要是固件接口标准化之前的遗留物。