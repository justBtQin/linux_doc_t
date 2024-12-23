**What**：`/sys/bus/iio/devices/triggerX/name = "bmc150_accel-any-motion-devX"`

**KernelVersion**：3.17

**Contact**：linux-iio@vger.kernel.org

**Description**：

BMC150 加速度计内核模块提供了一个额外的触发机制，它将驱动程序设置为一种模式，在这种模式下，仅当有任何运动时，数据才会被推送到缓冲区。