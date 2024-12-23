**What**: /sys/class/leds/dell::kbd_backlight/als_enabled

**Date**: December 2014

**KernelVersion**: 3.19

**Contact**: Gabriele Mazzotta <gabriele.mzt@gmail.com>, Pali Rohár <pali.rohar@gmail.com>

**Description**: 此文件可用于控制某些具有环境光传感器的系统上的自动键盘照明模式。向此文件写入 1 以启用自动模式，写入 0 以禁用它。

**What**: /sys/class/leds/dell::kbd_backlight/als_setting

**Date**: December 2014

**KernelVersion**: 3.19

**Contact**: Gabriele Mazzotta <gabriele.mzt@gmail.com>, Pali Rohár <pali.rohar@gmail.com>

**Description**: 此文件可用于指定环境光传感器报告的开/关阈值。

**What**: /sys/class/leds/dell::kbd_backlight/start_triggers

**Date**: December 2014

**KernelVersion**: 3.19

**Contact**: Gabriele Mazzotta <gabriele.mzt@gmail.com>, Pali Rohár <pali.rohar@gmail.com>

**Description**: 此文件可用于控制因不活动而禁用的键盘背光照明的输入触发器。读取该文件以查看可用的触发器。已启用的触发器前面有“+”，已禁用的触发器前面有“-”。要启用一个触发器，将其名称前面加上“+”写入此文件。要禁用一个触发器，将其名称前面加上“-”写入此文件。例如，要启用键盘作为触发器，请运行：

`echo +keyboard > /sys/class/leds/dell::kbd_backlight/start_triggers`

要禁用它：

`echo -keyboard > /sys/class/leds/dell::kbd_backlight/start_triggers`

注意，并非所有可用的触发器都可以配置。

**What**: /sys/class/leds/dell::kbd_backlight/stop_timeout

**Date**: December 2014

**KernelVersion**: 3.19

**Contact**: Gabriele Mazzotta <gabriele.mzt@gmail.com>, Pali Rohár <pali.rohar@gmail.com>

**Description**: 此文件可用于指定因不活动而禁用键盘照明的时间间隔。时间以秒、分钟、小时和天为单位表示，分别用“s”、“m”、“h”和“d”表示。要配置超时时间，请向此文件写入一个值以及上述任何单位。如果未指定单位，则假定值以秒为单位。例如，要将超时时间设置为 10 分钟，请运行：

`echo 10m > /sys/class/leds/dell::kbd_backlight/stop_timeout`

注意，当读取此文件时，返回的值可能以与设置超时时间时不同的单位表示。此外，仅支持某些超时时间，并且某些系统在向此文件写入无效超时时间时可能会回退到特定的超时时间。