**设备属性相关信息：**

**什么（What）：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/isku/roccatisku<minor>/actual_profile`

**日期（Date）：** 六月 2011 年

**联系人（Contact）：** Stefan Achatz <erazor_de@users.sourceforge.net>

**描述（Description）：** 此属性的整数值范围从 0 到 4。读取时，此属性返回实际配置文件的编号。此值是持久的，因此它等效于设备下次通电时处于活动状态的配置文件。写入时，此文件设置启动配置文件的编号，设备立即激活此配置文件。

**用户（Users）：** http://roccat.sourceforge.net

**什么（What）：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/isku/roccatisku<minor>/info`

**日期（Date）：** 六月 2011 年

**联系人（Contact）：** Stefan Achatz <erazor_de@users.sourceforge.net>

**描述（Description）：** 读取时，此文件返回诸如固件版本之类的常规数据。数据长度为 6 字节。此文件为只读。

**用户（Users）：** http://roccat.sourceforge.net

**什么（What）：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/isku/roccatisku<minor>/key_mask`

**日期（Date）：** 六月 2011 年

**联系人（Contact）：** Stefan Achatz <erazor_de@users.sourceforge.net>

**描述（Description）：** 写入时，此文件允许禁用某些键，如窗口键和应用程序键，以防止意外按下。此设置发生的配置文件编号包含在写入的数据中。数据必须为 6 字节长。在读取此文件之前，必须先写入控制以选择要读取的配置文件。

**用户（Users）：** http://roccat.sourceforge.net

**什么（What）：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/isku/roccatisku<minor>/keys_capslock`

**日期（Date）：** 六月 2011 年

**联系人（Contact）：** Stefan Achatz <erazor_de@users.sourceforge.net>

**描述（Description）：** 写入时，此文件允许为特定配置文件设置大写锁定键的功能。配置文件编号包含在写入的数据中。数据必须为 6 字节长。在读取此文件之前，必须先写入控制以选择要读取的配置文件。

**用户（Users）：** http://roccat.sourceforge.net

**什么（What）：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/isku/roccatisku<minor>/keys_easyzone`

**日期（Date）：** 六月 2011 年

**联系人（Contact）：** Stefan Achatz <erazor_de@users.sourceforge.net>

**描述（Description）：** 写入时，此文件允许为特定配置文件设置轻松区键的功能。配置文件编号包含在写入的数据中。数据必须为 65 字节长。在读取此文件之前，必须先写入控制以选择要读取的配置文件。

**用户（Users）：** http://roccat.sourceforge.net

**什么（What）：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/isku/roccatisku<minor>/keys_function`

**日期（Date）：** 六月 2011 年

**联系人（Contact）：** Stefan Achatz <erazor_de@users.sourceforge.net>

**描述（Description）：** 写入时，此文件允许为特定配置文件设置功能键的功能。配置文件编号包含在写入的数据中。数据必须为 41 字节长。在读取此文件之前，必须先写入控制以选择要读取的配置文件。

**用户（Users）：** http://roccat.sourceforge.net

**什么（What）：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/isku/roccatisku<minor>/keys_macro`

**日期（Date）：** 六月 2011 年

**联系人（Contact）：** Stefan Achatz <erazor_de@users.sourceforge.net>

**描述（Description）：** 写入时，此文件允许为特定配置文件设置宏键的功能。配置文件编号包含在写入的数据中。数据必须为 35 字节长。在读取此文件之前，必须先写入控制以选择要读取的配置文件。

**用户（Users）：** http://roccat.sourceforge.net

**什么（What）：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/isku/roccatisku<minor>/keys_media`

**日期（Date）：** 六月 2011 年

**联系人（Contact）：** Stefan Achatz <erazor_de@users.sourceforge.net>

**描述（Description）：** 写入时，此文件允许为特定配置文件设置媒体键的功能。配置文件编号包含在写入的数据中。数据必须为 29 字节长。在读取此文件之前，必须先写入控制以选择要读取的配置文件。

**用户（Users）：** http://roccat.sourceforge.net

**什么（What）：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/isku/roccatisku<minor>/keys_thumbster`

**日期（Date）：** 六月 2011 年

**联系人（Contact）：** Stefan Achatz <erazor_de@users.sourceforge.net>

**描述（Description）：** 写入时，此文件允许为特定配置文件设置拇指键的功能。配置文件编号包含在写入的数据中。数据必须为 23 字节长。在读取此文件之前，必须先写入控制以选择要读取的配置文件。

**用户（Users）：** http://roccat.sourceforge.net

**什么（What）：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/isku/roccatisku<minor>/last_set`

**日期（Date）：** 六月 2011 年

**联系人（Contact）：** Stefan Achatz <erazor_de@users.sourceforge.net>

**描述（Description）：** 写入时，此文件允许设置自纪元以来的秒数时间，即上次配置发生的时间。数据必须为 20 字节长。

**用户（Users）：** http://roccat.sourceforge.net

**什么（What）：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/isku/roccatisku<minor>/light`

**日期（Date）：** 六月 2011 年

**联系人（Contact）：** Stefan Achatz <erazor_de@users.sourceforge.net>

**描述（Description）：** 写入时，此文件允许为特定配置文件设置背光强度。配置文件编号包含在写入的数据中。对于 Isku，数据必须为 10 字节长；对于 IskuFX，需要 16 字节的数据。在读取此文件之前，必须先写入控制以选择要读取的配置文件。

**用户（Users）：** http://roccat.sourceforge.net

**什么（What）：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/isku/roccatisku<minor>/macro`

**日期（Date）：** 六月 2011 年

**联系人（Contact）：** Stefan Achatz <erazor_de@users.sourceforge.net>

**描述（Description）：** 写入时，此文件允许为特定按钮和特定配置文件存储最多 500 个按键操作的宏。按钮和配置文件编号包含在写入的数据中。数据必须为 2083 字节长。在读取此文件之前，必须先写入控制以选择要读取的配置文件和键。

**用户（Users）：** http://roccat.sourceforge.net

**什么（What）：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/isku/roccatisku<minor>/reset`

**日期（Date）：** 十一月 2012 年

**联系人（Contact）：** Stefan Achatz <erazor_de@users.sourceforge.net>

**描述（Description）：** 写入时，此文件允许重置设备。数据必须为 3 字节长。此文件为只写。

**用户（Users）：** http://roccat.sourceforge.net

**什么（What）：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/isku/roccatisku<minor>/control`

**日期（Date）：** 六月 2011 年

**联系人（Contact）：** Stefan Achatz <erazor_de@users.sourceforge.net>

**描述（Description）：** 写入时，此文件允许选择要读取的下一个数据的配置文件。数据必须为 3 字节长。此文件为只写。

**用户（Users）：** http://roccat.sourceforge.net

**什么（What）：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/isku/roccatisku<minor>/talk`

**日期（Date）：** 六月 2011 年

**联系人（Contact）：** Stefan Achatz <erazor_de@users.sourceforge.net>

**描述（Description）：** 写入时，此文件允许从主机触发轻松切换功能。数据必须为 16 字节长。此文件为只写。

**用户（Users）：** http://roccat.sourceforge.net

**什么（What）：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/isku/roccatisku<minor>/talkfx`

**日期（Date）：** 二月 2013 年

**联系人（Contact）：** Stefan Achatz <erazor_de@users.sourceforge.net>

**描述（Description）：** 写入时，此文件允许从主机触发临时颜色方案。数据必须为 16 字节长。

此文件为只写文件。

用户：[http://roccat.sourceforge.net](http://roccat.sourceforge.net)