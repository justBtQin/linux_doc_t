**What**: `/sys/devices/.../online`

**Date**: 四月 2013

**Contact**: Rafael J. Wysocki <rafael.j.wysocki@intel.com>

**Description**:

`/sys/devices/.../online` 属性仅存在于其总线类型提供 `.online()` 和 `.offline()` 回调函数的设备中。从该属性读取的数字（0 或 1）反映了设备的“offline”字段的值。如果该数字为 1 且向此文件写入“0”（或“n”，或“N”），则为该设备执行设备总线类型的 `.offline()` 回调函数（如果成功），并相应地更新其“offline”字段。反过来，如果该数字为 0 且向此文件写入“1”（或“y”，或“Y”），则为该设备执行设备总线类型的 `.online()` 回调函数（如果成功），并适当地更新其“offline”字段。

在成功执行总线类型的 `.offline()` 回调函数后，该设备在被移除（即对其调用 `device_del()`）或其总线类型的 `.online()` 成功执行之前，不能用于任何目的。