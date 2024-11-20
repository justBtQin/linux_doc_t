**RS485 串行通信**

# 1. 引言

EIA-485，也称为 TIA/EIA-485 或 RS-485，是一个定义平衡数字多点系统中驱动器和接收器电气特性的标准。
该标准广泛用于工业自动化通信，因为它可以在长距离和电气噪声环境中有效使用。

# 2. 硬件相关考虑

一些 CPU/UART（例如，Atmel AT91 或 16C950 UART）包含内置的半双工模式，能够通过切换 RTS 或 DTR 信号自动控制线方向。这可用于控制外部半双工硬件，如 RS485 收发器或任何通过 RS232 连接的半双工设备，如某些调制解调器。
	
对于这些微控制器，Linux 驱动程序应能够在两种模式下工作，并且应在用户级别提供适当的 ioctl（见后文），以允许从一种模式切换到另一种模式，反之亦然。

# 3. 内核中已有的数据结构

Linux 内核提供了 serial_rs485 结构（见[1]）来处理 RS485 通信。此数据结构用于在平台数据和 ioctl 中设置和配置 RS485 参数。
	
设备树还可以提供 RS485 启动时参数（有关绑定，请参见[2]）。驱动程序负责根据设备树提供的值填充此数据结构。

任何能够同时作为 RS232 和 RS485 工作的设备的驱动程序应至少提供以下 ioctl：

- TIOCSRS485（通常与编号 0x542F 相关联）。此 ioctl 用于从用户空间启用/禁用 RS485 模式

- TIOCGRS485（通常与编号 0x542E 相关联）。此 ioctl 用于将 RS485 模式从内核空间（即驱动程序）获取到用户空间。

换句话说，串行驱动程序应包含类似于以下的代码：
```
    static struct uart_ops atmel_pops = {
        /*... */
      .ioctl        = handle_ioctl,
    };

    static int handle_ioctl(struct uart_port *port,
        unsigned int cmd,
        unsigned long arg)
    {
        struct serial_rs485 rs485conf;

        switch (cmd) {
        case TIOCSRS485:
            if (copy_from_user(&rs485conf,
                (struct serial_rs485 *) arg,
                sizeof(rs485conf)))
                    return -EFAULT;

            /*... */
            break;

        case TIOCGRS485:
            if (copy_to_user((struct serial_rs485 *) arg,
               ...,
                sizeof(rs485conf)))
                    return -EFAULT;
            /*... */
            break;

        /*... */
        }
    }
```
# 4. 用户级别的使用

在用户级别，可以使用前面的 ioctl 来获取/设置 RS485 配置。例如，要设置 RS485，您可以使用以下代码：
```
    #include <linux/serial.h>

    /* 驱动程序特定的 ioctl： */
    #define TIOCGRS485      0x542E
    #define TIOCSRS485      0x542F

    /* 打开您的特定设备（例如，/dev/mydevice）： */
    int fd = open ("/dev/mydevice", O_RDWR);
    if (fd < 0) {
        /* 错误处理。参见 errno。 */
    }

    struct serial_rs485 rs485conf;

    /* 启用 RS485 模式： */
    rs485conf.flags |= SER_RS485_ENABLED;

    /* 设置发送时 RTS 引脚的逻辑电平为 1： */
    rs485conf.flags |= SER_RS485_RTS_ON_SEND;
    /* 或者，设置发送时 RTS 引脚的逻辑电平为 0： */
    rs485conf.flags &= ~(SER_RS485_RTS_ON_SEND);

    /* 设置发送后 RTS 引脚的逻辑电平为 1： */
    rs485conf.flags |= SER_RS485_RTS_AFTER_SEND;
    /* 或者，设置发送后 RTS 引脚的逻辑电平为 0： */
    rs485conf.flags &= ~(SER_RS485_RTS_AFTER_SEND);

    /* 如果需要，设置发送前的 rts 延迟： */
    rs485conf.delay_rts_before_send =...;

    /* 如果需要，设置发送后的 rts 延迟： */
    rs485conf.delay_rts_after_send =...;

    /* 如果您希望在发送数据时也接收数据，请设置此标志 */
    rs485conf.flags |= SER_RS485_RX_DURING_TX;

    if (ioctl (fd, TIOCSRS485, &rs485conf) < 0) {
        /* 错误处理。参见 errno。 */
    }

    /* 在此处使用 read() 和 write() 系统调用... */

    /* 完成后关闭设备： */
    if (close (fd) < 0) {
        /* 错误处理。参见 errno。 */
    }
```
# 5. 参考文献

[1] include/uapi/linux/serial.h

[2] Documentation/devicetree/bindings/serial/rs485.txt