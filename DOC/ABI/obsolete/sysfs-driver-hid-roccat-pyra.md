**What: /sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/pyra/roccatpyra<minor>/actual_cpi**

**Date: August 2010**

**Contact: Stefan Achatz <erazor_de@users.sourceforge.net>**

**Description: 可以通过按下一个按钮来切换鼠标的 CPI 设置。读取此文件时，它返回鼠标报告的实际 CPI 的原始数字。此数字必须进一步处理才能获得实际的 DPI 值。**

**VALUE DPI**

**1     400**

**2     800**

**4     1600**

**此文件为只读。从未被使用过。如果进行记录，是在用户空间工具中进行的。**

**What: /sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/pyra/roccatpyra<minor>/actual_profile**

**Date: August 2010**

**Contact: Stefan Achatz <erazor_de@users.sourceforge.net>**

**Description: 读取此文件时，它返回实际配置文件的编号，范围为 0-4。此文件为只读。请使用提供此信息的二进制属性“settings”。**

**What: /sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/pyra/roccatpyra<minor>/firmware_version**

**Date: August 2010**

**Contact: Stefan Achatz <erazor_de@users.sourceforge.net>**

**Description: 读取此文件时，它返回鼠标报告的固件的原始整数版本号。使用整数值便于在其他程序中进一步使用。要获得实际版本号，需将小数点向左移动 2 位。例如，返回值 138 表示 1.38。此文件为只读。请使用提供此信息的二进制属性“info”。**

**What: /sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/pyra/roccatpyra<minor>/profile[1-5]_buttons**

**Date: August 2010**

**Contact: Stefan Achatz <erazor_de@users.sourceforge.net>**

**Description: 鼠标可以存储 5 个配置文件，可以通过按下一个按钮进行切换。一个配置文件分为设置和按钮。profile_buttons 保存有关按钮布局的信息。读取这些文件时，它们返回相应的配置文件按钮。返回的数据大小为 19 字节。此文件为只读。写入控制以选择配置文件，然后读取 profile_buttons。**

**What: /sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/pyra/roccatpyra<minor>/profile[1-5]_settings**

**Date: August 2010**

**Contact: Stefan Achatz <erazor_de@users.sourceforge.net>**

**Description: 鼠标可以存储 5 个配置文件，可以通过按下一个按钮进行切换。一个配置文件分为设置和按钮。profile_settings 保存诸如分辨率、灵敏度和灯光效果等信息。读取这些文件时，它们返回相应的配置文件设置。返回的数据大小为 13 字节。此文件为只读。写入控制以选择配置文件，然后读取 profile_settings。**

**What: /sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/pyra/roccatpyra<minor>/startup_profile**

**Date: August 2010**

**Contact: Stefan Achatz <erazor_de@users.sourceforge.net>**

**Description: 此属性的整数值范围从 0 到 4。读取此属性时，它返回鼠标通电时处于活动状态的配置文件的编号。此文件为只读。请使用提供此信息的二进制属性“settings”。**