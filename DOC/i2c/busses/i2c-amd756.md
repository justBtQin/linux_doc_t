# 内核驱动程序 `i2c-amd756`

**支持的适配器**：
  * AMD 756
  * AMD 766
  * AMD 768
  * AMD 8111
    数据表：可在 AMD 网站上公开获取

  * nVidia nForce
    数据表：不可用

**作者**：
    Frodo Looijaard <frodol@dds.nl>
    Philip Edelbrock <phil@netroedge.com>

**描述**

此驱动程序支持 AMD 756、766、768 和 8111 外设总线控制器以及 nVidia nForce。

请注意，对于 8111，有两个 SMBus 适配器。此驱动程序支持 SMBus 1.0 适配器，而 SMBus 2.0 适配器由 `i2c-amd8111` 驱动程序支持。