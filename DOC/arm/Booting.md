# 引导 ARM Linux

=================

作者：Russell King

日期：2002 年 5 月 18 日

以下文档适用于 2.4.18-rmk6 及更高版本。

为了引导 ARM Linux，需要一个引导加载程序，它是在主内核之前运行的一个小程序。引导加载程序负责初始化各种设备，并最终调用 Linux 内核，向内核传递信息。

本质上，引导加载程序应该提供（至少）以下内容：

1. 设置并初始化 RAM。

2. 初始化一个串行端口。

3. 检测机器类型。

4. 设置内核标签列表。

5. 加载 initramfs。

6. 调用内核映像。

# 1. 设置并初始化 RAM

---------------------------

现有引导加载程序：必需

新引导加载程序：必需

引导加载程序应找到并初始化内核在系统中用于易失性数据存储的所有 RAM。它以与机器相关的方式执行此操作。（它可以使用内部算法自动定位和确定所有 RAM 的大小，或者使用机器中 RAM 的知识，或者引导加载程序设计者认为合适的任何其他方法。）

# 2. 初始化一个串行端口

-----------------------------

现有引导加载程序：可选，推荐

新引导加载程序：可选，推荐

引导加载程序应初始化并启用目标上的一个串行端口。这允许内核串行驱动程序自动检测它应该用于内核控制台的串行端口（通常用于调试目的或与目标通信）。

作为替代方案，引导加载程序可以通过标签列表将相关的“console=”选项传递给内核，指定端口和串行格式选项，如在

       Documentation/kernel-parameters.txt 中所述。

# 3. 检测机器类型

--------------------------

现有引导加载程序：可选

新引导加载程序：除仅 DT 平台外必需

引导加载程序应通过某种方法检测其正在运行的机器类型。这是硬编码值还是查看连接硬件的某种算法超出了本文档的范围。引导加载程序最终必须能够向内核提供 MACH_TYPE_xxx 值。（见 linux/arch/arm/tools/mach-types）。此值应通过寄存器 r1 传递给内核。

对于仅 DT 平台，机器类型将由设备树确定。将机器类型设置为全 1（~0）。这不是严格必需的，但可以确保它不会与任何现有类型匹配。

# 4. 设置引导数据

------------------

现有引导加载程序：可选，强烈推荐

新引导加载程序：必需

引导加载程序必须提供一个标签列表或一个 dtb 映像，以将配置数据传递给内核。引导数据的物理地址通过寄存器 r2 传递给内核。

## 4a. 设置内核标签列表

--------------------------------

引导加载程序必须创建并初始化内核标签列表。一个有效的标签列表以 ATAG_CORE 开始，以 ATAG_NONE 结束。ATAG_CORE 标签可以为空或不为空。空的 ATAG_CORE 标签的大小字段设置为“2”（0x00000002）。ATAG_NONE 必须将大小字段设置为零。

可以在列表中放置任意数量的标签。标签是否重复附加到前一个标签携带的信息上，或者是否完全替换其信息，这是未定义的；一些标签表现为前者，其他标签表现为后者。

引导加载程序必须至少传递系统内存的大小和位置，以及根文件系统的位置。因此，最小的标签列表应如下所示：

```
	+-----------+
base ->	| ATAG_CORE |  |
	+-----------+  |
	| ATAG_MEM  |  | 递增地址
	+-----------+  |
	| ATAG_NONE |  |
	+-----------+  v
```

标签列表应存储在系统 RAM 中。

标签列表必须放置在一个内存区域中，内核解压缩程序和 initrd“bootp”程序都不会覆盖它。推荐的放置位置是在 RAM 的前 16KiB 中。

## 4b. 设置设备树

-------------------------

引导加载程序必须将设备树映像（dtb）加载到系统 RAM 中的 64 位对齐地址，并使用引导数据对其进行初始化。dtb 格式在 Documentation/devicetree/booting-without-of.txt 中有文档记录。内核将在 dtb 的物理地址处查找 0xd00dfeed 的 dtb 魔术值，以确定是否传递了 dtb 而不是标签列表。

引导加载程序必须至少传递系统内存的大小和位置，以及根文件系统的位置。dtb 必须放置在一个内核解压缩程序不会覆盖它的内存区域中，同时仍在内核低内存映射所覆盖的区域内。

一个安全的位置是在 RAM 起始位置的 128MiB 边界之上。

# 5. 加载 initramfs。

------------------

现有引导加载程序：可选

新引导加载程序：可选

如果正在使用 initramfs，则必须将其放置在一个内核解压缩程序不会覆盖它的内存区域中，同时也在内核低内存映射所覆盖的区域内。

一个安全的位置是在设备树 blob 之上，设备树 blob 本身将按照上述建议加载在 RAM 起始位置的 128MiB 边界之上。

# 6. 调用内核映像

---------------------------

现有引导加载程序：必需

新引导加载程序：必需

有两种调用内核 zImage 的选项。如果 zImage 存储在闪存中，并且链接正确以从闪存运行，则引导加载程序可以直接在闪存中调用 zImage 是合法的。

zImage 也可以放置在系统 RAM 中并在那里调用。内核应放置在 RAM 的前 128MiB 中。建议将其加载在 32MiB 以上，以避免在解压缩之前需要重新定位，这将使引导过程稍微加快。

引导原始（非 zImage）内核时，约束更严格。在这种情况下，内核必须加载到系统中的偏移量等于 TEXT_OFFSET - PAGE_OFFSET。

在任何情况下，必须满足以下条件：

- 停止所有支持 DMA 的设备，以防止内存被虚假的网络数据包或磁盘数据损坏。这将为您节省大量调试时间。

- CPU 寄存器设置

  r0 = 0，

  r1 = 在（3）中发现的机器类型编号。

  r2 = 系统 RAM 中标签列表的物理地址，或

       系统 RAM 中设备树块（dtb）的物理地址

- CPU 模式

  所有中断形式必须禁用（IRQs 和 FIQs）

  对于不包括 ARM 虚拟化扩展的 CPU，CPU 必须处于 SVC 模式。（对于 Angel 存在一个特殊例外）

  包含虚拟化扩展支持的 CPU 可以进入 HYP 模式，以允许内核充分利用这些扩展。对于此类 CPU，这是推荐的引导方法，除非虚拟化已被预安装的管理程序使用。

  如果由于任何原因内核未进入 HYP 模式，则必须进入 SVC 模式。

- 缓存，MMU

  MMU 必须关闭。指令缓存可以打开或关闭。数据缓存必须关闭。

  如果内核进入 HYP 模式，上述要求除了适用于普通 PL1（特权内核模式）配置外，还适用于 HYP 模式配置。此外，必须禁用所有进入管理程序的陷阱，并且对于所有在架构上可能的外围设备和 CPU 资源，必须授予 PL1 访问权限。除了进入 HYP 模式外，系统配置应使得不包括虚拟化扩展支持的内核可以在没有额外帮助的情况下正确引导。

- 引导加载程序应通过直接跳转到内核映像的第一条指令来调用内核映像。

  对于支持 ARM 指令集的 CPU，即使对于 Thumb-2 内核，也必须以 ARM 状态进行入口。在仅支持 Thumb 指令集的 CPU 上，例如 Cortex-M 类 CPU，入口必须在 Thumb 状态下进行设置。