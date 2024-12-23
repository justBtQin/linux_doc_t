**What**: `/sys/devices/LNXSYSTM:00/LNXSYBUS:00/TOS{1900,620{0,7,8}}:00/kbd_backlight_mode`

**Date**: June 8, 2014

**KernelVersion**: 3.15

**Contact**: Azael Avalos <coproscefalo@gmail.com>

**Description**: 此文件控制键盘背光操作模式，有效

值为：

* 0x1 -> FN-Z

* 0x2 -> AUTO（也称为 TIMER）

* 0x8 -> ON

* 0x10 -> OFF

注意，从 3.16 版本的内核开始，此文件接受所有列出的

参数，3.15 版本的内核仅接受前两个（FN-Z 和 AUTO）。

此外，注意在类型 1 设备上切换此值需要重新启动才能使更改生效。

**Users**: KToshiba

**What**: `/sys/devices/LNXSYSTM:00/LNXSYBUS:00/TOS{1900,620{0,7,8}}:00/kbd_backlight_timeout`

**Date**: June 8, 2014

**KernelVersion**: 3.15

**Contact**: Azael Avalos <coproscefalo@gmail.com>

**Description**: 此文件控制键盘背光的超时时间，

当操作模式设置为 AUTO（或 TIMER）时，

有效范围为 0-60。

注意，3.15 版本的内核仅支持第一种键盘类型，3.16 版本的内核添加了对第二种

类型的支持，类型 2 接受的范围是 1-60。

请参阅名为“kbd_type”的条目

**Users**: KToshiba

**What**: `/sys/devices/LNXSYSTM:00/LNXSYBUS:00/TOS{1900,620{0,7,8}}:00/position`

**Date**: June 8, 2014

**KernelVersion**: 3.15

**Contact**: Azael Avalos <coproscefalo@gmail.com>

**Description**: 此文件显示内置加速度计的绝对位置。

**What**: `/sys/devices/LNXSYSTM:00/LNXSYBUS:00/TOS{1900,620{0,7,8}}:00/touchpad`

**Date**: June 8, 2014

**KernelVersion**: 3.15

**Contact**: Azael Avalos <coproscefalo@gmail.com>

**Description**: 此文件控制触摸板和指点杆（如果可用）的状态，有效

值为：

* 0 -> OFF

* 1 -> ON

**Users**: KToshiba

**What**: `/sys/devices/LNXSYSTM:00/LNXSYBUS:00/TOS{1900,620{0,7,8}}:00/available_kbd_modes`

**Date**: August 3, 2014

**KernelVersion**: 3.16

**Contact**: Azael Avalos <coproscefalo@gmail.com>

**Description**: 此文件显示系统支持的键盘背光模式，

可以是：

* 0x1 -> FN-Z

* 0x2 -> AUTO（也称为 TIMER）

* 0x8 -> ON

* 0x10 -> OFF

注意，并非所有键盘类型都支持列出的模式。

请参阅名为“available_kbd_modes”的条目

**Users**: KToshiba

**What**: `/sys/devices/LNXSYSTM:00/LNXSYBUS:00/TOS{1900,620{0,7,8}}:00/kbd_type`

**Date**: August 3, 2014

**KernelVersion**: 3.16

**Contact**: Azael Avalos <coproscefalo@gmail.com>

**Description**: 此文件显示当前键盘背光类型，

可以是：

* 1 -> 类型 1，支持模式 FN-Z 和 AUTO

* 2 -> 类型 2，支持模式 TIMER、ON 和 OFF

**Users**: KToshiba

**What**: `/sys/devices/LNXSYSTM:00/LNXSYBUS:00/TOS{1900,620{0,7,8}}:00/usb_sleep_charge`

**Date**: January 23, 2015

**KernelVersion**: 4.0

**Contact**: Azael Avalos <coproscefalo@gmail.com>

**Description**: 此文件控制 USB 睡眠与充电充电模式，

可以是：

* 0 -> 禁用（0x00）

* 1 -> 交替（0x09）

* 2 -> 自动（0x21）

* 3 -> 典型（0x11）

注意，从 4.1 版本的内核开始，此文件接受所有列出的

值，4.0 版本的内核仅支持前三个。

注意，此功能仅在连接到电源时有效，如果

要在电池下使用它，请参阅名为“sleep_functions_on_battery”的条目

**Users**: KToshiba

**What**: `/sys/devices/LNXSYSTM:00/LNXSYBUS:00/TOS{1900,620{0,7,8}}:00/sleep_functions_on_battery`

**Date**: January 23, 2015

**KernelVersion**: 4.0

**Contact**: Azael Avalos <coproscefalo@gmail.com>

**Description**: 此文件控制电池下的 USB 睡眠功能，并

设置将其禁用的级别，接受的值可以是：

* 0 -> 禁用

* 1-100 -> 禁用睡眠功能的电池电量

目前它打印两个值，第一个值表示该功能是启用还是禁用，

而第二个值显示当前设置的电池电量。

注意，当值设置为禁用时，睡眠功能仅在连接到电源时有效。

**Users**: KToshiba

**What**: `/sys/devices/LNXSYSTM:00/LNXSYBUS:00/TOS{1900,620{0,7,8}}:00/usb_rapid_charge`

**Date**: January 23, 2015

**KernelVersion**: 4.0

**Contact**: Azael Avalos <coproscefalo@gmail.com>

**Description**: 此文件控制 USB 快速充电状态，

可以是：

* 0 -> 禁用

* 1 -> 启用

注意，切换此值需要重新启动才能使更改生效。

**Users**: KToshiba

**What**: `/sys/devices/LNXSYSTM:00/LNXSYBUS:00/TOS{1900,620{0,7,8}}:00/usb_sleep_music`

**Date**: January 23, 2015

**KernelVersion**: 4.0

**Contact**: Azael Avalos <coproscefalo@gmail.com>

**Description**: 此文件控制睡眠与音乐状态，值可以是：

* 0 -> 禁用

* 1 -> 启用

注意，此功能仅在连接到电源时有效，如果

要在电池下使用它，请参阅名为“sleep_functions_on_battery”的条目

**Users**: KToshiba

**What**: `/sys/devices/LNXSYSTM:00/LNXSYBUS:00/TOS{1900,620{0,7,8}}:00/version`

**Date**: February 12, 2015

**KernelVersion**: 4.0

**Contact**: Azael Avalos <coproscefalo@gmail.com>

**Description**: 此文件显示驱动程序的当前版本

**Users**: KToshiba

**What**: `/sys/devices/LNXSYSTM:00/LNXSYBUS:00/TOS{1900,620{0,7,8}}:00/fan`

**Date**: February 12, 2015

**KernelVersion**: 4.0

**Contact**: Azael Avalos <coproscefalo@gmail.com>

**Description**: 此文件控制内部风扇的状态，有效

值为：

* 0 -> OFF

* 1 -> ON

**What**: `/sys/devices/LNXSYSTM:00/LNXSYBUS:00/TOS{1900,620{0,7,8}}:00/kbd_function_keys`

**Date**: February 12, 2015

**KernelVersion**: 4.0

**Contact**: Azael Avalos <coproscefalo@gmail.com>

**Description**: 此文件控制特殊功能（热键）操作

模式，有效值为：

* 0 -> 正常操作

* 1 -> 特殊功能

在“正常操作”模式下，F{1-12}键按常规使用，

热键通过 FN-F{1-12}访问。

在“特殊功能”模式下，F{1-12}键触发热键，

F{1-12}键通过 FN-F{1-12}访问。

注意，切换此值需要重新启动才能使更改生效。

**Users**: KToshiba

**What**: `/sys/devices/LNXSYSTM:00/LNXSYBUS:00/TOS{1900,620{0,7,8}}:00/panel_power_on`

**Date**: February 12, 2015

**KernelVersion**: 4.0

**Contact**: Azael Avalos <coproscefalo@gmail.com>

**Description**: 此文件控制笔记本电脑在盖子打开时是否应打开，有效

值为：

* 0 -> 禁用

* 1 -> 启用

注意，切换此值需要重新启动才能使更改生效。

**Users**: KToshiba

**What**: `/sys/devices/LNXSYSTM:00/LNXSYBUS:00/TOS{1900,620{0,7,8}}:00/usb_three`

**Date**: February 12, 2015

**KernelVersion**: 4.0

**Contact**: Azael Avalos <coproscefalo@gmail.com>

**Description**: 此文件控制 USB 3 功能，有效值为：

* 0 -> 禁用（充当常规 USB 2）

* 1 -> 启用（完整 USB 3 功能）

注意，切换此值需要重新启动才能使更改生效。

**Users**: KToshiba