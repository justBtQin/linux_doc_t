以下是为您排版成 Markdown 格式的内容：

# Linux 与设备树
-------------------------

Linux 中设备树数据的使用模型

作者：Grant Likely <grant.likely@secretlab.ca>

本文描述了 Linux 如何使用设备树。设备树数据格式的概述可以在 devicetree.org 的设备树使用页面[1]找到。

[1] http://devicetree.org/Device_Tree_Usage

“Open Firmware 设备树”，或简称设备树（DT），是一种用于描述硬件的数据结构和语言。更具体地说，它是一个操作系统可读的硬件描述，以便操作系统不需要对机器的细节进行硬编码。

从结构上讲，DT 是一棵树，或者是一个带有命名节点的无环图，节点可以有任意数量的命名属性，封装任意数据。还存在一种机制，可以在自然树结构之外创建从一个节点到另一个节点的任意链接。

从概念上讲，定义了一组称为“绑定”的常见使用约定，用于说明数据在树中应如何出现，以描述典型的硬件特性，包括数据总线、中断线、GPIO 连接和外围设备。

尽可能多地使用现有的绑定来描述硬件，以最大限度地利用现有的支持代码，但由于属性和节点名称只是文本字符串，因此很容易扩展现有绑定或通过定义新节点和属性来创建新的绑定。但是，请注意，在没有首先研究已存在的内容之前，不要创建新的绑定。目前有两个不同的、不兼容的 I2C 总线绑定，因为新的绑定是在没有首先研究现有系统中如何枚举 I2C 设备的情况下创建的。

1. 历史
----------
DT 最初是由 Open Firmware 创建的，作为从 Open Firmware 向客户端程序（如操作系统）传递数据的通信方法的一部分。操作系统使用设备树在运行时发现硬件的拓扑结构，从而在不需要硬编码信息的情况下支持大多数可用硬件（假设所有设备都有驱动程序）。

由于 Open Firmware 通常在 PowerPC 和 SPARC 平台上使用，因此这些架构的 Linux 支持长期以来一直在使用设备树。

2005 年，当 PowerPC Linux 开始进行重大清理并合并 32 位和 64 位支持时，决定在所有 PowerPC 平台上要求 DT 支持，无论它们是否使用 Open Firmware。为此，创建了一个称为扁平设备树（FDT）的 DT 表示，它可以作为二进制 blob 传递给内核，而不需要真正的 Open Firmware 实现。U-Boot、kexec 和其他引导加载程序被修改为支持传递设备树二进制（dtb）并在引导时修改 dtb。DT 也被添加到 PowerPC 引导包装器（arch/powerpc/boot/*）中，以便可以将 dtb 与内核映像一起包装，以支持引导现有的非 DT 感知固件。

一段时间后，FDT 基础设施被通用化，可供所有架构使用。在撰写本文时，6 个主线架构（arm、microblaze、mips、powerpc、sparc 和 x86）和 1 个非主线架构（nios）都有一定程度的 DT 支持。

2. 数据模型
-------------
如果您还没有阅读设备树使用[1]页面，那么现在去阅读。没关系，我等着......

2.1 高级视图
-------------------
最重要的是要理解 DT 只是一个描述硬件的数据结构。它没有什么神奇之处，也不会神奇地解决所有硬件配置问题。它所做的是为将硬件配置与 Linux 内核（或任何其他操作系统）中的主板和设备驱动程序支持分离提供了一种语言。使用它可以使主板和设备支持成为数据驱动；根据传递到内核的数据而不是基于每台机器的硬编码选择来做出设置决策。

理想情况下，数据驱动的平台设置应该导致更少的代码重复，并使单个内核映像更容易支持广泛的硬件。

Linux 将 DT 数据用于三个主要目的：
1）平台识别，
2）运行时配置，和
3）设备填充。

2.2 平台识别
---------------------------
首先，内核将使用 DT 中的数据来识别特定的机器。在一个完美的世界中，特定的平台对内核来说不应该重要，因为所有平台的细节都将由设备树以一致和可靠的方式完美描述。然而，硬件并不完美，因此内核必须在早期引导期间识别机器，以便有机会运行机器特定的修复。

在大多数情况下，机器身份无关紧要，内核将根据机器的核心 CPU 或 SoC 选择设置代码。例如，在 ARM 上，`arch/arm/kernel/setup.c` 中的 `setup_arch()` 会调用 `arch/arm/kernel/devtree.c` 中的 `setup_machine_fdt()`，它会搜索 `machine_desc` 表，并选择与设备树数据最匹配的 `machine_desc`。它通过查看根设备树节点中的“compatible”属性，并将其与 `struct machine_desc` 中的 `dt_compat` 列表（如果您好奇，它在 `arch/arm/include/asm/mach/arch.h` 中定义）进行比较来确定最佳匹配。

“compatible”属性包含一个按顺序排列的字符串列表，首先是机器的准确名称，然后是可选的它兼容的板的列表，从最兼容到最不兼容排序。例如，TI BeagleBoard 及其后续产品 BeagleBoard xM 板的根兼容属性可能分别如下：

```
compatible = "ti,omap3-beagleboard", "ti,omap3450", "ti,omap3";
compatible = "ti,omap3-beagleboard-xm", "ti,omap3450", "ti,omap3";
```

其中“ti,omap3-beagleboard-xm”指定了确切的模型，它还声称与 OMAP 3450 SoC 和一般的 omap3 系列 SoC 兼容。您会注意到列表从最具体（确切的板）到最不具体（SoC 系列）排序。

敏锐的读者可能会指出，Beagle xM 也可以声称与原始的 Beagle 板兼容。但是，在板级应该谨慎这样做，因为即使在同一产品线内，从一个板到另一个板通常也有很高的变化水平，而且很难确切地确定当一个板声称与另一个板兼容时意味着什么。对于顶级，最好谨慎行事，不要声称一个板与另一个板兼容。值得注意的例外情况是当一个板是另一个板的载体时，例如连接到载体板的 CPU 模块。

关于兼容值的另一个注意事项。在兼容属性中使用的任何字符串都必须记录其指示的内容。在 `Documentation/devicetree/bindings` 中添加兼容字符串的文档。

同样在 ARM 上，对于每个 `machine_desc`，内核会查看 `dt_compat` 列表中的任何条目是否出现在兼容属性中。如果有，那么该 `machine_desc` 是驱动机器的候选者。在搜索整个 `machine_desc` 表后，`setup_machine_fdt()` 根据每个 `machine_desc` 与兼容属性中的哪个条目匹配来返回“最兼容”的 `machine_desc`。如果没有找到匹配的 `machine_desc`，则返回 `NULL`。

这种方案背后的原因是观察到在大多数情况下，如果所有板都使用相同的 SoC 或相同系列的 SoC，单个 `machine_desc` 可以支持大量的板。然而，不可避免地会有一些特殊情况，特定的板需要在早期引导期间的特殊设置代码，而这些代码在一般情况下是无用的。特殊情况可以通过在通用设置代码中显式检查有问题的板来处理，但如果超过几个情况，这样做很快就会变得难看和/或难以维护。

相反，兼容列表允许通用的 `machine_desc` 通过在 `dt_compat` 列表中指定“不太兼容”的值来为广泛的通用板集提供支持。在上面的示例中，通用板支持可以声称与“ti,omap3”或“ti,omap3450”兼容。如果在原始的 Beagle 板上发现了一个需要在早期引导期间进行特殊解决方法的错误，那么可以添加一个新的 `machine_desc`，它实现了这些解决方法，并且只与“ti,omap3-beagleboard”匹配。

PowerPC 使用略有不同的方案，它从每个 `machine_desc` 调用 `.probe()` 钩子，并且返回 `TRUE` 的第一个被使用。然而，这种方法没有考虑兼容列表的优先级，对于新的架构支持可能应该避免。

2.3 运行时配置
-------------------------
在大多数情况下，DT 将是从固件到内核传递数据的唯一方法，因此也用于传递运行时和配置数据，如内核参数字符串和 initrd 映像的位置。

大部分数据包含在 `/chosen` 节点中，在引导 Linux 时它可能看起来像这样：

```
chosen {
    bootargs = "console=ttyS0,115200 loglevel=8";
    initrd-start = <0xc8000000>;
    initrd-end = <0xc8200000>;
};
```

`bootargs` 属性包含内核参数，`initrd-*` 属性定义了 initrd 块的地址和大小。请注意，`initrd-end` 是 initrd 映像之后的第一个地址，因此这与通常的 `struct resource` 语义不匹配。`chosen` 节点还可以可选地包含任意数量的平台特定配置数据的其他属性。

在早期引导期间，架构设置代码会多次调用 `of_scan_flat_dt()` ，并使用不同的辅助回调来在分页设置之前解析设备树数据。`of_scan_flat_dt()` 代码扫描设备树，并使用辅助函数提取早期引导期间所需的信息。通常，`early_init_dt_scan_chosen()` 辅助函数用于解析包括内核参数的 `chosen` 节点，`early_init_dt_scan_root()` 用于初始化 DT 地址空间模型，`early_init_dt_scan_memory()` 用于确定可用 RAM 的大小和位置。

在 ARM 上，`setup_machine_fdt()` 函数负责在选择支持主板的正确 `machine_desc` 后对设备树进行早期扫描。

2.4 设备填充
---------------------
在识别了主板，并解析了早期配置数据之后，内核初始化可以以正常方式进行。在这个过程中的某个时候，会调用 `unflatten_device_tree()` 将数据转换为更高效的运行时表示。这也是调用机器特定的设置钩子的时候，比如 ARM 上的 `machine_desc` 的 `.init_early()`、`.init_irq()` 和 `.init_machine()` 钩子。本节的其余部分使用 ARM 实现的示例，但所有架构在使用 DT 时都会做几乎相同的事情。

从名称可以猜到，`.init_early()` 用于在引导过程早期需要执行的任何机器特定的设置，`.init_irq()` 用于设置中断处理。使用 DT 并不会实质性地改变这两个函数的行为。如果提供了 DT，那么 `.init_early()` 和 `.init_irq()` 都能够调用任何 DT 查询函数（`of_*` 在 `include/linux/of*.h` 中）来获取有关平台的更多数据。

在 DT 上下文中最有趣的钩子是 `.init_machine()`，它主要负责用平台数据填充 Linux 设备模型。在历史上，这在嵌入式平台上是通过在主板支持的 `.c` 文件中定义一组静态时钟结构、`platform_devices` 和其他数据，并在 `.init_machine()` 中大规模注册来实现的。当使用 DT 时，不是为每个平台硬编码静态设备，而是可以通过解析 DT 并动态分配设备结构来获取设备列表。

最简单的情况是 `.init_machine()` 只负责注册一块 `platform_devices`。`platform_device` 是 Linux 用于内存或 I/O 映射设备（不能被硬件检测到）以及“复合”或“虚拟”设备（稍后更多介绍）的概念。虽然 DT 中没有“平台设备”的术语，但平台设备大致对应于树的根节点和简单内存映射总线节点的子节点。

现在是一个很好的时机来展示一个例子。这是 NVIDIA Tegra 板的部分设备树。

```
/ {
    compatible = "nvidia,harmony", "nvidia,tegra20";
    #address-cells = <1>;
    #size-cells = <1>;
    interrupt-parent = <&intc>;

    chosen { };
    aliases { };

    memory {
        device_type = "memory";
        reg = <0x00000000 0x40000000>;
    };

    soc {
        compatible = "nvidia,tegra20-soc", "simple-bus";
        #address-cells = <1>;
        #size-cells = <1>;
        ranges;

        intc: interrupt-controller@50041000 {
            compatible = "nvidia,tegra20-gic";
            interrupt-controller;
            #interrupt-cells = <1>;
            reg = <0x50041000 0x1000>, < 0x50040100 0x0100 >;
        };

        serial@70006300 {
            compatible = "nvidia,tegra20-uart";
            reg = <0x70006300 0x100>;
            interrupts = <122>;
        };

        i2s1: i2s@70002800 {
            compatible = "nvidia,tegra20-i2s";
            reg = <0x70002800 0x100>;
            interrupts = <77>;
            codec = <&wm8903>;
        };

        i2c@7000c000 {
            compatible = "nvidia,tegra20-i2c";
            #address-cells = <1>;
            #size-cells = <0>;
            reg = <0x7000c000 0x100>;
            interrupts = <70>;

            wm8903: codec@1a {
                compatible = "wlf,wm8903";
                reg = <0x1a>;
                interrupts = <347>;
            };
        };
    };

    sound {
        compatible = "nvidia,harmony-sound";
        i2s-controller = <&i2s1>;
        i2s-codec = <&wm8903>;
    };
};
```

在 `.init_machine()` 时，Tegra 主板支持代码需要查看这个 DT，并决定为哪些节点创建 `platform_devices`。然而，查看树，并不立即明显每个节点代表什么样的设备，甚至是否一个节点代表一个设备。`/chosen`、`/aliases` 和 `/memory` 节点是信息节点，不描述设备（尽管可以认为内存可以被视为一个设备）。`/soc` 节点的子节点是内存映射设备，但 `codec@1a` 是一个 I2C 设备，`sound` 节点不代表一个设备，而是代表其他设备如何连接在一起创建音频子系统。我知道每个设备是什么，因为我熟悉主板设计，但内核如何知道如何处理每个节点？

诀窍是内核从树的根开始，查找具有“compatible”属性的节点。首先，通常假设任何具有“compatible”属性的节点都代表某种设备，其次，可以假设树的根的任何节点要么直接连接到处理器总线，要么是无法以其他方式描述的杂项系统设备。对于这些节点中的每一个，Linux 分配并注册一个 `platform_device`，它可能会绑定到一个 `platform_driver`。

为什么为这些节点使用 `platform_device` 是一个安全的假设？好吧，对于 Linux 模型设备的方式，几乎所有的 `bus_types` 都假设其设备是总线控制器的子节点。例如，每个 `i2c_client` 是 `i2c_master` 的子节点。每个 `spi_device` 是 SPI 总线的子节点。类似地对于 USB、PCI、MDIO 等。同样的层次结构也在 DT 中找到，其中 I2C 设备节点只作为 I2C 总线节点的子节点出现。对于 SPI、MDIO、USB 等也是如此。唯一不需要特定类型的父设备的设备是 `platform_devices`（和 `amba_devices`，但稍后更多介绍），它们将愉快地存在于 Linux `/sys/devices` 树的基础上。因此，如果一个 DT 节点在树的根上，那么它很可能最好注册为 `platform_device`。

Linux 主板支持代码调用 `of_platform_populate(NULL, NULL, NULL, NULL)` 来启动对树根设备的发现。参数都是 `NULL`，因为从树的根开始，不需要提供起始节点（第一个 `NULL`）、父 `struct device`（最后一个 `NULL`），并且我们不使用匹配表（还）。对于只需要注册设备的主板，`.init_machine()` 除了 `of_platform_populate()` 调用外可以完全为空。

在 Tegra 示例中，这解释了 `/soc` 和 `/sound` 节点，但 SoC 节点的子节点呢？它们不也应该注册为 `platform_device` 吗？对于 Linux DT 支持，通用行为是子设备由父设备的设备驱动程序在驱动程序 `.probe()` 时注册。因此，I2C 总线设备驱动程序将为每个子节点注册一个 `i2c_client`，SPI 总线驱动程序将注册其 `spi_device` 子节点，对于其他 `bus_types` 也是类似的。根据该模型，可以编写一个驱动程序，绑定到 SoC 节点，并简单地为其 `/soc/interrupt-controller`、`/soc/serial`、`/soc/i2s` 和 `/soc/i2c` 在其 `.probe()` 钩子中注册 `platform_devices`。很简单，对吧？

实际上，事实证明，将某些 `platform_devices` 的子节点注册为更多的 `platform_devices` 是一种常见模式，设备树支持代码反映了这一点，并使上述示例更简单。`of_platform_populate()` 的第二个参数是 `of_device_id` 表，任何与该表中的条目匹配的节点也将注册其子节点。在 Tegra 案例中，代码可能看起来像这样：

```c
static void __init harmony_init_machine(void)
{
    /*... */
    of_platform_populate(NULL, of_default_bus_match_table, NULL, NULL);
}
```

“simple-bus”在 ePAPR 1.0 规范中被定义为表示简单内存映射总线的属性，因此 `of_platform_populate()` 代码可以被编写为假定与“simple-bus”兼容的节点将始终被遍历。然而，我们将其作为参数传递，以便主板支持代码始终可以覆盖默认行为。

[需要添加关于添加 I2C/SPI 等子设备的讨论]

## 附录 A：AMBA 设备
------------------------

ARM Primecells 是连接到 ARM AMBA 总线的某种设备，包括一些硬件检测和电源管理支持。在 Linux 中，`struct amba_device` 和 `amba_bus_type` 用于表示 Primecell 设备。然而，棘手的是，AMBA 总线上的并非所有设备都是 Primecells，对于 Linux，`amba_device` 和 `platform_device` 实例通常是同一总线段的兄弟节点。

当使用 DT 时，这给 `of_platform_populate()` 带来了问题，因为它必须决定是将每个节点注册为 `platform_device` 还是 `amba_device`。这不幸地使设备创建模型稍微复杂了一点，但解决方案结果并不是太具侵入性。如果一个节点与“arm,amba-primecell”兼容，那么 `of_platform_populate()` 将其注册为 `amba_device` 而不是 `platform_device`。