**Where**: /sys/bus/usb/.../powered

**Date**: August 2008

**Kernel Version**: 2.6.26

**Contact**: Harrison Metzger <harrisonmetz@gmail.com>

**Description**: 控制设备的显示屏是否通电。值为 0 表示关闭，非零值表示打开。

**Where**: /sys/bus/usb/.../mode_msb

**Where**: /sys/bus/usb/.../mode_lsb

**Date**: August 2008

**Kernel Version**: 2.6.26

**Contact**: Harrison Metzger <harrisonmetz@gmail.com>

**Description**: 控制设备的显示模式。对于 6 位显示屏，值为

MSB 0x06；LSB 0x3F，对于 8 位显示屏，值为

MSB 0x08；LSB 0xFF。

**Where**: /sys/bus/usb/.../textmode

**Date**: August 2008

**Kernel Version**: 2.6.26

**Contact**: Harrison Metzger <harrisonmetz@gmail.com>

**Description**: 控制设备解释其文本缓冲区的方式。

raw：每个字符手动控制其段

hex：每个字符在 0-15 之间

ascii：每个字符在 '0'-'9' 和 'A'-'F' 之间。

**Where**: /sys/bus/usb/.../text

**Date**: August 2008

**Kernel Version**: 2.6.26

**Contact**: Harrison Metzger <harrisonmetz@gmail.com>

**Description**: 设备要显示的文本（或数据）

**Where**: /sys/bus/usb/.../decimals

**Date**: August 2008

**Kernel Version**: 2.6.26

**Contact**: Harrison Metzger <harrisonmetz@gmail.com>

**Description**: 控制设备上的小数位数。要设置第 n 位小数，给该字段

赋予 10 ** n 的值。假设该字段的值为 k 且已设置 1 位或多位小数，

要设置第 m 位（其中 m 尚未设置），将此字段的值更改为 k + 10 ** m。