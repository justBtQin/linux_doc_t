**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/ryos/roccatryos<minor>/control`

**Date：** 十月 2013

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 写入此文件可让用户选择下一次要读取的特定配置文件中的数据。数据必须为 3 字节长。此文件为只写文件。

**Users：** http://roccat.sourceforge.net

**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/ryos/roccatryos<minor>/profile`

**Date：** 十月 2013

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 鼠标可存储 5 个配置文件，可通过按下一个按钮进行切换。`profile`保存实际配置文件的索引。此值是持久的，因此其值决定了下次设备通电时的活动配置文件。写入此文件，设备将立即激活设置的配置文件。数据必须为 3 字节长。设备将拒绝无效数据。

**Users：** http://roccat.sourceforge.net

**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/ryos/roccatryos<minor>/keys_primary`

**Date：** 十月 2013

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 写入此文件可让用户为特定配置文件设置所有键的默认值。写入数据中包含配置文件索引。数据必须为 125 字节长。在读取此文件之前，必须先写入`control`以选择要读取的配置文件。

**Users：** http://roccat.sourceforge.net

**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/ryos/roccatryos<minor>/keys_function`

**Date：** 十月 2013

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 写入此文件可让用户为特定配置文件设置功能键的功能。写入数据中包含配置文件索引。数据必须为 95 字节长。在读取此文件之前，必须先写入`control`以选择要读取的配置文件。

**Users：** http://roccat.sourceforge.net

**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/ryos/roccatryos<minor>/keys_macro`

**Date：** 十月 2013

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 写入此文件可让用户为特定配置文件设置宏键的功能。写入数据中包含配置文件索引。数据必须为 35 字节长。在读取此文件之前，必须先写入`control`以选择要读取的配置文件。

**Users：** http://roccat.sourceforge.net

**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/ryos/roccatryos<minor>/keys_thumbster`

**Date：** 十月 2013

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 写入此文件可让用户为特定配置文件设置拇指键的功能。写入数据中包含配置文件索引。数据必须为 23 字节长。在读取此文件之前，必须先写入`control`以选择要读取的配置文件。

**Users：** http://roccat.sourceforge.net

**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/ryos/roccatryos<minor>/keys_extra`

**Date：** 十月 2013

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 写入此文件可让用户为特定配置文件设置大小写锁定键和功能键的功能。写入数据中包含配置文件索引。数据必须为 8 字节长。在读取此文件之前，必须先写入`control`以选择要读取的配置文件。

**Users：** http://roccat.sourceforge.net

**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/ryos/roccatryos<minor>/keys_easyzone`

**Date：** 十月 2013

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 写入此文件可让用户为特定配置文件设置易区键的功能。写入数据中包含配置文件索引。数据必须为 294 字节长。在读取此文件之前，必须先写入`control`以选择要读取的配置文件。

**Users：** http://roccat.sourceforge.net

**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/ryos/roccatryos<minor>/key_mask`

**Date：** 十月 2013

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 写入此文件可让用户禁用某些键，如 Windows 和应用程序键，以防止意外按下。写入数据中包含此设置的配置文件索引。数据必须为 6 字节长。在读取此文件之前，必须先写入`control`以选择要读取的配置文件。

**Users：** http://roccat.sourceforge.net

**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/ryos/roccatryos<minor>/light`

**Date：** 十月 2013

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 写入此文件可让用户为特定配置文件设置背光强度。写入数据中包含配置文件索引。此属性仅对 glow 和 pro 变体有效。数据必须为 16 字节长。在读取此文件之前，必须先写入`control`以选择要读取的配置文件。

**Users：** http://roccat.sourceforge.net

**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/ryos/roccatryos<minor>/macro`

**Date：** 十月 2013

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 写入此文件可让用户为特定按钮和特定配置文件存储最多 480 个击键的宏。写入数据中包含按钮和配置文件索引。数据必须为 2002 字节长。在读取此文件之前，必须先写入`control`以选择要读取的配置文件和键。

**Users：** http://roccat.sourceforge.net

**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/ryos/roccatryos<minor>/info`

**Date：** 十月 2013

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 读取此文件将返回通用数据，如固件版本。数据为 8 字节长。此文件为只读文件。

**Users：** http://roccat.sourceforge.net

**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/ryos/roccatryos<minor>/reset`

**Date：** 十月 2013

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 写入此文件可让用户重置设备。数据必须为 3 字节长。此文件为只写文件。

**Users：** http://roccat.sourceforge.net

**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/ryos/roccatryos<minor>/talk`

**Date：** 十月 2013

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 写入此文件可让用户从主机触发易切换功能。数据必须为 16 字节长。此文件为只写文件。

**Users：** http://roccat.sourceforge.net

**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/ryos/roccatryos<minor>/light_control`

**Date：** 十月 2013

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 写入此文件可让用户在存储的和自定义的灯光设置之间切换。此属性仅对 pro 变体有效。数据必须为 8 字节长。此文件为只写文件。用户：http://roccat.sourceforge.net

内容：

- 什么：/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/ryos/roccatryos<minor>/stored_lights

- 日期：2013 年 10 月

- 联系人：Stefan Achatz <erazor_de@users.sourceforge.net>

- 描述：写入此文件可设置不同层的每键灯光。此属性仅对专业版变体有效。数据必须为 1382 字节长。在读取此文件之前，必须写入控制以选择要读取的配置文件。

- 什么：/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/ryos/roccatryos<minor>/custom_lights

- 日期：2013 年 10 月

- 联系人：Stefan Achatz <erazor_de@users.sourceforge.net>

- 描述：写入此文件可设置实际的每键灯光。此属性仅对专业版变体有效。数据必须为 20 字节长。此文件为只写文件。

- 什么：/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/ryos/roccatryos<minor>/light_macro

- 日期：2013 年 10 月

- 联系人：Stefan Achatz <erazor_de@users.sourceforge.net>

- 描述：写入此文件可设置一个灯光宏，每当设备进入昏暗模式时就会循环。此属性仅对专业版变体有效。数据必须为 2002 字节长。在读取此文件之前，必须写入控制以选择要读取的配置文件。