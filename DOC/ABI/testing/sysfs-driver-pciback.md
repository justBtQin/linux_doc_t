**What**: /sys/bus/pci/drivers/pciback/quirks

**Date**: Oct 2011

**KernelVersion**: 3.1

**Contact**: xen-devel@lists.xenproject.org

**Description**:

如果设置了允许属性，那么以 DDDD:BB:DD.F-REG:SIZE:MASK 格式写入字符串将允许客户机对 PCI 设备进行读写操作。即域:总线:设备.功能-寄存器:大小:掩码（域是可选的）。例如：

`#echo 00:19.0-E0:2:FF > /sys/bus/pci/drivers/pciback/quirks`

将允许客户机读取和写入配置寄存器 0x0E。