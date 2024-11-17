# Linux I2C 从设备 EEPROM 后端
==============================

由 Wolfram Sang <wsa@sang-engineering.com> 于 2014 - 15 年编写

这是一个概念验证后端，在连接的 I2C 总线上表现得像一个 EEPROM。内存内容可以通过位于 sysfs 中的此文件从用户空间进行修改：

`/sys/bus/i2c/devices/<device-direcory>/slave-eeprom`

截至 2015 年，Linux 不支持对二进制 sysfs 文件的轮询，因此当另一个主设备更改内容时没有通知。