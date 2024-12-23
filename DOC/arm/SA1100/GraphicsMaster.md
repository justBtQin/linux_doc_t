**ADS GraphicsMaster 单板计算机**

如需更多详情，请联系 Applied Data Systems 或访问 http://www.applieddata.net/products.html

此产品的原始 Linux 支持由 Nicolas Pitre <nico@fluxnic.net> 提供。由 Woojung Huh <whuh@applieddata.net> 继续进行开发工作。

在进行任何“make config”之前，请使用“make graphicsmaster_config”。这将为 GraphicsMaster 支持设置默认值。

内核 zImage 被链接为在 0xc0400000 处加载和执行。

Linux 可与较新修订版的板载 ADS 引导加载程序一起使用。请参阅其关于如何加载 Linux 的文档。

支持的外围设备：

- SA1100 LCD 帧缓冲区（8/16 位...某种程度上）

- SA1111 USB 主设备

- 板载 SMC 92C96 以太网网卡

- SA1100 串行端口

- 闪存访问（MTD/JFFS）

- PCMCIA、紧凑型闪存

- 触摸屏（ucb1200）

- ps/2 键盘

- LCD 屏幕上的控制台

- 串行端口（ttyS[0-2]）

  - ttyS0 是串行控制台的默认端口

- 智能 I/O（ADC、键盘、数字输入等）

  请访问 http://www.eurotech-inc.com/linux-sbc.asp 以获取 IOCTL 文档和示例用户空间代码。ps/2 键盘通过此驱动程序进行复用。

待办事项：

- 其他所有事项！ :-)

注意事项：

- 板载闪存分为 3 个分区。mtd0 是存储 zImage 的位置。它已被标记为只读，以防止覆盖引导加载程序。 :) mtd1 用于 ramdisk.gz 映像。mtd2 是用户闪存空间，可用于 JFFS，或者如果您感到疯狂，也可以在其上运行 ext2。如果您不使用 ADS 引导加载程序，也可以覆盖 mtd1 分区。

- 16 位模式需要与板载设备不同的电缆。请联系 ADS 或查看手册以自行布线。目前，如果您使用 16 位模式支持进行编译并切换到较低的 bpp 模式，时序会出错，导致图像损坏。此问题将很快得到修复。

任何贡献都可以发送至 nico@fluxnic.net，将非常受欢迎！