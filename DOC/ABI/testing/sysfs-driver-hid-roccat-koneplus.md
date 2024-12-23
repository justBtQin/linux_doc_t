**关于 /sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/koneplus/roccatkoneplus<minor>/actual_profile**

- **What**：/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/koneplus/roccatkoneplus<minor>/actual_profile

- **Date**：October 2010

- **Contact**：Stefan Achatz <erazor_de@users.sourceforge.net>

- **Description**：此属性的整数值范围从 0 到 4。读取时，此属性返回实际配置文件的编号。此值是持久的，因此它相当于下次鼠标通电时处于活动状态的配置文件。写入时，此文件设置启动配置文件的编号，鼠标立即激活此配置文件。

- **Users**：http://roccat.sourceforge.net

**关于 /sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/koneplus/roccatkoneplus<minor>/info**

- **What**：/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/koneplus/roccatkoneplus<minor>/info

- **Date**：November 2012

- **Contact**：Stefan Achatz <erazor_de@users.sourceforge.net>

- **Description**：读取时，此文件返回诸如固件版本之类的常规数据。写入时，设备可以重置。数据为 8 字节长。

- **Users**：http://roccat.sourceforge.net

**关于 /sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/koneplus/roccatkoneplus<minor>/macro**

- **What**：/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/koneplus/roccatkoneplus<minor>/macro

- **Date**：October 2010

- **Contact**：Stefan Achatz <erazor_de@users.sourceforge.net>

- **Description**：鼠标可以在内部存储一个最多 500 个键/按钮操作的宏。写入时，此文件允许为特定配置文件的特定按钮设置序列。写入数据中包含按钮和配置文件编号。数据必须为 2082 字节长。此文件为只写文件。

- **Users**：http://roccat.sourceforge.net

**关于 /sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/koneplus/roccatkoneplus<minor>/profile_buttons**

- **What**：/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/koneplus/roccatkoneplus<minor>/profile_buttons

- **Date**：August 2010

- **Contact**：Stefan Achatz <erazor_de@users.sourceforge.net>

- **Description**：鼠标可以存储 5 个配置文件，这些配置文件可以通过按下一个按钮进行切换。一个配置文件分为设置和按钮。profile_buttons 保存有关按钮布局的信息。写入时，此文件允许将相应的配置文件按钮写回鼠标。数据必须为 77 字节长。鼠标将拒绝无效数据。要写入的配置文件由数据中包含的配置文件编号确定。在读取此文件之前，必须先写入控制以选择要读取的配置文件。

- **Users**：http://roccat.sourceforge.net

**关于 /sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/koneplus/roccatkoneplus<minor>/profile_settings**

- **What**：/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/koneplus/roccatkoneplus<minor>/profile_settings

- **Date**：October 2010

- **Contact**：Stefan Achatz <erazor_de@users.sourceforge.net>

- **Description**：鼠标可以存储 5 个配置文件，这些配置文件可以通过按下一个按钮进行切换。一个配置文件分为设置和按钮。profile_settings 保存诸如分辨率、灵敏度和灯光效果等信息。写入时，此文件允许将相应的配置文件设置写回鼠标。数据必须为 43 字节长。鼠标将拒绝无效数据。要写入的配置文件由数据中包含的配置文件编号确定。在读取此文件之前，必须先写入控制以选择要读取的配置文件。

- **Users**：http://roccat.sourceforge.net

**关于 /sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/koneplus/roccatkoneplus<minor>/sensor**

- **What**：/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/koneplus/roccatkoneplus<minor>/sensor

- **Date**：October 2010

- **Contact**：Stefan Achatz <erazor_de@users.sourceforge.net>

- **Description**：鼠标具有跟踪和距离控制单元。这些可以被激活/停用，并且可以设置抬起距离。数据必须为 6 字节长。此文件为只写文件。

- **Users**：http://roccat.sourceforge.net

**关于 /sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/koneplus/roccatkoneplus<minor>/talk**

- **What**：/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/koneplus/roccatkoneplus<minor>/talk

- **Date**：May 2011

- **Contact**：Stefan Achatz <erazor_de@users.sourceforge.net>

- **Description**：用于从外部激活鼠标的一些简单功能。数据必须为 16 字节长。此文件为只写文件。

- **Users**：http://roccat.sourceforge.net

**关于 /sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/koneplus/roccatkoneplus<minor>/tcu**

- **What**：/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/koneplus/roccatkoneplus<minor>/tcu

- **Date**：October 2010

- **Contact**：Stefan Achatz <erazor_de@users.sourceforge.net>

- **Description**：写入时，可以启动/取消跟踪控制单元的校准过程。还可以读取/写入传感器寄存器。数据必须为 4 字节长。

- **Users**：http://roccat.sourceforge.net

**关于 /sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/koneplus/roccatkoneplus<minor>/tcu_image**

- **What**：/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/koneplus/roccatkoneplus<minor>/tcu_image

- **Date**：October 2010

- **Contact**：Stefan Achatz <erazor_de@users.sourceforge.net>

- **Description**：读取时，鼠标返回一个 30x30 像素的采样地下图像。此功能仅在通过 tcu 启动的校准过程中有效。返回的数据大小为 1028 字节。此文件为只读文件。

- **Users**：http://roccat.sourceforge.net