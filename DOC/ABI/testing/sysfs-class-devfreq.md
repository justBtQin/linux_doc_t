**What**: `/sys/class/devfreq/.../`

 - **Date**: 2011 年 9 月

 - **Contact**: MyungJoo Ham <myungjoo.ham@samsung.com>

 - **Description**: 在 sysfs 中为 devfreq 对象提供一个位置。这允许访问各种特定于 devfreq 的变量。表示为...的 devfreq 对象的名称与使用 devfreq 的设备的名称相同。

**What**: `/sys/class/devfreq/.../governor`

 - **Date**: 2011 年 9 月

 - **Contact**: MyungJoo Ham <myungjoo.ham@samsung.com>

 - **Description**: `/sys/class/devfreq/.../governor` 显示或设置相应 devfreq 对象使用的调控器的名称。

**What**: `/sys/class/devfreq/.../cur_freq`

 - **Date**: 2011 年 9 月

 - **Contact**: MyungJoo Ham <myungjoo.ham@samsung.com>

 - **Description**: `/sys/class/devfreq/.../cur_freq` 显示相应 devfreq 对象的当前频率。当 devfreq 驱动程序未实现 `get_cur_freq()` 时，与 `target_freq` 相同。

**What**: `/sys/class/devfreq/.../target_freq`

 - **Date**: 2012 年 9 月

 - **Contact**: Rajagopal Venkat <rajagopal.venkat@linaro.org>

 - **Description**: `/sys/class/devfreq/.../target_freq` 显示相应 devfreq 对象的下一个调控器预测的目标频率。

**What**: `/sys/class/devfreq/.../polling_interval`

 - **Date**: 2011 年 9 月

 - **Contact**: MyungJoo Ham <myungjoo.ham@samsung.com>

 - **Description**: `/sys/class/devfreq/.../polling_interval` 显示和设置相应 devfreq 对象的请求轮询间隔。值以毫秒为单位。如果值小于 1 个节拍，将其视为 0，表示不进行轮询。如果调控器不是轮询的，此值可能没有意义；因此，如果调控器未使用 devfreq 提供的中央轮询（`/sys/class/devfreq/.../central_polling` 为 0），此值可能无用。

**What**: `/sys/class/devfreq/.../trans_stat`

 - **Date**: 2012 年 10 月

 - **Contact**: MyungJoo Ham <myungjoo.ham@samsung.com>

 - **Descrtiption**: 此 ABI 显示特定设备上 devfreq 行为的统计信息。它显示在每个状态下花费的时间以及状态之间的转换次数。为了激活此 ABI，devfreq 目标设备驱动程序应使用其配置文件提供可用频率列表。

**What**: `/sys/class/devfreq/.../userspace/set_freq`

 - **Date**: 2011 年 9 月

 - **Contact**: MyungJoo Ham <myungjoo.ham@samsung.com>

 - **Description**: `/sys/class/devfreq/.../userspace/set_freq` 显示并设置如果用户空间调控器生效，则 devfreq 对象的请求频率。

**What**: `/sys/class/devfreq/.../available_frequencies`

 - **Date**: 2012 年 10 月

 - **Contact**: Nishanth Menon <nm@ti.com>

 - **Description**: `/sys/class/devfreq/.../available_frequencies` 显示相应 devfreq 对象的可用频率。这是可用频率的快照，不受最小/最大频率限制。

**What**: `/sys/class/devfreq/.../available_governors`

 - **Date**: 2012 年 10 月

 - **Contact**: Nishanth Menon <nm@ti.com>

 - **Description**: `/sys/class/devfreq/.../available_governors` 显示系统中当前可用的调控器。

**What**: `/sys/class/devfreq/.../min_freq`

 - **Date**: 2013 年 1 月

 - **Contact**: MyungJoo Ham <myungjoo.ham@samsung.com>

 - **Description**: `/sys/class/devfreq/.../min_freq` 显示并存储用户请求的最小频率。如果用户不关心，则为 0。`min_freq` 覆盖调控器请求的频率。

**What**: `/sys/class/devfreq/.../max_freq`

 - **Date**: 2013 年 1 月

 - **Contact**: MyungJoo Ham <myungjoo.ham@samsung.com>

 - **Description**: `/sys/class/devfreq/.../max_freq` 显示并存储用户请求的最大频率。如果用户不关心，则为 0。`max_freq` 覆盖调控器和 `min_freq` 请求的频率。`max_freq` 覆盖 `min_freq`，因为 `max_freq` 可能用于限制设备以避免过热。