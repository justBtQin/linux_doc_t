**What**: `/sys/class/leds/<led>/brightness`

**Date**: March 2006

**KernelVersion**: 2.6.17

**Contact**: Richard Purdie <rpurdie@rpsys.net>

**Description**: 设置 LED 的亮度。大多数 LED 没有硬件亮度支持，因此对于非零亮度设置，只会打开。该值介于 0 和 `/sys/class/leds/<led>/max_brightness` 之间。

**What**: `/sys/class/leds/<led>/max_brightness`

**Date**: March 2006

**KernelVersion**: 2.6.17

**Contact**: Richard Purdie <rpurdie@rpsys.net>

**Description**: 此 LED 的最大亮度级别，默认值为 255（LED_FULL）。

**What**: `/sys/class/leds/<led>/trigger`

**Date**: March 2006

**KernelVersion**: 2.6.17

**Contact**: Richard Purdie <rpurdie@rpsys.net>

**Description**: 设置此 LED 的触发。触发是基于内核的 LED 事件源。您可以以类似于选择 IO 调度程序的方式更改触发。一旦选择了给定的触发，特定于触发的参数可能会出现在 `/sys/class/leds/<led>` 中。

**What**: `/sys/class/leds/<led>/inverted`

**Date**: January 2011

**KernelVersion**: 2.6.38

**Contact**: Richard Purdie <rpurdie@rpsys.net>

**Description**: 反转 LED 的开/关状态。此参数特定于 gpio 和背光触发。对于背光触发，当驱动旨在指示处于待机状态的设备的 LED 时，此参数很有用。