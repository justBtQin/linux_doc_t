**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/operation_mode`

**Date**: March 2010

**Contact**: Bruno Prémont <bonbons@linux-vserver.org>

**Description**: 使能够在 LCD（固件）和引导加载程序（闪存器）操作模式之间切换 PicoLCD 设备。

读取：返回可用模式的列表，活动模式用括号（'[' 和 ']'）括起来。

写入：导致操作模式切换。允许的值是读取时列出的非活动模式名称。

注意：切换模式时，当前的 PicoLCD HID 设备会断开连接，并在上述延迟后重新连接（请参阅属性 operation_mode_delay 以获取其值）。

**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/operation_mode_delay`

**Date**: April 2010

**Contact**: Bruno Prémont <bonbons@linux-vserver.org>

**Description**: 当 operation_mode 更改时，PicoLCD 在新模式下重新启动之前等待的延迟。

读取/写入：以毫秒表示，允许的范围是 0..30000ms。

**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/fb_update_rate`

**Date**: March 2010

**Contact**: Bruno Prémont <bonbons@linux-vserver.org>

**Description**: 使能够调整 defio 刷新率。

读取：返回可用刷新率的列表（以 Hz 表示），活动刷新率用括号（'[' 和 ']'）括起来。

写入：接受以整数 Hz 表示的新刷新率，在允许的速率范围内。

注意：由于设备每秒几乎只能完成 2 次完整刷新，因此只有在只有一个或两个磁贴发生更改且不适合期望应用程序以高于默认速率显式刷新其微小更改的情况下，才应调整此值。