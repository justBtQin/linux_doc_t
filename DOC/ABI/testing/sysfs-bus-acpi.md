**What: /sys/bus/acpi/devices/.../path**

Date: December 2006

Contact: Rafael J. Wysocki <rjw@rjwysocki.net>

Description:

此属性指示与设备对象关联的 ACPI 命名空间对象的完整路径。例如，\_SB_.PCI0。此文件对于表示固定 ACPI 硬件功能（如电源和睡眠按钮）的设备对象不存在。

**What: /sys/bus/acpi/devices/.../modalias**

Date: July 2007

Contact: Rafael J. Wysocki <rjw@rjwysocki.net>

Description:

此属性指示设备对象的 PNP ID。即 acpi:HHHHHHHH:[CCCCCCC:]。其中每个 HHHHHHHH 或 CCCCCCCC 包含设备对象的 PNPID（_HID 或 _CID）。

**What: /sys/bus/acpi/devices/.../hid**

Date: April 2005

Contact: Rafael J. Wysocki <rjw@rjwysocki.net>

Description:

此属性指示设备对象的硬件 ID（_HID）。例如，PNP0103。此文件对于具有 _HID 控制方法的设备对象存在。

**What: /sys/bus/acpi/devices/.../description**

Date: October 2012

Contact: Rafael J. Wysocki <rjw@rjwysocki.net>

Description:

此属性包含设备对象的 _STR 控制方法的输出（如果存在）。

**What: /sys/bus/acpi/devices/.../adr**

Date: October 2012

Contact: Rafael J. Wysocki <rjw@rjwysocki.net>

Description:

此属性包含设备对象的 _ADR 控制方法的输出，对于表示具有标准枚举算法（如 PCI）的设备的 ACPI 设备对象存在。

**What: /sys/bus/acpi/devices/.../uid**

Date: October 2012

Contact: Rafael J. Wysocki <rjw@rjwysocki.net>

Description:

此属性包含设备对象的 _UID 控制方法的输出（如果存在）。

**What: /sys/bus/acpi/devices/.../eject**

Date: December 2006

Contact: Rafael J. Wysocki <rjw@rjwysocki.net>

Description:

向此属性写入 1 将触发此设备对象的热移除。此文件对于每个具有 _EJ0 方法的设备对象存在。