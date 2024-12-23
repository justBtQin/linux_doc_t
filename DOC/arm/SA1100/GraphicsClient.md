**ADS GraphicsClient Plus 单板计算机**

如需更多详情，请联系 Applied Data Systems 或访问 http://www.applieddata.net/products.html。

此产品的原始 Linux 支持由 Nicolas Pitre <nico@fluxnic.net> 提供。由 Woojung Huh <whuh@applieddata.net> 继续进行开发工作。

目前可以通过 NFS 挂载根文件系统以提供完整的 Linux 环境。否则，可以使用 ramdisk 镜像。该板支持 MTD/JFFS，因此也可以在其上挂载其他内容。在进行任何“make config”之前，请使用“make graphicsclient_config”。这将为 GraphicsClient Plus 支持设置默认值。

内核 zImage 被链接为在 0xc0200000 处加载和执行。此外，以下寄存器在进入时应具有指定的值：

r0 = 0

r1 = 29（这是 GraphicsClient 架构编号）

Linux 可与随较新版本板一起提供的 ADS BootLoader 一起使用。请参阅其关于如何加载 Linux 的文档。据我所知，Angel 不适用于 GraphicsClient Plus。

曾经有一个仅称为 GraphicsClient 的板，ADS 曾生产过，但已停产。此代码不适用于带有 ADS bootloader 的旧板，但仍应可与 Angel 一起使用，如下所述。在任何情况下，如果您计划大规模部署某些内容，您可能应该获得较新版本的板。

如果在旧板上使用 Angel，以下是如果通过 Angel Debug Monitor 加载内核时的典型 angel.opt 选项文件：

**开始 angelboot.opt**

base 0xc0200000

entry 0xc0200000

r0 0x00000000

r1 0x0000001d

device /dev/ttyS1

options "38400 8N1"

baud 115200

#otherfile ramdisk.gz

#otherbase 0xc0800000

exec minicom

**结束 angelboot.opt**

然后将加载内核（如果上面的 otherfile/otherbase 行未被注释，则还加载 ramdisk）：

angelboot -f angelboot.opt zImage

在此假设该板连接到您的 PC 上的 ttyS1，并且 minicom 已预先配置为 /dev/ttyS1，默认情况下为 38400 波特率、8N1、无流控制。

如果使用其他引导加载程序，请确保它实现相同的功能，特别是在跳入内核之前的 r0/r1 寄存器值。

支持的外设：

- SA1100 LCD 帧缓冲区（8/16 位...某种程度上）

- 板载 SMC 92C96 以太网 NIC

- SA1100 串行端口

- 闪存访问（MTD/JFFS）

- pcmcia

- 触摸屏（ucb1200）

- ps/2 键盘

- LCD 屏幕上的控制台

- 串行端口（ttyS[0-2]）

- ttyS0 是串行控制台的默认端口

- 智能 I/O（ADC、键盘、数字输入等）

请参阅 http://www.eurotech-inc.com/linux-sbc.asp 以获取 IOCTL 文档和示例用户空间代码。ps/2 键盘通过此驱动程序进行复用。

待办事项：

- 使用新的 ucb_generic 层实现 UCB1200 音频

- 其他所有事项！ :-)

注意：

- 板载闪存分为 3 个分区。mtd0 是存储 ADS 启动 ROM 和 zImage 的位置。它已被标记为只读，以防止您覆盖引导加载程序。:) mtd1 用于 ramdisk.gz 镜像。mtd2 是用户闪存空间，可用于 JFFS，或者如果您感到疯狂，也可以在其之上运行 ext2。如果您不使用 ADS 引导加载程序，也欢迎您覆盖 mtd1 分区。

- 16 位模式需要与板随附的电缆不同的电缆。请联系 ADS 或查看手册以自行连接。目前，如果您编译时支持 16 位模式并切换到较低的位模式，时序会出错，导致图像损坏。此问题将很快得到修复。

任何贡献都可以发送到 nico@fluxnic.net，将非常受欢迎！