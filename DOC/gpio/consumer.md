# GPIO 描述符消费者接口
==================================

本文档描述了 GPIO 框架的消费者接口。请注意，它描述的是新的基于描述符的接口。有关已弃用的基于整数的 GPIO 接口的描述，请参阅 `gpio-legacy.txt`。

# GPIO 消费者的指南
==============================

如果没有标准的 GPIO 调用就无法工作的驱动程序，其 Kconfig 条目应依赖于 `GPIOLIB`。允许驱动程序获取和使用 GPIO 的函数可以通过包含以下文件来使用：

```c
#include <linux/gpio/consumer.h>
```

所有基于描述符的 GPIO 接口的函数都以 `gpiod_` 为前缀。`gpio_` 前缀用于旧接口。内核中的其他函数不应使用这些前缀。

# 获取和释放 GPIO
=============================

在基于描述符的接口中，GPIO 通过一个不透明的、不可伪造的处理程序来标识，必须通过调用 `gpiod_get()` 函数之一来获取。与许多其他内核子系统一样，`gpiod_get()` 接受将使用 GPIO 的设备以及请求的 GPIO 应实现的功能：

```c
struct gpio_desc *gpiod_get(struct device *dev, const char *con_id,
                            enum gpiod_flags flags)
```

如果一个功能是通过一起使用多个 GPIO 实现的（例如，一个简单的显示数字的 LED 设备），则可以指定一个附加的索引参数：

```c
struct gpio_desc *gpiod_get_index(struct device *dev,
                                  const char *con_id, unsigned int idx,
                                  enum gpiod_flags flags)
```

`flags` 参数用于可选地指定 GPIO 的方向和初始值。值可以是：

* `GPIOD_ASIS` 或 0 表示根本不初始化 GPIO。方向必须稍后使用专用函数之一设置。
* `GPIOD_IN` 初始化 GPIO 为输入。
* `GPIOD_OUT_LOW` 初始化 GPIO 为输出，值为 0。
* `GPIOD_OUT_HIGH` 初始化 GPIO 为输出，值为 1。

这两个函数要么返回有效的 GPIO 描述符，要么返回可使用 `IS_ERR()` 检查的错误代码（它们永远不会返回 `NULL` 指针）。当且仅当没有为设备/功能/索引三元组分配 GPIO 时，将返回 `-ENOENT`，其他错误代码用于分配了 GPIO 但获取时发生错误的情况。这对于区分单纯的错误和可选 GPIO 参数不存在 GPIO 很有用。对于 GPIO 是可选的常见模式，可以使用 `gpiod_get_optional()` 和 `gpiod_get_index_optional()` 函数。如果没有为请求的功能分配 GPIO，这些函数将返回 `NULL` 而不是 `-ENOENT`：

```c
struct gpio_desc *gpiod_get_optional(struct device *dev,
                                     const char *con_id,
                                     enum gpiod_flags flags)

struct gpio_desc *gpiod_get_index_optional(struct device *dev,
                                           const char *con_id,
                                           unsigned int index,
                                           enum gpiod_flags flags)
```

对于使用多个 GPIO 的功能，可以通过一次调用获取所有这些 GPIO：

```c
struct gpio_descs *gpiod_get_array(struct device *dev,
                                   const char *con_id,
                                   enum gpiod_flags flags)
```

此函数返回一个 `struct gpio_descs`，其中包含一个描述符数组：

```c
struct gpio_descs {
    unsigned int ndescs;
    struct gpio_desc *desc[];
}
```

如果没有为请求的功能分配 GPIO，以下函数将返回 `NULL`：

```c
struct gpio_descs *gpiod_get_array_optional(struct device *dev,
                                            const char *con_id,
                                            enum gpiod_flags flags)
```

还定义了这些函数的设备管理变体：

```c
struct gpio_desc *devm_gpiod_get(struct device *dev, const char *con_id,
                                 enum gpiod_flags flags)

struct gpio_desc *devm_gpiod_get_index(struct device *dev,
                                       const char *con_id,
                                       unsigned int idx,
                                       enum gpiod_flags flags)

struct gpio_desc *devm_gpiod_get_optional(struct device *dev,
                                          const char *con_id,
                                          enum gpiod_flags flags)

struct gpio_desc *devm_gpiod_get_index_optional(struct device *dev,
                                                const char *con_id,
                                                unsigned int index,
                                                enum gpiod_flags flags)

struct gpio_descs *devm_gpiod_get_array(struct device *dev,
                                       const char *con_id,
                                       enum gpiod_flags flags)

struct gpio_descs *devm_gpiod_get_array_optional(struct device *dev,
                                                  const char *con_id,
                                                  enum gpiod_flags flags)
```

可以使用 `gpiod_put()` 函数释放 GPIO 描述符：

```c
void gpiod_put(struct gpio_desc *desc)
```

对于 GPIO 数组，可以使用此函数：

```c
void gpiod_put_array(struct gpio_descs *descs)
```

调用这些函数后，严格禁止使用描述符。也不允许从使用 `gpiod_get_array()` 获取的数组中单独释放描述符（使用 `gpiod_put()`）。

设备管理变体如下，不出所料：

```c
void devm_gpiod_put(struct device *dev, struct gpio_desc *desc)

void devm_gpiod_put_array(struct device *dev, struct gpio_descs *descs)
```

# 使用 GPIO
===========

# 设置方向
-----------------
驱动程序对 GPIO 要做的第一件事是设置其方向。如果没有向 `gpiod_get*()` 提供方向设置标志，则通过调用以下 `gpiod_direction_*()` 函数之一来完成：

```c
int gpiod_direction_input(struct gpio_desc *desc)
int gpiod_direction_output(struct gpio_desc *desc, int value)
```

返回值为 0 表示成功，否则为负的 `errno`。应进行检查，因为获取/设置调用不返回错误，并且可能存在错误配置。您通常应从任务上下文中发出这些调用。但是，对于自旋锁安全的 GPIO，在启用任务之前（作为早期主板设置的一部分）使用它们是可以的。

对于输出 GPIO，提供的值将成为初始输出值。这有助于避免系统启动期间的信号抖动。

驱动程序还可以查询 GPIO 的当前方向：

```c
int gpiod_get_direction(const struct gpio_desc *desc)
```

此函数将返回 `GPIOF_DIR_IN` 或 `GPIOF_DIR_OUT`。

请注意，GPIO 没有默认方向。因此，**在未首先设置其方向的情况下使用 GPIO 是非法的，并将导致未定义的行为！**

# 自旋锁安全的 GPIO 访问
-------------------------
大多数 GPIO 控制器可以通过内存读写指令访问。这些不需要睡眠，并且可以从内部硬（非线程）IRQ 处理程序和类似的上下文中安全地完成。

使用以下调用从原子上下文访问 GPIO：

```c
int gpiod_get_value(const struct gpio_desc *desc);
void gpiod_set_value(struct gpio_desc *desc, int value);
```

值为布尔值，0 表示低，非零表示高。读取输出引脚的值时，返回的值应该是引脚上看到的值。这并不总是与指定的输出值匹配，因为存在诸如开漏信号和输出延迟等问题。

获取/设置调用不返回错误，因为“无效的 GPIO”应更早地从 `gpiod_direction_*()` 中报告。但是，请注意，并非所有平台都可以读取输出引脚的值；无法读取的平台应始终返回 0。此外，对于不能在不睡眠的情况下安全访问的 GPIO（见下文）使用这些调用是错误的。

# GPIO 访问可能会睡眠
--------------------------
某些 GPIO 控制器必须使用基于消息的总线（如 I2C 或 SPI）进行访问。读取或写入这些 GPIO 值的命令需要等待排队发送命令并获得响应。这需要睡眠，这不能在 IRQ 处理程序内部完成。

支持此类 GPIO 的平台通过从此调用返回非零来区分它们与其他 GPIO：

```c
int gpiod_cansleep(const struct gpio_desc *desc)
```

要访问此类 GPIO，定义了一组不同的访问器：

```c
int gpiod_get_value_cansleep(const struct gpio_desc *desc)
void gpiod_set_value_cansleep(struct gpio_desc *desc, int value)
```

访问此类 GPIO 需要可能睡眠的上下文，例如线程化的 IRQ 处理程序，并且必须使用这些带有 `cansleep()` 名称后缀的访问器，而不是自旋锁安全的访问器。

除了这些访问器可能会睡眠并且将在不能从硬 IRQ 处理程序访问的 GPIO 上工作之外，这些调用的行为与自旋锁安全的调用相同。

# 低电平有效状态和原始 GPIO 值
------------------------------------
设备驱动程序喜欢管理 GPIO 的逻辑状态，即其设备实际接收的值，无论其与 GPIO 线路之间存在什么。在某些情况下，控制实际的 GPIO 线路值可能是有意义的。以下一组调用忽略 GPIO 的低电平有效属性并处理原始线路值：

```c
int gpiod_get_raw_value(const struct gpio_desc *desc)
void gpiod_set_raw_value(struct gpio_desc *desc, int value)
int gpiod_get_raw_value_cansleep(const struct gpio_desc *desc)
void gpiod_set_raw_value_cansleep(struct gpio_desc *desc, int value)
int gpiod_direction_output_raw(struct gpio_desc *desc, int value)
```

还可以使用以下调用查询 GPIO 的低电平有效状态：

```c
int gpiod_is_active_low(const struct gpio_desc *desc)
```

请注意，这些函数应谨慎使用；驱动程序不应关心物理线路电平。

# 使用单个函数调用设置多个 GPIO 输出
-----------------------------------------------------
以下函数设置 GPIO 数组的输出值：

```c
void gpiod_set_array(unsigned int array_size,
                     struct gpio_desc **desc_array,
                     int *value_array)
void gpiod_set_raw_array(unsigned int array_size,
                         struct gpio_desc **desc_array,
                         int *value_array)
void gpiod_set_array_cansleep(unsigned int array_size,
                              struct gpio_desc **desc_array,
                              int *value_array)
void gpiod_set_raw_array_cansleep(unsigned int array_size,
                                  struct gpio_desc **desc_array,
                                  int *value_array)
```

该数组可以是任意一组 GPIO。如果相应的芯片驱动程序支持，这些函数将尝试同时设置属于同一组或芯片的 GPIO，如果可能的话，性能会有显著提高。如果不能同时设置，则将依次设置 GPIO。

`gpiod_set_array()` 函数接受三个参数：
  * `array_size` - 数组元素的数量
  * `desc_array` - GPIO 描述符的数组
  * `value_array` - 要分配给 GPIO 的值的数组

描述符数组可以使用 `gpiod_get_array()` 函数或其变体之一获取。如果该函数返回的描述符组与所需的 GPIO 组匹配，则可以通过简单地使用 `gpiod_get_array()` 返回的 `struct gpio_descs` 来设置这些 GPIO：

```c
struct gpio_descs *my_gpio_descs = gpiod_get_array(...);
gpiod_set_array(my_gpio_descs->ndescs, my_gpio_descs->desc,
                my_gpio_values);
```

也可以设置完全任意的描述符数组。描述符可以通过任何 `gpiod_get()` 和 `gpiod_get_array()` 的组合获取。之后，在使用 `gpiod_set_array()` 之前，必须手动设置描述符数组。

请注意，为了获得最佳性能，属于同一芯片的 GPIO 在描述符数组中应是连续的。

# GPIO 映射到 IRQ
--------------------
GPIO 线经常可以用作 IRQ。您可以使用以下调用获取与给定 GPIO 对应的 IRQ 编号：

```c
int gpiod_to_irq(const struct gpio_desc *desc)
```

它将返回 IRQ 编号，或者如果无法完成映射（很可能是因为该特定 GPIO 不能用作 IRQ）则返回负的 `errno` 代码。使用未使用 `gpiod_direction_input()` 设置为输入的 GPIO 或使用未最初来自 `gpiod_to_irq()` 的 IRQ 编号是未经检查的错误。`gpiod_to_irq()` 不允许睡眠。

从 `gpiod_to_irq()` 返回的非错误值可以传递给 `request_irq()` 或 `free_irq()`。它们通常会由特定平台的初始化代码存储到平台设备的 IRQ 资源中。请注意，IRQ 触发选项是 IRQ 接口的一部分，例如 `IRQF_TRIGGER_FALLING`，系统唤醒功能也是如此。

# GPIO 和 ACPI
==============

在 ACPI 系统上，GPIO 由设备的 `_CRS` 配置对象中列出的 `GpioIo()`/`GpioInt()` 资源描述。这些资源不提供 GPIO 的连接 ID（名称），因此需要使用额外的机制来实现此目的。

符合 ACPI 5.1 或更高版本的系统可以提供 `_DSD` 配置对象，除其他外，它可用于为 `_CRS` 中的 `GpioIo()`/`GpioInt()` 资源描述的特定 GPIO 提供连接 ID。如果是这种情况，GPIO 子系统将自动处理。但是，如果不存在 `_DSD`，则需要由设备驱动程序提供 `GpioIo()`/`GpioInt()` 资源与 GPIO 连接 ID 之间的映射。

有关详细信息，请参阅 `Documentation/acpi/gpio-properties.txt`

# 与旧版 GPIO 子系统交互
==========================================
许多内核子系统仍然使用基于整数的旧版 GPIO 接口处理 GPIO。尽管强烈鼓励将它们升级到更安全的基于描述符的 API，但以下两个函数允许您在 GPIO 描述符和 GPIO 整数命名空间之间进行转换，反之亦然：

```c
int desc_to_gpio(const struct gpio_desc *desc)
struct gpio_desc *gpio_to_desc(unsigned gpio)
```

只要尚未释放 GPIO 描述符，`desc_to_gpio()` 返回的 GPIO 编号就可以安全使用。同样，传递给 `gpio_to_desc()` 的 GPIO 编号必须已正确获取，并且只有在释放 GPIO 编号后才能使用返回的 GPIO 描述符。

使用一个 API 释放通过另一个 API 获取的 GPIO 是禁止的，并且是未经检查的错误。