**What**：`/sys/devices/.../real_power_state`

**Date**：2013 年 1 月

**Contact**：Rafael J. Wysocki <rafael.j.wysocki@intel.com>

**Description**：

`/sys/devices/.../real_power_state` 属性仅存在于表示提供电源管理方法并使用 ACPI 电源资源进行电源管理的 ACPI 设备节点的设备对象中。如果存在，它包含一个字符串，表示给定设备节点的实际 ACPI 电源状态，该状态由 `_PSC` 控制方法返回或从电源资源的配置推断得出。其可能的值，"D0"、"D1"、"D2"、"D3hot" 和 "D3cold"，反映了 ACPI 规范（ACPI 4 及以上）中定义的电源状态名称。在某些情况下，此属性的值可能与同一设备对象的 `/sys/devices/.../power_state` 属性的值不同。如果发生这种情况，设备节点使用的一些共享电源资源仅处于开启状态，是因为此时其他一些设备正在使用它们。此属性为只读。 