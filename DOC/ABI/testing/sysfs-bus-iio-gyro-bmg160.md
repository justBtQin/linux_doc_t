**What**: `/sys/bus/iio/devices/triggerX/name = "bmg160-any-motion-devX"`

**KernelVersion**: 3.17

**Contact**: linux-iio@vger.kernel.org

**Description**:

    该 BMG160 陀螺仪内核模块提供了一个额外的触发机制，它将驱动程序设置为一种模式，即在只有有任何运动时才将数据推送到缓冲区。