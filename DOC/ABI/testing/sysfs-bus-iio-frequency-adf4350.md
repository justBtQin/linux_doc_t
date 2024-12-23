**What**: `/sys/bus/iio/devices/iio:deviceX/out_altvoltageY_frequency_resolution`

**KernelVersion**: 3.4.0

**Contact**: linux-iio@vger.kernel.org

**Description**:

存储通道 Y 的频率分辨率/通道间距（单位：Hz）。

该值直接影响分数-N 锁相环（PLL）使用的模数。假定用于计算各种分频器的算法能够为通道间距的倍数生成适当的值。

**What**: `/sys/bus/iio/devices/iio:deviceX/out_altvoltageY_refin_frequency`

**KernelVersion**: 3.4.0

**Contact**: linux-iio@vger.kernel.org

**Description**:

设置通道 Y 的 REFin 频率（单位：Hz）。在某些时钟链应用中，锁相环使用的参考频率可能在运行时发生变化。此属性允许用户相应地调整参考频率。在更新`out_altvoltageY_frequency`之前，写入的值无效。考虑在 REFin 变化期间使用`out_altvoltageY_powerdown`来关闭锁相环及其射频输出缓冲区。