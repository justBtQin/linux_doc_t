# 内核驱动程序 `i2c-gpio-mux`

**作者**：Peter Korsgaard <peter.korsgaard@barco.com>

**描述**

`i2c-gpio-mux` 是一个 I2C 复用器驱动程序，通过主 I2C 总线和通过 GPIO 引脚控制的硬件复用器提供对 I2C 总线段的访问。

例如：

  ----------              ----------  Bus segment 1   - - - - -
 |          | SCL/SDA    |          |-------------- |           |
 |          |------------|          |
 |          |            |          | Bus segment 2 |           |
 |  Linux   | GPIO 1..N  |   MUX    |---------------   Devices
 |          |------------|          |               |           |
 |          |            |          | Bus segment M
 |          |            |          |---------------|           |
  ----------              ----------                  - - - - -

主 I2C 总线的 SCL/SDA 根据 GPIO 引脚 1..N 的设置复用至总线段 1..M。

**用法**

`i2c-gpio-mux` 使用平台总线，因此您需要提供一个 `struct platform_device`，其 `platform_data` 指向一个 `struct gpio_i2cmux_platform_data`，其中包含主总线的 I2C 适配器编号、要创建的总线段数量以及用于控制它的 GPIO 引脚。有关详细信息，请参阅 `include/linux/i2c-gpio-mux.h` 。

例如，对于一个通过 3 个 GPIO 引脚提供 4 个总线段的复用器，类似于这样：

```c
#include <linux/i2c-gpio-mux.h>
#include <linux/platform_device.h>

static const unsigned myboard_gpiomux_gpios[] = {
    AT91_PIN_PC26, AT91_PIN_PC25, AT91_PIN_PC24
};

static const unsigned myboard_gpiomux_values[] = {
    0, 1, 2, 3
};

static struct gpio_i2cmux_platform_data myboard_i2cmux_data = {
   .parent        = 1,
   .base_nr       = 2, /* 可选 */
   .values        = myboard_gpiomux_values,
   .n_values      = ARRAY_SIZE(myboard_gpiomux_values),
   .gpios         = myboard_gpiomux_gpios,
   .n_gpios       = ARRAY_SIZE(myboard_gpiomux_gpios),
   .idle          = 4, /* 可选 */
};

static struct platform_device myboard_i2cmux = {
   .name          = "i2c-gpio-mux",
   .id            = 0,
   .dev           = {
       .platform_data = &myboard_i2cmux_data,
    },
};
```

如果在注册时不知道绝对 GPIO 引脚编号，则可以提供芯片名称（`.chip_name`）和相对 GPIO 引脚编号，`i2c-gpio-mux` 驱动程序将为您完成工作，包括如果 GPIO 芯片不可立即使用时的延迟探测。

**设备注册**

在注册 `i2c-gpio-mux` 设备时，应将其使用的任何 GPIO 引脚的编号作为设备 ID 传递。这可确保每个实例具有不同的 ID。

或者，如果您不需要稳定的设备名称，可以简单地将 `PLATFORM_DEVID_AUTO` 作为设备 ID 传递，平台核心将为您的设备分配一个动态 ID。如果在注册时不知道绝对 GPIO 引脚编号，这甚至是唯一的选择。