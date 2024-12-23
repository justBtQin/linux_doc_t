**What**：`/sys/bus/hid/drivers/wiimote/<dev>/led1`

**What**：`/sys/bus/hid/drivers/wiimote/<dev>/led2`

**What**：`/sys/bus/hid/drivers/wiimote/<dev>/led3`

**What**：`/sys/bus/hid/drivers/wiimote/<dev>/led4`

**Date**：2011 年 7 月

**KernelVersion**：3.1

**Contact**：David Herrmann <dh.herrmann@googlemail.com>

**Description**：能够设置/获取当前 LED 状态。读取时，如果 LED 关闭则返回 0，如果开启则返回 1。写入 0 会关闭 LED，写入 1 会开启 LED。

**What**：`/sys/bus/hid/drivers/wiimote/<dev>/extension`

**Date**：2011 年 8 月

**KernelVersion**：3.2

**Contact**：David Herrmann <dh.herrmann@gmail.com>

**Description**：此文件包含当前连接并初始化的扩展。它可以是以下之一：none、motionp、nunchuck、classic、motionp+nunchuck、motionp+classic。motionp 是任天堂官方的 Motion+ 扩展，nunchuck 是任天堂官方的 Nunchuck 扩展，classic 是任天堂的 Classic Controller 扩展。motionp 扩展可以与其他两个组合。从内核版本 3.11 开始，支持 Motion Plus 热插拔，如果检测到，它不再被报告为静态扩展。然后您将收到 motion-plus 设备的 uevent 通知。

**What**：`/sys/bus/hid/drivers/wiimote/<dev>/devtype`

**Date**：2013 年 5 月

**KernelVersion**：3.11

**Contact**：David Herrmann <dh.herrmann@gmail.com>

**Description**：当设备由 wiimote 驱动程序初始化时，我们执行设备检测，并在完成后发出“change”uevent。此文件显示检测到的设备类型。“pending”表示检测仍在进行中，“unknown”表示设备无法检测或加载。“generic”表示设备无法检测但支持基本的 Wii Remote 功能并且可以使用。每个设备类型的其他字符串可用，如果添加了新的设备特定检测，可能会添加。目前支持的有：gen10：第一代 Wii Remote，gen20：第二代 Wii Remote Plus（内置 MP），balanceboard：Wii 平衡板

**What**：`/sys/bus/hid/drivers/wiimote/<dev>/bboard_calib`

**Date**：2013 年 5 月

**KernelVersion**：3.11

**Contact**：David Herrmann <dh.herrmann@gmail.com>

**Description**：仅当设备被检测为平衡板时提供此属性。它为所有 4 个传感器提供一行 3 个校准值。值用冒号分隔，每个都是 2 字节长（编码为 4 位十六进制值）。首先写入所有 4 个传感器的 0kg 值，然后是所有 4 个传感器的 17kg 值，最后是所有 4 个传感器的 34kg 值。内核已将校准数据应用于所有输入值，但用户空间可用于执行其他转换。

**What**：`/sys/bus/hid/drivers/wiimote/<dev>/pro_calib`

**Date**：2013 年 10 月

**KernelVersion**：3.13

**Contact**：David Herrmann <dh.herrmann@gmail.com>

**Description**：仅当设备被检测为 pro-controller 时提供此属性。它为所有 4 个模拟摇杆提供一行 4 个校准值。格式为：“x1:y1 x2:y2”。数据带有 +/ - 前缀。每个值都是有符号的 16 位数字。数据以十进制数字编码，并指定 pro-controller 模拟摇杆的偏移量。内核已将校准数据应用于所有输入值，但用户空间可用于执行其他转换。内核在设备设置期间检测校准数据。您可以在此文件中写入“scan\n”重新触发校准。您也可以直接以“x1:y1 x2:y2”的形式写入数据手动设置校准值。 