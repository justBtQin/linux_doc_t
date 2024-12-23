这是第一个包含对一些主要与架构相关的子系统进行重大调整的内核。

首先，它对我们处理 MMU TLB 的方式进行了一些相当大的更改。现在，每个 MMU TLB 变体都完全独立处理 - 我们有 TLB v3、TLB v4（无写缓冲区）、TLB v4（有写缓冲区），最后是 TLB v4（有写缓冲区，带有 I TLB 无效条目）。每个这些函数内部都有更多的汇编代码，主要是为了将来允许更灵活的 TLB 处理。

其次，是 IRQ 子系统。2.5 内核将对 IRQ 的处理方式进行重大更改。不幸的是，这意味着触及 `irq_desc[]`数组的机器类型（基本上所有机器类型）将中断，这意味着我们当前拥有的每个机器类型。

让我们举个例子。在带有 Neponset 的 Assabet 上，我们有：

```
                  GPIO25                 IRR:2
        SA1100 ------------> Neponset -----------> SA1111
                                         IIR:1
                                      -----------> USAR
                                         IIR:0
                                      -----------> SMC9196
```

目前的工作方式是，所有 SA1111 中断彼此互斥 - 如果您正在处理来自 SA1111 的一个中断，而另一个中断进来，您必须等待该中断完成处理，然后才能处理新的中断。例如，SA1111 上基于 IDE PIO 的中断将排除所有其他 SA1111 和 SMC9196 中断，直到它完成传输其多扇区数据，这可能需要很长时间。还要注意，由于我们在 SA1111 IRQ 处理程序中循环，SA1111 IRQ 可以无限期地阻止 SMC9196 IRQ。

新方法带来了几个新想法...

我们引入了“父”和“子”的概念。例如，对于 Neponset 处理程序，“父”是 GPIO25，“子”是 SA1111、SMC9196 和 USAR。

我们还引入了 IRQ“芯片”的概念（主要是为了减少 irqdesc 数组的大小）。这不一定是一个真正的“IC”；实际上，SA11x0 IRQ 由两个独立的“芯片”结构处理，一个用于 GPIO0-10，另一个用于所有其他。它只是各种操作的容器（也许这将更改为一个更好的名称）。这个结构具有以下操作：

```
struct irqchip {
        /*
         * 确认 IRQ。
         * 如果这是基于电平的 IRQ，那么预计也会屏蔽 IRQ。
         */
        void (*ack)(unsigned int irq);
        /*
         * 在硬件中屏蔽 IRQ。
         */
        void (*mask)(unsigned int irq);
        /*
         * 在硬件中解除屏蔽 IRQ。
         */
        void (*unmask)(unsigned int irq);
        /*
         * 重新运行 IRQ
         */
        void (*rerun)(unsigned int irq);
        /*
         * 设置 IRQ 的类型。
         */
        int (*type)(unsigned int irq, unsigned int, type);
};
```

ack - 必需。对于由 do_level_IRQ 处理的 IRQ，可能与 mask 函数相同。

mask - 必需。

unmask - 必需。

rerun - 可选。如果您对使用此“irqchip”的所有 IRQ 都使用 do_level_IRQ，则不需要。通常，如果可能，应重新触发硬件 IRQ。如果不行，可以直接调用处理程序。

type - 可选。如果您不支持更改 IRQ 的类型，则应为 null，以便人们可以检测到他们是否无法设置 IRQ 类型。

对于每个 IRQ，我们保留以下信息：

        - “禁用”深度（没有 enable_irq()的 disable_irq()的数量）

        - 指示我们可以对该 IRQ 执行的操作的标志（有效、探测、noautounmask），与以前相同

        - IRQ 的状态（探测、启用等）

        - 芯片

        - 每个 IRQ 的处理程序

        - irqaction 结构列表

处理程序可以是 3 个标准处理程序之一 - “level”、“edge”和“simple”，或者如果您需要执行特殊操作，则可以是自己的特定处理程序。

“level”处理程序是我们目前拥有的 - 它非常简单。“edge”了解此类 IRQ 实现的缺陷 - 即在处理它时需要保持硬件 IRQ 启用，并在处理期间如果 IRQ 再次发生则排队进一步的 IRQ 事件。

“simple”处理程序非常基本，不执行任何硬件操作，也不进行状态跟踪。这对于上面的 SMC9196 和 USAR 很有用。

那么，有什么变化呢？

1. 机器实现不得写入 irqdesc 数组。

2. 用于操作 irqdesc 数组的新函数。前 4 个预计仅对特定于机器的代码有用。最后一个建议仅由特定于机器的代码使用，但在绝对必要时可在驱动程序中使用。

```
        set_irq_chip(irq,chip)
                设置处理此 IRQ 的屏蔽/解除屏蔽方法
        set_irq_handler(irq,handler)
                设置此 IRQ 的处理程序（level、edge、simple）
        set_irq_chained_handler(irq,handler)
                设置此 IRQ 的“链式”处理程序 - 自动启用此 IRQ（例如，Neponset 和 SA1111 处理程序）。
        set_irq_flags(irq,flags)
                设置有效/探测/noautoenable 标志。
        set_irq_type(irq,type)
                设置 IRQ 边沿/电平的活动状态。这取代了 SA1111 INTPOL 操作和 set_GPIO_IRQ_edge()函数。类型应为 <linux/irq.h>中定义的 IRQ_TYPE_xxx 之一。
```

3. set_GPIO_IRQ_edge()已过时，应被 set_irq_type 取代。

4. 直接访问 SA1111 INTPOL 已弃用。使用 set_irq_type 代替。

5. 处理程序应通过正确的芯片特定函数对父 IRQ 执行任何必要的确认。例如，如果 SA1111 直接连接到 SA1110 GPIO，则每次重新读取 SA1111 IRQ 状态时，都应确认 SA1110 IRQ。

6. 对于没有自己的 IRQ 使能/禁用控制的任何子设备（例如，SMC9196），处理程序在调用子处理程序时必须屏蔽或确认父 IRQ，并且子处理程序应为“simple”处理程序（不是“edge”也不是“level”）。处理程序完成后，应解除父 IRQ 的屏蔽，并重新检查所有子设备的状态以查看是否有挂起的事件。（有关详细信息，请参阅 Neponset IRQ 处理程序）。

7. fixup_irq()已消失，arch/arm/mach-*/include/mach/irq.h 也已消失。

请注意，这并不能解决所有问题 - 其中一些是基于硬件的。在同一父信号（例如 neponset）上混合基于电平的和基于边沿的 IRQ 是一个这样的领域，其中基于软件的解决方案无法为低 IRQ 延迟提供完整的答案。