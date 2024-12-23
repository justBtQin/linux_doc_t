**What**: video1394（又名“FireWire 的 OHCI-1394 视频支持”）

**Date**: 2010 年 5 月（计划中），最终在内核 v2.6.37 中被移除

**Contact**: linux1394-devel@lists.sourceforge.net

**Description**:

/dev/video1394/* 是字符设备文件，每个 FireWire 控制器一个，用于等时 I/O。它是作为 raw1394 的等时 I/O 功能的替代方案添加的，raw1394 的第一代在性能方面存在问题。任何 video1394 用户也必须使用 raw1394 + libraw1394，因为 video1394 没有为设备发现和配置提供异步 I/O。被 /dev/fw* 取代，即 firewire-core 的 <linux/firewire-cdev.h> ABI。

**Users**:

libdc1394（也可与 firewire-cdev 一起使用，对库 ABI 用户透明）