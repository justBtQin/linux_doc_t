**What**: `/sys/bus/amba/devices/.../driver_override`

**Date**: September 2014

**Contact**: Antonios Motakis <a.motakis@virtualopensystems.com>

**Description**:

此文件允许指定设备的驱动程序，该驱动程序将覆盖标准的 OF、ACPI、ID 表和名称匹配。当指定时，只有名称与写入`driver_override`的值匹配的驱动程序才有机会绑定到设备。通过将字符串写入`driver_override`文件（`echo vfio-amba > driver_override`）来指定覆盖，并且可以使用空字符串清除（`echo > driver_override`）。这将使设备返回到标准匹配规则绑定。写入`driver_override`不会自动将设备从其当前驱动程序中解除绑定，也不会尝试自动加载指定的驱动程序。如果当前内核中没有匹配名称的驱动程序加载，设备将不会绑定到任何驱动程序。这也允许设备使用`driver_override`名称（如“none”）选择退出驱动程序绑定。在覆盖中只能指定一个驱动程序，不支持解析分隔符。