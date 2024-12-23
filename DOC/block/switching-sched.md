在引导时选择 IO 调度程序，使用参数“elevator=deadline”。“noop”和“cfq”（默认值）也可用。目前仅在引导时全局分配 IO 调度程序。

每个 I/O 队列都有一组与之相关的 I/O 调度程序可调参数。这些可调参数控制 I/O 调度程序的工作方式。你可以在以下位置找到这些条目：

/sys/block/<设备>/queue/iosched

假设你在 /sys 上挂载了 sysfs。如果你没有挂载 sysfs，你可以通过输入以下命令来挂载：

`# mount none /sys -t sysfs`

从 Linux 2.6.10 内核开始，现在可以动态更改给定块设备的 IO 调度程序（例如，这使得为系统默认设置 CFQ 调度程序，但将特定设备设置为使用 deadline 或 noop 调度程序成为可能 - 这可以提高该设备的吞吐量）。

要设置特定的调度程序，只需执行以下操作：

echo SCHEDNAME > /sys/block/DEV/queue/scheduler

其中 SCHEDNAME 是已定义的 IO 调度程序的名称，DEV 是设备名称（hda、hdb、sga 或你拥有的任何设备）。

可以通过简单地执行“cat /sys/block/DEV/queue/scheduler”来找到已定义调度程序的列表 - 将显示有效名称的列表，当前选择的调度程序用括号括起来：

`# cat /sys/block/hda/queue/scheduler`

noop deadline [cfq]

`# echo deadline > /sys/block/hda/queue/scheduler`

`# cat /sys/block/hda/queue/scheduler`

noop [deadline] cfq