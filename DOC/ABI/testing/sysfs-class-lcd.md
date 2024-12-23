**What**: `/sys/class/lcd/<lcd>/lcd_power`

**Date**: April 2005

**KernelVersion**: 2.6.12

**Contact**: Richard Purdie <rpurdie@rpsys.net>

**Description**:

控制 LCD 电源，值为 fb.h 中的 FB_BLANK_*

- FB_BLANK_UNBLANK (0) ：开启电源。

- FB_BLANK_POWERDOWN (4) ：关闭电源。

**What**: `/sys/class/lcd/<lcd>/contrast`

**Date**: April 2005

**KernelVersion**: 2.6.12

**Contact**: Richard Purdie <rpurdie@rpsys.net>

**Description**:

此 LCD 设备的当前对比度。值介于 0 和 `/sys/class/lcd/<lcd>/max_contrast` 之间。

**What**: `/sys/class/lcd/<lcd>/max_contrast`

**Date**: April 2005

**KernelVersion**: 2.6.12

**Contact**: Richard Purdie <rpurdie@rpsys.net>

**Description**:

此 LCD 设备的最大对比度。