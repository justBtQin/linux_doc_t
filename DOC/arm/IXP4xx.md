**Linux 在 Intel 的 IXP4xx 网络处理器上的发布说明**

由 Deepak Saxena <dsaxena@plexity.net> 维护

**1. 概述**

Intel 的 IXP4xx 网络处理器是一款高度集成的系统级芯片（SOC），主要用于网络应用。由于其低成本和低功耗，它在工业控制和其他领域也变得很受欢迎。IXP4xx 系列目前包括几个支持不同网络卸载功能（如加密、路由、防火墙等）的处理器。IXP46x 系列是更新版本，支持更快的速度、新的内存和闪存配置，以及更多的集成，如片上 I2C 控制器。

有关 CPU 各种版本的更多信息，请参阅：

   http://developer.intel.com/design/network/products/npfamily/ixp4xx.htm

Intel 还曾制造过 IXCP1100 CPU，它是 IXP4xx 的精简版，去除了很多网络智能。

**2. Linux 支持**

Linux 当前在 IXP4xx 芯片上支持以下功能：

- 双串口

- PCI 接口

- 闪存访问（MTD/JFFS）

- IXP42x 上通过 GPIO 的 I2C

- 用于输入/输出/中断的 GPIO

  有关访问函数，请参阅 arch/arm/mach-ixp4xx/include/mach/platform.h。

- 定时器（看门狗、操作系统）

芯片的以下组件不受 Linux 支持，需要使用 Intel 的专有 CSR 软件：

- USB 设备接口

- 网络接口（HSS、Utopia、NPEs 等）

- 网络卸载功能

如果需要使用上述任何功能，需要从以下地址下载 Intel 的软件：

   http://developer.intel.com/design/network/products/npfamily/ixp425.htm

请勿在 Linux 邮件列表中发布有关专有软件的问题。

有几个网站提供了使用 Intel 软件的指导/指针：

   http://sourceforge.net/projects/ixp4xx-osdg/

   用于使用 uClinux 和 Intel 库的开源开发者指南

http://gatewaymaker.sourceforge.net/

   使用 IXP425 和 Linux 构建网关的简单一页总结

http://ixp425.sourceforge.net/

   依赖 Intel 库的 IXP425 的 ATM 设备驱动程序

**3. 已知问题/限制**

**3a. 有限的入站 PCI 窗口**

IXP4xx 系列允许最多 256MB 的内存，但 PCI 接口只能将其中的 64MB 暴露给 PCI 总线。这意味着如果运行时内存大于 64MB，所有超出可访问范围的 PCI 缓冲区将使用 arch/arm/common/dmabounce.c 中的例程进行回退。

**3b. 有限的出站 PCI 窗口**

IXP4xx 提供两种访问 PCI 内存空间的方法：

1) 从 0x48000000 到 0x4bffffff 的直接映射窗口（64MB）。要通过此空间访问 PCI，我们只需将 BAR 映射到内核中，就可以使用标准的 read[bwl]/write[bwl]宏。由于速度原因，这是首选方法，但它将系统限制为只有 64MB 的 PCI 内存。如果使用视频卡和其他内存密集型设备，这可能会有问题。

2) 如果需要超过 64MB 的内存空间，IXP4xx 可以配置为使用间接寄存器来访问 PCI。这允许在总线上最多使用 128MB（0x48000000 到 0x4fffffff）的内存。这种方法的缺点是每个 PCI 访问都需要三次本地寄存器访问加上一个自旋锁，但在某些情况下，性能损失是可以接受的。此外，由于 PCI 窗口的间接性质，在这种情况下不能 mmap()PCI 设备。

默认情况下，出于性能原因使用直接方法。如果需要更多的 PCI 内存，请启用 IXP4XX_INDIRECT_PCI 配置选项。

**3c. GPIO 作为中断**

目前，代码仅处理电平敏感的 GPIO 中断。

**4. 支持的平台**

ADI Engineering Coyote 网关参考平台

http://www.adiengineering.com/productsCoyote.html

   ADI Coyote 平台是为构建小型住宅/办公室网关的参考设计。一个 NPE 连接到 10/100 接口，一个连接到 4 端口 10/100 交换机，第三个连接到 ADSL 接口。此外，它还支持通过 SLICs 连接的两个 POTs 接口。请注意，Linux ATM 不支持这些接口。最后，该平台有两个迷你 PCI 插槽，用于 802.11[bga]卡。最后，扩展总线上挂接了一个 IDE 端口。

Gateworks Avila 网络平台

http://www.gateworks.com/support/overview.php

   Avila 平台基本上是一个 IXDP425，4 个 PCI 插槽被迷你 PCI 插槽替换，扩展总线上挂接了一个 CF IDE 接口。

Intel IXDP425 开发平台

http://www.intel.com/design/network/products/npfamily/ixdpg425.htm

   这是 Intel 用于 IXDP425 的标准参考平台，也称为 Richfield 板。它包含 4 个 PCI 插槽、16MB 闪存、两个 10/100 端口和一个 ADSL 端口。

Intel IXDP465 开发平台

http://www.intel.com/design/network/products/npfamily/ixdp465.htm

   这基本上是一个 IXDP425，带有一个 IXP465 和 32MB 闪存，而不是只有 16MB。

Intel IXDPG425 开发平台

   这基本上是一个 ADI Coyote 板，添加了一个 NEC EHCI 控制器。该板的一个问题是，迷你 PCI 插槽仅连接了 3.3v 线，因此不能使用 PCI 到迷你 PCI 适配器和 E100 卡。因此，要使用 NFS 根文件系统，需要使用 CSR 或 WiFi 卡和一个 ramdisk，该 ramdisk 进行 BOOTP 然后执行 pivot_root 到 NFS。

Motorola PrPMC1100 处理器夹层卡

http://www.fountainsys.com

   PrPMC1100 基于 IXCP1100，旨在插入 IXP2400/2800 系统中充当系统控制器。它仅在板上包含一个 CPU 和 16MB 闪存，需要插入承载板才能正常工作。目前，Linux 仅支持该平台的 Motorola PrPMC 承载板。

**5. TODO 列表**

- 添加对 Coyote IDE 的支持

- 添加对基于边缘的 GPIO 中断的支持

- 添加对扩展总线上的 CF IDE 的支持

**6. 感谢**

IXP4xx 工作由 Intel Corp. 和 MontaVista Software, Inc. 资助。

以下人员贡献了补丁/评论等：

Lennerty Buytenhek

Lutz Jaenicke

Justin Mayfield

Robert E. Ranslam

[我知道我忘记了其他人，请通过电子邮件联系我以添加]

**最后更新：01/04/2005**