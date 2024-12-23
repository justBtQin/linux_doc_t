**What**: `/sys/kernel/profiling`

**Date**: 2008 年 9 月

**Contact**: Dave Hansen <dave@linux.vnet.ibm.com>

**Description**:

`/sys/kernel/profiling` 是运行时等效于启动时的 `profile=` 选项。

你可以通过运行以下命令获得相同的效果：

`echo 2 > /sys/kernel/profiling`

就像在启动命令行中发出 `profile=2` 一样。