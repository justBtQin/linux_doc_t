**What**: `/sys/dev`

**Date**: 2008 年 4 月

**KernelVersion**: 2.6.26

**Contact**: Dan Williams <dan.j.williams@intel.com>

**Description**: `/sys/dev 树提供了一种使用`stat(2)`返回的信息查找设备的 sysfs 路径的方法。在`/sys/dev`下有两个目录，“block”和“char”，包含形式为“<主设备号>:<次设备号>”的符号链接。这些链接指向给定设备的相应 sysfs 路径。

示例：

`$ readlink /sys/dev/block/8:32`

`../../block/sdc`

在`/sys/dev/char`和`/sys/dev/block`中的条目将在设备进入和离开系统时动态创建和销毁。

**Users**: mdadm <linux-raid@vger.kernel.org>