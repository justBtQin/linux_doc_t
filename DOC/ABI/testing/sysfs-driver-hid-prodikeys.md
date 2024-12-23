**What**: `/sys/bus/hid/drivers/prodikeys/.../channel`

**Date**: April 2010

**KernelVersion**: 2.6.34

**Contact**: Don Prince <dhprince.devel@yahoo.co.uk>

**Description**: 允许通过软件控制 pc-midi 键盘将输出 midi 数据的 midi 通道。范围：0..15，类型：读/写

**What**: `/sys/bus/hid/drivers/prodikeys/.../sustain`

**Date**: April 2010

**KernelVersion**: 2.6.34

**Contact**: Don Prince <dhprince.devel@yahoo.co.uk>

**Description**: 允许通过软件控制 pc-midi 驱动程序所保持音符的延音持续时间。0 表示延音模式已禁用。范围：0..5000（毫秒），类型：读/写

**What**: `/sys/bus/hid/drivers/prodikeys/.../octave`

**Date**: April 2010

**KernelVersion**: 2.6.34

**Contact**: Don Prince <dhprince.devel@yahoo.co.uk>

**Description**: 控制 pc-midi 驱动程序中的八度音移调修饰符。可以通过软件将八度音向上/向下移调 2 个八度。0 表示无八度音移调。范围：-2..2（负 2 到正 2），类型：读/写