**What**: /sys/class/backlight/<backlight>/als_channel

**Date**: May 2012

**KernelVersion**: 3.5

**Contact**: Johan Hovold <jhovold@gmail.com>

**Description**: 获取在 ALS-current-control 模式下用作输入的 ALS 输出通道（0，1），其中：

 - 0 - out_current0（背光 0）

 - 1 - out_current1（背光 1）

**What**: /sys/class/backlight/<backlight>/als_en

**Date**: May 2012

**KernelVersion**: 3.5

**Contact**: Johan Hovold <jhovold@gmail.com>

**Description**: 启用 ALS-current-control 模式（0，1）。

**What**: /sys/class/backlight/<backlight>/id

**Date**: April 2012

**KernelVersion**: 3.5

**Contact**: Johan Hovold <jhovold@gmail.com>

**Description**: 获取此背光的 ID（0，1）。

**What**: /sys/class/backlight/<backlight>/linear

**Date**: April 2012

**KernelVersion**: 3.5

**Contact**: Johan Hovold <jhovold@gmail.com>

**Description**: 设置亮度映射模式（0，1），其中：

 - 0 - 指数模式

 - 1 - 线性模式

**What**: /sys/class/backlight/<backlight>/pwm

**Date**: April 2012

**KernelVersion**: 3.5

**Contact**: Johan Hovold <jhovold@gmail.com>

**Description**: 设置 PWM 输入控制掩码（5 位），其中：

 - 位 5 - 在区域 4 中启用 PWM 输入

 - 位 4 - 在区域 3 中启用 PWM 输入

 - 位 3 - 在区域 2 中启用 PWM 输入

 - 位 2 - 在区域 1 中启用 PWM 输入

 - 位 1 - 在区域 0 中启用 PWM 输入

 - 位 0 - 启用 PWM 输入