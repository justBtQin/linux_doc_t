# GPIO 描述符驱动程序接口
================================

本文档是 GPIO 芯片驱动程序编写者的指南。请注意，它描述的是新的基于描述符的接口。有关已弃用的基于整数的 GPIO 接口的描述，请参阅 `gpio-legacy.txt`。

每个 GPIO 控制器驱动程序都需要包含以下头文件，其中定义了用于定义 GPIO 驱动程序的结构：

```c
#include <linux/gpio/driver.h>
```

# GPIO 的内部表示
================================

在 GPIO 驱动程序内部，各个 GPIO 通过其硬件编号来标识，硬件编号是 0 到 n 之间的唯一数字，n 是芯片管理的 GPIO 数量。这个数字纯粹是内部的：特定 GPIO 描述符的硬件编号永远不会在驱动程序外部可见。

在此内部编号之上，每个 GPIO 还需要在整数 GPIO 命名空间中有一个全局编号，以便可以与旧的 GPIO 接口一起使用。因此，每个芯片必须有一个“基”编号（可以自动分配），对于每个 GPIO，全局编号将是（基 + 硬件编号）。尽管整数表示被认为已弃用，但它仍然有许多用户，因此需要维护。

例如，一个平台可以将 32 - 159 用于 GPIO，其中一个控制器在“基”为 32 的情况下定义 128 个 GPIO；而另一个平台使用 0..63 与一组 GPIO 控制器，64 - 79 与另一种类型的 GPIO 控制器，在一个特定的板上使用 80 - 95 与一个 FPGA。这些数字不一定是连续的；这些平台中的任何一个也可以使用 2000 - 2063 来标识 I2C GPIO 扩展器中的 GPIO 组。

# 控制器驱动程序：`gpio_chip`
=============================

在 `gpiolib` 框架中，每个 GPIO 控制器都被打包为一个“`struct gpio_chip`”（有关其完整定义，请参阅 `linux/gpio/driver.h`），具有该类型的每个控制器共有的成员：

 - 用于设置 GPIO 方向的方法
 - 用于访问 GPIO 值的方法
 - 用于返回与给定 GPIO 相关联的 IRQ 编号的方法
 - 表示其方法的调用是否可能睡眠的标志
 - 可选的 `debugfs` 转储方法（显示诸如上拉配置等额外状态）
 - 可选的基编号（如果省略将自动分配）
 - 用于诊断和使用平台数据进行 GPIO 映射的标签

实现 `gpio_chip` 的代码应支持控制器的多个实例，可能使用驱动模型。该代码将配置每个 `gpio_chip` 并发出 `gpiochip_add()`。删除 GPIO 控制器的情况应该很少见；在不可避免的情况下使用 `gpiochip_remove()`。

通常，`gpio_chip` 是特定实例结构的一部分，其状态未通过 GPIO 接口暴露，例如寻址、电源管理等。诸如编解码器之类的芯片将具有复杂的非 GPIO 状态。

任何 `debugfs` 转储方法通常都应忽略尚未请求为 GPIO 的信号。它们可以使用 `gpiochip_is_requested()`，该函数返回 `NULL` 或请求该 GPIO 时关联的标签。

# 提供 IRQ 的 GPIO 驱动程序
---------------------------

通常，GPIO 驱动程序（GPIO 芯片）也提供中断，通常从父中断控制器级联，在某些特殊情况下，GPIO 逻辑与 SoC 的主中断控制器融合。

GPIO 块的 IRQ 部分使用 `irqchip` 实现，使用 `<linux/irq.h>` 头文件。因此，基本上这样的驱动程序同时利用两个子系统：GPIO 和 IRQ。

GPIO `irqchips` 通常属于以下两类之一：

* 链式 GPIO `irqchips`：这些通常是嵌入在 SoC 中的类型。这意味着对于 GPIO 有一个快速的 IRQ 处理程序，从父 IRQ 处理程序（最典型的是系统中断控制器）以链的形式调用。这意味着使用 `irq_set_chained_handler()` 或相应的 `gpiochip_set_chained_irqchip()` 辅助函数注册 GPIO `irqchip`，并且 GPIO `irqchip` 处理程序将在禁用 IRQ 的情况下立即从父 `irqchip` 调用，同时保持 IRQ 禁用。然后，GPIO `irqchip` 在其中断处理程序中最终会调用类似这样的序列：

```c
static irqreturn_t tc3589x_gpio_irq(int irq, void *data)
    chained_irq_enter(...);
    generic_handle_irq(...);
    chained_irq_exit(...);
```

链式 GPIO `irqchips` 通常不能在 `struct gpio_chip` 上设置 `.can_sleep` 标志，因为一切都直接在回调中发生。

* 嵌套线程化 GPIO `irqchips`：这些是片外 GPIO 扩展器和位于睡眠总线另一侧的任何其他 GPIO `irqchip`。当然，需要通过慢速总线流量读取 IRQ 状态等类似操作的此类驱动程序，这些操作可能反过来引发其他 IRQ 发生，不能在禁用 IRQ 的快速 IRQ 处理程序中处理。相反，它们需要生成一个线程，然后屏蔽父 IRQ 线，直到驱动程序处理中断。这种驱动程序的标志是在其中断处理程序中调用类似这样的内容：

```c
static irqreturn_t tc3589x_gpio_irq(int irq, void *data)
   ...
    handle_nested_irq(irq);
```

线程化 GPIO `irqchips` 的标志是它们将 `struct gpio_chip` 上的 `.can_sleep` 标志设置为 `true`，表示该芯片在访问 GPIO 时可能睡眠。

为了帮助处理 GPIO `irqchips` 的设置和管理以及相关的 `irqdomain` 和资源分配回调，`gpiolib` 有一些辅助函数，可以通过选择 `GPIOLIB_IRQCHIP` `Kconfig` 符号启用：

* `gpiochip_irqchip_add()`：向 `gpiochip` 添加一个 `irqchip`。它将把芯片的 `struct gpio_chip*` 传递给所有 IRQ 回调，因此回调需要将 `gpio_chip` 嵌入其状态容器中，并使用 `container_of()` 获取容器的指针。（请参阅 `Documentation/driver-model/design-patterns.txt`）

* `gpiochip_set_chained_irqchip()`：为来自父 IRQ 的 `gpio_chip` 设置一个链式 `irq` 处理程序，并将 `struct gpio_chip*` 作为处理程序数据传递。（注意处理程序数据，因为 `irqchip` 数据可能由父 `irqchip` 使用！）这是用于链式类型的芯片。如果传递 `NULL` 作为处理程序，也用于设置嵌套 `irqchip`。

要使用这些辅助函数，请记住以下几点：

 - 确保正确分配 `struct gpio_chip` 的所有相关成员，以便 `irqchip` 可以初始化。例如，`.dev` 和 `.can_sleep` 应正确设置。

任何 IRQ 消费者都可以从任何 `irqchip` 请求 IRQ，无论它是否是组合的 GPIO + IRQ 驱动程序。基本前提是 `gpio_chip` 和 `irq_chip` 是正交的，并相互独立地提供服务。

`gpiod_to_irq()` 只是一个方便的函数，用于找出某个 GPIO 线的 IRQ，不应依赖于在使用 IRQ 之前已被调用。

因此，始终在 GPIO 和 `irqchip` API 的相应回调中准备硬件并使其准备好运行。不要依赖于首先调用 `gpiod_to_irq()`。

这种正交性导致了我们需要解决的歧义：如果子系统内部存在竞争，哪一方正在使用资源（例如某个 GPIO 线和寄存器），它需要拒绝某些操作并在 `gpiolib` 子系统内部跟踪使用情况。这就是为什么存在以下 API 的原因。

# 锁定 IRQ 使用
-----------------

输入 GPIO 可以用作 IRQ 信号。发生这种情况时，驱动程序被要求将 GPIO 标记为用作 IRQ：

```c
int gpiochip_lock_as_irq(struct gpio_chip *chip, unsigned int offset)
```

这将阻止使用非 IRQ 相关的 GPIO API，直到释放 GPIO IRQ 锁：

```c
void gpiochip_unlock_as_irq(struct gpio_chip *chip, unsigned int offset)
```

在 GPIO 驱动程序中实现 `irqchip` 时，这两个函数通常应在 `irqchip` 的 `.startup()` 和 `.shutdown()` 回调中调用。

# 请求自有的 GPIO 引脚
-------------------------------

有时，允许 GPIO 芯片驱动程序通过 `gpiolib` API 请求自己的 GPIO 描述符是很有用的。为此目的使用 `gpio_request()` 没有帮助，因为它会将模块永远固定在内核中（它调用 `try_module_get()`）。GPIO 驱动程序可以使用以下函数来请求和释放描述符，而不会永远固定在内核中。

```c
struct gpio_desc *gpiochip_request_own_desc(struct gpio_desc *desc,
                                            const char *label)

void gpiochip_free_own_desc(struct gpio_desc *desc)
```

使用 `gpiochip_request_own_desc()` 请求的描述符必须使用 `gpiochip_free_own_desc()` 释放。

使用这些函数时必须小心，因为它们不会影响模块的使用计数。不要使用这些函数来请求不属于调用驱动程序的 GPIO 描述符。