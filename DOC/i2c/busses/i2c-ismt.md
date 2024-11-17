# 内核驱动程序 `i2c-ismt`

**支持的适配器**：
  * Intel S12xx 系列 SOC

**作者**：
  Bill Brown <bill.e.brown@intel.com>

**模块参数**

* bus_speed（无符号整数）
允许更改总线速度。通常，总线速度由 BIOS 设置，并且永远不需要更改。但是，某些 SMBus 分析仪在调试期间监测总线的速度太慢，因此需要此模块参数。以 kHz 为单位指定总线速度。
可用的总线频率设置：
  0  无更改
  80 kHz
  100 kHz
  400 kHz
  1000 kHz

**描述**

S12xx 系列 SOC 具有一对集成的 SMBus 2.0 控制器，主要针对微服务器和存储市场。

S12xx 系列包含一对 PCI 功能。`lspci` 的输出将类似于以下内容：

  00:13.0 系统外设：Intel Corporation Centerton SMBus 2.0 控制器 0
  00:13.1 系统外设：Intel Corporation Centerton SMBus 2.0 控制器 1