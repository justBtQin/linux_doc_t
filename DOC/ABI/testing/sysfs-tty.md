**/sys/class/tty/console/active**

- **Date**: Nov 2010

- **Contact**: Kay Sievers <kay.sievers@vrfy.org>

- **Description**: 显示当前配置的控制台设备列表，如 'tty1 ttyS0'。文件中的最后一个条目是连接到 /dev/console 的活动设备。该文件支持 poll() 来检测虚拟控制台切换。

**/sys/class/tty/tty0/active**

- **Date**: Nov 2010

- **Contact**: Kay Sievers <kay.sievers@vrfy.org>

- **Description**: 显示当前活动的虚拟控制台设备，如 'tty1'。该文件支持 poll() 来检测虚拟控制台切换。

**/sys/class/tty/ttyS0/uartclk**

- **Date**: Sep 2012

- **Contact**: Tomas Hlavacek <tmshlvck@gmail.com>

- **Description**: 显示与 serial_core 中的 UART 端口相关联的当前 uartclk 值，该 UART 端口绑定到 TTY 如 ttyS0。uartclk = 16 * baud_base。这些 sysfs 值通过 sysfs 而不是通过 ioctls 公开 TIOCGSERIAL 接口。

**/sys/class/tty/ttyS0/type**

- **Date**: October 2012

- **Contact**: Alan Cox <alan@linux.intel.com>

- **Description**: 显示此端口的当前 tty 类型。这些 sysfs 值通过 sysfs 而不是通过 ioctls 公开 TIOCGSERIAL 接口。

**/sys/class/tty/ttyS0/line**

- **Date**: October 2012

- **Contact**: Alan Cox <alan@linux.intel.com>

- **Description**: 显示此端口的当前 tty 线路号。这些 sysfs 值通过 sysfs 而不是通过 ioctls 公开 TIOCGSERIAL 接口。

**/sys/class/tty/ttyS0/port**

- **Date**: October 2012

- **Contact**: Alan Cox <alan@linux.intel.com>

- **Description**: 显示此端口的当前 tty 端口 I/O 地址。这些 sysfs 值通过 sysfs 而不是通过 ioctls 公开 TIOCGSERIAL 接口。

**/sys/class/tty/ttyS0/irq**

- **Date**: October 2012

- **Contact**: Alan Cox <alan@linux.intel.com>

- **Description**: 显示此端口的当前主中断。这些 sysfs 值通过 sysfs 而不是通过 ioctls 公开 TIOCGSERIAL 接口。

**/sys/class/tty/ttyS0/flags**

- **Date**: October 2012

- **Contact**: Alan Cox <alan@linux.intel.com>

- **Description**: 显示此端口的 tty 端口状态标志。这些 sysfs 值通过 sysfs 而不是通过 ioctls 公开 TIOCGSERIAL 接口。

**/sys/class/tty/ttyS0/xmit_fifo_size**

- **Date**: October 2012

- **Contact**: Alan Cox <alan@linux.intel.com>

- **Description**: 显示此端口的发送 FIFO 大小。这些 sysfs 值通过 sysfs 而不是通过 ioctls 公开 TIOCGSERIAL 接口。

**/sys/class/tty/ttyS0/close_delay**

- **Date**: October 2012

- **Contact**: Alan Cox <alan@linux.intel.com>

- **Description**: 显示此端口的关闭延迟时间（以毫秒为单位）。这些 sysfs 值通过 sysfs 而不是通过 ioctls 公开 TIOCGSERIAL 接口。

**/sys/class/tty/ttyS0/closing_wait**

- **Date**: October 2012

- **Contact**: Alan Cox <alan@linux.intel.com>

- **Description**: 显示此端口的关闭等待时间（以毫秒为单位）。这些 sysfs 值通过 sysfs 而不是通过 ioctls 公开 TIOCGSERIAL 接口。

**/sys/class/tty/ttyS0/custom_divisor**

- **Date**: October 2012

- **Contact**: Alan Cox <alan@linux.intel.com>

- **Description**: 显示此端口上设置的自定义除数（如果有）。这些 sysfs 值通过 sysfs 而不是通过 ioctls 公开 TIOCGSERIAL 接口。

**/sys/class/tty/ttyS0/io_type**

- **Date**: October 2012

- **Contact**: Alan Cox <alan@linux.intel.com>

- **Description**: 显示要与 iomem 基地址一起使用的 I/O 类型。这些 sysfs 值通过 sysfs 而不是通过 ioctls 公开 TIOCGSERIAL 接口。

**/sys/class/tty/ttyS0/iomem_base**

- **Date**: October 2012

- **Contact**: Alan Cox <alan@linux.intel.com>

- **Description**: 此端口的 I/O 内存基地址。这些 sysfs 值通过 sysfs 而不是通过 ioctls 公开 TIOCGSERIAL 接口。

**/sys/class/tty/ttyS0/iomem_reg_shift**

- **Date**: October 2012

- **Contact**: Alan Cox <alan@linux.intel.com>

- **Description**: 显示指示在此 iomem 地址上要使用的访问间距的寄存器移位。这些 sysfs 值通过 sysfs 而不是通过 ioctls 公开 TIOCGSERIAL 接口。

**/sys/class/tty/ttyS0/rx_trig_bytes**

- **Date**: May 2014

- **Contact**: Yoshihiro YUNOMAE <yoshihiro.yunomae.ez@hitachi.com>

- **Description**: 显示当前的 RX 中断触发字节或设置用户指定的值以更改 FIFO 缓冲区。用户可以显示或设置此值，而无需打开串行设备文件。对于 UART 串行，RX 触发可以设置为四种值之一。当用户向此接口输入无意义的值时，RX 触发将更改为设备规范的最接近的较低值。例如，当用户在 16550A 上设置 7 字节时，由于其触发为 1/4/8/14 字节，RX 触发将自动更改为 4 字节。