**/sys/class/rc/**

- **What**: 系统类目录

- **Date**: 2010 年 4 月

- **KernelVersion**: 2.6.35

- **Contact**: Mauro Carvalho Chehab <m.chehab@samsung.com>

- **Description**: rc/类子目录属于远程控制器核心，并提供用于配置红外远程控制器接收器的 sysfs 接口。

**/sys/class/rc/rcN/**

- **What**: 每个远程控制接收器设备的目录

- **Date**: 2010 年 4 月

- **KernelVersion**: 2.6.35

- **Contact**: Mauro Carvalho Chehab <m.chehab@samsung.com>

- **Description**: 为每个远程控制接收器设备创建一个 /sys/class/rc/rcN 目录，其中 N 是接收器的编号。

**/sys/class/rc/rcN/protocols**

- **What**: 协议文件

- **Date**: 2010 年 6 月

- **KernelVersion**: 2.6.36

- **Contact**: Mauro Carvalho Chehab <m.chehab@samsung.com>

- **Description**: 读取此文件将返回可用协议的列表，例如："rc5 [rc6] nec jvc [sony]"。方括号中的协议为启用的协议。写入 "+proto" 将向启用的协议列表中添加一个协议。写入 "-proto" 将从启用的协议列表中删除一个协议。写入 "proto" 将仅启用 "proto"。写入 "none" 将禁用所有协议。如果使用无效的协议组合或未知的协议名称，写入将失败并返回 EINVAL。

**/sys/class/rc/rcN/filter**

- **What**: 扫描码过滤器期望值文件

- **Date**: 2014 年 1 月

- **KernelVersion**: 3.15

- **Contact**: Mauro Carvalho Chehab <m.chehab@samsung.com>

- **Description**: 与 /sys/class/rc/rcN/filter_mask 结合使用以设置过滤器掩码中设置的位的期望值。如果硬件支持，则不匹配过滤器的扫描码将被忽略。否则，写入将失败并返回错误。如果当前协议更改，此值可以重置为 0。

**/sys/class/rc/rcN/filter_mask**

- **What**: 扫描码过滤器掩码文件

- **Date**: 2014 年 1 月

- **KernelVersion**: 3.15

- **Contact**: Mauro Carvalho Chehab <m.chehab@samsung.com>

- **Description**: 与 /sys/class/rc/rcN/filter 结合使用以设置要与期望值进行比较的扫描码的位。值为 0 时禁用过滤器以允许处理所有有效扫描码。如果硬件支持，则不匹配过滤器的扫描码将被忽略。否则，写入将失败并返回错误。如果当前协议更改，此值可以重置为 0。

**/sys/class/rc/rcN/wakeup_protocols**

- **What**: 唤醒协议文件

- **Date**: 2014 年 2 月

- **KernelVersion**: 3.15

- **Contact**: Mauro Carvalho Chehab <m.chehab@samsung.com>

- **Description**: 读取此文件将返回可用于唤醒过滤器的可用协议列表，例如："rc5 rc6 nec jvc [sony]"。方括号中的协议为启用的唤醒协议。写入 "+proto" 将向启用的唤醒协议列表中添加一个协议。写入 "-proto" 将从启用的唤醒协议列表中删除一个协议。写入 "proto" 将使用 "proto" 进行唤醒事件。写入 "none" 将禁用唤醒。如果使用无效的协议组合或未知的协议名称，或者硬件不支持唤醒，写入将失败并返回 EINVAL。

**/sys/class/rc/rcN/wakeup_filter**

- **What**: 扫描码唤醒过滤器期望值文件

- **Date**: 2014 年 1 月

- **KernelVersion**: 3.15

- **Contact**: Mauro Carvalho Chehab <m.chehab@samsung.com>

- **Description**: 与 /sys/class/rc/rcN/wakeup_filter_mask 结合使用以设置要与唤醒过滤器掩码中设置的位的期望值，以触发系统唤醒事件。如果硬件支持并且 wakeup_filter_mask 不为 0，则匹配过滤器的扫描码将使系统从例如挂起到内存或关机中唤醒。否则，写入将失败并返回错误。如果唤醒协议更改，此值可以重置为 0。

**/sys/class/rc/rcN/wakeup_filter_mask**

- **What**: 扫描码唤醒过滤器掩码文件

- **Date**: 2014 年 1 月

- **KernelVersion**: 3.15

- **Contact**: Mauro Carvalho Chehab <m.chehab@samsung.com>

- **Description**: 与 /sys/class/rc/rcN/wakeup_filter 结合使用以设置要与期望值进行比较的扫描码的位，以触发系统唤醒事件。如果硬件支持并且 wakeup_filter_mask 不为 0，则匹配过滤器的扫描码将使系统从例如挂起到内存或关机中唤醒。否则，写入将失败并返回错误。如果唤醒协议更改，此值可以重置为 0。