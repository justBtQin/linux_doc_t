**What**: `/sys/class/leds/<led>/flash_brightness`

**Date**: March 2015

**KernelVersion**: 4.0

**Contact**: Jacek Anaszewski <j.anaszewski@samsung.com>

**Description**: 可读/写

    在闪光频闪模式下设置此 LED 的亮度，以微安为单位。仅为支持设置闪光亮度的闪光 LED 设备创建此文件。

    值介于 0 与 `/sys/class/leds/<led>/max_flash_brightness` 之间。

**What**: `/sys/class/leds/<led>/max_flash_brightness`

**Date**: March 2015

**KernelVersion**: 4.0

**Contact**: Jacek Anaszewski <j.anaszewski@samsung.com>

**Description**: 只读

    此 LED 在闪光频闪模式下的最大亮度级别，以微安为单位。

**What**: `/sys/class/leds/<led>/flash_timeout`

**Date**: March 2015

**KernelVersion**: 4.0

**Contact**: Jacek Anaszewski <j.anaszewski@samsung.com>

**Description**: 可读/写

    闪光的硬件超时时间，以微秒为单位。从闪光开始经过此时间段后，闪光频闪将停止。仅为支持设置闪光超时的闪光 LED 设备创建此文件。

**What**: `/sys/class/leds/<led>/max_flash_timeout`

**Date**: March 2015

**KernelVersion**: 4.0

**Contact**: Jacek Anaszewski <j.anaszewski@samsung.com>

**Description**: 只读

    此 LED 的最大闪光超时时间，以微秒为单位。

**What**: `/sys/class/leds/<led>/flash_strobe`

**Date**: March 2015

**KernelVersion**: 4.0

**Contact**: Jacek Anaszewski <j.anaszewski@samsung.com>

**Description**: 可读/写

    闪光频闪状态。写入 1 时触发闪光频闪，写入 0 时关闭闪光。读取时，1 表示当前正在频闪，0 表示闪光已关闭。

**What**: `/sys/class/leds/<led>/flash_fault`

**Date**: March 2015

**KernelVersion**: 4.0

**Contact**: Jacek Anaszewski <j.anaszewski@samsung.com>

**Description**: 只读

    可能发生的闪光故障的空格分隔列表。闪光故障在频闪后会重新读取。可能的闪光故障：

    * led-over-voltage - 闪光控制器到闪光 LED 的电压已超过闪光控制器特定的限制

    * flash-timeout-exceeded - 当用户设置的超时时间到期时，闪光频闪仍在进行；并非所有闪光控制器在所有情况下都可能设置此故障

    * controller-over-temperature - 闪光控制器过热

    * controller-short-circuit - 闪光控制器的短路保护已触发

    * led-power-supply-over-current - LED 电源中的电流已超过闪光控制器特定的限制

    * indicator-led-fault - 闪光控制器在指示 LED 上检测到短路或开路情况

    * led-under-voltage - 闪光控制器到闪光 LED 的电压低于闪光特定的最低限制

    * controller-under-voltage - 闪光控制器的输入电压低于在全电流下无法进行闪光频闪的限制；此条件在该标志不再设置之前一直存在

    * led-over-temperature - LED 的温度已超过其允许的上限