**What**：`/sys/class/pwm/`

**Date**：2013 年 5 月

**KernelVersion**：3.11

**Contact**：H Hartley Sweeten <hsweeten@visionengravers.com>

**Description**：

`pwm/`类子目录属于通用 PWM 框架，并为使用 PWM 通道提供了 sysfs 接口。

**What**：`/sys/class/pwm/pwmchipN/`

**Date**：2013 年 5 月

**KernelVersion**：3.11

**Contact**：H Hartley Sweeten <hsweeten@visionengravers.com>

**Description**：

对于每个探测到的 PWM 控制器/芯片，都会创建一个`/sys/class/pwm/pwmchipN`目录，其中 N 是 PWM 芯片的基数。

**What**：`/sys/class/pwm/pwmchipN/npwm`

**Date**：2013 年 5 月

**KernelVersion**：3.11

**Contact**：H Hartley Sweeten <hsweeten@visionengravers.com>

**Description**：

PWM 芯片支持的 PWM 通道数量。

**What**：`/sys/class/pwm/pwmchipN/export`

**Date**：2013 年 5 月

**KernelVersion**：3.11

**Contact**：H Hartley Sweeten <hsweeten@visionengravers.com>

**Description**：

从 PWM 芯片导出一个 PWM 通道以进行 sysfs 控制。值在 0 到`/sys/class/pwm/pwmchipN/npwm - 1`之间。

**What**：`/sys/class/pwm/pwmchipN/unexport`

**Date**：2013 年 5 月

**KernelVersion**：3.11

**Contact**：H Hartley Sweeten <hsweeten@visionengravers.com>

**Description**：

取消导出一个 PWM 通道。

**What**：`/sys/class/pwm/pwmchipN/pwmX`

**Date**：2013 年 5 月

**KernelVersion**：3.11

**Contact**：H Hartley Sweeten <hsweeten@visionengravers.com>

**Description**：

对于每个导出的 PWM 通道，都会创建一个`/sys/class/pwm/pwmchipN/pwmX`目录，其中 X 是导出的 PWM 通道编号。

**What**：`/sys/class/pwm/pwmchipN/pwmX/period`

**Date**：2013 年 5 月

**KernelVersion**：3.11

**Contact**：H Hartley Sweeten <hsweeten@visionengravers.com>

**Description**：

以纳秒为单位设置 PWM 信号周期。

**What**：`/sys/class/pwm/pwmchipN/pwmX/duty_cycle`

**Date**：2013 年 5 月

**KernelVersion**：3.11

**Contact**：H Hartley Sweeten <hsweeten@visionengravers.com>

**Description**：

以纳秒为单位设置 PWM 信号的占空比。

**What**：`/sys/class/pwm/pwmchipN/pwmX/polarity`

**Date**：2013 年 5 月

**KernelVersion**：3.11

**Contact**：H Hartley Sweeten <hsweeten@visionengravers.com>

**Description**：

将 PWM 信号的输出极性设置为“正常”或“反向”。

**What**：`/sys/class/pwm/pwmchipN/pwmX/enable`

**Date**：2013 年 5 月

**KernelVersion**：3.11

**Contact**：H Hartley Sweeten <hsweeten@visionengravers.com>

**Description**：

启用/禁用 PWM 信号。0 表示禁用，1 表示启用。