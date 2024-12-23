**What**: `/sys/.../events/in_illuminance0_thresh_either_en`

**Date**: April 2012

**KernelVersion**: 3.5

**Contact**: Johan Hovold <jhovold@gmail.com>

**Description**: 当通道在每个方向（上升|下降）通过四个阈值之一且区域发生变化时生成的事件。可以从 `in_illuminance0_zone` 读取相应的光区域。

**What**: `/sys/.../events/in_illuminance0_threshY_hysteresis`

**Date**: May 2012

**KernelVersion**: 3.5

**Contact**: Johan Hovold <jhovold@gmail.com>

**Description**: 获取阈值 Y 的滞后，即 `threshY_hysteresis = threshY_raising - threshY_falling`。

**What**: `/sys/.../events/illuminance_threshY_falling_value`

**What**: `/sys/.../events/illuminance_threshY_raising_value`

**Date**: April 2012

**KernelVersion**: 3.5

**Contact**: Johan Hovold <jhovold@gmail.com>

**Description**: 指定设备针对由 `in_illuminance0_thresh_either_en` 启用的事件进行比较的阈值值（0..255），其中 Y 为 0..3。注意，`threshY_falling` 必须小于或等于 `threshY_raising`。这些阈值对应于八个区域边界寄存器（`boundaryY_{low,high}`）并定义了五个光区域。

**What**: `/sys/bus/iio/devices/iio:deviceX/in_illuminance0_zone`

**Date**: April 2012

**KernelVersion**: 3.5

**Contact**: Johan Hovold <jhovold@gmail.com>

**Description**: 获取由 `in_illuminance0_threshY_{falling,rising}` 阈值定义的当前光区域（0..4）。

**What**: `/sys/bus/iio/devices/iio:deviceX/out_currentY_raw`

**Date**: May 2012

**KernelVersion**: 3.5

**Contact**: Johan Hovold <jhovold@gmail.com>

**Description**: 获取通道 Y（0..255）的输出电流，即 `out_currentY_currentZ_raw`，其中 Z 是当前区域。

**What**: `/sys/bus/iio/devices/iio:deviceX/out_currentY_currentZ_raw`

**Date**: May 2012

**KernelVersion**: 3.5

**Contact**: Johan Hovold <jhovold@gmail.com>

**Description**: 设置通道 `out_currentY` 在区域 Z（0..255）中的输出电流，其中 Y 为 0..2 且 Z 为 0..4。这些值对应于 ALS 映射器 Y + 1 的 ALS 映射器目标寄存器。