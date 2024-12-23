**What**：`/sys/devices/.../resource_in_use`

**Date**：2013 年 1 月

**Contact**：Rafael J. Wysocki <rafael.j.wysocki@intel.com>

**Description**：

`/sys/devices/.../resource_in_use`属性仅存在于表示 ACPI 电源资源的设备对象中。如果存在，它包含一个数字（0 或 1），表示给定电源资源的当前状态（0 表示该资源未被使用，因此已关闭）。此属性为只读。