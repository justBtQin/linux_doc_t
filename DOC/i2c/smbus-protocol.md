# SMBus 协议总结
======================

以下是 SMBus 协议的总结。它适用于该协议的所有修订版（1.0、1.1 和 2.0）。
本文件末尾简要描述了此软件包不支持的某些协议功能。

某些适配器仅理解 SMBus（系统管理总线）协议，它是 I2C 协议的子集。幸运的是，许多设备仅使用相同的子集，这使得它们可以在 SMBus 上使用。

如果您为某些 I2C 设备编写驱动程序，请尽可能尝试使用 SMBus 命令（如果设备仅使用 I2C 协议的该子集）。这使得设备驱动程序可以在 SMBus 适配器和 I2C 适配器上使用（SMBus 命令集在 I2C 适配器上自动转换为 I2C，但大多数纯 SMBus 适配器根本无法处理纯 I2C 命令）。

下面是 SMBus 协议操作及其执行的函数列表。请注意，SMBus 协议规范中使用的名称通常与这些函数名称不匹配。对于某些传递单个数据字节的操作，使用 SMBus 协议操作名称的函数执行的是完全不同的协议操作。

每个事务类型对应一个功能标志。在调用事务函数之前，设备驱动程序应始终（仅一次）检查相应的功能标志，以确保底层 I2C 适配器支持相关事务。有关详细信息，请参阅 <file:Documentation/i2c/functionality>。

# 符号关键
==============

`S`  (1 位) : 起始位
`P`  (1 位) : 停止位
`Rd/Wr` (1 位) : 读/写位。`Rd` 等于 1，`Wr` 等于 0。
`A, NA` (1 位) : 接受和反向接受位。 
`Addr`  (7 位): I2C 7 位地址。请注意，这通常可以扩展以获得 10 位 I2C 地址。
`Comm`  (8 位): 命令字节，一个数据字节，通常在设备上选择一个寄存器。
`Data`  (8 位): 一个普通的数据字节。有时，对于 16 位数据，我会写 `DataLow, DataHigh`。
`Count` (8 位): 一个包含块操作长度的数据字节。

`[..]`：由 I2C 设备发送的数据，与主机适配器发送的数据相反。

# SMBus 快速命令
===================

这在 `Rd/Wr` 位的位置向设备发送单个位。

`A Addr Rd/Wr [A] P`

功能标志：`I2C_FUNC_SMBUS_QUICK`

# SMBus 接收字节：`i2c_smbus_read_byte()`
==========================================

这从设备读取单个字节，不指定设备寄存器。有些设备非常简单，此接口就足够了；对于其他设备，如果您想读取与上一个 SMBus 命令相同的寄存器，这是一种简写。

`S Addr Rd [A] [Data] NA P`

功能标志：`I2C_FUNC_SMBUS_READ_BYTE`

# SMBus 发送字节：`i2c_smbus_write_byte()`
========================================

此操作是接收字节的反向操作：它向设备发送单个字节。有关更多信息，请参阅接收字节。

`S Addr Wr [A] Data [A] P`

功能标志：`I2C_FUNC_SMBUS_WRITE_BYTE`

# SMBus 读字节：`i2c_smbus_read_byte_data()`
============================================

这从设备的指定寄存器读取单个字节。寄存器通过 `Comm` 字节指定。

`S Addr Wr [A] Comm [A] S Addr Rd [A] [Data] NA P`

功能标志：`I2C_FUNC_SMBUS_READ_BYTE_DATA`

# SMBus 读字：`i2c_smbus_read_word_data()`
============================================

此操作与读字节非常相似；同样，数据从设备的指定寄存器读取，通过 `Comm` 字节指定。但这次，数据是一个完整的字（16 位）。

`S Addr Wr [A] Comm [A] S Addr Rd [A] [DataLow] A [DataHigh] NA P`

功能标志：`I2C_FUNC_SMBUS_READ_WORD_DATA`

请注意，有一个方便的函数 `i2c_smbus_read_word_swapped` 可用于读取两个数据字节顺序相反的情况（不符合 SMBus 规范，但很常见）。

# SMBus 写字节：`i2c_smbus_write_byte_data()`
==============================================

这将单个字节写入设备的指定寄存器。寄存器通过 `Comm` 字节指定。这是读字节操作的相反操作。

`S Addr Wr [A] Comm [A] Data [A] P`

功能标志：`I2C_FUNC_SMBUS_WRITE_BYTE_DATA`

# SMBus 写字：`i2c_smbus_write_word_data()`
==============================================

这是读字操作的相反操作。16 位的数据被写入设备的指定寄存器，通过 `Comm` 字节指定。 

`S Addr Wr [A] Comm [A] DataLow [A] DataHigh [A] P`

功能标志：`I2C_FUNC_SMBUS_WRITE_WORD_DATA`

请注意，有一个方便的函数 `i2c_smbus_write_word_swapped` 可用于写入两个数据字节顺序相反的情况（不符合 SMBus 规范，但很常见）。

# SMBus 进程调用：
===================

此命令选择设备寄存器（通过 `Comm` 字节），向其发送 16 位数据，并读取 16 位数据作为返回。

`S Addr Wr [A] Comm [A] DataLow [A] DataHigh [A] 
                             S Addr Rd [A] [DataLow] A [DataHigh] NA P`

功能标志：`I2C_FUNC_SMBUS_PROC_CALL`

# SMBus 块读：`i2c_smbus_read_block_data()`
==============================================

此命令从设备的指定寄存器（通过 `Comm` 字节指定）读取最多 32 字节的块。数据量由设备在 `Count` 字节中指定。

`S Addr Wr [A] Comm [A] 
           S Addr Rd [A] [Count] A [Data] A [Data] A... A [Data] NA P`

功能标志：`I2C_FUNC_SMBUS_READ_BLOCK_DATA`

# SMBus 块写：`i2c_smbus_write_block_data()`
================================================

与块读命令相反，这将最多 32 字节写入设备的指定寄存器，通过 `Comm` 字节指定。数据量在 `Count` 字节中指定。

`S Addr Wr [A] Comm [A] Count [A] Data [A] Data [A]... [A] Data [A] P`

功能标志：`I2C_FUNC_SMBUS_WRITE_BLOCK_DATA`

# SMBus 块写 - 块读进程调用
===========================================

SMBus 块写 - 块读进程调用在规范的修订版 2.0 中引入。

此命令选择设备寄存器（通过 `Comm` 字节），向其发送 1 至 31 字节的数据，并读取 1 至 31 字节的数据作为返回。

`S Addr Wr [A] Comm [A] Count [A] Data [A]...
                             S Addr Rd [A] [Count] A [Data]... A P`

功能标志：`I2C_FUNC_SMBUS_BLOCK_PROC_CALL`

# SMBus 主机通知
=================

此命令由作为主设备的 SMBus 设备发送给作为从设备的 SMBus 主机。
它与写字节的形式相同，只是命令代码被替换为警报设备的地址。

`[S] [HostAddr] [Wr] A [DevAddr] A [DataLow] A [DataHigh] A [P]`

# 数据包错误检查 (PEC)
===========================

数据包错误检查在规范的修订版 1.1 中引入。

PEC 在终止的 STOP 之前立即为使用它的传输添加一个 CRC-8 错误检查字节。

# 地址解析协议 (ARP)
=================================

地址解析协议在规范的修订版 2.0 中引入。它是一个高层协议，使用上述消息。

ARP 为协议添加了设备枚举和动态地址分配。所有 ARP 通信都使用从设备地址 0x61，并需要 PEC 校验和。

# SMBus 警报
===========

SMBus 警报在规范的修订版 1.0 中引入。

SMBus 警报协议允许多个 SMBus 从设备在 SMBus 主设备上共享单个中断引脚，同时仍然允许主设备知道是哪个从设备触发了中断。

在 Linux 内核中，这是通过以下方式实现的：
* 支持 SMBus 警报的 I2C 总线驱动程序应调用 `i2c_setup_smbus_alert()` 来设置 SMBus 警报支持。
* 可以触发 SMBus 警报的设备的 I2C 驱动程序应实现可选的 `alert()` 回调。

# I2C 块事务
======================

SMBus 层支持以下 I2C 块事务，为了完整性在此描述。
它们不是由 SMBus 规范定义的。

I2C 块事务不限制传输的字节数，但 SMBus 层限制为 32 字节。

# I2C 块读：`i2c_smbus_read_i2c_block_data()`
================================================

此命令从设备的指定寄存器读取字节块。

`S Addr Wr [A] Comm [A] 
           S Addr Rd [A] [Data] A [Data] A... A [Data] NA P`

功能标志：`I2C_FUNC_SMBUS_READ_I2C_BLOCK`

# I2C 块写：`i2c_smbus_write_i2c_block_data()`
==================================================

与块读命令相反，这将字节写入设备的指定寄存器。请注意，支持 0、2 或更多字节的命令长度，因为它们与数据无法区分。

`S Addr Wr [A] Comm [A] Data [A] Data [A]... [A] Data [A] P`

功能标志：`I2C_FUNC_SMBUS_WRITE_I2C_BLOCK`