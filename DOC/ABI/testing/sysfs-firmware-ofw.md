**What**: `/sys/firmware/devicetree/*`

**Date**: November 2013

**Contact**: Grant Likely <grant.likely@linaro.org>

**Description**:

当使用 OpenFirmware 或扁平化设备树来枚举硬件时，设备树结构将在此目录中公开。

可能存在多个设备树目录。一些设备驱动程序使用单独的分离设备树，它们与系统树没有关联，将出现在 `/sys/firmware/devicetree` 下的不同子目录中。

用户空间不应直接使用 `/sys/firmware/devicetree/base` 路径，而应遵循 `/proc/device-tree` 符号链接。将来绝对路径可能会更改，但符号链接是稳定的 ABI。

`/proc/device-tree` 符号链接取代了设备树 `/proc` 文件系统支持，并且具有大致相同的语义，应该与现有的用户空间兼容。

`/sys/firmware/devicetree/` 的内容是一个目录层次结构，每个设备树节点一个目录。目录名称是解析后的路径组件名称（节点名称加上地址）。属性在目录中表示为文件。每个文件的内容是设备树中的精确二进制数据。