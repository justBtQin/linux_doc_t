**What**: `/sys/devices/.../firmware_node/`

**Date**: September 2012

**Contact**: `<>`

**Description**:  `/sys/devices/.../firmware_node` 目录包含允许用户空间检查和修改给定设备的一些固件相关属性的属性。

**What**: `/sys/devices/.../firmware_node/description`

**Date**: September 2012

**Contact**: Lance Ortiz <lance.ortiz@hp.com>

**Description**: `/sys/devices/.../firmware/description` 属性包含一个字符串，该字符串通过 ACPI 命名空间中的 `_STR` 方法描述设备。此属性为只读。如果设备在 ACPI 命名空间中没有与之关联的 `_STR` 方法，则此属性不存在。