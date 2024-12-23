**What**: `/sys/devices/.../sun`

**Date**: 2012 年 10 月

**Contact**: Yasuaki Ishimatsu <isimatu.yasuaki@jp.fujitsu.com>

**Description**:

该文件包含一个由 ACPI 命名空间中的`_SUN`方法提供的插槽唯一 ID。该值在高级配置和电源接口规范中如下编写：

“`_SUN`值在相同类型的插槽中必须是唯一的。并且还建议在可能的情况下，此编号应与物理插槽上打印的插槽编号匹配。”

因此，读取 sysfs 文件，我们可以在系统中识别出插槽的物理位置。