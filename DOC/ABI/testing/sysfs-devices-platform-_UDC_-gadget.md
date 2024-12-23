**What**: /sys/devices/platform/_UDC_/gadget/suspended

**Date**: April 2010

**Contact**: Fabien Chouteau <fabien.chouteau@barco.com>

**Description**:

展示 USB 复合设备的挂起状态。1 -> 挂起，0 -> 恢复。

（_UDC_ 是 USB 设备控制器驱动的名称）

**What**: /sys/devices/platform/_UDC_/gadget/gadget-lunX/nofua

**Date**: July 2010

**Contact**: Andy Shevchenko <andy.shevchenko@gmail.com>

**Description**:

在 USB 大容量存储模式下的设备中，显示或设置对 SCSI WRITE(10,12)命令中的 FUA（强制单元访问）位的响应。

可能的值为：

1 -> 忽略 FUA 标志

0 -> 遵守 FUA 标志