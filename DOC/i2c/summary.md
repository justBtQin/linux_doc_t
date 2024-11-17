# I2C 和 SMBus

I2C（发音：I 平方 C）是由飞利浦开发的一种协议。它是一种慢速的双线协议（速度可变，最高可达 400 kHz），具有高速扩展（3.4 MHz）。它为连接许多通信需求不频繁或带宽低的设备提供了一种廉价的总线。I2C 在嵌入式系统中被广泛使用。一些系统使用不符合品牌要求的变体，因此不被宣传为 I2C。

SMBus（系统管理总线）基于 I2C 协议，主要是 I2C 协议和信号的子集。许多 I2C 设备可以在 SMBus 上工作，但一些 SMBus 协议添加了超出实现 I2C 品牌所需的语义。现代 PC 主板依赖于 SMBus。通过 SMBus 连接的最常见设备是使用 I2C EEPROM 配置的 RAM 模块和硬件监控芯片。

由于 SMBus 主要是广义 I2C 总线的子集，我们可以在许多 I2C 系统上使用其协议。然而，有些系统不符合 SMBus 和 I2C 的电气限制；还有一些系统无法实现所有常见的 SMBus 协议语义或消息。

# 术语

当我们谈论 I2C 时，我们使用以下术语：

  总线  ->   算法
            适配器
  设备  ->   驱动程序
            客户端

算法驱动程序包含可用于整个 I2C 适配器类的通用代码。每个特定的适配器驱动程序要么依赖于一个算法驱动程序，要么包含自己的实现。

驱动程序驱动程序（是的，这听起来很荒谬，抱歉）包含访问某种类型设备的通用代码。每个检测到的设备在客户端结构中都有自己的数据。通常，驱动程序和客户端的集成比算法和适配器更紧密。

对于给定的配置，您将需要一个 I2C 总线的驱动程序，以及您的 I2C 设备的驱动程序（通常每个设备一个驱动程序）。