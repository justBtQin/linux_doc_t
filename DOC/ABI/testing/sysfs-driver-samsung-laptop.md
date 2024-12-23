**What**: `/sys/devices/platform/samsung/performance_level`

**Date**: January 1, 2010

**KernelVersion**: 2.6.33

**Contact**: Greg Kroah-Hartman <gregkh@linuxfoundation.org>

**Description**: 某些三星笔记本电脑具有不同的“性能级别”，可以通过功能键以及此 sysfs 文件进行修改。这些值并不总是很有意义，但一些用户喜欢修改它们，不惜一切代价保持风扇安静。读取此文件将显示当前的性能级别。写入此文件可以更改此值。有效选项：

- “silent”

- “normal”

- “overclock”

注意，并非所有笔记本电脑都支持所有这些选项。具体而言，并非所有笔记本电脑都支持“overclock”选项，并且尚不清楚此值是否会更改任何内容，除了让用户感觉更好之外。

**What**: `/sys/devices/platform/samsung/battery_life_extender`

**Date**: December 1, 2011

**KernelVersion**: 3.3

**Contact**: Corentin Chary <corentin.chary@gmail.com>

**Description**: 最大电池充电水平可以修改，通过降低最大电池充电水平可以延长电池循环寿命。0 表示正常电池模式（100％充电），1 表示电池寿命扩展模式（80％充电）。

**What**: `/sys/devices/platform/samsung/usb_charge`

**Date**: December 1, 2011

**KernelVersion**: 3.3

**Contact**: Corentin Chary <corentin.chary@gmail.com>

**Description**: 即使笔记本电脑已关闭，也可以使用 USB 端口为设备充电。1 表示启用，0 表示禁用。

**What**: `/sys/devices/platform/samsung/lid_handling`

**Date**: December 11, 2014

**KernelVersion**: 3.19

**Contact**: Julijonas Kikutis <julijonas.kikutis@gmail.com>

**Description**: 一些三星笔记本电脑处理盖子关闭的速度更快，并且只有在此模式启用时才处理盖子打开。1 表示启用，0 表示禁用。