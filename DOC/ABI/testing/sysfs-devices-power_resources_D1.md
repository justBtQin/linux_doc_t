**What：** `/sys/devices/.../power_resources_D1/`

**Date：** 2013 年 1 月

**Contact：** Rafael J. Wysocki <rafael.j.wysocki@intel.com>

**Description：** 

- `/sys/devices/.../power_resources_D1/`目录仅存在于表示使用 ACPI 电源资源进行电源管理并支持 ACPI 电源状态 D1 的 ACPI 设备节点的设备对象中。

- 如果存在，它包含指向表示需要为给定设备节点处于 ACPI 电源状态 D1 而需打开的 ACPI 电源资源的设备目录的符号链接。链接的名称与它们指向的目录的名称相同。