# 这篇文档描述了 i2c 协议。或者说，在完成后会描述  :-)

# 符号关键
==============

`S`  (1 位) ：起始位
`P`  (1 位) ：停止位
`Rd/Wr` (1 位) ：读/写位。`Rd` 等于 1，`Wr` 等于 0。
`A, NA` (1 位) ：接受和反向接受位。
`Addr`  (7 位)：I2C 7 位地址。请注意，这通常可以扩展以获得 10 位 I2C 地址。
`Comm`  (8 位)：命令字节，一个数据字节，通常在设备上选择一个寄存器。
`Data`  (8 位)：一个普通的数据字节。有时，对于 16 位数据，我会写 `DataLow, DataHigh`。
`Count` (8 位)：一个包含块操作长度的数据字节。

`[..]`：由 I2C 设备发送的数据，与主机适配器发送的数据相反。

# 简单发送事务
======================

这对应于 `i2c_master_send`。

  `S Addr Wr [A] Data [A] Data [A]... [A] Data [A] P`

# 简单接收事务
===========================

这对应于 `i2c_master_recv`

  `S Addr Rd [A] [Data] A [Data] A... A [Data] NA P`

# 组合事务
====================

这对应于 `i2c_transfer`

它们就像上面的事务一样，但是不是发送停止位 `P`，而是发送起始位 `S` 并且事务继续。例如，一个字节读取，后跟一个字节写入：

  `S Addr Rd [A] [Data] NA S Addr Wr [A] Data [A] P`

# 修改事务
=====================

通过为 i2c 消息设置这些标志，还可以生成对 I2C 协议的以下修改。除了 `I2C_M_NOSTART` 之外，它们通常仅用于解决设备问题：

`I2C_M_IGNORE_NAK`：
    通常，如果客户端有 [NA]，消息会立即中断。设置此标志会将任何 [NA] 视为 [A]，并且发送所有消息。
    这些消息仍可能因 SCL lo->hi 超时而失败。

`I2C_M_NO_RD_ACK`：
    在读取消息中，主设备的 A/NA 位将被跳过。

`I2C_M_NOSTART`：
    在组合事务中，在某些时候不会生成 `S Addr Wr/Rd [A]`。例如，在第二个部分消息上设置 `I2C_M_NOSTART` 会生成类似这样的内容：
      `S Addr Rd [A] [Data] NA Data [A] P`
    如果为第一个部分消息设置 `I2C_M_NOSTART` 变量，我们不会生成 `Addr`，但会生成起始位 `S`。这可能会使总线上的所有其他客户端感到困惑，所以不要尝试这样做。

    这通常用于将系统内存中多个数据缓冲区的传输收集到对 I2C 设备看起来像单个传输的内容，但也可能用于某些罕见设备在方向更改之间使用。

`I2C_M_REV_DIR_ADDR`：
    此标志切换 `Rd/Wr` 标志。也就是说，如果您想要写入，但需要发出 `Rd` 而不是 `Wr`，反之亦然，您设置此标志。例如：
      `S Addr Rd [A] Data [A] Data [A]... [A] Data [A] P`

`I2C_M_STOP`：
    在消息之后强制停止条件（P）。一些与 I2C 相关的协议（如 SCCB）需要这样。通常，您真的不希望在一个传输的消息之间被中断。