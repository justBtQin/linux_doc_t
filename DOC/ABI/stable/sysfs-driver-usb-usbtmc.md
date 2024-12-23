**What**: /sys/bus/usb/drivers/usbtmc/*/interface_capabilities

**What**: /sys/bus/usb/drivers/usbtmc/*/device_capabilities

**Date**: 2008 年 8 月

**Contact**: Greg Kroah-Hartman <gregkh@linuxfoundation.org>

**Description**:

这些文件展示了设备自身所描述的各种 USB TMC 能力。位域的完整描述可以在 USB-IF 发布的 USB TMC 文档“通用串行总线测试和测量类规范 (USBTMC) 修订版 1.0”的 4.2.1.8 节中找到。

这些文件是只读的。

**What**: /sys/bus/usb/drivers/usbtmc/*/usb488_interface_capabilities

**What**: /sys/bus/usb/drivers/usbtmc/*/usb488_device_capabilities

**Date**: 2008 年 8 月

**Contact**: Greg Kroah-Hartman <gregkh@linuxfoundation.org>

**Description**:

这些文件展示了设备自身所描述的各种 USB TMC 能力。位域的完整描述可以在 USB-IF 发布的“通用串行总线测试和测量类、子类 USB488 规范 (USBTMC-USB488) 修订版 1.0”的 4.2.2 节中找到。

这些文件是只读的。

**What**: /sys/bus/usb/drivers/usbtmc/*/TermChar

**Date**: 2008 年 8 月

**Contact**: Greg Kroah-Hartman <gregkh@linuxfoundation.org>

**Description**:

此文件是要发送到 USB TMC 设备的 TermChar 值，如 USB-IF 发布的“通用串行总线测试和测量类规范 (USBTMC) 修订版 1.0”文档中所描述。请注意，TermCharEnabled 文件决定是否将此值发送到设备。

**What**: /sys/bus/usb/drivers/usbtmc/*/TermCharEnabled

**Date**: 2008 年 8 月

**Contact**: Greg Kroah-Hartman <gregkh@linuxfoundation.org>

**Description**:

此文件决定是否在每次事务中都将 TermChar 发送到设备。有关此的更多详细信息，请参阅 USB-IF 发布的“通用串行总线测试和测量类规范 (USBTMC) 修订版 1.0”文档。

**What**: /sys/bus/usb/drivers/usbtmc/*/auto_abort

**Date**: 2008 年 8 月

**Contact**: Greg Kroah-Hartman <gregkh@linuxfoundation.org>

**Description**:

此文件决定如果 USB TMC 设备的事务出现任何错误是否自动中止。有关此的更多详细信息，请参阅 USB-IF 发布的“通用串行总线测试和测量类规范 (USBTMC) 修订版 1.0”文档。