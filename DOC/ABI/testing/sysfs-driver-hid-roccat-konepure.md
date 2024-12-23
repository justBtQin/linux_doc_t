**关于鼠标相关的 USB 设备信息：**

**设备路径及名称**：/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/konepure/roccatkonepure<minor>/actual_profile

**日期**：December 2012

**联系人**：Stefan Achatz <erazor_de@users.sourceforge.net>

**描述**：鼠标可存储 5 个配置文件，可通过按下一个按钮进行切换。actual_profile 保存实际配置文件的编号。此值是持久的，因此其值决定了下次鼠标通电时激活的配置文件。写入时，鼠标会立即激活设置的配置文件。数据必须为 3 字节长。鼠标将拒绝无效数据。

**用户**：http://roccat.sourceforge.net

**设备路径及名称**：/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/konepure/roccatkonepure<minor>/control

**日期**：December 2012

**联系人**：Stefan Achatz <erazor_de@users.sourceforge.net>

**描述**：写入时，此文件可让用户选择要读取的下一个配置文件的数据。数据必须为 3 字节长。此文件为只写文件。

**用户**：http://roccat.sourceforge.net

**设备路径及名称**：/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/konepure/roccatkonepure<minor>/info

**日期**：December 2012

**联系人**：Stefan Achatz <erazor_de@users.sourceforge.net>

**描述**：读取时，此文件返回诸如固件版本等通用数据。写入时，可重置设备。数据为 6 字节长。

**用户**：http://roccat.sourceforge.net

**设备路径及名称**：/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/konepure/roccatkonepure<minor>/macro

**日期**：December 2012

**联系人**：Stefan Achatz <erazor_de@users.sourceforge.net>

**描述**：鼠标可在内部存储最多 500 个按键/按钮操作的宏。写入时，此文件可让用户为特定配置文件中的特定按钮设置序列。按钮和配置文件编号包含在写入的数据中。数据必须为 2082 字节长。此文件为只写文件。

**用户**：http://roccat.sourceforge.net

**设备路径及名称**：/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/konepure/roccatkonepure<minor>/profile_buttons

**日期**：December 2012

**联系人**：Stefan Achatz <erazor_de@users.sourceforge.net>

**描述**：鼠标可存储 5 个配置文件，可通过按下一个按钮进行切换。一个配置文件分为设置和按钮。profile_buttons 保存按钮布局信息。写入时，此文件可让用户将相应的配置文件按钮写回鼠标。数据必须为 59 字节长。鼠标将拒绝无效数据。要写入的配置文件由数据中包含的配置文件编号确定。在读取此文件之前，必须先写入 control 以选择要读取的配置文件。

**用户**：http://roccat.sourceforge.net

**设备路径及名称**：/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/konepure/roccatkonepure<minor>/profile_settings

**日期**：December 2012

**联系人**：Stefan Achatz <erazor_de@users.sourceforge.net>

**描述**：鼠标可存储 5 个配置文件，可通过按下一个按钮进行切换。一个配置文件分为设置和按钮。profile_settings 保存诸如分辨率、灵敏度和灯光效果等信息。写入时，此文件可让用户将相应的配置文件设置写回鼠标。数据必须为 31 字节长。鼠标将拒绝无效数据。要写入的配置文件由数据中包含的配置文件编号确定。在读取此文件之前，必须先写入 control 以选择要读取的配置文件。

**用户**：http://roccat.sourceforge.net

**设备路径及名称**：/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/konepure/roccatkonepure<minor>/sensor

**日期**：December 2012

**联系人**：Stefan Achatz <erazor_de@users.sourceforge.net>

**描述**：鼠标具有跟踪和距离控制单元。这些单元可以激活/停用，并且可以设置抬起距离。数据必须为 6 字节长。此文件为只写文件。

**用户**：http://roccat.sourceforge.net

**设备路径及名称**：/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/konepure/roccatkonepure<minor>/talk

**日期**：December 2012

**联系人**：Stefan Achatz <erazor_de@users.sourceforge.net>

**描述**：用于从外部激活鼠标的一些简单功能。数据必须为 16 字节长。此文件为只写文件。

**用户**：http://roccat.sourceforge.net

**设备路径及名称**：/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/konepure/roccatkonepure<minor>/tcu

**日期**：December 2012

**联系人**：Stefan Achatz <erazor_de@users.sourceforge.net>

**描述**：写入时，可启动/取消跟踪控制单元的校准过程。也可让用户读取/写入传感器寄存器。数据必须为 4 字节长。

**用户**：http://roccat.sourceforge.net

**设备路径及名称**：/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/konepure/roccatkonepure<minor>/tcu_image

**日期**：December 2012

**联系人**：Stefan Achatz <erazor_de@users.sourceforge.net>

**描述**：读取时，鼠标返回一个 30x30 像素的采样地下图像。此功能仅在通过 tcu 启动的校准过程中有效。返回的数据大小为 1028 字节。此文件为只读文件。

**用户**：http://roccat.sourceforge.net