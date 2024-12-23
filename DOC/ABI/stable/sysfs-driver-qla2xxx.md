**What**：`/sys/bus/pci/drivers/qla2xxx/.../devices/*`

**Date**：2009 年 9 月

**Contact**：QLogic Linux Driver <linux-driver@qlogic.com>

**Description**：`qla2xxx-udev.sh`目前正在寻找`uevent CHANGE`事件，以指示驱动程序已生成固件转储并且准备好进行检索。

**Users**：`qla2xxx-udev.sh`。建议的更改应发送至`linux-driver@qlogic.com`