# S3C24XX USB Host support

========================

## Introduction

  此文档详细介绍了 S3C2410/S3C2440 内置的 OHCI USB 主机支持。

## Configuration

  至少启用以下内核选项：

  在 menuconfig 中：

   - 设备驱动程序 -->

     - USB 支持 -->

       - <*> 支持主机端 USB

       - <*> OHCI HCD 支持

  在.config 中：

    - CONFIG_USB

    - CONFIG_USB_OHCI_HCD

  一旦配置了这些选项，就可以配置和使用标准的 USB 设备驱动程序。

## Board Support

  驱动程序附加到一个平台设备，需要由 linux/arch/arm/mach-s3c2410 中的板级特定支持文件（如 mach-bast.c 或 mach-smdk2410.c）添加该设备。

  如果板级实现了额外的电源控制或过流监控，则仅需要平台设备的 platform_data 字段。OHCI 驱动程序不确保 S3C2410 的 MISCCTRL 寄存器的状态，因此如果两个端口都要用于主机，则板级支持文件有责任确保第二个端口配置为连接到 OHCI 核心。

## Platform Data

  有关平台设备数据的描述，请参阅 arch/arm/mach-s3c2410/include/mach/usb-control.h。在 linux/arch/arm/mach-s3c2410/usb-simtec.c 中可以找到一个实现。`struct s3c2410_hcd_info`包含一对函数，用于调用以启用过流检测和控制端口电源状态。

  端口编号为 0 和 1。

  power_control：

    用于启用或禁用端口上的电源。

  enable_oc：

    用于启用或禁用过流监控。此函数应获取或释放用于检查端口电源状况的资源，例如中断。

  report_oc：

    OHCI 驱动程序在端口的过流状态发生变化时填充此字段，以供过流代码调用。端口参数是一个位掩码，每个端口一位，位 X 表示端口 X 上的过流。已提供函数 s3c2410_usb_report_oc()以确保此调用正确。

  port[x]：

    此结构描述每个端口，0 或 1。如果端口已启用，平台驱动程序应将每个端口的标志字段设置为 S3C_HCDFLG_USED。

## Document Author

Ben Dooks，版权 2005 年 Simtec Electronics