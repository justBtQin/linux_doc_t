**What: /sys/class/extcon/.../**

Date: February 2012

Contact: MyungJoo Ham <myungjoo.ham@samsung.com>

Description:

提供 sysfs 中的一个位置用于 extcon 对象。这允许访问 extcon 特定的变量。以...表示的 extcon 对象的名称是通过 extcon_dev_register 给出的名称。

一个 extcon 设备表示一个单一的外部连接器端口。一个外部连接器可能同时有多个电缆连接。许多底座、支架和附件电缆都具有这种能力。例如，Nuri 板（/arch/arm/mach-exynos）的 30 针端口可能同时连接 HDMI 和充电器，或模拟音频、视频和 USB 电缆。

如果有相互排斥的电缆，这种二进制关系可以通过 extcon_dev 的 mutually_exclusive 数组来表示。

**What: /sys/class/extcon/.../name**

Date: February 2012

Contact: MyungJoo Ham <myungjoo.ham@samsung.com>

Description:

/sys/class/extcon/.../name 显示 extcon 对象的名称。如果 extcon 对象定义了可选的回调“show_name”，该回调将通过此 sysfs 节点提供名称。

**What: /sys/class/extcon/.../state**

Date: February 2012

Contact: MyungJoo Ham <myungjoo.ham@samsung.com>

Description:

/sys/class/extcon/.../state 显示并存储相应 extcon 对象的电缆连接/断开信息。如果 extcon 对象定义了可选的回调“show_state”，显示功能将被可选的回调覆盖。

如果使用默认的显示功能回调，格式如下：

`# cat state`

USB_OTG=1

HDMI=0

TA=1

EAR_JACK=0


在这个例子中，extcon 设备连接了 USB_OTG 和 TA 电缆，HDMI 和 EAR_JACK 电缆断开。

为了更新 extcon 设备的状态，输入一个以 0x 开头的十六进制状态数：

`# echo 0xHEX > state`

这将更新 extcon 设备的整个状态。如果存在，所有方法的输入都需要满足相互排斥的条件。如果需要原子地设置值，建议使用此“全局”状态接口。与每个电缆相关的后续状态接口不能同时更新 extcon 设备的多个电缆状态。

**What: /sys/class/extcon/.../cable.x/name**

Date: February 2012

Contact: MyungJoo Ham <myungjoo.ham@samsung.com>

Description:

/sys/class/extcon/.../cable.x/name 显示 extcon 设备的电缆“x”（0 到 31 之间的整数）的名称。

**What: /sys/class/extcon/.../cable.x/state**

Date: February 2012

Contact: MyungJoo Ham <myungjoo.ham@samsung.com>

Description:

/sys/class/extcon/.../cable.x/state 显示并存储 extcon 设备的电缆“x”（0 到 31 之间的整数）的状态。状态值为 0（断开）或 1（连接）。

**What: /sys/class/extcon/.../mutually_exclusive/...**

Date: December 2011

Contact: MyungJoo Ham <myungjoo.ham@samsung.com>

Description:

显示相互排斥的关系。例如，如果 extcon 设备的 mutually_exclusive 数组是{0x3, 0x5, 0xC, 0x0}，则输出为：

`# ls mutually_exclusive/`

0x3

0x5

0xc

注意，mutually_exclusive 是 extcon 设备的子目录，mutually_exclusive 目录下的文件名显示相互排斥的集合，而不是文件的内容。