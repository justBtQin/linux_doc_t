**标题：I2C 设备访问技术报告**

**一、引言**

通常，I2C 设备由内核驱动程序控制。但也可以通过 `/dev` 接口从用户空间访问适配器上的所有设备。为此，您需要加载 `i2c-dev` 模块。

每个注册的 I2C 适配器都会获得一个编号，从 0 开始计数。您可以检查 `/sys/class/i2c-dev/` 以查看哪个编号对应哪个适配器。或者，您可以运行 `i2cdetect -l` 以获取系统中当前存在的所有 I2C 适配器的格式化列表。`i2cdetect` 是 `i2c-tools` 包的一部分。

I2C 设备文件是字符设备文件，主设备号为 89，次设备号与上述分配的编号相对应。它们应被称为 `i2c-%d`（`i2c-0`、`i2c-1`、...、`i2c-10`、...）。所有 256 个次设备号都为 I2C 保留。

**二、C 示例**

假设您想从 C 程序访问 I2C 适配器。首先要做的是 `#include <linux/i2c-dev.h>`。请注意，有两个名为 `i2c-dev.h` 的文件，一个随 Linux 内核分发，旨在从内核驱动程序代码中包含，另一个随 `i2c-tools` 分发，旨在从用户空间程序中包含。您显然需要这里的第二个。

现在，您必须决定要访问哪个适配器。您应该检查 `/sys/class/i2c-dev/` 或运行 `i2cdetect -l` 来决定。适配器编号的分配有些动态，因此您不能对它们做太多假设。它们甚至可能在每次启动时都发生变化。

接下来，打开设备文件，如下所示：

```c
int file;
int adapter_nr = 2; /* 可能是动态确定的 */
char filename[20];

snprintf(filename, 19, "/dev/i2c-%d", adapter_nr);
file = open(filename, O_RDWR);
if (file < 0) {
    /* 错误处理；您可以检查 errno 以查看出了什么问题 */
    exit(1);
}
```

当您打开设备时，必须指定要与之通信的设备地址：

```c
int addr = 0x40; /* I2C 地址 */

if (ioctl(file, I2C_SLAVE, addr) < 0) {
    /* 错误处理；您可以检查 errno 以查看出了什么问题 */
    exit(1);
}
```

好了，您现在已经全部设置好了。您现在可以使用 SMBus 命令或普通的 I2C 与您的设备进行通信。如果设备支持，SMBus 命令是首选。下面将对两者进行说明。

```c
__u8 reg = 0x10; /* 要访问的设备寄存器 */
__s32 res;
char buf[10];

/* 使用 SMBus 命令 */
res = i2c_smbus_read_word_data(file, reg);
if (res < 0) {
    /* 错误处理：i2c 事务失败 */
} else {
    /* res 包含读取的字 */
}

/* 使用 I2C 写入，相当于
   i2c_smbus_write_word_data(file, reg, 0x6543) */
buf[0] = reg;
buf[1] = 0x43;
buf[2] = 0x65;
if (write(file, buf, 3)!= 3) {
    /* 错误处理：i2c 事务失败 */
}

/* 使用 I2C 读取，相当于 i2c_smbus_read_byte(file) */
if (read(file, buf, 1)!= 1) {
    /* 错误处理：i2c 事务失败 */
} else {
    /* buf[0] 包含读取的字节 */
}
```

请注意，通过 `read()` 和 `write()` 调用只能实现 I2C 和 SMBus 协议的子集。特别是，所谓的组合事务（在同一事务中混合读取和写入消息）不受支持。因此，此接口几乎从未被用户空间程序使用。

重要提示：由于使用了内联函数，在编译程序时必须使用 `-O` 或其变体！

**三、完整接口描述**

定义了以下 IOCTL：

- `ioctl(file, I2C_SLAVE, long addr)`：更改从设备地址。地址在参数的 7 个低位中传递（对于 10 位地址，在 10 个低位中传递）。
- `ioctl(file, I2C_TENBIT, long select)`：如果 `select` 不为 0，则选择 10 位地址，如果 `select` 为 0，则选择普通的 7 位地址。默认值为 0。此请求仅在适配器具有 `I2C_FUNC_10BIT_ADDR` 时有效。
- `ioctl(file, I2C_PEC, long select)`：如果 `select` 不为 0，则选择 SMBus PEC（数据包错误检查）生成和验证，如果 `select` 为 0，则禁用。默认值为 0。仅用于 SMBus 事务。此请求仅在适配器具有 `I2C_FUNC_SMBUS_PEC` 时有效；如果没有，它仍然是安全的，只是没有任何效果。
- `ioctl(file, I2C_FUNCS, unsigned long *funcs)`：获取适配器功能并将其放入 `*funcs` 中。
- `ioctl(file, I2C_RDWR, struct i2c_rdwr_ioctl_data *msgset)`：在中间不停止的情况下进行组合的读/写事务。仅在适配器具有 `I2C_FUNC_I2C` 时有效。参数是指向

```c
struct i2c_rdwr_ioctl_data {
    struct i2c_msg *msgs;  /* 指向简单消息数组的指针 */
    int nmsgs;             /* 要交换的消息数量 */
}
```

的指针。

`msgs[]` 本身包含指向数据缓冲区的进一步指针。函数将根据特定消息中是否设置了 `I2C_M_RD` 标志来写入或从该缓冲区读取数据。从设备地址和是否使用 10 位地址模式必须在每个消息中设置，覆盖上述 IOCTL 设置的值。

- `ioctl(file, I2C_SMBUS, struct i2c_smbus_ioctl_data *args)`：不打算直接调用；而是使用下面的访问函数。

您可以通过使用 `read(2)` 和 `write(2)` 调用进行普通的 I2C 事务。您不需要传递地址字节；而是在尝试访问设备之前通过 `ioctl I2C_SLAVE` 设置。

您可以通过以下函数进行 SMBus 级别的事务（有关详细信息，请参阅文档文件 `smbus-protocol`）：

```c
__s32 i2c_smbus_write_quick(int file, __u8 value);
__s32 i2c_smbus_read_byte(int file);
__s32 i2c_smbus_write_byte(int file, __u8 value);
__s32 i2c_smbus_read_byte_data(int file, __u8 command);
__s32 i2c_smbus_write_byte_data(int file, __u8 command, __u8 value);
__s32 i2c_smbus_read_word_data(int file, __u8 command);
__s32 i2c_smbus_write_word_data(int file, __u8 command, __u16 value);
__s32 i2c_smbus_process_call(int file, __u8 command, __u16 value);
__s32 i2c_smbus_read_block_data(int file, __u8 command, __u8 *values);
__s32 i2c_smbus_write_block_data(int file, __u8 command, __u8 length, __u8 *values);
```

所有这些事务在失败时返回 `-1`；您可以读取 `errno` 以查看发生了什么。`write` 事务在成功时返回 0；`read` 事务返回读取的值，除了 `read_block`，它返回读取的值的数量。块缓冲区不需要超过 32 字节。

上述函数都是内联函数，解析为对 `i2c_smbus_access` 函数的调用，该函数又以特定格式调用特定的 IOCTL。如果您想了解幕后发生的情况，请阅读源代码。

**四、实现细节**

对于感兴趣的人，以下是在您使用内核中的 `/dev` 接口到 I2C 时发生的代码流程：

1. 您的程序打开 `/dev/i2c-N` 并按上述“C 示例”部分中所述对其进行 `ioctl()` 调用。

2. 这些 `open()` 和 `ioctl()` 调用由 `i2c-dev` 内核驱动程序处理：分别参见 `i2c-dev.c:i2cdev_open()` 和 `i2c-dev.c:i2cdev_ioctl()`。您可以将 `i2c-dev` 视为可以从用户空间进行编程的通用 I2C 芯片驱动程序。

3. 一些 `ioctl()` 调用用于管理任务，并由 `i2c-dev` 直接处理。示例包括 `I2C_SLAVE`（设置您要访问的设备的地址）和 `I2C_PEC`（启用或禁用未来事务上的 SMBus 错误检查）。

4. 其他 `ioctl()` 调用由 `i2c-dev` 转换为内核函数调用。示例包括 `I2C_FUNCS`，它使用 `i2c.h:i2c_get_functionality()` 查询 I2C 适配器功能，以及 `I2C_SMBUS`，它使用 `i2c-core.c:i2c_smbus_xfer()` 执行 SMBus 事务。

`i2c-dev` 驱动程序负责检查来自用户空间的所有参数的有效性。在此之后，这些通过 `i2c-dev` 从用户空间发出的调用与内核 I2C 芯片驱动程序直接执行的调用之间没有区别。这意味着 I2C 总线驱动程序不需要实现任何特殊的东西来支持从用户空间的访问。

5. 这些 `i2c-core.c/i2c.h` 函数是您的 I2C 总线驱动程序的实际实现的包装器。每个适配器必须声明实现这些标准调用的回调函数。`i2c.h:i2c_get_functionality()` 调用 `i2c_adapter.algo->functionality()`，而 `i2c-core.c:i2c_smbus_xfer()` 如果实现了则调用 `adapter.algo->smbus_xfer()`，否则调用 `i2c-core.c:i2c_smbus_xfer_emulated()`，它又调用 `i2c_adapter.algo->master_xfer()`。

在您的 I2C 总线驱动程序处理这些请求后，执行沿着调用链向上运行，除了 `i2c-dev` 将返回的数据（如果有）打包为适合 `ioctl` 的格式外，几乎没有进行任何处理。