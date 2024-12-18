# _DSD 与 GPIO 相关的设备属性

随着 ACPI 5.1 的发布，_DSD 配置对象最终允许为_CRS 返回的 GPIO（以及其他内容）赋予名称。以前，我们只能使用整数索引来查找相应的 GPIO，这很容易出错（例如，它取决于_CRS 的输出顺序）。

使用_DSD，我们现在可以使用名称而不是整数索引来查询 GPIO，如下所示的 ASL 示例：

```
  // 带有复位和关机 GPIO 的蓝牙设备
  Device (BTH)
  {
      Name (_HID, ...)

      Name (_CRS, ResourceTemplate ()
      {
          GpioIo (Exclusive, PullUp, 0, 0, IoRestrictionInputOnly,
                  "\\_SB.GPO0", 0, ResourceConsumer) {15}
          GpioIo (Exclusive, PullUp, 0, 0, IoRestrictionInputOnly,
                  "\\_SB.GPO0", 0, ResourceConsumer) {27, 31}
      })

      Name (_DSD, Package ()
      {
          ToUUID("daffd814-6eba-4d8c-8a91-bc9bbf4aa301"),
          Package ()
	  {
              Package () {"reset-gpio", Package() {^BTH, 1, 1, 0 }},
              Package () {"shutdown-gpio", Package() {^BTH, 0, 0, 0 }},
          }
      })
  }
```

支持的 GPIO 属性的格式为：

```
  Package () { "name", Package () { ref, index, pin, active_low }}
```

ref - 包含 GpioIo()/GpioInt()资源的 _CRS 的设备，通常是该设备本身（在我们的例子中是 BTH）。

index - _CRS 中 GpioIo()/GpioInt()资源的索引，从 0 开始。

pin - GpioIo()/GpioInt()资源中的引脚。通常为 0。

active_low - 如果为 1，则将 GPIO 标记为低电平有效。

由于 ACPI GpioIo()资源没有说明它是低电平有效还是高电平有效的字段，因此这里可以使用“active_low”参数。将其设置为 1 表示 GPIO 为低电平有效。

在我们的蓝牙示例中，“reset-gpio”指的是第二个 GpioIo()资源，该资源中的第二个引脚，GPIO 编号为 31。

## 驱动程序提供的 ACPI GPIO 映射


在某些系统中，ACPI 表不包含 _DSD，但为 _CRS 提供了 GpioIo()/GpioInt()资源，而设备驱动程序仍然需要与之配合使用。

在这些情况下，驱动程序可用的 ACPI 设备标识对象，_HID、_CID、_CLS、_SUB、_HRV 可用于标识设备，并且应该足以确定 _CRS 返回的 GpioIo()/GpioInt()资源列出的所有 GPIO 线的含义和用途。换句话说，一旦驱动程序识别了设备，就应该知道如何使用 GpioIo()/GpioInt()资源。完成此操作后，它可以简单地为要使用的 GPIO 线分配名称，并为 GPIO 子系统提供这些名称与相应的 ACPI GPIO 资源之间的映射。

要做到这一点，驱动程序需要将映射表定义为一个以 NULL 结尾的`struct acpi_gpio_mapping`对象数组，每个对象包含一个名称、一个指向行数据（`struct acpi_gpio_params`）对象数组的指针以及该数组的大小。每个`struct acpi_gpio_params`对象由三个字段组成，`crs_entry_index`、`line_index`、`active_low`，分别表示 _CRS 中目标 GpioIo()/GpioInt()资源的索引（从 0 开始）、该资源中目标线的索引（从 0 开始）以及该线的低电平有效标志，与上面指定的 _DSD GPIO 属性格式类似。

对于前面讨论的示例蓝牙设备，相关的数据结构如下所示：

```
static const struct acpi_gpio_params reset_gpio = { 1, 1, false };

static const struct acpi_gpio_params shutdown_gpio = { 0, 0, false };

static const struct acpi_gpio_mapping bluetooth_acpi_gpios[] = {

  { "reset-gpio", &reset_gpio, 1 },

  { "shutdown-gpio", &shutdown_gpio, 1 },

  { },

};
```

接下来，映射表需要作为第二个参数传递给`acpi_dev_add_driver_gpios()`，该函数将其注册到由其第一个参数指向的 ACPI 设备对象中。这应该在驱动程序的.probe()例程中完成。在删除时，驱动程序应该通过在之前注册该表的 ACPI 设备对象上调用`acpi_dev_remove_driver_gpios()`来注销其 GPIO 映射表。