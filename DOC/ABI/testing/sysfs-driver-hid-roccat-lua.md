**What：** `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/control`

**Date：** 2012 年 10 月

**Contact：** Stefan Achatz <erazor_de@users.sourceforge.net>

**Description：** 当进行写入操作时，可以配置 cpi、按钮和灯光设置。当进行读取操作时，会返回实际的 cpi 设置和传感器数据。数据必须为 8 字节长。

**Users：** http://roccat.sourceforge.net