*** CerfBoard/Cube 的 StrongARM 版本已停产 ***

Intrinsyc CerfBoard 是基于 StrongARM 1110 的单板计算机，板的尺寸约为 2" 见方。它在背面包括一个以太网控制器、一个 RS232 兼容串口、一个 USB 功能端口和一个 CompactFlash+ 插槽。图片可在 Intrinsyc 网站 http://www.intrinsyc.com 找到。

本文档描述了 Linux 内核对 Intrinsyc CerfBoard 的支持。

此版本支持：

 - CompactFlash+ 插槽（在通用设置中选择 PCMCIA 以及可能需要的任何选项）

 - 板载 Crystal CS8900 以太网控制器（在网络设备中支持 Cerf CS8900A）

 - 带有串行控制台的串口（硬编码为 38400 8N1）

为了将此内核加载到您的 Cerf 上，您需要一台同时运行 BOOTP 和 TFTP 的服务器。关于如何使用引导加载程序的详细说明应该随您的评估套件一起提供。以下这组命令就足够了：

   make ARCH=arm CROSS_COMPILE=arm-linux- cerfcube_defconfig

   make ARCH=arm CROSS_COMPILE=arm-linux- zImage

   make ARCH=arm CROSS_COMPILE=arm-linux- modules

   cp arch/arm/boot/zImage <TFTP 目录>

support@intrinsyc.com