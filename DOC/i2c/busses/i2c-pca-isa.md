# 内核驱动程序 `i2c-pca-isa`

**支持的适配器**：
此驱动程序支持使用飞利浦 PCA 9564 并行总线转 I2C 总线控制器的 ISA 板

**作者**：Ian Campbell <icampbell@arcom.com>，Arcom Control Systems

**模块参数**

* base int
  I/O 基地址
* irq int
  IRQ 中断
* clock int
  时钟速率，如 PCA9564 数据表表 1 中所述

**描述**

此驱动程序支持使用飞利浦 PCA 9564 并行总线转 I2C 总线控制器的 ISA 板