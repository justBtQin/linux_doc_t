**What**: `/sys/bus/iio/devices/triggerX/trigger_now`

**KernelVersion**: 2.6.38

**Contact**: linux-iio@vger.kernel.org

**Description**:

此文件由 iio-trig-sysfs 独立触发驱动程序提供。向此文件写入任何值都会触发与该触发相关联的事件驱动驱动程序，将数据捕获到内核缓冲区中。在自动化测试期间或在其他触发方法不适用的情况下（例如没有 RTC 或备用 GPIO），这种方法可能很有价值。其中，X 是触发的 IIO 索引。