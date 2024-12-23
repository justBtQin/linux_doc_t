**What**：`/sys/devices/.../lpss_ltr/`

**Date**：2013 年 3 月

**Contact**：Rafael J. Wysocki <rafael.j.wysocki@intel.com>

**Description**：

`/sys/devices/.../lpss_ltr/` 目录仅存在于包含在英特尔 Lynxpoint 低功耗子系统（LPSS）中的设备中。如果存在，它包含包含设备的 LTR 模式和 LTR 寄存器值的属性。

**What**：`/sys/devices/.../lpss_ltr/ltr_mode`

**Date**：2013 年 3 月

**Contact**：Rafael J. Wysocki <rafael.j.wysocki@intel.com>

**Description**：

`/sys/devices/.../lpss_ltr/ltr_mode` 属性包含一个整数（0 或 1），表示设备的 LTR 功能是否在软件模式（1）下工作。此属性为只读。如果设备的运行时 PM 状态不是“active”，尝试从此属性读取将导致返回 -EAGAIN。

**What**：`/sys/devices/.../lpss_ltr/auto_ltr`

**Date**：2013 年 3 月

**Contact**：Rafael J. Wysocki <rafael.j.wysocki@intel.com>

**Description**：

`/sys/devices/.../lpss_ltr/auto_ltr` 属性包含设备的 AUTO_LTR 寄存器的当前值（原始），表示为 8 位十六进制数。此属性为只读。如果设备的运行时 PM 状态不是“active”，尝试从此属性读取将导致返回 -EAGAIN。

**What**：`/sys/devices/.../lpss_ltr/sw_ltr`

**Date**：2013 年 3 月

**Contact**：Rafael J. Wysocki <rafael.j.wysocki@intel.com>

**Description**：

`/sys/devices/.../lpss_ltr/auto_ltr` 属性（应为 `/sys/devices/.../lpss_ltr/sw_ltr`）包含设备的 SW_LTR 寄存器的当前值（原始），表示为 8 位十六进制数。此属性为只读。如果设备的运行时 PM 状态不是“active”，尝试从此属性读取将导致返回 -EAGAIN。 