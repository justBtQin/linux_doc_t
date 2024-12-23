**What：** `/sys/bus/event_source/devices/hv_24x7/interface/catalog`

**Date：** 2014 年 2 月

**Contact：** Linux on PowerPC Developer List <linuxppc-dev@lists.ozlabs.org>

**Description：**

提供对 POWER7 和 8 系统上管理程序提供的二进制“24x7 目录”的访问。此目录列出了 powerpc“hv_24x7”pmu 可用的事件。其格式在此处记录：

https://raw.githubusercontent.com/jmesmon/catalog-24x7/master/hv-24x7-catalog.h

**What：** `/sys/bus/event_source/devices/hv_24x7/interface/catalog_length`

**Date：** 2014 年 2 月

**Contact：** Linux on PowerPC Developer List <linuxppc-dev@lists.ozlabs.org>

**Description：**

一个等于目录字节长度的数字。这也可以从提供的二进制“catalog”sysfs 条目中提取。

**What：** `/sys/bus/event_source/devices/hv_24x7/interface/catalog_version`

**Date：** 2014 年 2 月

**Contact：** Linux on PowerPC Developer List <linuxppc-dev@lists.ozlabs.org>

**Description：**

公开 24x7 目录的“版本”字段。这也可以从提供的二进制“catalog”sysfs 条目中提取。

**What：** `/sys/bus/event_source/devices/hv_24x7/event_descs/<event-name>`

**Date：** 2014 年 2 月

**Contact：** Linux on PowerPC Developer List <linuxppc-dev@lists.ozlabs.org>

**Description：**

提供由固件提供的特定事件的描述。如果固件未提供描述，则不会创建文件。

注意，事件名称缺少在 events/目录中的事件附加的域后缀。

**What：** `/sys/bus/event_source/devices/hv_24x7/event_long_descs/<event-name>`

**Date：** 2014 年 2 月

**Contact：** Linux on PowerPC Developer List <linuxppc-dev@lists.ozlabs.org>

**Description：**

提供由固件提供的特定事件的“长”描述。如果固件未提供描述，则不会创建文件。

注意，事件名称缺少在 events/目录中的事件附加的域后缀。