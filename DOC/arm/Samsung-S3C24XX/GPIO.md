# S3C24XX GPIO 控制

====================

## 引言

- s3c2410 内核提供了一个接口，用于配置和操作 GPIO 引脚的状态，并获取有关它们的其他信息。

- s3c2410 GPIO 系统的配置有一些条件，请阅读三星提供的数据手册/用户手册以获取完整列表。

- 有关核心实现，请参阅 Documentation/arm/Samsung/GPIO.txt。

## GPIOLIB

- 随着 drivers/gpio 中 GPIOLIB 的出现，一些 GPIO 功能（如读取和写入引脚）的支持将被删除，以支持这种通用访问方法。

- 一旦所有现存的驱动程序都已转换，下面列出的函数将被删除（它们可能在不久的将来被标记为__deprecated）。

- 以下函数现在要么有特定于 s3c 的变体，要么被合并到 gpiolib 中。请参阅 arch/arm/plat-samsung/include/plat/gpio-cfg.h 中的定义：

    - s3c2410_gpio_setpin() -> gpio_set_value() 或 gpio_direction_output()

    - s3c2410_gpio_getpin() -> gpio_get_value() 或 gpio_direction_input()

    - s3c2410_gpio_getirq() -> gpio_to_irq()

    - s3c2410_gpio_cfgpin() -> s3c_gpio_cfgpin()

    - s3c2410_gpio_getcfg() -> s3c_gpio_getcfg()

    - s3c2410_gpio_pullup() -> s3c_gpio_setpull()

## GPIOLIB 转换

- 如果需要将板或驱动程序从已逐步淘汰的 s3c2410 API 转换为使用 gpiolib，则以下是有关该过程的一些注意事项：

    1. 如果您的板仅使用 GPIO（例如控制外设电源），则在使用之前需要使用 gpio_request() 声明该 GPIO。建议在初始化期间至少使用 WARN_ON() 检查返回值。

    2. s3c2410_gpio_cfgpin() 可以直接替换为 s3c_gpio_cfgpin()，因为它们具有相同的参数，并且可以接受引脚特定的值或更通用的特殊功能编号参数。

    3. s3c2410_gpio_pullup() 的更改存在问题，虽然 s3c2410_gpio_pullup(x, 1) 可以轻松转换为 s3c_gpio_setpull(x, S3C_GPIO_PULL_NONE)，但 s3c2410_gpio_pullup(x, 0) 则不那么容易。s3c2410_gpio_pullup(x, 0) 情况启用上拉（或在某些设备的情况下，下拉），因此新 API 区分了上拉和下拉情况。如果这成为问题，目前没有“仅打开”设置。

    4. s3c2410_gpio_setpin() 可以由 gpio_set_value() 替换，旧调用不会隐式将相关 GPIO 配置为输出。在使用 gpio_set_value() 之前，应更改 GPIO 方向。

    5. 如果引脚已设置为输入，则 s3c2410_gpio_getpin() 可以由 gpio_get_value() 替换。目前尚不清楚在输出引脚上使用 gpio_get_value() 时的行为是什么（s3c2410_gpio_getpin 将返回引脚应输出的值）。

    6. s3c2410_gpio_getirq() 应该可以直接替换为 gpio_to_irq() 调用。

- s3c2410_gpio 和 gpio_ 调用始终在相同的 GPIO 编号空间上操作，因此在调用之间转换 GPIO 编号没有问题。

## 头文件

- 有关 GPIO 引脚的列表及其配置值，请参阅 arch/arm/mach-s3c24xx/include/mach/regs-gpio.h。可以通过使用 #include <mach/regs-gpio.h> 包含此文件。

## PIN 编号

- 每个引脚在 regs-gpio.h 中都有一个唯一的编号，例如 S3C2410_GPA(0) 或 S3C2410_GPF(1)。这些定义用于告诉 GPIO 函数要使用哪个引脚。

- 随着向 gpiolib 的转换，不再像早期内核那样直接从 GPIO 引脚编号转换为寄存器基地址。这是由于较新的 SoC 所需的编号空间，其中较晚的 GPIO 不是连续的。

## 配置引脚

- 以下函数允许更改给定引脚的配置：

    void s3c_gpio_cfgpin(unsigned int pin, unsigned int function);

- 例如：

    s3c_gpio_cfgpin(S3C2410_GPA(0), S3C_GPIO_SFN(1));

    s3c_gpio_cfgpin(S3C2410_GPE(8), S3C_GPIO_SFN(2));

    这将把 GPA(0) 变为最低地址线 A0，并将 GPE(8) 连接到 SDIO/MMC 控制器的 SDDAT1 线。

## 读取当前配置

- 可以通过使用标准的 gpiolib 函数读取引脚的当前配置：

    s3c_gpio_getcfg(unsigned int pin);

- 返回值将来自可以传递给 s3c_gpio_cfgpin() 的相同值集。

## 配置上拉电阻

- S3C2410 上的大部分 GPIO 引脚可以启用弱上拉电阻。可以通过以下函数进行配置：

    void s3c_gpio_setpull(unsigned int pin, unsigned int to);

- 其中 to 值为 S3C_GPIO_PULL_NONE 以关闭上拉，S3C_GPIO_PULL_UP 以启用指定的上拉。其他任何值目前都是未定义的。

## 获取和设置引脚状态

- 这些调用现在由相关的 gpiolib 调用实现，请将您的板或驱动程序转换为使用 gpiolib。

## 获取与引脚关联的 IRQ 编号

- 标准的 gpiolib 函数可以将给定的引脚编号映射到 IRQ 编号，以传递给 IRQ 系统。

    int gpio_to_irq(unsigned int pin);

- 注意，并非所有引脚都有 IRQ。

## 作者

- Ben Dooks，2004 年 10 月 3 日

- 版权所有 2004 Ben Dooks，Simtec Electronics