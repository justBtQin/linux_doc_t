**What**：`/sys/devices/.../power/`

**Date**：2009 年 1 月

**Contact**：Rafael J. Wysocki <rjw@rjwysocki.net>

**Description**：

`/sys/devices/.../power` 目录包含允许用户空间检查和修改给定设备的一些电源管理相关属性的属性。

**What**：`/sys/devices/.../power/wakeup`

**Date**：2009 年 1 月

**Contact**：Rafael J. Wysocki <rjw@rjwysocki.net>

**Description**：

`/sys/devices/.../power/wakeup` 属性允许用户空间检查设备是否被启用从睡眠状态（如内存睡眠状态（挂起到内存）和休眠（挂起到磁盘））唤醒系统，并根据需要启用或禁用它。

一些设备支持“唤醒”事件，这些事件是用于从睡眠状态激活系统的硬件信号。对于此类设备，sysfs `power/wakeup` 文件具有以下两个值之一：

+ "Enabled\n" 以发出事件；

+ "Disabled\n" 不发出事件；

在这种情况下，用户空间可以通过向此文件写入"Enabled"或"Disabled"来更改此文件内容所表示的设置。

对于无法生成系统唤醒事件的设备，此文件不存在。在这种情况下，设备无法从睡眠状态唤醒系统。

**What**：`/sys/devices/.../power/control`

**Date**：2009 年 1 月

**Contact**：Rafael J. Wysocki <rjw@rjwysocki.net>

**Description**：

`/sys/devices/.../power/control` 属性允许用户空间控制设备的运行时电源管理。

所有设备的 `power/control` 文件具有以下两个值之一：

+ "auto\n" 允许在运行时对设备进行电源管理；

+ "on\n" 防止设备进行电源管理；

所有设备的默认值均为"auto"，这意味着它们可能会根据其驱动程序进行自动电源管理。将此属性更改为"on"可防止驱动程序在运行时对设备进行电源管理。在设备挂起时进行此操作会导致其被唤醒。

**What**：`/sys/devices/.../power/async`

**Date**：2009 年 1 月

**Contact**：Rafael J. Wysocki <rjw@rjwysocki.net>

**Description**：

`/sys/devices/.../async` 属性允许用户空间启用或禁用设备在系统范围的电源转换（例如挂起到内存、休眠）期间的挂起和恢复回调异步执行（即在单独的线程中，与主挂起/恢复线程并行）。

所有设备的 `power/async` 文件具有以下两个值之一：

+ "Enabled\n" 允许异步挂起/恢复；

+ "Disabled\n" 禁止；

可以通过向该文件写入"Enabled"或"Disabled"来更改此属性的值。

通常，除非确定设备的所有 PM 依赖项都为 PM 核心所知，否则允许设备异步挂起/恢复是不安全的。但是，对于某些设备，此属性由总线类型代码或设备驱动程序设置为"Enabled"，在这种情况下，保留默认值应该是安全的。

**What**：`/sys/devices/.../power/wakeup_count`

**Date**：2010 年 9 月

**Contact**：Rafael J. Wysocki <rjw@rjwysocki.net>

**Description**：

`/sys/devices/.../wakeup_count` 属性包含与设备相关的已发出唤醒事件的数量。此属性为只读。如果设备无法从睡眠状态唤醒系统，则此属性不存在。如果设备未启用从睡眠状态唤醒系统，则此属性为空。

**What**：`/sys/devices/.../power/wakeup_active_count`

**Date**：2010 年 9 月

**Contact**：Rafael J. Wysocki <rjw@rjwysocki.net>

**Description**：

`/sys/devices/.../wakeup_active_count` 属性包含与设备相关的唤醒事件处理完成的次数（在内核级别）。此属性为只读。如果设备无法从睡眠状态唤醒系统，则此属性不存在。如果设备未启用从睡眠状态唤醒系统，则此属性为空。

**What**：`/sys/devices/.../power/wakeup_abort_count`

**Date**：2012 年 2 月

**Contact**：Rafael J. Wysocki <rjw@rjwysocki.net>

**Description**：

`/sys/devices/.../wakeup_abort_count` 属性包含与设备相关的唤醒事件处理可能导致系统正在进行的睡眠状态转换中止的次数。此属性为只读。如果设备无法从睡眠状态唤醒系统，则此属性不存在。如果设备未启用从睡眠状态唤醒系统，则此属性为空。

**What**：`/sys/devices/.../power/wakeup_expire_count`

**Date**：2012 年 2 月

**Contact**：Rafael J. Wysocki <rjw@rjwysocki.net>

**Description**：

`/sys/devices/.../wakeup_expire_count` 属性包含与设备相关的唤醒事件报告的超时次数。此属性为只读。如果设备无法从睡眠状态唤醒系统，则此属性不存在。如果设备未启用从睡眠状态唤醒系统，则此属性为空。

**What**：`/sys/devices/.../power/wakeup_active`

**Date**：2010 年 9 月

**Contact**：Rafael J. Wysocki <rjw@rjwysocki.net>

**Description**：

`/sys/devices/.../power/wakeup_active` 属性包含 1 或 0，具体取决于是否正在处理与设备相关的唤醒事件（1）。此属性为只读。如果设备无法从睡眠状态唤醒系统，则此属性不存在。如果设备未启用从睡眠状态唤醒系统，则此属性为空。

**What**：`/sys/devices/.../power/wakeup_total_time_ms`

**Date**：2010 年 9 月

**Contact**：Rafael J. Wysocki <rjw@rjwysocki.net>

**Description**：

`/sys/devices/.../wakeup_total_time_ms` 属性包含处理与设备相关的唤醒事件的总时间（以毫秒为单位）。此属性为只读。如果设备无法从睡眠状态唤醒系统，则此属性不存在。如果设备未启用从睡眠状态唤醒系统，则此属性为空。

**What**：`/sys/devices/.../power/wakeup_max_time_ms`

**Date**：2010 年 9 月

**Contact**：Rafael J. Wysocki <rjw@rjwysocki.net>

**Description**：

`/sys/devices/.../wakeup_max_time_ms` 属性包含处理与设备相关的单个唤醒事件的最大时间（以毫秒为单位）。此属性为只读。如果设备无法从睡眠状态唤醒系统，则此属性不存在。如果设备未启用从睡眠状态唤醒系统，则此属性为空。

**What**：`/sys/devices/.../power/wakeup_last_time_ms`

**Date**：2010 年 9 月

**Contact**：Rafael J. Wysocki <rjw@rjwysocki.net>

**Description**：

`/sys/devices/.../wakeup_last_time_ms` 属性包含与设备相关的最后一个唤醒事件发出信号时对应的单调时钟值（以毫秒为单位）。此属性为只读。如果设备未启用从睡眠状态唤醒系统，则此属性不存在。如果设备未启用从睡眠状态唤醒系统，则此属性为空。

**What**：`/sys/devices/.../power/wakeup_prevent_sleep_time_ms`

**Date**：2012 年 2 月

**Contact**：Rafael J. Wysocki <rjw@rjwysocki.net>

**Description**：

`/sys/devices/.../wakeup_prevent_sleep_time_ms` 属性包含设备阻止机会性转换到睡眠状态发生的总时间。此属性为只读。如果设备无法从睡眠状态唤醒系统，则此属性不存在。如果设备未启用从睡眠状态唤醒系统，则此属性为空。

**What**：`/sys/devices/.../power/autosuspend_delay_ms`

**Date**: September 2010

**Contact**: Alan Stern <stern@rowland.harvard.edu>

**Description**:

该 `/sys/devices/.../power/autosuspend_delay_ms` 属性包含自动暂停延迟值（以毫秒为单位）。一些驱动程序在运行时设备变为空闲时不希望立即暂停；它们希望设备首先在一定的最小时间内保持不活动状态。该时间段称为自动暂停延迟。负值将阻止设备在运行时暂停（类似于将“on”写入 power/control 属性）。值 >= 1000 将导致自动暂停计时器到期向上舍入到最接近的秒。

并非所有驱动程序都支持此属性。如果不支持，尝试读取或写入它将产生 I/O 错误。

**What**: `/sys/devices/.../power/pm_qos_resume_latency_us`

**Date**: March 2012

**Contact**: Rafael J. Wysocki <rjw@rjwysocki.net>

**Description**:

该 `/sys/devices/.../power/pm_qos_resume_latency_us` 属性包含给定设备的 PM QoS 恢复延迟限制，即在运行时设备被暂停后，从恢复请求到设备准备好处理 I/O 的时刻之间，允许的最大恢复延迟时间，以微秒为单位。然而，如果它等于 0，则意味着 PM QoS 恢复延迟可以是任意的。

并非所有驱动程序都支持此属性。如果不支持，它不存在。

此属性对系统范围的暂停/恢复和休眠没有影响。

**What**: `/sys/devices/.../power/pm_qos_latency_tolerance_us`

**Date**: January 2014

**Contact**: Rafael J. Wysocki <rjw@rjwysocki.net>

**Description**:

该 `/sys/devices/.../power/pm_qos_latency_tolerance_us` 属性包含给定设备的 PM QoS 活动状态延迟容忍限制，以微秒为单位。即设备在对用户空间功能没有任何可见不利影响的情况下可以承受的最大内存访问延迟。如果该值是字符串“any”，则延迟对用户空间根本不重要，但硬件不应允许自动为设备设置延迟容忍度。

从该文件读取“auto”意味着设备的最大内存访问延迟可以根据需要由硬件自动确定。将“auto”写入它允许硬件在内核方面没有其他延迟容忍要求的情况下切换到此模式。

此属性仅在硬件支持它控制的功能时存在。

此属性对设备的运行时暂停和恢复以及系统范围的暂停/恢复和休眠没有影响。

**What**: `/sys/devices/.../power/pm_qos_no_power_off`

**Date**: September 2012

**Contact**: Rafael J. Wysocki <rjw@rjwysocki.net>

**Description**:

该 `/sys/devices/.../power/pm_qos_no_power_off` 属性用于操作 PM QoS“不关闭电源”标志。如果设置，此标志向内核指示不应完全从设备中移除电源。

并非所有驱动程序都支持此属性。如果不支持，它不存在。

此属性对系统范围的暂停/恢复和休眠没有影响。

**What**: `/sys/devices/.../power/pm_qos_remote_wakeup`

**Date**: September 2012

**Contact**: Rafael J. Wysocki <rjw@rjwysocki.net>

**Description**:

该 `/sys/devices/.../power/pm_qos_remote_wakeup` 属性用于操作 PM QoS“需要远程唤醒”标志。如果设置，此标志向内核指示该设备是必须从其低功耗状态发出信号的用户事件的源。

并非所有驱动程序都支持此属性。如果不支持，它不存在。

此属性对系统范围的暂停/恢复和休眠没有影响。