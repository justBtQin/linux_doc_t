**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/arvo/roccatarvo<minor>/actual_profile`

**Date**: Januar 2011

**Contact**: Stefan Achatz <erazor_de@users.sourceforge.net>

**Description**: 此属性的整数值范围从 1 到 5。读取时，此属性返回实际配置文件的编号，该编号也是设备启动时处于活动状态的配置文件。写入时，此属性立即激活所选配置文件。

**Users**: http://roccat.sourceforge.net

**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/arvo/roccatarvo<minor>/button`

**Date**: Januar 2011

**Contact**: Stefan Achatz <erazor_de@users.sourceforge.net>

**Description**: 键盘可以在内部存储由 1 个按钮和多个修饰键组成的短宏。写入时，此文件可让用户为特定配置文件中的特定按钮设置序列。写入数据中包含按钮和配置文件编号。数据必须为 24 字节长。此文件为只写文件。

**Users**: http://roccat.sourceforge.net

**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/arvo/roccatarvo<minor>/info`

**Date**: Januar 2011

**Contact**: Stefan Achatz <erazor_de@users.sourceforge.net>

**Description**: 读取时，此文件返回有关设备的一些信息，如安装的固件版本。数据大小为 8 字节。此文件为只读文件。

**Users**: http://roccat.sourceforge.net

**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/arvo/roccatarvo<minor>/key_mask`

**Date**: Januar 2011

**Contact**: Stefan Achatz <erazor_de@users.sourceforge.net>

**Description**: 键盘允许用户停用 5 个特定键，如 Windows 和应用程序键，以保护用户免受意外按下这些键的影响。此属性的整数值根据相应键的状态设置位 0-4。读取时，此文件返回按钮的当前状态。写入时，立即激活/停用给定的按钮。

**Users**: http://roccat.sourceforge.net

**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/arvo/roccatarvo<minor>/mode_key`

**Date**: Januar 2011

**Contact**: Stefan Achatz <erazor_de@users.sourceforge.net>

**Description**: 键盘具有不带数字锁定键的精简布局。取而代之的是，它使用模式键来激活一种游戏模式，在该模式下，数字块的分配会发生变化。此属性的整数值范围从 0（关闭）到 1（打开）。读取时，此文件返回键的实际状态。写入时，立即激活/停用该键。

**Users**: http://roccat.sourceforge.net