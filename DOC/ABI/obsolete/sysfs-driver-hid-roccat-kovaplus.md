**设备属性信息**

- **/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/kovaplus/roccatkovaplus<minor>/actual_cpi**

    - **日期**：January 2011

    - **联系人**：Stefan Achatz <erazor_de@users.sourceforge.net>

    - **描述**：此属性的整数值范围从 1 到 4。读取时，此属性返回活动的 CPI 级别编号。此文件为只读。从未使用过。如果进行记账，是在用户空间工具中完成。

    - **用户**：http://roccat.sourceforge.net

- **/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/kovaplus/roccatkovaplus<minor>/actual_sensitivity_x**

    - **日期**：January 2011

    - **联系人**：Stefan Achatz <erazor_de@users.sourceforge.net>

    - **描述**：此属性的整数值范围从 1 到 10。读取时，此属性返回 x 方向的实际灵敏度编号。此文件为只读。从未使用过。如果进行记账，是在用户空间工具中完成。

    - **用户**：http://roccat.sourceforge.net

- **/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/kovaplus/roccatkovaplus<minor>/actual_sensitivity_y**

    - **日期**：January 2011

    - **联系人**：Stefan Achatz <erazor_de@users.sourceforge.net>

    - **描述**：此属性的整数值范围从 1 到 10。读取时，此属性返回 y 方向的实际灵敏度编号。此文件为只读。从未使用过。如果进行记账，是在用户空间工具中完成。

    - **用户**：http://roccat.sourceforge.net

- **/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/kovaplus/roccatkovaplus<minor>/firmware_version**

    - **日期**：January 2011

    - **联系人**：Stefan Achatz <erazor_de@users.sourceforge.net>

    - **描述**：读取时，此文件返回鼠标报告的固件的原始整数值版本号。使用整数值便于在其他程序中进一步使用。要接收实际版本号，需将小数点向左移动 2 位。例如，返回值 121 表示 1.21。此文件为只读。已被二进制 sysfs 属性“info”取代。

    - **用户**：http://roccat.sourceforge.net

- **/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/kovaplus/roccatkovaplus<minor>/profile[1-5]_buttons**

    - **日期**：January 2011

    - **联系人**：Stefan Achatz <erazor_de@users.sourceforge.net>

    - **描述**：鼠标可以存储 5 个配置文件，可通过按下一个按钮进行切换。一个配置文件分为设置和按钮。profile_buttons 保存有关按钮布局的信息。读取时，这些文件返回相应的配置文件按钮。返回的数据大小为 23 字节。此文件为只读。写入控制以选择配置文件并读取 profile_buttons 代替。

    - **用户**：http://roccat.sourceforge.net

- **/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/kovaplus/roccatkovaplus<minor>/profile[1-5]_settings**

    - **日期**：January 2011

    - **联系人**：Stefan Achatz <erazor_de@users.sourceforge.net>

    - **描述**：鼠标可以存储 5 个配置文件，可通过按下一个按钮进行切换。一个配置文件分为设置和按钮。profile_settings 保存诸如分辨率、灵敏度和灯光效果等信息。读取时，这些文件返回相应的配置文件设置。返回的数据大小为 16 字节。此文件为只读。写入控制以选择配置文件并读取 profile_settings 代替。

    - **用户**：http://roccat.sourceforge.net