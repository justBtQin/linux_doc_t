**What**: `/sys/devices/.../power_resources_wakeup/`

**Date**: 四月 2013

**Contact**: Rafael J. Wysocki <rafael.j.wysocki@intel.com>

**Description**:

`/sys/devices/.../power_resources_wakeup/`目录仅存在于表示需要 ACPI 电源资源进行唤醒信号的 ACPI 设备节点的设备对象中。

如果存在，它包含指向表示需要为给定设备节点启用以能够发出唤醒信号的 ACPI 电源资源的设备目录的符号链接。链接的名称与它们指向的目录的名称相同。