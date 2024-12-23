# ARM Marvell SoCs

这篇文档列出了当前 Linux 内核主线中支持的所有 ARM Marvell SoC。由于 Marvell 的 SoC 系列庞大而复杂，很难理解在 Linux 内核中何处可获得对特定 SoC 的支持。本文档旨在帮助理解这些 SoC 在哪里受到支持，并在可用时将它们与相应的公开数据表进行匹配。

# Orion 系列

- **种类**：

```
    - 88F5082
    - 88F5181
    - 88F5181L
    - 88F5182
        - **数据表**：http://www.embeddedarm.com/documentation/third-party/MV88F5182-datasheet.pdf
        - **程序员用户指南**：http://www.embeddedarm.com/documentation/third-party/MV88F5182-opensource-manual.pdf
        - **用户手册**：http://www.embeddedarm.com/documentation/third-party/MV88F5182-usermanual.pdf
    - 88F5281
        - **数据表**：http://www.ocmodshop.com/images/reviews/networking/qnap_ts409u/marvel_88f5281_data_sheet.pdf
    - 88F6183
```

- **核心**：Feroceon ARMv5 兼容

- **Linux 内核 mach 目录**：arch/arm/mach-orion5x

- **Linux 内核 plat 目录**：arch/arm/plat-orion

# Kirkwood 系列

- **种类**：

```
    - 88F6282（又名 Armada 300）
        - **产品简介**：http://www.marvell.com/embedded-processors/armada-300/assets/armada_310.pdf
    - 88F6283（又名 Armada 310）
        - **产品简介**：http://www.marvell.com/embedded-processors/armada-300/assets/armada_310.pdf
    - 88F6190
        - **产品简介**：http://www.marvell.com/embedded-processors/kirkwood/assets/88F6190-003_WEB.pdf
        - **硬件规格**：http://www.marvell.com/embedded-processors/kirkwood/assets/HW_88F619x_OpenSource.pdf
        - **功能规格**：http://www.marvell.com/embedded-processors/kirkwood/assets/FS_88F6180_9x_6281_OpenSource.pdf
    - 88F6192
        - **产品简介**：http://www.marvell.com/embedded-processors/kirkwood/assets/88F6192-003_ver1.pdf
        - **硬件规格**：http://www.marvell.com/embedded-processors/kirkwood/assets/HW_88F619x_OpenSource.pdf
        - **功能规格**：http://www.marvell.com/embedded-processors/kirkwood/assets/FS_88F6180_9x_6281_OpenSource.pdf
    - 88F6182
    - 88F6180
        - **产品简介**：http://www.marvell.com/embedded-processors/kirkwood/assets/88F6180-003_ver1.pdf
        - **硬件规格**：http://www.marvell.com/embedded-processors/kirkwood/assets/HW_88F6180_OpenSource.pdf
        - **功能规格**：http://www.marvell.com/embedded-processors/kirkwood/assets/FS_88F6180_9x_6281_OpenSource.pdf
    - 88F6281
        - **产品简介**：http://www.marvell.com/embedded-processors/kirkwood/assets/88F6281-004_ver1.pdf
        - **硬件规格**：http://www.marvell.com/embedded-processors/kirkwood/assets/HW_88F6281_OpenSource.pdf
        - **功能规格**：http://www.marvell.com/embedded-processors/kirkwood/assets/FS_88F6180_9x_6281_OpenSource.pdf
```

- **主页**：http://www.marvell.com/embedded-processors/kirkwood/

- **核心**：Feroceon ARMv5 兼容

- **Linux 内核 mach 目录**：arch/arm/mach-mvebu

- **Linux 内核 plat 目录**：无

# Discovery 系列

- **种类**：

```
    - MV78100
        - **产品简介**：http://www.marvell.com/embedded-processors/discovery-innovation/assets/MV78100-003_WEB.pdf
        - **硬件规格**：http://www.marvell.com/embedded-processors/discovery-innovation/assets/HW_MV78100_OpenSource.pdf
        - **功能规格**：http://www.marvell.com/embedded-processors/discovery-innovation/assets/FS_MV76100_78100_78200_OpenSource.pdf
    - MV78200
        - **产品简介**：http://www.marvell.com/embedded-processors/discovery-innovation/assets/MV78200-002_WEB.pdf
        - **硬件规格**：http://www.marvell.com/embedded-processors/discovery-innovation/assets/HW_MV78200_OpenSource.pdf
        - **功能规格**：http://www.marvell.com/embedded-processors/discovery-innovation/assets/FS_MV76100_78100_78200_OpenSource.pdf
    - MV76100
        - 未被 Linux 内核支持。
```

- **核心**：Feroceon ARMv5 兼容

- **Linux 内核 mach 目录**：arch/arm/mach-mv78xx0

- **Linux 内核 plat 目录**：arch/arm/plat-orion

# EBU Armada 系列

```
- Armada 370 种类：
    - 88F6710
    - 88F6707
    - 88F6W11
    - **产品简介**：http://www.marvell.com/embedded-processors/armada-300/assets/Marvell_ARMADA_370_SoC.pdf
    - **硬件规格**：http://www.marvell.com/embedded-processors/armada-300/assets/ARMADA370-datasheet.pdf
    - **功能规格**：http://www.marvell.com/embedded-processors/armada-300/assets/ARMADA370-FunctionalSpec-datasheet.pdf
- Armada 375 种类：
    - 88F6720
    - **产品简介**：http://www.marvell.com/embedded-processors/armada-300/assets/ARMADA_375_SoC-01_product_brief.pdf
- Armada 380/385 种类：
    - 88F6810
    - 88F6820
    - 88F6828
- Armada 390/398 种类：
    - 88F6920
    - 88F6928
    - **产品信息**：http://www.marvell.com/embedded-processors/armada-39x/
- Armada XP 种类：
    - MV78230
    - MV78260
    - MV78460
    - **注意**：不要与非 SMP 的 78xx0 SoC 混淆
    - **产品简介**：http://www.marvell.com/embedded-processors/armada-xp/assets/Marvell-ArmadaXP-SoC-product%20brief.pdf
    - **功能规格**：http://www.marvell.com/embedded-processors/armada-xp/assets/ARMADA-XP-Functional-SpecDatasheet.pdf
    - **硬件规格**：
        - http://www.marvell.com/embedded-processors/armada-xp/assets/HW_MV78230_OS.PDF
        - http://www.marvell.com/embedded-processors/armada-xp/assets/HW_MV78260_OS.PDF
        - http://www.marvell.com/embedded-processors/armada-xp/assets/HW_MV78460_OS.PDF
```
- **核心**：Sheeva ARMv7 兼容

- **Linux 内核 mach 目录**：arch/arm/mach-mvebu

- **Linux 内核 plat 目录**：无

# Avanta 系列

- **种类**：

```
    - 88F6510
    - 88F6530P
    - 88F6550
    - 88F6560
```

- **主页**：http://www.marvell.com/broadband/

- **产品简介**：http://www.marvell.com/broadband/assets/Marvell_Avanta_88F6510_305_060-001_product_brief.pdf

- 无公开数据表可用。核心：兼容 ARMv5

Linux 内核 mach 目录：主线中尚无代码，计划未来添加

Linux 内核 plat 目录：主线中尚无代码，计划未来添加

# Dove 系列（应用处理器）

-------------------

  版本：
```
        88AP510 又名 Armada 510
                产品简介：http://www.marvell.com/application-processors/armada-500/assets/Marvell_Armada510_SoC.pdf
                硬件规格：http://www.marvell.com/application-processors/armada-500/assets/Armada-510-Hardware-Spec.pdf
                功能规格：http://www.marvell.com/application-processors/armada-500/assets/Armada-510-Functional-Spec.pdf
```

  主页：http://www.marvell.com/application-processors/armada-500/

  核心：兼容 ARMv7

  目录：arch/arm/mach-mvebu（支持 DT 的平台）

             arch/arm/mach-dove（不支持 DT 的平台）

# PXA 2xx/3xx/93x/95x 系列

----------------------

  版本：

```
        PXA21x、PXA25x、PXA26x
             仅为应用处理器
             核心：ARMv5 XScale 核心
        PXA270、PXA271、PXA272
             产品简介：http://www.marvell.com/application-processors/pxa-family/assets/pxa_27x_pb.pdf
             设计指南：http://www.marvell.com/application-processors/pxa-family/assets/pxa_27x_design_guide.pdf
             开发者手册：http://www.marvell.com/application-processors/pxa-family/assets/pxa_27x_dev_man.pdf
             规格说明：http://www.marvell.com/application-processors/pxa-family/assets/pxa_27x_emts.pdf
             规格更新：http://www.marvell.com/application-processors/pxa-family/assets/pxa_27x_spec_update.pdf
             仅为应用处理器
             核心：ARMv5 XScale 核心
        PXA300、PXA310、PXA320
             PXA 300 产品简介：http://www.marvell.com/application-processors/pxa-family/assets/PXA300_PB_R4.pdf
             PXA 310 产品简介：http://www.marvell.com/application-processors/pxa-family/assets/PXA310_PB_R4.pdf
             PXA 320 产品简介：http://www.marvell.com/application-processors/pxa-family/assets/PXA320_PB_R4.pdf
             设计指南：http://www.marvell.com/application-processors/pxa-family/assets/PXA3xx_Design_Guide.pdf
             开发者手册：http://www.marvell.com/application-processors/pxa-family/assets/PXA3xx_Developers_Manual.zip
             规格说明：http://www.marvell.com/application-processors/pxa-family/assets/PXA3xx_EMTS.pdf
             规格更新：http://www.marvell.com/application-processors/pxa-family/assets/PXA3xx_Spec_Update.zip
             参考手册：http://www.marvell.com/application-processors/pxa-family/assets/PXA3xx_TavorP_BootROM_Ref_Manual.pdf
             仅为应用处理器
             核心：ARMv5 XScale 核心
        PXA930、PXA935
             带有通信处理器的应用处理器
             核心：ARMv5 XScale 核心
        PXA955
             带有通信处理器的应用处理器
             核心：兼容 ARMv7 的 Sheeva PJ4 核心
```

  注释：

    * 这一系列 SoC 源自英特尔开发的 XScale 系列，并于约 2006 年被 Marvell 收购。PXA21x、PXA25x、PXA26x、PXA27x、PXA3xx 和 PXA93x 由英特尔开发，而后期的 PXA95x 由 Marvell 开发。

    * 由于其 XScale 起源，这些 SoC 与 Marvell 的其他（Kirkwood、Dove 等）系列 SoC 几乎没有共同之处，除了与 MMP/MMP2 系列 SoC 之外。

  Linux 内核 mach 目录：arch/arm/mach-pxa

  Linux 内核 plat 目录：arch/arm/plat-pxa

# MMP/MMP2 系列（通信处理器）

-------------------------

  版本：

```
        PXA168，又名 Armada 168
             主页：http://www.marvell.com/application-processors/armada-100/armada-168.jsp
             产品简介：http://www.marvell.com/application-processors/armada-100/assets/pxa_168_pb.pdf
             硬件手册：http://www.marvell.com/application-processors/armada-100/assets/armada_16x_datasheet.pdf
             软件手册：http://www.marvell.com/application-processors/armada-100/assets/armada_16x_software_manual.pdf
             规格更新：http://www.marvell.com/application-processors/armada-100/assets/ARMADA16x_Spec_update.pdf
             Boot ROM 手册：http://www.marvell.com/application-processors/armada-100/assets/armada_16x_ref_manual.pdf
             App 节点包：http://www.marvell.com/application-processors/armada-100/assets/armada_16x_app_note_package.pdf
             仅为应用处理器
             核心：兼容 ARMv5 的 Marvell PJ1（Mohawk）
        PXA910
             主页：http://www.marvell.com/communication-processors/pxa910/
             产品简介：http://www.marvell.com/communication-processors/pxa910/assets/Marvell_PXA910_Platform-001_PB_final.pdf
             带有通信处理器的应用处理器
             核心：兼容 ARMv5 的 Marvell PJ1（Mohawk）
        MMP2，又名 Armada 610
             产品简介：http://www.marvell.com/application-processors/armada-600/assets/armada610_pb.pdf
             仅为应用处理器
             核心：兼容 ARMv7 的 Sheeva PJ4 核心
```

  注释：

    * 这一系列 SoC 源自英特尔开发的 XScale 系列，并于约 2006 年被 Marvell 收购。MMP/MMP2 系列的所有处理器均由 Marvell 开发。

    * 由于其 XScale 起源，这些 SoC 与 Marvell 的其他（Kirkwood、Dove 等）系列 SoC 几乎没有共同之处，除了与 PXA 系列 SoC 之外。

  Linux 内核 mach 目录：arch/arm/mach-mmp

  Linux 内核 plat 目录：arch/arm/plat-pxa

# Berlin 系列（数字娱乐）

---------------------

  版本：
```
	88DE3005，Armada 1500-mini
		设计名称：BG2CD
		核心：ARM Cortex-A9，PL310 L2CC
		主页：http://www.marvell.com/digital-entertainment/armada-1500-mini/
	88DE3100，Armada 1500
		设计名称：BG2
		核心：Marvell PJ4B（ARMv7），Tauros3 L2CC
		主页：http://www.marvell.com/digital-entertainment/armada-1500/
		产品简介：http://www.marvell.com/digital-entertainment/armada-1500/assets/Marvell-ARMADA-1500-Product-Brief.pdf
	88DE3114，Armada 1500 Pro
		设计名称：BG2-Q
		核心：四核 ARM Cortex-A9，PL310 L2CC
		主页：http://www.marvell.com/digital-entertainment/armada-1500-pro/
		产品简介：http://www.marvell.com/digital-entertainment/armada-1500-pro/assets/Marvell_ARMADA_1500_PRO-01_product_brief.pdf
	88DE???? 	设计名称：BG3
		核心：ARM Cortex-A15，CA15 集成 L2CC
```

  主页：http://www.marvell.com/digital-entertainment/

  目录：arch/arm/mach-berlin

  注释：

   * 这一系列 SoC 基于 Marvell Sheeva 或 ARM Cortex CPU，带有 Synopsys DesignWare（IRQ、GPIO、定时器等）和 PXA IP（SDHCI、USB、ETH 等）。

# 长期计划

---------------

 * 将 mach-dove/、mach-mv78xx0/、mach-orion5x/ 统一到 mach-mvebu/ 中，以在单个 mach-<foo> 目录中支持 Marvell EBU（工程业务部门）的所有 SoC。因此 plat-orion/ 将消失。

 * 将 mach-mmp/ 和 mach-pxa/ 统一到同一个 mach-pxa 目录中。因此 plat-pxa/ 将消失。

# 贡献者

-------

 Maen Suleiman <maen@marvell.com>

 Lior Amsalem <alior@marvell.com>

 Thomas Petazzoni <thomas.petazzoni@free-electrons.com>

 Andrew Lunn <andrew@lunn.ch>

 Nicolas Pitre <nico@fluxnic.net>

 Eric Miao <eric.y.miao@gmail.com>