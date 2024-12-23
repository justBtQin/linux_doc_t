# Samsung ARM Linux 概述
==========================

**介绍**
------------

三星的 ARM SoC 系列涵盖了许多相似的设备，从最初的 ARM9 到最新的 ARM 内核。本文档展示了当前内核支持的概述、如何使用以及支持代码的位置。

当前支持的 SoC 有：

- S3C24XX：完整列表请参阅 Documentation/arm/Samsung-S3C24XX/Overview.txt
- S3C64XX：S3C6400 和 S3C6410
- S5PC110 / S5PV210

**S3C24XX 系统**
---------------

在 Documnetation/arm/Samsung-S3C24XX/ 中仍有文档，涉及这些设备特定的架构和驱动程序。

有关实现细节和特定支持的更多信息，请参阅 Documentation/arm/Samsung-S3C24XX/Overview.txt 。

**配置**
-------------

由于目前没有将所有 SoC 统一到一个内核的方法，因此提供了许多配置。

s5pc110_defconfig - S5PC110 特定的默认配置
s5pv210_defconfig - S5PV210 特定的默认配置

**布局**
------

目录布局目前正在重组，包括几个平台目录以及为构建的 CPU 的特定机器目录。

plat-samsung 为所有实现提供了基础，并且是为构建处理的包含目录中的最后一个。它包含使系统运行的基本时钟、GPIO 和设备定义。

plat-s3c24xx 用于 s3c24xx 特定的构建，请参阅 S3C24XX 文档。

plat-s5p 用于 s5p 特定的构建，并包含 S5P 特定系统的通用支持。由于硬件差异，并非所有 S5P 都使用此目录中的所有功能。

**布局更改**
--------------

旧的 plat-s3c 和 plat-s5pc1xx 目录已被删除，必要时将支持移至 plat-samsung 或 plat-s5p。这些移动是为了简化包含和依赖关系问题，因为有太多不同的平台目录。

**端口贡献者**
-----------------

Ben Dooks (BJD)

Vincent Sanders

Herbert Potzl

Arnaud Patard (RTP)

Roc Wu

Klaus Fetscher

Dimitry Andric

Shannon Holland

Guillaume Gourat (NexVision)

Christer Weinigel (wingel) (Acer N30)

Lucas Correia Villa Real (S3C2400 端口)

**文档作者**
---------------

版权 2009 - 2010 Ben Dooks <ben-linux@fluff.org> 