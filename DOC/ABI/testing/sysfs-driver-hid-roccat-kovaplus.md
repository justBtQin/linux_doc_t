**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/kovaplus/roccatkovaplus<minor>/actual_profile`

**Date**: January 2011

**Contact**: Stefan Achatz <erazor_de@users.sourceforge.net>

**Description**: 此属性的整数值范围从 0 到 4。读取时，此属性返回活动配置文件的编号。写入时，鼠标立即激活此配置文件。断电时活动的配置文件与鼠标通电时活动的配置文件相同。

**Users**: http://roccat.sourceforge.net

**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/kovaplus/roccatkovaplus<minor>/info`

**Date**: November 2012

**Contact**: Stefan Achatz <erazor_de@users.sourceforge.net>

**Description**: 读取时，此文件返回诸如固件版本等常规数据。写入时，设备可以重置。数据为 6 字节长。

**Users**: http://roccat.sourceforge.net

**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/kovaplus/roccatkovaplus<minor>/profile_buttons`

**Date**: January 2011

**Contact**: Stefan Achatz <erazor_de@users.sourceforge.net>

**Description**: 鼠标可以存储 5 个配置文件，通过按下一个按钮可以切换。一个配置文件分为设置和按钮。`profile_buttons`保存有关按钮布局的信息。写入时，此文件允许将相应的配置文件按钮写回鼠标。数据必须为 23 字节长。鼠标将拒绝无效数据。要写入的配置文件由数据中包含的配置文件编号确定。在读取此文件之前，必须先写入控制以选择要读取的配置文件。

**Users**: http://roccat.sourceforge.net

**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/kovaplus/roccatkovaplus<minor>/profile_settings`

**Date**: January 2011

**Contact**: Stefan Achatz <erazor_de@users.sourceforge.net>

**Description**: 鼠标可以存储 5 个配置文件，通过按下一个按钮可以切换。一个配置文件分为设置和按钮。`profile_settings`保存诸如分辨率、灵敏度和灯光效果等信息。写入时，此文件允许将相应的配置文件设置写回鼠标。数据必须为 16 字节长。鼠标将拒绝无效数据。要写入的配置文件由数据中包含的配置文件编号确定。在读取此文件之前，必须先写入控制以选择要读取的配置文件。

**Users**: http://roccat.sourceforge.net