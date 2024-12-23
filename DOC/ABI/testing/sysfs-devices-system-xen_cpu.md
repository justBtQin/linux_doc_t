**什么：** `/sys/devices/system/xen_cpu/`

**日期：** 2012 年 5 月

**联系人：** 刘劲松 <jinsong.liu@intel.com>

**描述：**

这是一个全局/单个 Xen 物理 CPU 属性的集合。

单个物理 CPU 属性包含在以 Xen 的逻辑 CPU 编号命名的子目录中，例如：

`/sys/devices/system/xen_cpu/xen_cpu#/`

**什么：** `/sys/devices/system/xen_cpu/xen_cpu#/online`

**日期：** 2012 年 5 月

**联系人：** 刘劲松 <jinsong.liu@intel.com>

**描述：**

这是一个用于在线/离线 Xen 物理 CPU 的接口。

在 Xen 平台下运行时，它为用户提供了在线/离线物理 CPU 的接口，但由于一些逻辑限制和假设，不包括 cpu0。