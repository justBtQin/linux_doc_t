# GPIO 映射
=============

本文档解释了如何将 GPIO 分配给给定的设备和功能。请注意，它仅适用于新的基于描述符的接口。有关已弃用的基于整数的 GPIO 接口的描述，请参阅 `gpio-legacy.txt`（实际上，旧接口没有真正的映射可能；您只是从某处获取一个整数并请求相应的 GPIO）。

使用 GPIO 的平台必须在其 Kconfig 中选择 `ARCH_REQUIRE_GPIOLIB`（如果 GPIO 使用是强制的）或 `ARCH_WANT_OPTIONAL_GPIOLIB`（如果 GPIO 支持可以省略）。然后，GPIO 的映射方式取决于平台用于描述其硬件布局的方式。目前，映射可以通过设备树、ACPI 和平台数据来定义。

**设备树**
-----------
在设备树中，GPIO 可以很容易地映射到设备和功能。确切的方法取决于提供 GPIO 的 GPIO 控制器，请参阅您的控制器的设备树绑定。

GPIO 映射在消费设备的节点中定义，在名为 `<function>-gpios` 的属性中，其中 `<function>` 是驱动程序将通过 `gpiod_get()` 请求的功能。例如：

```
foo_device {
    compatible = "acme,foo";
   ...
    led-gpios = <&gpio 15 GPIO_ACTIVE_HIGH>, /* 红 */
                <&gpio 16 GPIO_ACTIVE_HIGH>, /* 绿 */
                <&gpio 17 GPIO_ACTIVE_HIGH>; /* 蓝 */

    power-gpios = <&gpio 1 GPIO_ACTIVE_LOW>;
};
```

此属性将使 GPIO 15、16 和 17 可在驱动程序的“led”功能下使用，而 GPIO 1 作为“power”GPIO：

```c
struct gpio_desc *red, *green, *blue, *power;

red = gpiod_get_index(dev, "led", 0);
green = gpiod_get_index(dev, "led", 1);
blue = gpiod_get_index(dev, "led", 2);

power = gpiod_get(dev, "power");
```

“led”GPIO 将是高电平有效，而“power”GPIO 将是低电平有效（即 `gpiod_is_active_low(power)` 将为真）。

**ACPI**
----
ACPI 也以类似于 DT 的方式支持 GPIO 的功能名称。上述 DT 示例可以在 ACPI 5.1 中引入的 `_DSD`（设备特定数据）的帮助下转换为等效的 ACPI 描述：

```
Device (FOO) {
    Name (_CRS, ResourceTemplate () {
        GpioIo (Exclusive,..., IoRestrictionOutputOnly,
            "\\_SB.GPI0") {15} // 红
        GpioIo (Exclusive,..., IoRestrictionOutputOnly,
            "\\_SB.GPI0") {16} // 绿
        GpioIo (Exclusive,..., IoRestrictionOutputOnly,
            "\\_SB.GPI0") {17} // 蓝
        GpioIo (Exclusive,..., IoRestrictionOutputOnly,
            "\\_SB.GPI0") {1} // 电源
    })

    Name (_DSD, Package () {
        ToUUID("daffd814-6eba-4d8c-8a91-bc9bbf4aa301"),
        Package () {
            Package () {
                "led-gpios",
                Package () {
                    ^FOO, 0, 0, 1,
                    ^FOO, 1, 0, 1,
                    ^FOO, 2, 0, 1,
                }
            },
            Package () {
                "power-gpios",
                Package () {^FOO, 3, 0, 0},
            },
        }
    })
}
```

有关 ACPI GPIO 绑定的更多信息，请参阅 `Documentation/acpi/gpio-properties.txt`。

**平台数据**
-------------
最后，GPIO 可以使用平台数据绑定到设备和功能。希望这样做的板文件需要包含以下头文件：

```c
#include <linux/gpio/machine.h>
```

GPIO 通过查找表的方式映射，其中包含 `gpiod_lookup` 结构的实例。定义了两个宏来帮助声明此类映射：

```c
GPIO_LOOKUP(chip_label, chip_hwnum, dev_id, con_id, flags)
GPIO_LOOKUP_IDX(chip_label, chip_hwnum, dev_id, con_id, idx, flags)
```

其中：

  - `chip_label` 是提供 GPIO 的 `gpiod_chip` 实例的标签
  - `chip_hwnum` 是芯片内 GPIO 的硬件编号
  - `dev_id` 是将使用此 GPIO 的设备的标识符。它可以为 `NULL`，在这种情况下，它将与使用 `NULL` 设备的 `gpiod_get()` 调用匹配。
  - `con_id` 是从设备角度看的 GPIO 功能的名称。它可以为 `NULL`，在这种情况下，它将匹配任何功能。
  - `idx` 是功能内 GPIO 的索引。
  - `flags` 用于指定以下属性：
    * `GPIOF_ACTIVE_LOW` - 将 GPIO 配置为低电平有效
    * `GPIOF_OPEN_DRAIN` - GPIO 引脚为开漏类型。
    * `GPIOF_OPEN_SOURCE` - GPIO 引脚为开源类型。

在未来，这些标志可能会扩展以支持更多属性。

请注意，`GPIO_LOOKUP()` 只是 `GPIO_LOOKUP_IDX()` 中 `idx = 0` 的快捷方式。

然后可以如下定义查找表，空条目定义其结束：

```c
struct gpiod_lookup_table gpios_table = {
   .dev_id = "foo.0",
   .table = {
        GPIO_LOOKUP_IDX("gpio.0", 15, "led", 0, GPIO_ACTIVE_HIGH),
        GPIO_LOOKUP_IDX("gpio.0", 16, "led", 1, GPIO_ACTIVE_HIGH),
        GPIO_LOOKUP_IDX("gpio.0", 17, "led", 2, GPIO_ACTIVE_HIGH),
        GPIO_LOOKUP("gpio.0", 1, "power", GPIO_ACTIVE_LOW),
        { },
    },
};
```

并且板代码可以如下添加该表：

```c
gpiod_add_lookup_table(&gpios_table);
```

控制“foo.0”的驱动程序然后能够如下获取其 GPIO：

```c
struct gpio_desc *red, *green, *blue, *power;

red = gpiod_get_index(dev, "led", 0);
green = gpiod_get_index(dev, "led", 1);
blue = gpiod_get_index(dev, "led", 2);

power = gpiod_get(dev, "power");
gpiod_direction_output(power, 1);
```

由于“power”GPIO 被映射为低电平有效，此代码之后其实际信号将为 0。与旧的整数 GPIO 接口不同，低电平有效属性在映射期间处理，因此对 GPIO 使用者是透明的。