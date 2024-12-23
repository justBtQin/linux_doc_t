# 内核驱动 `lp855x`

====================

用于 `LP855x` 集成电路的背光驱动

支持的芯片：

    德州仪器 `LP8550`、`LP8551`、`LP8552`、`LP8553`、`LP8555`、`LP8556` 和

    `LP8557`

作者：Milo(Woogyom) Kim <milo.kim@ti.com>

# 描述

-----------

* 亮度控制

亮度可以通过 `pwm` 输入或 `i2c` 命令进行控制。`lp855x` 驱动程序支持这两种情况。

* 设备属性

1) `bl_ctl_mode`

背光控制模式。值：基于 `pwm` 或基于寄存器

2) `chip_id`

`lp855x` 芯片 ID。值：`lp8550/lp8551/lp8552/lp8553/lp8555/lp8556/lp8557`

# `lp855x` 的平台数据

------------------------

为了支持特定于平台的数据，可以使用 `lp855x` 平台数据。

* `name`：背光驱动名称。如果未定义，则设置默认名称。

* `device_control`：`DEVICE CONTROL` 寄存器的值。

* `initial_brightness`：背光亮度的初始值。

* `period_ns`：特定于平台的 `PWM` 周期值。单位为纳秒。仅在亮度为 `pwm` 输入模式时有效。

* `size_program`：`lp855x_rom_data` 的总大小。

* `rom_data`：新的 `eeprom/eprom` 寄存器列表。

示例 1) `lp8552` 平台数据：带有新 `eeprom` 数据的 `i2c` 寄存器模式

```
`#define EEPROM_A5_ADDR 0xA5`
`#define EEPROM_A5_VAL 0x4f /* EN_VSYNC=0 */`
`static struct lp855x_rom_data lp8552_eeprom_arr[] = {
    {EEPROM_A5_ADDR, EEPROM_A5_VAL},
};`
`static struct lp855x_platform_data lp8552_pdata = {
   .name = "lcd-bl",
   .device_control = I2C_CONFIG(LP8552),
   .initial_brightness = INITIAL_BRT,
   .size_program = ARRAY_SIZE(lp8552_eeprom_arr),
   .rom_data = lp8552_eeprom_arr,
};`
```

示例 2) `lp8556` 平台数据：带有默认 `rom` 数据的 `pwm` 输入模式

```
`static struct lp855x_platform_data lp8556_pdata = {
   .device_control = PWM_CONFIG(LP8556),
   .initial_brightness = INITIAL_BRT,
   .period_ns = 1000000,
};`
```