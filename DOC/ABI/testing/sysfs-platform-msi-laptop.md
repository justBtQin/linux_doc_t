**/sys/devices/platform/msi-laptop-pf/lcd_level**

- **What**：/sys/devices/platform/msi-laptop-pf/lcd_level

- **Date**：Oct 2006

- **KernelVersion**：2.6.19

- **Contact**："Lennart Poettering <mzxreary@0pointer.de>"

- **Description**：屏幕亮度：包含 0 到 8 范围内的单个整数。

**/sys/devices/platform/msi-laptop-pf/auto_brightness**

- **What**：/sys/devices/platform/msi-laptop-pf/auto_brightness

- **Date**：Oct 2006

- **KernelVersion**：2.6.19

- **Contact**："Lennart Poettering <mzxreary@0pointer.de>"

- **Description**：启用自动亮度控制：包含 0 或 1。如果设置为 1，当电源线插入/拔出时，硬件会自动调整屏幕亮度。

**/sys/devices/platform/msi-laptop-pf/wlan**

- **What**：/sys/devices/platform/msi-laptop-pf/wlan

- **Date**：Oct 2006

- **KernelVersion**：2.6.19

- **Contact**："Lennart Poettering <mzxreary@0pointer.de>"

- **Description**：WLAN 子系统启用：包含 0 或 1。

**/sys/devices/platform/msi-laptop-pf/bluetooth**

- **What**：/sys/devices/platform/msi-laptop-pf/bluetooth

- **Date**：Oct 2006

- **KernelVersion**：2.6.19

- **Contact**："Lennart Poettering <mzxreary@0pointer.de>"

- **Description**：蓝牙子系统启用：包含 0 或 1。请注意，如果没有蓝牙硬件，此文件始终为 0。

**/sys/devices/platform/msi-laptop-pf/touchpad**

- **What**：/sys/devices/platform/msi-laptop-pf/touchpad

- **Date**：Nov 2012

- **KernelVersion**：3.8

- **Contact**："Maxim Mikityanskiy <maxtram95@gmail.com>"

- **Description**：包含 0 或 1，表示触摸板是否打开。触摸板状态只能通过按下 Fn+F3 来切换。

**/sys/devices/platform/msi-laptop-pf/turbo_mode**

- **What**：/sys/devices/platform/msi-laptop-pf/turbo_mode

- **Date**：Nov 2012

- **KernelVersion**：3.8

- **Contact**："Maxim Mikityanskiy <maxtram95@gmail.com>"

- **Description**：包含 0 或 1，表示涡轮模式是否开启。在涡轮模式下，电源指示灯为橙色，处理器被超频。涡轮模式仅在充电时可用。只能通过按下 Fn+F10 来切换涡轮模式状态，并且在后续切换之间有几秒钟的冷却时间。如果用户频繁按下 Fn+F10，涡轮模式状态不会改变。

**/sys/devices/platform/msi-laptop-pf/eco_mode**

- **What**：/sys/devices/platform/msi-laptop-pf/eco_mode

- **Date**：Nov 2012

- **KernelVersion**：3.8

- **Contact**："Maxim Mikityanskiy <maxtram95@gmail.com>"

- **Description**：包含 0 或 1，表示 ECO 模式是否开启。在 ECO 模式下，电源指示灯为绿色，用户空间应执行一些节能操作。ECO 模式仅在电池电源下可用。只能通过按下 Fn+F10 来切换 ECO 模式。

**/sys/devices/platform/msi-laptop-pf/turbo_cooldown**

- **What**：/sys/devices/platform/msi-laptop-pf/turbo_cooldown

- **Date**：Nov 2012

- **KernelVersion**：3.8

- **Contact**："Maxim Mikityanskiy <maxtram95@gmail.com>"

- **Description**：包含 0 到 3 范围内的值：

    - 0 -> 涡轮模式关闭

    - 1 -> 涡轮模式开启，尚未关闭

    - 2 -> 涡轮模式关闭，尚未开启

    - 3 -> 涡轮模式开启

**/sys/devices/platform/msi-laptop-pf/auto_fan**

- **What**：/sys/devices/platform/msi-laptop-pf/auto_fan

- **Date**：Nov 2012

- **KernelVersion**：3.8

- **Contact**："Maxim Mikityanskiy <maxtram95@gmail.com>"

- **Description**：包含 0 或 1，表示风扇速度是自动控制（1）还是以最大速度运行（0）。可以在软件中切换。