**/sys/firmware/acpi/bgrt/**

日期：2012 年 1 月

联系人：Matthew Garrett <mjg@redhat.com>

描述：

BGRT 是 ACPI 5.0 的一个功能，它允许操作系统获取固件启动画面的副本以及一些相关的元数据。这旨在被启动画面应用程序使用，以便与固件启动画面进行交互，避免突兀的过渡。

- `image`：图像位图。目前是 32 位 BMP。

- `status`：如果图像有效则为 1，若固件使它无效则为 0。

- `type`：0 表示图像为 BMP 格式。

- `version`：BGRT 的版本。目前为 1。

- `xoffset`：屏幕左侧与图像左边缘之间的像素数。

- `yoffset`：屏幕顶部与图像上边缘之间的像素数。

**/sys/firmware/acpi/hotplug/**

日期：2013 年 2 月

联系人：Rafael J. Wysocki <rafael.j.wysocki@intel.com>

描述：

对于 ACPI 支持的不同类别的设备，有单独的热插拔配置文件，如容器、内存模块、处理器、PCI 根桥等。给定类别的设备的热插拔配置文件是一组定义该类设备将由 ACPI 核心热插拔代码处理方式的设置。这些配置文件在 sysfs 中表示为 /sys/firmware/acpi/hotplug/ 的子目录。

对于每个热插拔配置文件，用户空间可用的以下设置：

`enabled`：如果设置，ACPI 核心将处理与给定类别的设备相关的热插拔事件通知，并允许通过 _EJ0 控制方法弹出这些设备。取消设置它将有效地禁用相应类别的设备的热插拔。

上述属性的值是一个整数值：1（设置）或 0（未设置）。尝试写入其他值将导致 -EINVAL 被返回。

**/sys/firmware/acpi/hotplug/force_remove**

日期：2013 年 5 月

联系人：Rafael J. Wysocki <rafael.j.wysocki@intel.com>

描述：

此文件中的数字（0 或 1）确定 ACPI 子系统是否（1）或不（0）允许设备热移除，即使它们无法从内核的角度优雅地离线。可以通过向此文件写入布尔值来更改该数字。

**/sys/firmware/acpi/interrupts/**

日期：2008 年 2 月

联系人：Len Brown <lenb@kernel.org>

描述：

所有 ACPI 中断都通过单个 IRQ（系统控制中断（SCI））处理，该中断在 /proc/interrupts 中显示为“acpi”。

然而，ACPI 的主要功能之一是使平台在没有特殊驱动程序支持的情况下理解随机硬件。因此，虽然 SCI 处理一些众所周知的（固定功能）中断源，如电源按钮，但它也可以处理可变数量的“通用事件”（GPE）。

GPE 指向 AML 中的指定处理程序，该处理程序可以根据 BIOS 编写者的意愿在 OS 上下文中执行任何操作。例如，GPE 0x12 将指向称为 _L12 或 _E12 的电平或边缘处理程序。

处理程序可以执行其业务并返回。或者，处理程序可以向在 ACPI 设备（如电池或处理器）上注册的 Linux 设备驱动程序发送通知事件。

要确定所有 SCI 的来源，/sys/firmware/acpi/interrupts 包含一个文件，列出每个可能的源以及触发的次数。

```
cd /sys/firmware/acpi/interrupts
grep. *
error:	     0
ff_gbl_lock:	   0   enable
ff_pmtimer:	  0  invalid
ff_pwr_btn:	  0   enable
ff_rt_clk:	 2  disable
ff_slp_btn:	  0  invalid
gpe00:	     0	invalid
gpe01:	     0	 enable
gpe02:	   108	 enable
gpe03:	     0	invalid
gpe04:	     0	invalid
gpe05:	     0	invalid
gpe06:	     0	 enable
gpe07:	     0	 enable
gpe08:	     0	invalid
gpe09:	     0	invalid
gpe0A:	     0	invalid
gpe0B:	     0	invalid
gpe0C:	     0	invalid
gpe0D:	     0	invalid
gpe0E:	     0	invalid
gpe0F:	     0	invalid
gpe10:	     0	invalid
gpe11:	     0	invalid
gpe12:	     0	invalid
gpe13:	     0	invalid
gpe14:	     0	invalid
gpe15:	     0	invalid
gpe16:	     0	invalid
gpe17:	  1084	 enable
gpe18:	     0	 enable
gpe19:	     0	invalid
gpe1A:	     0	invalid
gpe1B:	     0	invalid
gpe1C:	     0	invalid
gpe1D:	     0	invalid
gpe1E:	     0	invalid
gpe1F:	     0	invalid
gpe_all:    1192
sci:	1194
sci_not:     0
```
`sci`：ACPI SCI 被调用并声明中断的次数。

`sci_not`：ACPI SCI 被调用但未声明中断的次数。

`gpe_all`：GPE 导致的 SCI 计数。

`gpeXX`：单个 GPE 源的计数。

`ff_gbl_lock`：全局锁。

`ff_pmtimer`：PM 定时器。

`ff_pwr_btn`：电源按钮。

`ff_rt_clk`：实时时钟。

`ff_slp_btn`：睡眠按钮。

`error`：上述无法解释的中断。

`invalid`：它是一个 GPE 或固定事件，没有事件处理程序。

`disable`：GPE/固定事件有效但已禁用。

`enable`：GPE/固定事件有效且已启用。

根具有清除这些计数器的权限。例如：

```
# echo 0 > gpe11
```
可以通过清除总的“sci”来清除所有计数器：

```
# echo 0 > sci
```
这些计数器都不会影响系统的功能，它们只是统计信息。

此外，用户还可以向这些文件写入特定的字符串，以在用户空间启用/禁用/清除 ACPI 中断，可用于调试一些 ACPI 中断风暴问题。

注意，只允许写入有效的 GPE/固定事件，即用户只能通过安装事件处理程序来更改运行时 GPE 和固定事件的状态。

以电源按钮固定事件为例，请杀死 acpid 和其他用户空间应用程序，以便在按下电源按钮时机器不会关机。

```
# cat ff_pwr_btn
0	enabled
```
按下电源按钮 3 次；

```
# cat ff_pwr_btn
3	enabled
```
```
# echo disable > ff_pwr_btn
```
```
# cat ff_pwr_btn
3	disabled
```
按下电源按钮 3 次；

```
# cat ff_pwr_btn
3	disabled
```
```
# echo enable > ff_pwr_btn
```
```
# cat ff_pwr_btn
4	enabled
```
/*

这是因为即使启用位被清除，状态位也会被设置，并且当再次设置启用位时会触发 ACPI 固定事件

*/

按下电源按钮 3 次；

```
# cat ff_pwr_btn
7	enabled
```
```
# echo disable > ff_pwr_btn
```
按下电源按钮 3 次；

```
# echo clear > ff_pwr_btn  /* 清除状态位 */
```
```
# echo disable > ff_pwr_btn
```
```
# cat ff_pwr_btn
7	enabled
```