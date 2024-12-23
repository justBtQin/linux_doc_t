**关于 /sys/bus/usb/devices/ 下的设备文件说明：**

**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/savu/roccatsavu<minor>/buttons`

**Date：** 2012 年 5 月

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 鼠标可以存储 5 个配置文件，通过按下一个按钮可以进行切换。一个配置文件分为通用设置和按钮设置。`buttons` 保存有关按钮布局的信息。写入此文件可将相应的配置文件按钮写入鼠标。数据必须为 47 字节长。鼠标将拒绝无效数据。要写入的配置文件由数据中包含的配置文件编号确定。

**Users：** http://roccat.sourceforge.net

**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/savu/roccatsavu<minor>/control`

**Date：** 2012 年 5 月

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 写入此文件可选择要读取的下一个配置文件中的数据。数据必须为 3 字节长。此文件为只写文件。

**Users：** http://roccat.sourceforge.net

**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/savu/roccatsavu<minor>/general`

**Date：** 2012 年 5 月

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 鼠标可以存储 5 个配置文件，通过按下一个按钮可以进行切换。一个配置文件分为通用设置和按钮设置。`profile` 保存诸如分辨率、灵敏度和灯光效果等信息。写入此文件可将相应的配置文件设置写回鼠标。数据必须为 43 字节长。鼠标将拒绝无效数据。要写入的配置文件由数据中包含的配置文件编号确定。此文件为只写文件。

**Users：** http://roccat.sourceforge.net

**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/savu/roccatsavu<minor>/info`

**Date：** 2012 年 5 月

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 读取此文件将返回诸如固件版本等通用数据。写入此文件可重置设备。数据为 8 字节长。

**Users：** http://roccat.sourceforge.net

**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/savu/roccatsavu<minor>/macro`

**Date：** 2012 年 5 月

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 写入此文件可针对特定按钮和特定配置文件存储最多 500 个按键操作的宏。按钮和配置文件编号包含在写入的数据中。数据必须为 2083 字节长。在读取此文件之前，必须写入控制以选择要读取的配置文件和按键。

**Users：** http://roccat.sourceforge.net

**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/savu/roccatsavu<minor>/profile`

**Date：** 2012 年 5 月

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 鼠标可以存储 5 个配置文件，通过按下一个按钮可以进行切换。`profile` 保存实际配置文件的编号。此值是持久的，因此其值确定下次鼠标通电时激活的配置文件。写入此文件，鼠标将立即激活设置的配置文件。数据必须为 3 字节长。鼠标将拒绝无效数据。

**Users：** http://roccat.sourceforge.net

**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/savu/roccatsavu<minor>/sensor`

**Date：** 2012 年 7 月

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 鼠标具有 Avago ADNS-3090 传感器。此文件允许读取和写入鼠标传感器寄存器。数据必须为 4 字节长。

**Users：** http://roccat.sourceforge.net