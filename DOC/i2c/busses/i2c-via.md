# 内核驱动程序 `i2c-via`

**支持的适配器**：
  * VIA Technologies, InC. VT82C586B
    数据表：可在 VIA 网站公开获取

**作者**：Kyösti Mälkki <kmalkki@cc.hut.fi>

**描述**

`i2c-via` 是适用于具有 VIA 芯片组的主板的 I2C 总线驱动程序。

支持以下 VIA PCI 芯片组：
 - MVP3、VP3、VP2/97、VPX/97 
 - 其他带有南桥 VT82C586B 的芯片组

您的 `lspci` 列表必须显示以下内容：

`Bridge: VIA Technologies, Inc. VT82C586B ACPI (rev 10)`

**问题？**

问：您的主板上有 VT82C586B，但列表中没有。

答：进入 BIOS 设置，找到 PCI 设备或类似部分。打开 USB 支持，然后再试。

问：没有错误消息，但 I2C 似乎仍然无法工作。

答：这种情况可能会发生。此驱动程序使用 VIA 在其数据表中推荐的引脚，但主板制造商实际连接线路的方式可能有多种。