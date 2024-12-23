**What**: `/sys/bus/hsi`

**Date**: 四月 2012

**KernelVersion**: 3.4

**Contact**: Carlos Chinea <carlos.chinea@nokia.com>

**Description**:

高速度同步串行接口（HSI）是一种串行接口，主要用于在蜂窝手机中将应用引擎（APE）与蜂窝调制解调器引擎（CMT）连接起来。该总线将填充代表系统中可用协议的设备（hsi_clients）。总线驱动程序实现这些协议。

**What**: `/sys/bus/hsi/devices/.../modalias`

**Date**: 四月 2012

**KernelVersion**: 3.4

**Contact**: Carlos Chinea <carlos.chinea@nokia.com>

**Description**: 存储由 uevent 发出的相同 MODALIAS 值

格式：hsi:<hsi_client 设备名称>