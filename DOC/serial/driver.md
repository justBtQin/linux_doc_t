**低级串行 API**
--------------------

本文件旨在对新串行驱动程序的某些方面进行简要概述。它并不完整，您有任何问题应联系 <rmk@arm.linux.org.uk>

参考实现包含在 `amba_pl011.c` 中。

**低级串行硬件驱动程序**
--------------------------------

低级串行硬件驱动程序负责向核心串行驱动程序提供端口信息（由 `uart_port` 定义）和一组控制方法（由 `uart_ops` 定义）。低级驱动程序还负责处理端口的中断，并提供任何控制台支持。

**控制台支持**
---------------

串行核心提供了一些辅助函数。包括识别正确的端口结构（通过 `uart_get_console`）和解析命令行参数（`uart_parse_options`）。

还有一个辅助函数（`uart_write_console`），它执行逐字符写入，将换行符转换为 `CRLF` 序列。建议驱动程序编写者使用此函数，而非实现自己的版本。

**锁定**
-------

使用 `port->lock` 执行必要的锁定是低级硬件驱动程序的责任。有一些例外（在下面的 `uart_ops` 列表中描述）。

有三个锁：每个端口的自旋锁、每个端口的 `tmpbuf` 信号量和一个整体信号量。

从核心驱动程序的角度来看，`port->lock` 锁定以下数据：

`port->mctrl`
`port->icount`
`info->xmit.head`（`circ->head`）
`info->xmit.tail`（`circ->tail`）

低级驱动程序可以自由使用此锁来提供任何额外的锁定。

核心驱动程序使用 `info->tmpbuf_sem` 锁来防止多线程访问用于端口写入的 `info->tmpbuf` 缓冲区。

`port_sem` 信号量用于防止在不适当的时候添加/删除或重新配置端口。自 `v2.6.27` 以来，此信号量一直是 `tty_port` 结构的 `'mutex'` 成员，通常称为端口互斥锁（或 `port->mutex`）。

**uart_ops**
--------

`uart_ops` 结构是 `serial_core` 和硬件特定驱动程序之间的主要接口。它包含了控制硬件的所有方法。

  `tx_empty(port)`
    此函数测试由 `port` 描述的端口的发送器 FIFO 和移位器是否为空。如果为空，此函数应返回 `TIOCSER_TEMT`，否则返回 0。如果端口不支持此操作，则应返回 `TIOCSER_TEMT`。
    锁定：无
    中断：取决于调用者
    此调用不得休眠

  `set_mctrl(port, mctrl)`
    此函数将由 `port` 描述的端口的调制解调器控制线设置为 `mctrl` 描述的状态。`mctrl` 的相关位包括：
    - `TIOCM_RTS`：RTS 信号。
    - `TIOCM_DTR`：DTR 信号。
    - `TIOCM_OUT1`：OUT1 信号。
    - `TIOCM_OUT2`：OUT2 信号。
    - `TIOCM_LOOP`：将端口设置为回环模式。
    如果相应的位被设置，信号应被驱动为有效。如果位被清除，信号应被驱动为无效。
    锁定：获取 `port->lock`
    中断：本地禁用
    此调用不得休眠

`get_mctrl(port)`
    返回调制解调器控制输入的当前状态。不应返回输出的状态，因为核心会跟踪其状态。状态信息应包括：
    - `TIOCM_CAR`：DCD 信号的状态
    - `TIOCM_CTS`：CTS 信号的状态
    - `TIOCM_DSR`：DSR 信号的状态
    - `TIOCM_RI`：RI 信号的状态
    如果信号当前被驱动为有效，则该位被设置。如果端口不支持 CTS、DCD 或 DSR，驱动程序应指示信号永久有效。如果 RI 不可用，信号不应被指示为有效。

    锁定：获取 `port->lock`
    中断：本地禁用
    此调用不得休眠

`stop_tx(port)`
    停止发送字符。这可能是由于 CTS 线变为无效或终端层指示由于 XOFF 字符要停止传输。

    驱动程序应尽快停止发送字符。

    锁定：获取 `port->lock`
    中断：本地禁用
    此调用不得休眠

`start_tx(port)`
    开始发送字符。

    锁定：获取 `port->lock`
    中断：本地禁用
    此调用不得休眠

`send_xchar(port,ch)`
    发送高优先级字符，即使端口已停止。这用于实现 XON/XOFF 流控制和 `tcflow()`。如果串行驱动程序未实现此函数，终端核心将字符附加到循环缓冲区，然后调用 `start_tx()` / `stop_tx()` 刷新数据。

    如果 `ch == '\0'`（`__DISABLED_CHAR`），则不发送。

    锁定：无
    中断：取决于调用者

`stop_rx(port)`
    停止接收字符；端口正在关闭过程中。

    锁定：获取 `port->lock`
    中断：本地禁用
    此调用不得休眠

`enable_ms(port)`
    启用调制解调器状态中断。

    此方法可能会被多次调用。在调用 `shutdown` 方法时，应禁用调制解调器状态中断。

    锁定：获取 `port->lock`
    中断：本地禁用
    此调用不得休眠

`break_ctl(port,ctl)`
    控制中断信号的传输。如果 `ctl` 非零，则应传输中断信号。当再次以零 `ctl` 调用时，应终止信号。

    锁定：无
    中断：取决于调用者
    此调用不得休眠

`startup(port)`
    获取任何中断资源并初始化任何低级驱动程序状态。启用端口进行接收。它不应激活 RTS 也不应激活 DTR；这将通过单独调用 `set_mctrl` 完成。

    此方法仅在端口最初打开时调用。

    锁定：获取 `port_sem`
    中断：全局禁用

`shutdown(port)`
    禁用端口，禁用任何可能有效的中断条件，并释放任何中断资源。它不应禁用 RTS 也不应禁用 DTR；这将已经通过单独调用 `set_mctrl` 完成。

    驱动程序在此调用完成后不得访问 `port->info`。

    此方法仅在此端口没有更多用户时调用。

    锁定：获取 `port_sem`
    中断：取决于调用者

`flush_buffer(port)`
    刷新任何写入缓冲区，重置任何 DMA 状态并停止任何正在进行的 DMA 传输。

    每当清除 `port->info->xmit` 循环缓冲区时，将调用此函数。

    锁定：获取 `port->lock`
    中断：本地禁用
    此调用不得休眠

`set_termios(port,termios,oldtermios)`
    更改端口参数，包括字长、奇偶校验、停止位。更新 `read_status_mask` 和 `ignore_status_mask` 以指示我们感兴趣接收的事件类型。相关的 `termios->c_cflag` 位包括：
    - `CSIZE` - 字大小
    - `CSTOPB` - 2 个停止位
    - `PARENB` - 奇偶校验启用
    - `PARODD` - 奇数奇偶校验（当 `PARENB` 生效时）
    - `CREAD` - 启用接收字符（如果未设置，仍从端口接收字符，但丢弃它们）
    - `CRTSCTS` - 如果设置，启用 CTS 状态更改报告
    - `CLOCAL` - 如果未设置，启用调制解调器状态更改报告
    相关的 `termios->c_iflag` 位包括：
    - `INPCK` - 启用帧和奇偶校验错误事件传递到终端层
    - `BRKINT`
    - `PARMRK` - 这两者都启用中断事件传递到终端层

    `IGNPAR` - 忽略奇偶校验和帧错误
    `IGNBRK` - 忽略中断错误，如果 `IGNPAR` 也设置，也忽略溢出错误
    `iflag` 位的相互作用如下（以奇偶校验错误为例）：
    奇偶校验错误  `INPCK`  `IGNPAR`
    不适用  0  不适用  接收字符，标记为 `TTY_NORMAL`
    无  1  不适用  接收字符，标记为 `TTY_NORMAL`
    是  1  0  接收字符，标记为 `TTY_PARITY`
    是  1  1  丢弃字符

    如果您的硬件支持硬件“软”流控制，还可以使用其他标志（例如，xon/xoff 字符）

    锁定：调用者持有 `port->mutex`
    中断：取决于调用者
    此调用不得休眠

`pm(port,state,oldstate)`
    在指定的端口上执行任何与电源管理相关的活动。`state` 表示新状态（由 `enum uart_pm_state` 定义），`oldstate` 表示以前的状态。

    此函数不应被用于获取任何资源。

    此函数将在端口最初打开和最终关闭时被调用，除非该端口也是系统控制台。这即使在未设置 `CONFIG_PM` 时也会发生。

    锁定：无
    中断：取决于调用者

  `type(port)`
    返回一个指向描述指定端口的字符串常量的指针，或者返回 `NULL`，在这种情况下将替换为字符串“unknown”。
    锁定：无。
    中断：调用者相关。

  `release_port(port)`
    释放当前由端口使用的任何内存和 I/O 区域资源。
    锁定：无。
    中断：调用者相关。

  `request_port(port)`
    请求端口所需的任何内存和 I/O 区域资源。如果任何请求失败，此函数返回时不应注册任何资源，并且在失败时应返回 `-EBUSY`。
    锁定：无。
    中断：调用者相关。

  `config_port(port,type)`
    为端口执行任何所需的自动配置步骤。`type` 包含所需配置的位掩码。`UART_CONFIG_TYPE` 表示端口需要检测和识别。`port->type` 应设置为找到的类型，或者如果未检测到端口，则设置为 `PORT_UNKNOWN`。

    `UART_CONFIG_IRQ` 表示中断信号的自动配置，应使用标准内核自动探测技术进行探测。在端口中断在内部硬连线的平台上（例如，片上系统实现），这不是必需的。
    锁定：无。
    中断：调用者相关。

  `verify_port(port,serinfo)`
    验证 `serinfo` 中包含的新串行端口信息是否适合此端口类型。
    锁定：无。
    中断：调用者相关。

  `ioctl(port,cmd,arg)`
    执行任何特定于端口的 `IOCTL`。`IOCTL` 命令必须使用 `<asm/ioctl.h>` 中找到的标准编号系统定义。
    锁定：无。
    中断：调用者相关。

  `poll_init(port)`
    由 `kgdb` 调用，执行支持 `poll_put_char()` 和 `poll_get_char()` 所需的最小硬件初始化。与 `->startup()` 不同，这不应请求中断。
    锁定：获取 `tty_mutex` 和 `tty_port->mutex`。
    中断：不适用。

  `poll_put_char(port,ch)`
    由 `kgdb` 调用，直接向串行端口写入单个字符。它可以并且应该阻塞，直到 TX FIFO 中有空间。
    锁定：无。
    中断：调用者相关。
    此调用不得休眠

  `poll_get_char(port)`
    由 `kgdb` 调用，直接从串行端口读取单个字符。如果有数据可用，应返回；否则，函数应立即返回 `NO_POLL_CHAR`。
    锁定：无。
    中断：调用者相关。
    此调用不得休眠

**其他函数**
---------------

`uart_update_timeout(port,cflag,baud)`
    根据位数、奇偶校验、停止位和波特率更新 `port->timeout`（FIFO 排空超时）。
    锁定：调用者应获取 `port->lock`
    中断：不适用

`uart_get_baud_rate(port,termios,old,min,max)`
    返回指定 `termios` 的数字波特率，考虑到特殊的 38400 波特“技巧”。B0 波特率映射到 9600 波特。
    如果波特率不在 `min..max` 内，并且 `old` 不为 `NULL`，则将尝试原始波特率。如果超出 `min..max` 约束，则返回 9600 波特。`termios` 将更新为使用的波特率。
    注意：`min..max` 必须始终允许选择 9600 波特。
    锁定：调用者相关。
    中断：不适用

`uart_get_divisor(port,baud)`
    返回指定波特率的除数（`baud_base / baud`），适当舍入。
    如果选择 38400 波特和自定义除数，则返回自定义除数。
    锁定：调用者相关。
    中断：不适用

`uart_match_port(port1,port2)`
    此实用函数可用于确定两个 `uart_port` 结构是否描述相同的端口。
    锁定：不适用
    中断：不适用

`uart_write_wakeup(port)`
    当发送缓冲区中的字符数量低于阈值时，驱动程序应调用此函数。
    锁定：应持有 `port->lock`。
    中断：不适用

`uart_register_driver(drv)`
    向核心驱动程序注册一个 `uart` 驱动程序。我们反过来向终端层注册，并初始化每个端口的核心驱动程序状态。
    `drv->port` 应为 `NULL`，并且在调用成功后应使用 `uart_add_one_port` 注册每个端口的结构。
    锁定：无
    中断：启用

`uart_unregister_driver()`
    从核心驱动程序中删除对驱动程序的所有引用。如果低级驱动程序通过 `uart_add_one_port()` 注册了它们的端口，则必须使用 `uart_remove_one_port()` 删除所有端口。
    锁定：无
    中断：启用

`uart_suspend_port()`

`uart_resume_port()`

`uart_add_one_port()`

`uart_remove_one_port()`

**其他注意事项**
-----------

计划有一天从 `uart_port` 中删除“未使用”的条目，并允许低级驱动程序向核心注册它们自己的单独 `uart_port`。这将允许驱动程序将 `uart_port` 用作指向包含 `uart_port` 条目及其自身扩展的结构的指针，例如：

```
struct my_port {
    struct uart_port    port;
    int                 my_stuff;
};
```

**通过 GPIO 控制调制解调器控制线**
----------------------------

提供了一些帮助函数，以便通过 GPIO 设置/获取调制解调器控制线。

`mctrl_gpio_init(dev, idx)`：
    如果存在，将从设备树中获取 `{cts,rts,...}-gpios`，请求它们，设置方向等，并返回一个已分配的结构。使用了 `devm_*` 函数，因此无需调用 `mctrl_gpio_free()`。

`mctrl_gpio_free(dev, gpios)`：
    将释放 `mctrl_gpio_init()` 中请求的 `gpios`。由于使用了 `devm_*` 函数，通常无需调用此函数。

`mctrl_gpio_to_gpiod(gpios, gidx)`：
    返回与调制解调器线索引相关联的 `gpio` 结构。

`mctrl_gpio_set(gpios, mctrl)`：
    将根据 `mctrl` 状态设置 `gpios`。

`mctrl_gpio_get(gpios, mctrl)`：
    将使用 `gpios` 的值更新 `mctrl`。
