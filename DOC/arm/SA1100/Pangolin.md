穿山甲是由对话技术（http://www.dialogue.com.tw/）生产的基于 StrongARM 1110 的评估平台。它具有 EISA 插槽，便于与 SDRAM/闪存卡、USB/串行/音频卡、紧凑型闪存卡、PCMCIA/IDE 卡和 TFT-LCD 卡进行配置。

要为穿山甲编译，您必须发出以下命令：

`make pangolin_config`

`make oldconfig`

`make zImage`

支持的外围设备：

- SA1110 串口（UART1/UART2/UART3）

- 闪存访问

- 紧凑型闪存驱动程序

- UDA1341 声音驱动程序

- 用于 800x600 16 位彩色 TFT-LCD 的 SA1100 LCD 控制器

- 用于 800x600 16 位彩色 TFT-LCD 的 MQ-200 驱动程序

- Penmount（触摸面板）驱动程序

- PCMCIA 驱动程序

- SMC91C94 局域网驱动程序

- IDE 驱动程序（实验性）