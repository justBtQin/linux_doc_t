# I2C 或 SMBus 设备内核驱动编写指南

这是为那些希望使用 Linux 作为协议主机/主设备（非从设备）为 I2C 或 SMBus 设备编写内核驱动程序的人提供的一个小指南。

要设置一个驱动程序，您需要做几件事。有些是可选的，有些事情可以稍有不同或完全不同。将此作为指南，而不是规则手册！

## 一、一般说明

尽量保持内核命名空间尽可能干净。做到这一点的最佳方法是为所有全局符号使用唯一的前缀。这对于导出的符号尤其重要，但对于未导出的符号这样做也是一个好主意。在本教程中，我们将使用前缀 `foo_' 。

## 二、驱动结构

通常，您将实现一个单一的驱动结构，并从其实例化所有客户端。请记住，驱动结构包含一般访问例程，并且除了您提供数据的字段外，应初始化为零。客户端结构保存设备特定的信息，如驱动模型设备节点及其 I2C 地址。

```c
static struct i2c_device_id foo_idtable[] = {
    { "foo", my_id_for_foo },
    { "bar", my_id_for_bar },
    { }
};

MODULE_DEVICE_TABLE(i2c, foo_idtable);

static struct i2c_driver foo_driver = {
   .driver = {
       .name    = "foo",
       .pm      = &foo_pm_ops,    /* 可选 */
    },

   .id_table    = foo_idtable,
   .probe       = foo_probe,
   .remove      = foo_remove,
    /* 如果需要设备自动检测： */
   .class       = I2C_CLASS_SOMETHING,
   .detect      = foo_detect,
   .address_list = normal_i2c,

   .shutdown    = foo_shutdown,    /* 可选 */
   .command     = foo_command,     /* 可选，已弃用 */
}
```

名称字段是驱动程序的名称，并且不得包含空格。它应与模块名称匹配（如果驱动程序可以编译为模块），尽管您可以使用 MODULE_ALIAS（在此示例中传递“foo”）为模块添加另一个名称。如果驱动程序名称与模块名称不匹配，则模块不会自动加载（热插拔/冷插拔）。

所有其他字段都是回调函数，将在下面解释。

## 三、额外客户端数据

每个客户端结构都有一个特殊的 `data` 字段，可以指向任何结构。您应该使用它来保存设备特定的数据。

```c
/* 存储值 */
void i2c_set_clientdata(struct i2c_client *client, void *data);

/* 检索值 */
void *i2c_get_clientdata(const struct i2c_client *client);
```

请注意，从内核 2.6.34 开始，您不再需要在 `remove()` 中或 `probe()` 失败时将 `data` 字段设置为 `NULL` 。`i2c-core` 在这些情况下会自动执行此操作。这些也是内核唯一会触及此字段的情况。

## 四、访问客户端

假设我们有一个有效的客户端结构。在某些时候，我们需要从客户端收集信息，或向客户端写入新信息。

我发现为此定义 `foo_read` 和 `foo_write` 函数很有用。在某些情况下，直接调用 `i2c` 函数会更容易，但许多芯片都有某种寄存器值的概念，可以很容易地封装起来。

以下函数是简单的示例，不应逐字复制。

```c
int foo_read_value(struct i2c_client *client, u8 reg)
{
    if (reg < 0x10)    /* 字节大小的寄存器 */
        return i2c_smbus_read_byte_data(client, reg);
    else        /* 字大小的寄存器 */
        return i2c_smbus_read_word_data(client, reg);
}

int foo_write_value(struct i2c_client *client, u8 reg, u16 value)
{
    if (reg == 0x10)    /* 不可能写入 - 驱动程序错误！ */
        return -EINVAL;
    else if (reg < 0x10)    /* 字节大小的寄存器 */
        return i2c_smbus_write_byte_data(client, reg, value);
    else            /* 字大小的寄存器 */
        return i2c_smbus_write_word_data(client, reg, value);
}
```

## 五、探测和附加

Linux I2C 堆栈最初是为支持对 PC 主板上的硬件监控芯片的访问而编写的，因此曾经嵌入了一些更适合 SMBus（和 PC）而不是 I2C 的假设。其中一个假设是，大多数适配器和设备驱动程序支持 `SMBUS_QUICK` 协议来探测设备存在。另一个假设是，设备及其驱动程序可以仅使用此类探测原语进行充分配置。

随着 Linux 及其 I2C 堆栈在嵌入式系统和复杂组件（如 DVB 适配器）中得到更广泛的使用，这些假设变得更加有问题。为 I2C 设备发出中断的驱动程序需要更多（和不同）的配置信息，处理无法通过协议探测区分的芯片变体或需要一些特定于板的信息才能正确运行的驱动程序也是如此。

### （一）设备/驱动程序绑定

系统基础设施，通常是特定于板的初始化代码或引导固件，报告存在哪些 I2C 设备。例如，可能有一个表，在内核中或来自引导加载程序，识别 I2C 设备并将它们与特定于板的有关 IRQ 和其他布线工件、芯片类型等的配置信息链接起来。这可用于为每个 I2C 设备创建 `i2c_client` 对象。

使用此绑定模型的 I2C 设备驱动程序与 Linux 中的任何其他类型的驱动程序一样工作：它们提供一个 `probe()` 方法来绑定到这些设备，并提供一个 `remove()` 方法来解除绑定。

```c
static int foo_probe(struct i2c_client *client,
                     const struct i2c_device_id *id);
static int foo_remove(struct i2c_client *client);
```

请记住，`i2c_driver` 不会创建那些客户端句柄。该句柄可以在 `foo_probe()` 期间使用。如果 `foo_probe()` 报告成功（零而不是负状态码），它可以保存句柄并使用它，直到 `foo_remove()` 返回。大多数 Linux 驱动程序都使用这种绑定模型。

当 `id_table` 中的名称字段中的条目与设备的名称匹配时，将调用探测函数。它将传递匹配的条目，以便驱动程序知道表中的哪一个匹配。

### （二）设备创建

如果您确切知道 I2C 设备连接到给定的 I2C 总线，则可以通过简单地填充 `i2c_board_info` 结构（其中包含设备地址和驱动程序名称）并调用 `i2c_new_device()` 来实例化该设备。这将创建设备，然后驱动程序核心将负责找到正确的驱动程序并调用其 `probe()` 方法。如果驱动程序支持不同的设备类型，则可以使用 `type` 字段指定您想要的类型。如果需要，您还可以指定 IRQ 和平台数据。

有时您知道设备连接到给定的 I2C 总线，但您不知道它使用的确切地址。例如，在电视适配器上会发生这种情况，其中同一驱动程序支持数十种略有不同的型号，并且 I2C 设备地址从一个型号更改为下一个型号。在这种情况下，您可以使用 `i2c_new_probed_device()` 变体，它与 `i2c_new_device()` 类似，不同之处在于它接受要探测的可能 I2C 地址的附加列表。为列表中的第一个响应地址创建一个设备。如果您期望在地址范围内存在多个设备，只需多次调用 `i2c_new_probed_device()` 。

对 `i2c_new_device()` 或 `i2c_new_probed_device()` 的调用通常发生在 I2C 总线驱动程序中。您可能希望保存返回的 `i2c_client` 引用以供以后使用。

### （三）设备检测

有时您事先不知道哪些 I2C 设备连接到给定的 I2C 总线。例如，PC 的 SMBus 上的硬件监控设备就是这种情况。在这种情况下，您可能希望让您的驱动程序自动检测支持的设备。这是传统模型的工作方式，现在作为标准驱动程序模型的扩展可用。

您只需定义一个检测回调，该回调将尝试识别支持的设备（对于支持的设备返回 0，对于不支持的设备返回 -ENODEV），要探测的地址列表以及设备类型（或类），以便仅探测可能连接了该类型设备（而未以其他方式枚举）的 I2C 总线。例如，需要自动检测的硬件监控芯片的驱动程序将其类设置为 `I2C_CLASS_HWMON`，并且只有具有包括 `I2C_CLASS_HWMON` 类的 I2C 适配器才会被此驱动程序探测。请注意，缺少匹配的类并不会阻止在给定的 I2C 适配器上使用该类型的设备。它所阻止的只是自动检测；设备的显式实例化仍然是可能的。

请注意，此机制纯粹是可选的，并非适用于所有设备。您需要一些可靠的方法来识别支持的设备（通常使用设备特定的专用标识寄存器），否则很可能会发生错误检测，事情可能会很快出错。请记住，I2C 协议不包括在给定地址检测芯片存在的任何标准方法，更不用说识别设备的标准方法了。更糟糕的是总线传输相关的语义缺失，这意味着相同的传输可能被一个芯片视为读操作，而被另一个芯片视为写操作。由于这些原因，在可能的情况下，应始终优先选择显式设备实例化而不是自动检测。

### （四）设备删除

使用 `i2c_new_device()` 或 `i2c_new_probed_device()` 创建的每个 I2C 设备都可以通过调用 `i2c_unregister_device()` 来注销。如果您未显式调用它，它将在底层 I2C 总线本身被删除之前自动调用，因为在设备驱动程序模型中，设备无法在其父设备之前存活。

## 六、初始化驱动程序

当内核启动或插入您的 `foo` 驱动程序模块时，您需要进行一些初始化。幸运的是，通常只需注册驱动程序模块就足够了。

```c
static int __init foo_init(void)
{
    return i2c_add_driver(&foo_driver);
}
module_init(foo_init);

static void __exit foo_cleanup(void)
{
    i2c_del_driver(&foo_driver);
}
module_exit(foo_cleanup);
```

`module_i2c_driver()` 宏可用于减少上述代码。

```c
module_i2c_driver(foo_driver);
```

请注意，有些函数被标记为 `__init` 。这些函数可以在内核启动（或模块加载）完成后删除。同样，被标记为 `__exit` 的函数在代码构建到内核中时会被编译器丢弃，因为它们永远不会被调用。

## 七、驱动程序信息

```c
/* 替换为您自己的名称和电子邮件地址 */
MODULE_AUTHOR("Frodo Looijaard <frodol@dds.nl>"
MODULE_DESCRIPTION("Driver for Barf Inc. Foo I2C devices");

/* 也允许一些非 GPL 许可证类型 */
MODULE_LICENSE("GPL");
```

## 八、电源管理

如果您的 I2C 设备在进入系统低功耗状态时需要特殊处理 - 例如将收发器置于低功耗模式，或激活系统唤醒机制 - 通过为驱动程序的 `dev_pm_ops` 实现适当的回调（如暂停和恢复）来实现。

这些是标准的驱动程序模型调用，它们的工作方式与任何其他驱动程序堆栈一样。调用可以睡眠，并且可以使用 I2C 消息传递到要暂停或恢复的设备（因为在发出这些调用时，它们的父 I2C 适配器处于活动状态，并且 IRQ 仍然启用）。

## 九、系统关机

如果您的 I2C 设备在系统关闭或重新启动（包括 `kexec`）时需要特殊处理 - 例如关闭某些东西 - 请使用 `shutdown()` 方法。

同样，这是标准的驱动程序模型调用，其工作方式与任何其他驱动程序堆栈相同：调用可以睡眠，并且可以使用 I2C 消息传递。

## 十、命令函数

支持通用的类似 `ioctl` 的函数回调。您很少需要这个，并且无论如何它的使用已被弃用，因此较新的设计不应使用它。

## 十一、发送和接收

如果您想与您的设备通信，有几个函数可以做到这一点。您可以在 `<linux/i2c.h>` 中找到所有这些函数。

如果您可以在普通 I2C 通信和 SMBus 级别通信之间进行选择，请使用后者。所有适配器都理解 SMBus 级别命令，但只有一些适配器理解普通 I2C ！

### （一）普通 I2C 通信

```c
int i2c_master_send(struct i2c_client *client, const char *buf,
                    int count);
int i2c_master_recv(struct i2c_client *client, char *buf, int count);
```

这些例程从/向客户端读取和写入一些字节。客户端包含 I2C 地址，因此您不必包含它。第二个参数包含要读取/写入的字节，第三个参数是要读取/写入的字节数（必须小于缓冲区的长度，也应小于 64k，因为 `msg.len` 是 `u16` 。）返回实际读取/写入的字节数。

```c
int i2c_transfer(struct i2c_adapter *adap, struct i2c_msg *msg,
                 int num);
```

这会发送一系列消息。每个消息可以是读或写，并且它们可以以任何方式混合。事务是组合的：事务之间不发送停止位。`i2c_msg` 结构对于每个消息包含客户端地址、消息的字节数和消息数据本身。

您可以阅读文件 `i2c-protocol` 以获取有关实际 I2C 协议的更多信息。

### （二）SMBus 通信

```c
s32 i2c_smbus_xfer(struct i2c_adapter *adapter, u16 addr,
                   unsigned short flags, char read_write, u8 command,
                   int size, union i2c_smbus_data *data);
```

这是通用的 SMBus 函数。以下所有函数都是基于它实现的。永远不要直接使用此函数！

```c
s32 i2c_smbus_read_byte(struct i2c_client *client);
s32 i2c_smbus_write_byte(struct i2c_client *client, u8 value);
s32 i2c_smbus_read_byte_data(struct i2c_client *client, u8 command);
s32 i2c_smbus_write_byte_data(struct i2c_client *client,
                              u8 command, u8 value);
s32 i2c_smbus_read_word_data(struct i2c_client *client, u8 command);
s32 i2c_smbus_write_word_data(struct i2c_client *client,
                              u8 command, u16 value);
s32 i2c_smbus_read_block_data(struct i2c_client *client,
                              u8 command, u8 *values);
s32 i2c_smbus_write_block_data(struct i2c_client *client,
                               u8 command, u8 length, const u8 *values);
s32 i2c_smbus_read_i2c_block_data(struct i2c_client *client,
                                  u8 command, u8 length, u8 *values);
s32 i2c_smbus_write_i2c_block_data(struct i2c_client *client,
                                   u8 command, u8 length,
                                   const u8 *values);
```

以下这些已从 `i2c-core` 中删除，因为它们没有用户，但如果需要，可以稍后添加回来：

```c
s32 i2c_smbus_write_quick(struct i2c_client *client, u8 value);
s32 i2c_smbus_process_call(struct i2c_client *client,
                           u8 command, u16 value);
s32 i2c_smbus_block_process_call(struct i2c_client *client,
                                 u8 command, u8 length, u8 *values);
```

所有这些事务在失败时返回负的 `errno` 值。`write` 事务在成功时返回 0；`read` 事务返回读取的值，除了块事务，它返回读取的值的数量。块缓冲区不需要超过 32 字节。

您可以阅读文件 `smbus-protocol` 以获取有关实际 SMBus 协议的更多信息。

## 十二、通用例程

下面列出了之前未提及的所有通用例程。

```c
/* 返回特定适配器的适配器编号 */
int i2c_adapter_id(struct i2c_adapter *adap);
```