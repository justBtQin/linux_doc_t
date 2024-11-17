# 内核驱动程序 `i2c-viapro`

**支持的适配器**：
  * VIA Technologies, Inc. VT82C596A/B
    数据表：有时可在 VIA 网站获取
  * VIA Technologies, Inc. VT82C686A/B
    数据表：有时可在 VIA 网站获取
  * VIA Technologies, Inc. VT8231, VT8233, VT8233A
    数据表：可向 VIA 索取
  * VIA Technologies, Inc. VT8235, VT8237R, VT8237A, VT8237S, VT8251
    数据表：可向 VIA 索取且需签署保密协议
  * VIA Technologies, Inc. CX700
    数据表：可向 VIA 索取且需签署保密协议
  * VIA Technologies, Inc. VX800/VX820
    数据表：可在 http://linux.via.com.tw 获取
  * VIA Technologies, Inc. VX855/VX875
    数据表：可在 http://linux.via.com.tw 获取
  * VIA Technologies, Inc. VX900
    数据表：可在 http://linux.via.com.tw 获取

**作者**：
    Kyösti Mälkki <kmalkki@cc.hut.fi>
    Mark D. Studebaker <mdsxyz123@yahoo.com>
    Jean Delvare <jdelvare@suse.de>

**模块参数**

* force：整数
  强制启用 SMBus 控制器。危险！
* force_addr：整数
  在给定地址强制启用 SMBus。极其危险！

**描述**

`i2c-viapro` 是适用于具有支持的 VIA 南桥之一的主板的真正 SMBus 主机驱动程序。

您的 `lspci -n` 列表必须显示以下之一：

`device 1106:3050  (VT82C596A 功能 3)`
`device 1106:3051  (VT82C596B 功能 3)`
`device 1106:3057  (VT82C686 功能 4)`
`device 1106:3074  (VT8233)`
`device 1106:3147  (VT8233A)`
`device 1106:8235  (VT8231 功能 4)`
`device 1106:3177  (VT8235)`
`device 1106:3227  (VT8237R)`
`device 1106:3337  (VT8237A)`
`device 1106:3372  (VT8237S)`
`device 1106:3287  (VT8251)`
`device 1106:8324  (CX700)`
`device 1106:8353  (VX800/VX820)`
`device 1106:8409  (VX855/VX875)`
`device 1106:8410  (VX900)`

如果以上都未显示，您应该在 BIOS 中查找诸如启用 ACPI / SMBus 甚至 USB 之类的设置。

除了最旧的芯片（VT82C596A/B、VT82C686A 以及很可能 VT8231），此驱动程序支持 I2C 块事务。此类事务主要用于对 EEPROM 进行读取和写入。

CX700/VX800/VX820 另外似乎支持 SMBus PEC，但此驱动程序尚未实现。