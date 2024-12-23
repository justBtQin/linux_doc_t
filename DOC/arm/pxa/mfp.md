# PXA2xx/PXA3xx 处理器的 MFP 配置

Eric Miao <eric.miao@marvell.com>

MFP 代表多功能引脚，是 PXA3xx 及后续 PXA 系列处理器上的引脚复用逻辑。本文档描述了现有的 MFP API，以及板级/平台驱动程序作者如何使用它。

# 基本概念

===============

与 PXA25x 和 PXA27x 上的 GPIO 备用功能设置不同，从 PXA3xx 开始引入了一种新的 MFP 机制，将引脚复用功能完全从 GPIO 控制器中移出。除了引脚复用配置之外，MFP 还控制每个引脚的低功耗状态、驱动强度、上拉/下拉和事件检测。以下是 MFP 逻辑与剩余 SoC 外设之间的内部连接图：

```
 +--------+
 |        |--(GPIO19)--+
 |  GPIO  |            |
 |        |--(GPIO...) |
 +--------+            |
                       |       +---------+
 +--------+            +------>|         |
 |  PWM2  |--(PWM_OUT)-------->|   MFP   |
 +--------+            +------>|         |-------> 到外部 PAD
                       | +---->|         |
 +--------+            | | +-->|         |
 |  SSP2  |---(TXD)----+ | |   +---------+
 +--------+              | |
                         | |
 +--------+              | |
 | Keypad |--(MKOUT4)----+ |
 +--------+                |
                           |
 +--------+                |
 |  UART2 |---(TXD)--------+
 +--------+
```

注意：外部焊盘被命名为 MFP_PIN_GPIO19，这并不一定意味着它专用于 GPIO19，只是暗示内部此引脚可以从 GPIO 控制器的 GPIO19 路由。

为了更好地理解从 PXA25x/PXA27x GPIO 备用功能到这种新的 MFP 机制的变化，这里有几个关键点：

1. PXA3xx 上的 GPIO 控制器现在是一个专用控制器，与 PWM、SSP 和 UART 等其他内部控制器相同，具有 128 个内部信号，可以通过一个或多个 MFP 路由到外部（例如，GPIO<0>可以通过 MFP_PIN_GPIO0 以及 MFP_PIN_GPIO0_2 进行路由，参见 arch/arm/mach-pxa/mach/include/mfp-pxa300.h）。

2. 此 GPIO 控制器的备用功能配置已被删除，其余功能是纯 GPIO 特定的，即：

- GPIO 信号电平控制

- GPIO 方向控制

- GPIO 电平变化检测

3. 每个引脚的低功耗状态现在由 MFP 控制，这意味着 PXA2xx 上的 PGSRx 寄存器在 PXA3xx 上现在无用。

4. 唤醒检测现在由 MFP 控制，PWER 不再控制来自 GPIO 的唤醒，根据睡眠状态，ADxER（如 pxa3xx-regs.h 中所定义）控制来自 MFP 的唤醒。

注意：由于 MFP 和 GPIO 的这种明确分离，通常通过 GPIO<xx>表示它是一个 GPIO 信号，通过 MFP<xxx>或引脚 xxx 表示一个物理焊盘（或球）。

# MFP API 使用

===============

对于板级代码编写者，这里有一些指导原则：

1. 在你的 <board>.c 中包含以下头文件之一：

- #include <mach/mfp-pxa25x.h>

- #include <mach/mfp-pxa27x.h>

- #include <mach/mfp-pxa300.h>

- #include <mach/mfp-pxa320.h>

- #include <mach/mfp-pxa930.h>

注意：在你的 <board>.c 中只有一个文件，具体取决于使用的处理器，因为引脚配置定义在这些文件中可能会冲突（即相同的名称，不同的含义和在不同处理器上的设置）。例如，对于支持 PXA300/PXA310 和 PXA320 的 zylonite 平台，引入了两个单独的文件：zylonite_pxa300.c 和 zylonite_pxa320.c（除了处理 MFP 配置差异之外，它们还处理这两种组合之间的其他差异）。

注意：PXA300 和 PXA310 在引脚配置上几乎相同（PXA310 支持一些额外的配置），因此实际上差异在单个 mfp-pxa300.h 中已涵盖。

2. 为初始引脚配置准备一个数组，例如：

```
static unsigned long mainstone_pin_config[] __initdata = {
	/* Chip Select */
	GPIO15_nCS_1,
	/* LCD - 16bpp Active TFT */
	GPIOxx_TFT_LCD_16BPP,
	GPIO16_PWM0_OUT, /* Backlight */
	/* MMC */
	GPIO32_MMC_CLK,
	GPIO112_MMC_CMD,
	GPIO92_MMC_DAT_0,
	GPIO109_MMC_DAT_1,
	GPIO110_MMC_DAT_2,
	GPIO111_MMC_DAT_3,
	... 
	/* GPIO */
	GPIO1_GPIO | WAKEUP_ON_EDGE_BOTH,
};
```

a) 一旦引脚配置被传递给 pxa{2xx,3xx}_mfp_config()并写入实际寄存器，它们就无用了，可以丢弃，添加 '__initdata' 将有助于在此处节省一些额外的字节。

b) 当一个组件只有一种可能的引脚配置时，可以使用一些简化的定义，例如 PXA25x 和 PXA27x 处理器上的 GPIOxx_TFT_LCD_16BPP。

c) 如果根据板级设计，一个引脚可以配置为从低功耗状态唤醒系统，则可以将其与以下任何一个进行“或”运算：

WAKEUP_ON_EDGE_BOTH

WAKEUP_ON_EDGE_RISE

WAKEUP_ON_EDGE_FALL

WAKEUP_ON_LEVEL_HIGH - 特别用于启用键盘 GPIO，

以指示此引脚具有唤醒系统的能力以及在哪个边缘（s）上。然而，这并不一定意味着该引脚将唤醒系统，只有当使用相应的 GPIO IRQ（GPIO_IRQ(xx) 或 gpio_to_irq()）调用 set_irq_wake()并最终调用 gpio_set_wake()进行实际寄存器设置时才会唤醒系统。

d) 尽管 PXA3xx MFP 支持每个引脚的边缘检测，但内部逻辑只会在 ADxER 寄存器中的那些特定位被设置时才唤醒系统，这些位可以很好地映射到相应的外设，因此可以使用外设 IRQ 调用 set_irq_wake()来启用唤醒。

# PXA3xx 上的 MFP

===============

PXA3xx 上的每个外部 I/O 焊盘（不包括用于特殊目的的焊盘）都有一个与之关联的 MFP 逻辑，并由一个 MFP 寄存器（MFPR）控制。

MFPR 具有以下位定义（对于 PXA300/PXA310/PXA320）：

```
 31                        16 15 14 13 12 11 10  9  8  7  6  5  4  3  2  1  0
  +-------------------------+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
  |         RESERVED        |PS|PU|PD|  DRIVE |SS|SD|SO|EC|EF|ER|--| AF_SEL |
  +-------------------------+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
位 3：保留
位 4：EDGE_RISE_EN - 启用此引脚上的上升沿检测
位 5：EDGE_FALL_EN - 启用此引脚上的下降沿检测
位 6：EDGE_CLEAR - 禁用此引脚上的边缘检测
位 7：SLEEP_OE_N - 在低功耗模式下启用输出
位 8：SLEEP_DATA - 在低功耗模式下在引脚上输出数据
位 9：SLEEP_SEL - 低功耗模式信号的选择控制
位 13：PULLDOWN_EN - 启用此引脚上的内部下拉电阻
位 14：PULLUP_EN - 启用此引脚上的内部上拉电阻
位 15：PULL_SEL - 由所选备用功能控制的拉状态（0）或由 PULL{UP,DOWN}_EN 位控制（1）
位 0 - 2：AF_SEL - 备用功能选择，有 8 种可能性，从 0-7
位 10-12：DRIVE - 驱动强度和 slew 率
	0b000 - 快速 1mA
	0b001 - 快速 2mA
	0b002 - 快速 3mA
	0b003 - 快速 4mA
	0b004 - 慢速 6mA
	0b005 - 快速 6mA
	0b006 - 慢速 10mA
	0b007 - 快速 10mA
```

# PXA2xx/PXA3xx 的 MFP 设计

==============================

由于 PXA2xx 和 PXA3xx 在引脚复用处理方面的差异，引入了一个统一的 MFP API 来涵盖这两个系列的处理器。

此设计的基本思想是为所有可能的引脚配置引入定义，这些定义与处理器和平台无关，实际的 API 用于将这些定义转换为寄存器设置并使其在之后生效。

# 涉及的文件

--------------

- arch/arm/mach-pxa/include/mach/mfp.h

用于：

1. 统一引脚定义 - 所有可配置引脚的枚举常量

2.用于可能的 MFP 配置的处理器中立位定义

- `arch/arm/mach-pxa/include/mach/mfp-pxa3xx.h`

用于 PXA3xx 特定的 MFPR 寄存器位定义和 PXA3xx 通用引脚配置

- `arch/arm/mach-pxa/include/mach/mfp-pxa2xx.h`

用于 PXA2xx 特定定义和 PXA25x/PXA27x 通用引脚配置

- `arch/arm/mach-pxa/include/mach/mfp-pxa25x.h`、`arch/arm/mach-pxa/include/mach/mfp-pxa27x.h`、`arch/arm/mach-pxa/include/mach/mfp-pxa300.h`、`arch/arm/mach-pxa/include/mach/mfp-pxa320.h`、`arch/arm/mach-pxa/include/mach/mfp-pxa930.h`

用于处理器特定的定义

- `arch/arm/mach-pxa/mfp-pxa3xx.c`

- `arch/arm/mach-pxa/mfp-pxa2xx.c`

用于使引脚配置在实际处理器中生效的实现。

# 引脚配置

-----------------

以下注释从`mfp.h`复制而来（请参阅实际源代码以获取最新信息）

```
/*
 * 一个可能的 MFP 配置由一个 32 位整数表示
 *
 * 位 0..9 - MFP 引脚编号（最大 1024 个引脚）
 * 位 10..12 - 备用功能选择
 * 位 13..15 - 驱动强度
 * 位 16..18 - 低功耗模式状态
 * 位 19..20 - 低功耗模式边缘检测
 * 位 21..22 - 运行模式拉状态
 *
 * 为了便于定义，提供了以下宏
 *
 * MFP_CFG_DEFAULT - 默认 MFP 配置值，其中
 * 		  备用功能 = 0，
 * 		  驱动强度 = 快速 3mA（MFP_DS03X）
 * 		  低功耗模式 = 默认
 * 		  边缘检测 = 无
 *
 * MFP_CFG - 带有备用功能的默认 MFPR 值
 * MFP_CFG_DRV - 带有备用功能和引脚驱动强度的默认 MFPR 值
 * MFP_CFG_LPM - 带有备用功能和低功耗模式的默认 MFPR 值
 * MFP_CFG_X - 带有备用功能、引脚驱动强度和低功耗模式的默认 MFPR 值
 */
```

引脚配置的示例如下：

`#define GPIO94_SSP3_RXD MFP_CFG_X(GPIO94, AF1, DS08X, FLOAT)`

这表示读取 GPIO94 可以配置为 SSP3_RXD，备用功能选择为 1，驱动强度为 0b101，在低功耗模式下为浮置状态。

注意：这是将该引脚配置为 SSP3_RXD 的默认设置，在板级代码中可以对其进行一些修改，但不建议这样做，仅仅是因为这个默认设置通常经过精心编码，并且在大多数情况下应该可以正常工作。

# 寄存器设置

-----------------

PXA3xx 上引脚配置的寄存器设置实际上非常直接，大多数位可以以更简单的方式直接转换为 MFPR 值。计算了两组 MFPR 值：运行时的值和低功耗模式的值，以允许不同的设置。

从通用引脚配置到 PXA2xx 上实际寄存器设置的转换有点复杂：涉及许多寄存器，包括 GAFRx、GPDRx、PGSRx、PWER、PKWR、PFER 和 PRER。请参阅`mfp-pxa2xx.c`了解转换是如何进行的。