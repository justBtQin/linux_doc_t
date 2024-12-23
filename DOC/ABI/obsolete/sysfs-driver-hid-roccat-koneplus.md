**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/koneplus/roccatkoneplus<minor>/startup_profile`

**Date**: October 2010

**Contact**: Stefan Achatz <erazor_de@users.sourceforge.net>

**Description**: 此属性的整数值范围从 0 到 4。读取时，此属性返回实际配置文件的编号。此值是持久的，因此它等效于下次鼠标通电时处于活动状态的配置文件。写入时，此文件设置启动配置文件的编号，鼠标会立即激活此配置文件。请使用 actual_profile，它执行相同的操作。

**Users**: http://roccat.sourceforge.net

**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/koneplus/roccatkoneplus<minor>/firmware_version`

**Date**: October 2010

**Contact**: Stefan Achatz <erazor_de@users.sourceforge.net>

**Description**: 读取时，此文件返回鼠标报告的固件的原始整数值版本号。使用整数值便于在其他程序中进一步使用。要接收实际版本号，需将小数点向左移动 2 位。例如，返回值 121 表示 1.21。此文件为只读。请读取包含固件版本的二进制属性信息。

**Users**: http://roccat.sourceforge.net

**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/koneplus/roccatkoneplus<minor>/profile[1-5]_buttons`

**Date**: August 2010

**Contact**: Stefan Achatz <erazor_de@users.sourceforge.net>

**Description**: 鼠标可以存储 5 个配置文件，可以通过按下一个按钮进行切换。一个配置文件分为设置和按钮。profile_buttons 保存有关按钮布局的信息。读取时，这些文件返回相应的配置文件按钮。返回的数据大小为 77 字节。此文件为只读。写入控制以选择配置文件，然后读取 profile_buttons 代替。

**Users**: http://roccat.sourceforge.net

**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/koneplus/roccatkoneplus<minor>/profile[1-5]_settings`

**Date**: August 2010

**Contact**: Stefan Achatz <erazor_de@users.sourceforge.net>

**Description**: 鼠标可以存储 5 个配置文件，可以通过按下一个按钮进行切换。一个配置文件分为设置和按钮。profile_settings 保存诸如分辨率、灵敏度和灯光效果等信息。读取时，这些文件返回相应的配置文件设置。返回的数据大小为 43 字节。此文件为只读。写入控制以选择配置文件，然后读取 profile_settings 代替。

**Users**: http://roccat.sourceforge.net