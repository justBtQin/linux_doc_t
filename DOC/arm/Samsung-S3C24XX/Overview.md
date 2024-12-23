# S3C24XX ARM Linux 概述

==========================

# 引言

------------

三星 S3C24XX 系列的 ARM9 片上系统 CPU 由 ARM Linux 的“s3c2410”架构支持。目前支持 S3C2410、S3C2412、S3C2413、S3C2416、S3C2440、S3C2442、S3C2443 和 S3C2450 设备。

S3C2400 和 S3C24A0 系列的支持从未完成，相应的代码在一段时间后已被删除。如果有人希望恢复这项工作，可以从早期的 Linux 版本中获取部分支持。

S3C2416 和 S3C2450 设备非常相似，S3C2450 的支持包含在 arch/arm/mach-s3c2416 目录中。请注意，虽然这些 SoC 的核心支持已经存在，但一些额外的外设和中断的工作仍在进行中。

# 配置

-------------

提供了一个通用的 S3C2410 配置，可以通过`make s3c2410_defconfig`将其用作默认配置。此配置支持所有的机器及其常用功能。

某些机器可能也有自己的默认配置，请查看特定机器的文档。

# 布局

------

核心支持文件位于 arch/arm/plat-s3c24xx 中的平台代码中，头文件位于 include/asm-arm/plat-s3c24xx 中。此目录应保留平台代码（arch/arm/plat-s3c24xx）和 arch/arm/mach-s3c24*代码之间共享的项目。

每个 CPU 都有一个包含其支持文件的目录，以及承载该设备的机器。例如，S3C2410 包含在 arch/arm/mach-s3c2410 中，S3C2440 包含在 arch/arm/mach-s3c2440 中。

寄存器、内核和平台数据定义位于 arch/arm/mach-s3c2410 目录中的./include/mach 中。

arch/arm/plat-s3c24xx：

这里的文件要么对所有 s3c24xx 系列通用，要么仅对其中一些通用，并通过名称指示此状态。不是对所有设备通用的文件通常以系列中最初支持的 CPU 名称命名，以确保名称简短且不会与较新的设备混淆。

例如，最初 s3c244x 将涵盖 s3c2440 和 s3c2442，但随着 s3c2443 在该目录中共享的许多驱动程序不同，该名称变得无效。我们坚持使用 s3c2440-<x>来表示与 s3c2440 和 s3c2442 兼容的驱动程序。

这确实意味着要查找任何给定 SoC 的状态，可能需要搜索多个目录。

# 机器

--------

目前支持的机器如下：

Simtec Electronics EB2410ITX（BAST）

通用开发板，有关详细信息请参阅 EB2410ITX.txt。

Simtec Electronics IM2440D20（Osiris）

来自 Simtec Electronics 的 CPU 模块，带有 S3C2440A CPU、NAND 闪存和 PCMCIA 控制器。

Samsung SMDK2410

三星自己的开发板，专为 PDA 工作设计。

Samsung/Aiji SMDK2412

SMDK2440 的 S3C2412 版本。

Samsung/Aiji SMDK2413

SMDK2440 的 S3C2412 版本。

Samsung/Meritech SMDK2440

与 SMDK2440 兼容的 S3C2440 版本，具有 S3C2440 或 S3C2442 CPU 模块的选项。

Thorcom VR1000

定制嵌入式板。

HP IPAQ 1940

手持设备（IPAQ），有多种版本。

HP iPAQ rx3715

基于 S3C2440 的 IPAQ，根据出货的功能有多种变化。

Acer N30

基于 S3C2410 的 PDA，在 http://handhelds.org/moin/moin.cgi/AcerN30Documentation 有一个 Wiki 页面。

AML M5900

美国微系统的 M5900。

Nex Vision Nexcoder

Nex Vision Otom

Nex Vision 的两款机器。

# 添加新机器

-------------------

该架构设计为在一个内核构建中支持尽可能多的机器，在修改自己机器文件之外的项目之前，任何未来的添加都应牢记这一点。

机器定义应保留在 linux/arch/arm/mach-s3c2410 中，并且可以查看许多示例。

在提交补丁之前，请阅读内核补丁提交策略以及 Documentation/arm 目录。ARM 内核系列由 Russell King 管理，在 http://www.arm.linux.org.uk/developer/patches/ 有一个补丁系统，以及可以从同一站点找到的邮件列表。

作为礼貌，请将任何新机器或其他修改通知 <ben-linux@fluff.org>。

任何大规模的修改或新驱动程序在尝试之前都应在 ARM 内核邮件列表（linux-arm-kernel）上进行讨论。有关邮件列表信息，请参阅 http://www.arm.linux.org.uk/mailinglists/。

# I2C

---

CPU 中的硬件 I2C 核心支持单主模式，可以通过平台数据进行配置。

# RTC

---

支持板载 RTC 单元，包括闹钟功能。

最近已升级为使用新的 RTC 核心，该模块已重命名为 rtc-s3c，以适应新的 RTC 命名方案。

# 看门狗

--------

片上看门狗可通过标准看门狗接口使用。

# NAND

----

当前的内核现在支持 s3c2410 NAND 控制器。如果有任何问题，可以从 http://www.linux-mtd.infradead.org/ 获取最新的 linux-mtd 代码。

有关更多信息，请参阅 Documentation/arm/Samsung-S3C24XX/NAND.txt。

# SD/MMC

------

当前内核支持 S3C2443 之前的 SD/MMC 硬件，驱动程序是 drivers/mmc/host/s3cmci.c，支持 1 位和 4 位 SD 或 MMC 卡。

此驱动程序的 SDIO 行为尚未完全测试。目前不支持硬件 SDIO 中断。

# 串口

------

s3c2410 串口驱动程序支持内部串口。这些设备显示为 /dev/ttySAC0 到 3。

要为这些设备创建设备节点，请使用以下命令：

    mknod ttySAC0 c 204 64

    mknod ttySAC1 c 204 65

    mknod ttySAC2 c 204 66

# GPIO

----

核心包含操作 GPIO 的支持，在与本文件相同的目录中的 GPIO.txt 中有文档。

较新的内核带有 GPIOLIB，并且正在朝着这个方向迁移一些较旧的支持，以准备删除一些旧的调用。

从 v2.6.34 开始，朝着使用 gpiolib 支持的迁移几乎完成，只剩下很少的旧调用。

有关 S3C24XX 特定支持，请参阅 Documentation/arm/Samsung-S3C24XX/GPIO.txt，有关核心三星实现，请参阅 Documentation/arm/Samsung/GPIO.txt。

# 时钟管理

----------------

核心提供了 include/asm-arm/hardware/clock.h 头文件中定义的接口，以允许控制各种时钟单元。

# 挂起到内存

--------------

对于提供挂起到内存支持的板子，系统可以进入低功耗挂起状态。

有关更多信息，请参阅 Suspend.txt。

# SPI

---

内置硬件（尽管尚未支持 DMA）和通用 GPIO 解决方案都有 SPI 驱动程序。

# LEDs

----

通过平台驱动程序在 LED 子系统中支持基于 GPIO 的 LED。

# 平台数据

-------------

每当设备有基于机器的平台特定数据时，应注意确保以下几点：

    1) 如果机器在启动时未设置默认数据，不要将其留在设备中，以免混淆驱动程序。

    2) （如果可能）数据应标记为__initdata，以确保如果机器不是当前正在使用的机器，则数据将被丢弃。

最好的方法是创建一个函数，该函数 kmalloc()分配一块内存区域，然后复制__initdata 并设置相关设备的平台数据。**函数`__init`的作用**

使函数`__init`负责确保它与其他初始化代码一起被丢弃。

```c
static __init void s3c24xx_xxx_set_platdata(struct xxx_data *pd)
{
    struct s3c2410_xxx_mach_info *npd;

    npd = kmalloc(sizeof(struct s3c2410_xxx_mach_info), GFP_KERNEL);
    if (npd) {
        memcpy(npd, pd, sizeof(struct s3c2410_xxx_mach_info));
        s3c_device_xxx.dev.platform_data = npd;
    } else {
        printk(KERN_ERR "no memory for xxx platform data\n");
    }
}
```

注意，由于该代码被标记为`__init`，它不应在`arch/arm/mach-s3c2410/`之外导出，也不应通过`EXPORT_SYMBOL()`和相关函数导出到模块中。

# 端口贡献者

- Ben Dooks (BJD)

- Vincent Sanders

- Herbert Potzl

- Arnaud Patard (RTP)

- Roc Wu

- Klaus Fetscher

- Dimitry Andric

- Shannon Holland

- Guillaume Gourat (NexVision)

- Christer Weinigel (wingel) (Acer N30)

- Lucas Correia Villa Real (S3C2400 端口)

# 文档作者

Ben Dooks，版权 2004 - 2006 年 Simtec Electronics