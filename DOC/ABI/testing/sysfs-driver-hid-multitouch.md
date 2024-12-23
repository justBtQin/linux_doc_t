**What**：`/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/quirks`

**Date**：2011 年 11 月

**Contact**：Benjamin Tissoires <benjamin.tissoires@gmail.com>

**Description**：此属性的整数值对应于实际用于处理设备协议的怪癖。读取时，此属性返回当前设置（见`hid-multitouch.c`中的`MT_QUIRKS_*`）。写入时，此属性会即时更改怪癖，然后处理设备的协议。