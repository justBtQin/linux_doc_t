**What**：/sys/devices

**Date**：2006 年 2 月

**Contact**：Greg Kroah-Hartman <gregkh@linuxfoundation.org>

**Description**：

/sys/devices 树包含内核设备树内部状态的快照。随着机器运行，设备将动态添加和删除，并且在不同的内核版本之间，此树中设备的布局将发生变化。

请不要依赖此树的格式，因为这个原因。如果一个程序希望在树中找到不同的东西，请使用 /sys/class 结构，并依赖那里的符号链接指向单个设备在 /sys/devices 树中的正确位置。或者依赖 uevent 消息来通知程序设备被添加到和从该树中删除，以找到这些设备的位置。

请注意，有时沿着目录链的所有设备并不都会发出 uevent 消息，因此用户空间程序必须能够处理这种情况。

**Users**：

udev <linux-hotplug-devel@lists.sourceforge.net>