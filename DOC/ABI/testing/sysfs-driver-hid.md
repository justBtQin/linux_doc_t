**关于 USB 设备：**

- 文件路径：`/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/report_descriptor`

- 日期：Jan 2011

- 内核版本：2.0.39

- 联系人：Alan Ott <alan@signal11.us>

- 描述：读取此文件时，将返回设备的原始二进制 HID 报告描述符。此文件不可写入。

- 用户：HIDAPI 库（http://www.signal11.us/oss/hidapi）

**关于 BT 设备：**

- 文件路径：`/sys/class/bluetooth/hci<addr>/<hid-bus>:<vendor-id>:<product-id>.<num>/report_descriptor`

- 日期：February 2015

- 内核版本：3.19

- 联系人：Olivier Gay <ogay@logitech.com>

- 描述：读取此文件时，将以 ASCII 码形式返回设备的 HID 国家代码的十六进制整数值（例如美国为 21）。此文件不可写入。

**关于 USB 设备的国家代码相关：**

- 文件路径：`/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/country`

- 日期：February 2015

- 内核版本：3.19

- 联系人：Olivier Gay <ogay@logitech.com>

- 描述：读取此文件时，将以 ASCII 码形式返回设备的 HID 国家代码的十六进制整数值（例如美国为 21）。此文件不可写入。

**关于 BT 设备的国家代码相关：**

- 文件路径：`/sys/class/bluetooth/hci<addr>/<hid-bus>:<vendor-id>:<product-id>.<num>/country`

- 日期：February 2015

- 内核版本：3.19

- 联系人：Olivier Gay <ogay@logitech.com>

- 描述：读取此文件时，将以 ASCII 码形式返回设备的 HID 国家代码的十六进制整数值（例如美国为 21）。此文件不可写入。

**符号链接：**

- USB 设备：`/sys/class/hidraw/hidraw<num>/device/report_descriptor`

- BT 设备：`/sys/class/hidraw/hidraw<num>/device/country`