**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/pyra/roccatpyra<minor>/info`

**Date**: November 2012

**Contact**: Stefan Achatz <erazor_de@users.sourceforge.net>

**Description**: 读取时，此文件返回诸如固件版本等通用数据。写入时，可重置设备。数据为 6 字节长。

**Users**: http://roccat.sourceforge.net

**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/pyra/roccatpyra<minor>/profile_settings`

**Date**: August 2010

**Contact**: Stefan Achatz <erazor_de@users.sourceforge.net>

**Description**: 鼠标可存储 5 个配置文件，可通过按下按钮进行切换。一个配置文件分为设置和按钮。`profile_settings`保存诸如分辨率、灵敏度和灯光效果等信息。写入时，此文件可将相应的配置文件设置写回鼠标。数据必须为 13 字节长。鼠标将拒绝无效数据。要写入的配置文件由数据中包含的配置文件编号确定。读取此文件之前，必须先写入控制以选择要读取的配置文件。

**Users**: http://roccat.sourceforge.net

**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/pyra/roccatpyra<minor>/profile_buttons`

**Date**: August 2010

**Contact**: Stefan Achatz <erazor_de@users.sourceforge.net>

**Description**: 鼠标可存储 5 个配置文件，可通过按下按钮进行切换。一个配置文件分为设置和按钮。`profile_buttons`保存关于按钮布局的信息。写入时，此文件可将相应的配置文件按钮写回鼠标。数据必须为 19 字节长。鼠标将拒绝无效数据。要写入的配置文件由数据中包含的配置文件编号确定。读取此文件之前，必须先写入控制以选择要读取的配置文件。

**Users**: http://roccat.sourceforge.net

**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/pyra/roccatpyra<minor>/settings`

**Date**: August 2010

**Contact**: Stefan Achatz <erazor_de@users.sourceforge.net>

**Description**: 读取时，此文件返回鼠标中存储的设置。数据大小为 3 字节，包含关于启动配置文件的信息。写入时，此文件可将设置写回鼠标。数据必须为 3 字节长。鼠标将拒绝无效数据。

**Users**: http://roccat.sourceforge.net