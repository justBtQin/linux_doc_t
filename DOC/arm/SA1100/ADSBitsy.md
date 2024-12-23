# ADS Bitsy 单板计算机

（它与康柏的 Bitsy(iPAQ)不同）

如需更多详细信息，请联系 Applied Data Systems 或查看

http://www.applieddata.net/products.html

此产品的 Linux 支持由 Woojung Huh <whuh@applieddata.net> 提供

在进行任何“make config”之前使用“make adsbitsy_config”。这将为 ADS Bitsy 支持设置默认值。

内核 zImage 被链接以在 0xc0400000 处加载和执行。

Linux 可与随较新修订版板卡一起提供的 ADS 引导加载程序一起使用。请查看他们关于如何加载 Linux 的文档。

支持的外围设备：

- SA1100 LCD 帧缓冲区（8/16 位像素格式……某种程度上）

- SA1111 USB 主设备

- SA1100 串口

- pcmcia，紧凑型闪存

- 触摸屏(ucb1200)

- LCD 屏幕上的控制台

- 串口（ttyS[0-2]）

  - ttyS0 是串口控制台的默认设置

待办事项：

- 其他所有事项！ :-)

注意事项：

- 板载闪存被划分为 3 个分区。你在使用板载闪存时应小心。其分区与 GraphicsClient Plus 和 GraphicsMaster 不同

- 16 位像素模式需要与板卡随附的不同电缆。请联系 ADS 或查看手册以自行接线。目前，

  如果使用 16 位模式支持并切换到较低的 bpp 模式，时序可能会出错，导致图像损坏。此问题将很快得到修复。

欢迎任何贡献发送至 nico@fluxnic.net！