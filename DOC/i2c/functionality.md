# 介绍

由于并非每个 I2C 或 SMBus 适配器都实现了 I2C 规范中的所有内容，因此当客户端有机会连接到适配器时，不能相信它需要的所有功能都已实现：客户端需要某种方法来检查适配器是否具有所需的功能。

# 功能常量

有关最新的功能常量列表，请查看 <uapi/linux/i2c.h>！

  `I2C_FUNC_I2C`  纯 I2C 级别的命令（纯 SMBus 适配器通常无法执行这些）
  `I2C_FUNC_10BIT_ADDR`  处理 10 位地址扩展
  `I2C_FUNC_PROTOCOL_MANGLING`  了解 `I2C_M_IGNORE_NAK`、`I2C_M_REV_DIR_ADDR` 和 `I2C_M_NO_RD_ACK` 标志（这些会修改 I2C 协议！）
  `I2C_FUNC_NOSTART`  可以跳过重复的起始序列
  `I2C_FUNC_SMBUS_QUICK`  处理 SMBus `write_quick` 命令
  `I2C_FUNC_SMBUS_READ_BYTE`  处理 SMBus `read_byte` 命令
  `I2C_FUNC_SMBUS_WRITE_BYTE`  处理 SMBus `write_byte` 命令
  `I2C_FUNC_SMBUS_READ_BYTE_DATA`  处理 SMBus `read_byte_data` 命令
  `I2C_FUNC_SMBUS_WRITE_BYTE_DATA`  处理 SMBus `write_byte_data` 命令
  `I2C_FUNC_SMBUS_READ_WORD_DATA`  处理 SMBus `read_word_data` 命令
  `I2C_FUNC_SMBUS_WRITE_WORD_DATA`  处理 SMBus `write_word_data` 命令
  `I2C_FUNC_SMBUS_PROC_CALL`  处理 SMBus `process_call` 命令
  `I2C_FUNC_SMBUS_READ_BLOCK_DATA`  处理 SMBus `read_block_data` 命令
  `I2C_FUNC_SMBUS_WRITE_BLOCK_DATA`  处理 SMBus `write_block_data` 命令
  `I2C_FUNC_SMBUS_READ_I2C_BLOCK`  处理 SMBus `read_i2c_block_data` 命令
  `I2C_FUNC_SMBUS_WRITE_I2C_BLOCK`  处理 SMBus `write_i2c_block_data` 命令

为了方便起见，还定义了上述标志的一些组合：

  `I2C_FUNC_SMBUS_BYTE`  处理 SMBus `read_byte` 和 `write_byte` 命令
  `I2C_FUNC_SMBUS_BYTE_DATA`  处理 SMBus `read_byte_data` 和 `write_byte_data` 命令
  `I2C_FUNC_SMBUS_WORD_DATA`  处理 SMBus `read_word_data` 和 `write_word_data` 命令
  `I2C_FUNC_SMBUS_BLOCK_DATA`  处理 SMBus `read_block_data` 和 `write_block_data` 命令
  `I2C_FUNC_SMBUS_I2C_BLOCK`  处理 SMBus `read_i2c_block_data` 和 `write_i2c_block_data` 命令
  `I2C_FUNC_SMBUS_EMUL`  处理可以由真正的 I2C 适配器（使用透明仿真层）仿真的所有 SMBus 命令

在 3.5 之前的内核版本中，`I2C_FUNC_NOSTART` 是作为 `I2C_FUNC_PROTOCOL_MANGLING` 的一部分实现的。

# 适配器实现

当您编写新的适配器驱动程序时，必须实现一个函数回调 `functionality`。下面给出了典型的实现。

典型的仅 SMBus 适配器将列出它支持的所有 SMBus 事务。这个示例来自 `i2c-piix4` 驱动程序：

```c
static u32 piix4_func(struct i2c_adapter *adapter)
{
    return I2C_FUNC_SMBUS_QUICK | I2C_FUNC_SMBUS_BYTE |
           I2C_FUNC_SMBUS_BYTE_DATA | I2C_FUNC_SMBUS_WORD_DATA |
           I2C_FUNC_SMBUS_BLOCK_DATA;
}
```

典型的全 I2C 适配器将使用以下内容（来自 `i2c-pxa` 驱动程序）：

```c
static u32 i2c_pxa_functionality(struct i2c_adapter *adap)
{
    return I2C_FUNC_I2C | I2C_FUNC_SMBUS_EMUL;
}
```

`I2C_FUNC_SMBUS_EMUL` 包括所有 SMBus 事务（加上 I2C 块事务），i2c-core 可以使用 `I2C_FUNC_I2C` 在没有适配器驱动程序任何帮助的情况下进行仿真。其思想是让客户端驱动程序检查对 SMBus 功能的支持，而不必关心所述功能是由适配器在硬件中实现，还是由 i2c-core 在 I2C 适配器之上的软件中仿真。

# 客户端检查

在客户端尝试连接到适配器，甚至进行测试以检查其支持的设备之一是否存在于适配器上之前，应该检查是否存在所需的功能。典型的方法如下（来自 `lm75` 驱动程序）：

```c
static int lm75_detect(...)
{
    (...)
    if (!i2c_check_functionality(adapter, I2C_FUNC_SMBUS_BYTE_DATA |
                                 I2C_FUNC_SMBUS_WORD_DATA))
        goto exit;
    (...)
}
```

在这里，`lm75` 驱动程序检查适配器是否可以同时进行 SMBus 字节数据和 SMBus 字数据事务。如果不能，则该驱动程序无法在此适配器上工作，继续下去没有意义。如果上述检查成功，则驱动程序知道它可以调用以下函数：`i2c_smbus_read_byte_data()`、`i2c_smbus_write_byte_data()`、`i2c_smbus_read_word_data()` 和 `i2c_smbus_write_word_data()`。作为经验法则，您使用 `i2c_check_functionality()` 测试的功能常量应与您的驱动程序调用的 `i2c_smbus_*` 函数完全匹配。

请注意，上述检查并不能告诉您功能是由底层适配器在硬件中实现还是由 i2c-core 在软件中仿真。客户端驱动程序不必关心这一点，因为 i2c-core 将在 I2C 适配器之上透明地实现 SMBus 事务。

# 通过 `/dev` 检查

如果您尝试从用户空间程序访问适配器，您将必须使用 `/dev` 接口。当然，您仍然需要检查是否支持您需要的功能。这是使用 `I2C_FUNCS` ioctl 完成的。以下是从 `i2cdetect` 程序改编的示例：

```c
int file;
if (file = open("/dev/i2c-0", O_RDWR) < 0) {
    /* 某种错误处理 */
    exit(1);
}
if (ioctl(file, I2C_FUNCS, &funcs) < 0) {
    /* 某种错误处理 */
    exit(1);
}
if (!(funcs & I2C_FUNC_SMBUS_QUICK)) {
    /* 哎呀，所需的功能（SMBus write_quick 函数）不可用！ */
    exit(1);
}
/* 现在可以安全地使用 SMBus write_quick 命令 */
```