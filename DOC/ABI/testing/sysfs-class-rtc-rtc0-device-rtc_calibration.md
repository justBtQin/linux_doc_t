**What**: 用于校准 ST-Ericsson AB8500 实时时钟的属性

**Date**: 2011 年 10 月

**KernelVersion**: 3.0

**Contact**: Mark Godfrey <mark.godfrey@stericsson.com>

**Description**: rtc_calibration 属性允许用户空间校准 AB8500.s 的 32KHz 实时时钟。每 60 秒，AB8500 将通过向其添加此属性的值来校正 RTC 的值。该属性的范围是 -127 到 +127，单位为 30.5 微秒（32KHz 时钟的半百万分之一）

**Users**: /vendor/st-ericsson/base_utilities/core/rtc_calibration 守护进程使用此接口。