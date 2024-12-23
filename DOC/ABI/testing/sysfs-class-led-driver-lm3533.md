**What**：`/sys/class/leds/<led>/als_channel`

**Date**：2012 年 5 月

**KernelVersion**：3.5

**Contact**：Johan Hovold <jhovold@gmail.com>

**Description**：

在 ALS 电流控制模式（1、2）中设置要用作输入的 ALS 输出通道，其中

1 - `out_current1`

2 - `out_current2`

**What**：`/sys/class/leds/<led>/als_en`

**Date**：2012 年 5 月

**KernelVersion**：3.5

**Contact**：Johan Hovold <jhovold@gmail.com>

**Description**：

启用 ALS 电流控制模式（0、1）。

**What**：`/sys/class/leds/<led>/falltime`

**What**：`/sys/class/leds/<led>/risetime`

**Date**：2012 年 4 月

**KernelVersion**：3.5

**Contact**：Johan Hovold <jhovold@gmail.com>

**Description**：

设置模式生成器的下降和上升时间（0..7），其中

0 - 2048 微秒

1 - 262 毫秒

2 - 524 毫秒

3 - 1.049 秒

4 - 2.097 秒

5 - 4.194 秒

6 - 8.389 秒

7 - 16.78 秒

**What**：`/sys/class/leds/<led>/id`

**Date**：2012 年 4 月

**KernelVersion**：3.5

**Contact**：Johan Hovold <jhovold@gmail.com>

**Description**：

获取此 led 的 ID（0..3）。

**What**：`/sys/class/leds/<led>/linear`

**Date**：2012 年 4 月

**KernelVersion**：3.5

**Contact**：Johan Hovold <jhovold@gmail.com>

**Description**：

设置亮度映射模式（0、1），其中

0 - 指数模式

1 - 线性模式

**What**：`/sys/class/leds/<led>/pwm`

**Date**：2012 年 4 月

**KernelVersion**：3.5

**Contact**：Johan Hovold <jhovold@gmail.com>

**Description**：

设置 PWM 输入控制掩码（5 位），其中

第 5 位 - 在区域 4 中启用 PWM 输入

第 4 位 - 在区域 3 中启用 PWM 输入

第 3 位 - 在区域 2 中启用 PWM 输入

第 2 位 - 在区域 1 中启用 PWM 输入

第 1 位 - 在区域 0 中启用 PWM 输入

第 0 位 - 启用 PWM 输入 