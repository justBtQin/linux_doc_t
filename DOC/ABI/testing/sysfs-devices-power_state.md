**What**: `/sys/devices/.../power_state`

**Date**: January 2013

**Contact**: Rafael J. Wysocki <rafael.j.wysocki@intel.com>

**Description**:

该 `/sys/devices/.../power_state` 属性仅存在于表示提供电源管理方法的 ACPI 设备节点的设备对象中。

如果存在，它包含一个字符串，表示给定设备节点的当前 ACPI 电源状态。其可能的值为“D0”、“D1”、“D2”、“D3hot”和“D3cold”，反映了 ACPI 规范（ACPI 4 及以上）定义的电源状态名称。

如果设备节点使用共享的 ACPI 电源资源，此状态确定需要保持开启的电源资源列表。然而，由于其他设备当前正在使用，设备节点在更高功率（更低编号）状态下所需的一些电源资源也可能处于开启状态。

此属性为只读属性。