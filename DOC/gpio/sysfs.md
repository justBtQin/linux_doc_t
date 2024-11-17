# GPIO 用户空间的 Sysfs 接口
==================================

使用“gpiolib”实现者框架的平台可以选择为 GPIO 配置 Sysfs 用户接口。这与 debugfs 接口不同，因为它提供了对 GPIO 方向和值的控制，而不仅仅是显示 GPIO 状态摘要。此外，它可以在没有调试支持的生产系统中存在。

如果有系统的适当硬件文档，用户空间可以知道例如 GPIO #23 控制用于保护闪存中引导加载程序段的写保护线。系统升级过程可能需要暂时删除该保护，首先导入 GPIO，然后更改其输出状态，然后在重新启用写保护之前更新代码。在正常使用中，GPIO #23 永远不会被触及，内核也不需要知道它。

同样，根据适当的硬件文档，在某些系统上，用户空间 GPIO 可用于确定标准内核不知道的系统配置数据。对于某些任务，简单的用户空间 GPIO 驱动程序可能就是系统真正需要的。

请注意，对于常见的“LED 和按钮”GPIO 任务，存在标准的内核驱动程序：分别为“leds-gpio”和“gpio_keys”。使用它们而不是直接与 GPIO 通信；它们与内核框架的集成比您的用户空间代码更好。

# Sysfs 中的路径
--------------

在 /sys/class/gpio 中有三种类型的条目：

 - 用于获取用户空间对 GPIO 控制的控制接口；

 - GPIO 本身；和

 - GPIO 控制器（“gpio_chip”实例）。

这除了包括“device”符号链接在内的标准文件之外。

控制接口是只写的：

    /sys/class/gpio/

    	“export”... 用户空间可以通过向此文件写入其编号来请求内核将 GPIO 的控制权导出到用户空间。

		示例：“echo 19 > export”将为 GPIO #19 创建一个“gpio19”节点，如果内核代码未请求。

    	“unexport”... 反转导出到用户空间的效果。

		示例：“echo 19 > unexport”将删除使用“export”文件导出的“gpio19”节点。

GPIO 信号的路径类似于 /sys/class/gpio/gpio42/（对于 GPIO #42），并具有以下读写属性：

    /sys/class/gpio/gpioN/

	“direction”... 读取为“in”或“out”。此值通常可以写入。写入为“out”默认为将值初始化为低。为确保无毛刺操作，可以写入“low”和“high”值将 GPIO 配置为具有该初始值的输出。

		请注意，如果内核不支持更改 GPIO 的方向，或者它是由未明确允许用户空间重新配置此 GPIO 方向的内核代码导出的，则此属性将不存在。

	“value”... 读取为 0（低）或 1（高）。如果 GPIO 配置为输出，则可以写入此值；任何非零值都被视为高。

		如果引脚可以配置为生成中断的中断，并且已配置为生成中断（请参阅“edge”的描述），则可以对此文件进行 poll(2)，并且每当触发中断时 poll(2) 将返回。如果使用 poll(2)，请设置事件 POLLPRI 和 POLLERR。如果使用 select(2)，请在 exceptfds 中设置文件描述符。poll(2) 返回后，可以 lseek(2) 到 sysfs 文件的开头并读取新值，或者关闭文件并重新打开它以读取值。

	“edge”... 读取为“none”、“rising”、“falling”或“both”。写入这些字符串以选择将使“value”文件上的 poll(2) 返回的信号边沿。

		此文件仅在引脚可以配置为生成中断的输入引脚时存在。

	“active_low”... 读取为 0（假）或 1（真）。写入任何非零值以反转读和写的“value”属性。对于“rising”和“falling”边沿，现有的和后续的 poll(2) 支持配置通过“edge”属性将遵循此设置。

GPIO 控制器的路径类似于 /sys/class/gpio/gpiochip42/（对于从 #42 开始实现 GPIO 的控制器），并具有以下只读属性：

    /sys/class/gpio/gpiochipN/

    	“base”... 与 N 相同，此芯片管理的第一个 GPIO

    	“label”... 提供用于诊断（不总是唯一的）

    	“ngpio”... 管理多少个 GPIO（N 到 N + ngpio - 1）

大多数情况下，板级文档应涵盖 GPIO 用于何种目的。但是，这些编号并不总是稳定的；子卡上的 GPIO 可能会根据所使用的基板或堆叠中的其他卡而有所不同。在这种情况下，您可能需要使用 gpiochip 节点（可能与原理图结合）来确定给定信号应使用的正确 GPIO 编号。

# 从内核代码导出
--------------------------

内核代码可以显式管理使用 `gpio_request()` 已经请求的 GPIO 的导出：

```c
/* 将 GPIO 导出到用户空间 */
int gpiod_export(struct gpio_desc *desc, bool direction_may_change);

/* 反转 gpio_export() */
void gpiod_unexport(struct gpio_desc *desc);

/* 创建到导出的 GPIO 节点的 sysfs 链接 */
int gpiod_export_link(struct device *dev, const char *name,
                      struct gpio_desc *desc);

/* 在 sysfs 中更改 GPIO 节点的极性 */
int gpiod_sysfs_set_active_low(struct gpio_desc *desc, int value);
```

在内核驱动程序请求 GPIO 后，只有通过 `gpiod_export()` 才能在 sysfs 接口中提供。驱动程序可以控制信号方向是否可以更改。这有助于驱动程序防止用户空间代码意外破坏重要的系统状态。

这种显式导出有助于调试（通过使某些类型的实验更容易），或者可以提供一个始终存在的接口，适合作为板支持包的一部分进行记录。

在 GPIO 已导出后，`gpiod_export_link()` 允许从 sysfs 中的其他位置创建到 GPIO sysfs 节点的符号链接。驱动程序可以使用此功能在其自己的 sysfs 设备下提供具有描述性名称的接口。

驱动程序可以使用 `gpiod_sysfs_set_active_low()` 向用户空间隐藏不同板之间 GPIO 线路极性的差异。极性更改可以在 `gpiod_export()` 之前和之后进行，并且之前为上升或下降边沿启用的 poll(2) 支持将重新配置以遵循此设置。