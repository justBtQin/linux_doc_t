# 基于 ACPI 的设备枚举

ACPI 5 引入了一组新的资源（UartTSerialBus、I2cSerialBus、

SpiSerialBus、GpioIo 和 GpioInt），可用于枚举串行总线控制器后面的从设备。

此外，我们开始看到集成在 SoC/芯片组中的外设仅出现在 ACPI 命名空间中。这些通常是通过内存映射寄存器访问的设备。

为了支持这一点并尽可能重用现有驱动程序，我们决定执行以下操作：

- 没有总线连接器资源的设备表示为平台设备。

- 有连接器资源的真实总线上的设备表示为 struct spi_device 或 struct i2c_device（标准 UART 不是总线，因此没有 struct uart_device）。

由于 ACPI 和设备树都表示设备（及其资源）的树，因此此实现尽可能遵循设备树方式。

ACPI 实现枚举总线上的设备（平台、SPI 和 I2C），创建物理设备并将它们绑定到 ACPI 命名空间中的 ACPI 句柄。

这意味着当 ACPI_HANDLE(dev)返回非 NULL 时，该设备是从 ACPI 命名空间中枚举的。此句柄可用于提取其他设备特定的配置。下面是一个示例。

## 平台总线支持

由于我们使用平台设备来表示未连接到任何物理总线的设备，因此我们只需要为设备实现一个平台驱动程序并添加支持的 ACPI ID。如果在其他非 ACPI 平台上使用相同的 IP 块，驱动程序可能可以立即使用，也可能需要一些小的更改。

为现有驱动程序添加 ACPI 支持应该非常简单。这是最简单的示例：

```c
#ifdef CONFIG_ACPI
static struct acpi_device_id mydrv_acpi_match[] = {
    /* ACPI IDs 在这里 */
    { }
};
MODULE_DEVICE_TABLE(acpi, mydrv_acpi_match);
#endif

static struct platform_driver my_driver = {
  ... 
   .driver = {
       .acpi_match_table = ACPI_PTR(mydrv_acpi_match),
    },
};
```

如果驱动程序需要执行更复杂的初始化，如获取和配置 GPIO，它可以获取其 ACPI 句柄并从 ACPI 表中提取此信息。

## DMA 支持

通过 ACPI 枚举的 DMA 控制器应在系统中注册，以提供对其资源的通用访问。例如，希望通过通用 API 调用 dma_request_slave_channel() 可访问从设备的驱动程序必须在探测函数结束时注册自己，如下所示：

```c
err = devm_acpi_dma_controller_register(dev, xlate_func, dw);
/* 处理错误（如果不是!CONFIG_ACPI 的情况） */
```

并在需要时实现自定义转换函数（通常 acpi_dma_simple_xlate() 就足够了），该函数将 struct acpi_dma_spec 提供的 FixedDMA 资源转换为相应的 DMA 通道。这种情况下的代码可能如下所示：

```c
#ifdef CONFIG_ACPI
struct filter_args {
    /* 为 filter_func 提供必要的信息 */
  ... 
};

static bool filter_func(struct dma_chan *chan, void *param)
{
    /* 选择正确的通道 */
  ... 
}

static struct dma_chan *xlate_func(struct acpi_dma_spec *dma_spec,
        struct acpi_dma *adma)
{
    dma_cap_mask_t cap;
    struct filter_args args;

    /* 为 filter_func 准备参数 */
  ... 
    return dma_request_channel(cap, filter_func, &args);
}
#else
static struct dma_chan *xlate_func(struct acpi_dma_spec *dma_spec,
        struct acpi_dma *adma)
{
    return NULL;
}
#endif
```
dma_request_slave_channel() 将为每个注册的 DMA 控制器调用 xlate_func()。在转换函数中，必须根据 struct acpi_dma_spec 中的信息和 struct acpi_dma 提供的控制器属性选择正确的通道。

客户端必须使用与特定 FixedDMA 资源对应的字符串参数调用 dma_request_slave_channel()。默认情况下，“tx”表示 FixedDMA 资源数组的第一个条目，“rx”表示第二个条目。下表显示了布局：

```
Device (I2C0)
{
  ... 
    Method (_CRS, 0, NotSerialized)
    {
        Name (DBUF, ResourceTemplate ()
        {
            FixedDMA (0x0018, 0x0004, Width32bit, _Y48)
            FixedDMA (0x0019, 0x0005, Width32bit, )
        })
  ... 
}
```
因此，在这个例子中，请求线为 0x0018 的 FixedDMA 是“tx”，下一个是“rx”。

在健壮的情况下，客户端不幸需要直接调用 acpi_dma_request_slave_chan_by_index()，并因此通过其索引选择特定的 FixedDMA 资源。

## SPI 串行总线支持

SPI 总线上的从设备有 SpiSerialBus 资源附加到它们。SPI 核心会自动提取此资源，并且当总线驱动程序调用 spi_register_master() 时，从设备将被枚举。

以下是 SPI 从设备的 ACPI 命名空间可能的样子：

```
Device (EEP0)
{
    Name (_ADR, 1)
    Name (_CID, Package() {
        "ATML0025",
        "AT25",
    })
  ... 
    Method (_CRS, 0, NotSerialized)
    {
        SPISerialBus(1, PolarityLow, FourWireMode, 8,
            ControllerInitiated, 1000000, ClockPolarityLow,
            ClockPhaseFirst, "\\_SB.PCI0.SPI1",)
    }
  ... 
```
SPI 设备驱动程序只需以与平台设备驱动程序类似的方式添加 ACPI ID。下面是一个将 ACPI 支持添加到 at25 SPI eeprom 驱动程序的示例（这是针对上面的 ACPI 代码片段）：

```c
#ifdef CONFIG_ACPI
static struct acpi_device_id at25_acpi_match[] = {
    { "AT25", 0 },
    { },
};
MODULE_DEVICE_TABLE(acpi, at25_acpi_match);
#endif

static struct spi_driver at25_driver = {
   .driver = {
      ... 
       .acpi_match_table = ACPI_PTR(at25_acpi_match),
    },
};
```
请注意，此驱动程序实际上需要更多信息，如 eeprom 的页面大小等，但在编写本文时，没有标准的传递方式。一个想法是在_DSM 方法中返回此信息，如下所示：

```
Device (EEP0)
{
  ... 
    Method (_DSM, 4, NotSerialized)
    {
        Store (Package (6)
        {
            "byte-len", 1024,
            "addr-mode", 2,
            "page-size, 32
        }, Local0)

        // 检查 UUID 等
        Return (Local0)
    }
```
然后 at25 SPI 驱动程序可以通过在其 ACPI 句柄上调用_DSM 来获取此配置，如下所示：

```c
struct acpi_buffer output = { ACPI_ALLOCATE_BUFFER, NULL };
struct acpi_object_list input;
acpi_status status;

/* 填充输入缓冲区 */

status = acpi_evaluate_object(ACPI_HANDLE(&spi->dev), "_DSM",
                              &input, &output);
if (ACPI_FAILURE(status))
    /* 处理错误 */

/* 在这里提取数据 */

kfree(output.pointer);
```

## I2C 串行总线支持

I2C 总线控制器后面的从设备只需像平台和 SPI 驱动程序一样添加 ACPI ID。I2C 核心在注册适配器后会自动枚举控制器设备后面的任何从设备。

下面是如何将 ACPI 支持添加到现有的 mpu3050 输入驱动程序的示例：

```c
#ifdef CONFIG_ACPI
static struct acpi_device_id mpu3050_acpi_match[] = {
    { "MPU3050", 0 },
    { },
};
MODULE_DEVICE_TABLE(acpi, mpu3050_acpi_match);
#endif

static struct i2c_driver mpu3050_i2c_driver = {
   .driver = {
       .name = "mpu3050",
       .owner = THIS_MODULE,
       .pm = &mpu3050_pm,
       .of_match_table = mpu3050_of_match,
       .acpi_match_table = ACPI_PTR(mpu3050_acpi_match),
    },
   .probe = mpu3050_probe,
   .remove = mpu3050_remove,
   .id_table = mpu3050_ids,
};
```
## GPIO 支持

ACPI 5 引入了两个新的资源来描述 GPIO 连接：GpioIo 和 GpioInt。这些资源可用于将设备使用的 GPIO 编号传递给驱动程序。ACPI 5.1 对此进行了扩展，添加了_DSD（设备特定数据），这使得可以命名 GPIO 等。

例如：

```
Device (DEV)
{
    Method (_CRS, 0, NotSerialized)
    {
        Name (SBUF, ResourceTemplate()
        {
            ...
			// 用于打开/关闭设备
			GpioIo (Exclusive, PullDefault, 0x0000, 0x0000,
				IoRestrictionOutputOnly, "\\_SB.PCI0.GPI0",
				0x00, ResourceConsumer,,)
			{
				// 引脚列表
				0x0055
			}

			// 设备的中断
			GpioInt (Edge, ActiveHigh, ExclusiveAndWake, PullNone,
					0x0000, "\\_SB.PCI0.GPI0", 0x00, ResourceConsumer,,)
			{
				// 引脚列表
				0x0058
			}

			... 
		}

		Return (SBUF)
	}

	// ACPI 5.1 _DSD 用于命名 GPIOs
	Name (_DSD, Package ()
	{
		ToUUID("daffd814-6eba-4d8c-8a91-bc9bbf4aa301"),
		Package ()
		{
			Package () {"power-gpios", Package() {^DEV, 0, 0, 0 }},
			Package () {"irq-gpios", Package() {^DEV, 1, 0, 0 }},
		}
	})
	... 
```
	
这些 GPIO 编号是相对于控制器的，路径 "\\_SB.PCI0.GPI0" 指定了到控制器的路径。为了在 Linux 中使用这些 GPIOs，我们需要将它们转换为相应的 Linux GPIO 描述符。有一个标准的 GPIO API 用于此目的，并在 Documentation/gpio/ 中有文档记录。

在上述示例中，我们可以使用如下代码获取相应的两个 GPIO 描述符：

```
#include <linux/gpio/consumer.h>
... 
struct gpio_desc *irq_desc, *power_desc;

irq_desc = gpiod_get(dev, "irq");
if (IS_ERR(irq_desc))
    // 处理错误

power_desc = gpiod_get(dev, "power");
if (IS_ERR(power_desc))
    // 处理错误

/* 现在我们可以使用 GPIO 描述符 */
```

还有这些函数的 devm_* 版本，它们在设备释放时会释放描述符。

有关与 GPIOs 相关的_DSD 绑定的更多信息，请参阅 Documentation/acpi/gpio-properties.txt。

## MFD 设备

MFD 设备将其子设备注册为平台设备。对于子设备，需要有一个 ACPI 句柄，它们可以使用该句柄来引用与它们相关的 ACPI 命名空间的部分。在 Linux MFD 子系统中，我们提供了两种方式：

o 子设备共享父 ACPI 句柄。

o MFD 单元可以指定设备的 ACPI id。

对于第一种情况，MFD 驱动程序不需要做任何事情。生成的子平台设备将其 ACPI_COMPANION() 设置为指向父设备。

如果 ACPI 命名空间中有一个我们可以使用 ACPI id 匹配的设备，则应如下设置 id：

```
static struct mfd_cell my_subdevice_cell = {
   .name = "my_subdevice",
    /* 设置相对于父设备的资源 */
   .acpi_pnpid = "XYZ0001",
};
```

然后，ACPI id "XYZ0001" 用于直接在 MFD 设备下查找 ACPI 设备，如果找到，则将该 ACPI 配套设备绑定到生成的子平台设备。