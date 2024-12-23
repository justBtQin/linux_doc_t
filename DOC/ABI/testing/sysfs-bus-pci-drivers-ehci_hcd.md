**What：**

 - `/sys/bus/pci/drivers/ehci_hcd/.../companion`

 - `/sys/bus/usb/devices/usbN/../companion`

**Date：** 2007 年 1 月

**KernelVersion：** 2.6.21

**Contact：** Alan Stern <stern@rowland.harvard.edu>

**Description：**

PCI 基于的 EHCI USB 控制器（即高速 USB - 2.0 控制器）通常与一组“配套”的全/低速 USB - 1.1 控制器一起实现。当插入高速设备时，连接被路由到 EHCI 控制器；当插入全速或低速设备时，连接被路由到配套控制器。

有时你想强制高速设备以全速连接，这可以通过强制连接路由到配套控制器来完成。这就是这个文件的作用。将端口号写入该文件会导致该端口上的连接被路由到配套控制器，而写入端口号的负数会使端口恢复正常操作。

例如：要强制连接到总线 2 上的端口 4 的高速设备以全速运行：

`echo 4 >/sys/bus/usb/devices/usb2/../companion`

要使端口恢复高速操作：

`echo -4 >/sys/bus/usb/devices/usb2/../companion`

读取该文件可获得当前强制连接到配套控制器的端口列表。

注意：某些 EHCI 控制器没有配套设备；它们可能包含内部“事务转换器”，或者可能直接连接到“速率匹配集线器”。此机制不适用于此类控制器。此外，它不能用于强制高速集线器上的端口以全速连接。

注意：当这个文件首次添加时，它出现在不同的 sysfs 目录中。上述位置对于 2.6.35（以及可能几个早期的内核版本）是正确的。