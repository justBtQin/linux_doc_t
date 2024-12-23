**What**: `/sys/bus/acpi/intel-rapid-start/wakeup_events`

**Date**: July 2, 2013

**KernelVersion**: 3.11

**Contact**: Matthew Garrett <mjg59@srcf.ucam.org>

**Description**: 一个表示一组唤醒事件的整数，如下所示：

 - 1：当唤醒定时器到期时唤醒进入休眠状态

 - 2：当电池电量达到临界水平时唤醒进入休眠状态

这些值是按位或在一起的。例如，值为 3 表示系统将在唤醒定时器到期或电池达到临界水平时唤醒进入休眠状态。

**What**: `/sys/bus/acpi/intel-rapid-start/wakeup_time`

**Date**: July 2, 2013

**KernelVersion**: 3.11

**Contact**: Matthew Garrett <mjg59@srcf.ucam.org>

**Description**: 一个表示系统在唤醒进入休眠状态之前将保持睡眠的时间长度的整数。此值以分钟为单位。