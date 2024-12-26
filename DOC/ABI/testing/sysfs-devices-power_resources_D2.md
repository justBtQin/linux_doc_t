**What**: `/sys/devices/.../power_resources_D2/`

**Date**: 2013 年 1 月

**Contact**: Rafael J. Wysocki <rafael.j.wysocki@intel.com>

**Description**:

`/sys/devices/.../power_resources_D2/` 目录仅存在于表示使用 ACPI 电源资源进行电源管理并支持 ACPI 电源状态 D2 的 ACPI 设备节点的设备对象中。

如果存在，它包含指向设备目录的符号链接，这些设备目录表示为使给定设备节点处于 ACPI 电源状态 D2 而需要打开的 ACPI 电源资源。链接的名称与它们指向的目录的名称相同。