**What**: `/sys/devices/*/<our-device>/eeprom`

**Date**: August 2013

**Contact**: Oliver Schinagl <oliver@schinagl.nl>

**Description**: 对当前 Allwinner 的 A 系列 SoC 上的 SID（安全 ID）的只读访问。目前支持 A10、A10s、A13 和 A20 CPU。早期的 A1x 系列 SoC 导出 16 字节，而较新的 A20 SoC 则暴露 512 字节并分为多个部分。除了 16 字节的 SID 外，还有一个 SJTAG 区域、HDMI-HDCP 密钥和一些自定义密钥。以下是一个快速概述，详细信息请参阅用户手册：

 - 0x000：128 位根密钥（sun[457]i）

 - 0x010：128 位引导密钥（sun7i）

 - 0x020：64 位安全 JTAG 密钥（sun7i）

 - 0x028：16 位密钥配置（sun7i）

 - 0x02b：16 位自定义供应商密钥（sun7i）

 - 0x02c：320 位低通用密钥（sun7i）

 - 0x040：32 位读取控制访问（sun7i）

 - 0x064：224 位低通用密钥（sun7i）

 - 0x080：2304 位 HDCP 密钥（sun7i）

 - 0x1a0：768 位高通用密钥（sun7i）

**Users**: 任何想要读取 Allwinner 的 A 系列 CPU 上的 SID 的用户空间应用程序。