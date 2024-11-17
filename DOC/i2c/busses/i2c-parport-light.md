# 内核驱动程序 `i2c-parport-light`

**作者**：Jean Delvare <jdelvare@suse.de>

此驱动程序是 `i2c-parport` 的精简版。它不依赖于 `parport` 驱动程序，而是使用直接 I/O 访问。在嵌入式系统中，这种方式可能更受欢迎，因为为干净但繁重的 `parport` 处理浪费内存不是一个好的选择。缺点是可移植性降低，并且无法菊花链连接其他并行端口设备。

请参阅 `i2c-parport` 以获取文档。

**模块参数**：

* type：适配器的类型（请参阅 `i2c-parport` 或 `modinfo`）

* base：基本 I/O 地址
  默认值为 0x378，这对于并行端口来说相当常见，至少在 PC 上是这样。

* irq：可选的 IRQ
  如果您需要 SMBus 警报支持，则必须传递此参数，前提是您的适配器实际支持此功能。