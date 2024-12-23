**What**：

- `/sys/bus/usb/device/.../authorized`

- `Date`：2008 年 7 月

- `KernelVersion`：2.6.26

- `Contact`：David Vrabel <david.vrabel@csr.com>

- `Description`：授权的设备可供设备驱动程序使用，未授权的则不可。默认情况下，有线 USB 设备是授权的。经过认证的无线 USB 设备最初未授权，在设备经过身份验证后应（通过写入 1）授权。

**What**：

- `/sys/bus/usb/device/.../wusb_cdid`

- `Date`：2008 年 7 月

- `KernelVersion`：2.6.27

- `Contact`：David Vrabel <david.vrabel@csr.com>

- `Description`：仅适用于经过认证的无线 USB 设备。设备的 CDID，以 16 个空格分隔的十六进制八位字节表示。

**What**：

- `/sys/bus/usb/device/.../wusb_disconnect`

- `Date`：2008 年 7 月

- `KernelVersion`：2.6.27

- `Contact`：David Vrabel <david.vrabel@csr.com>

- `Description`：仅适用于经过认证的无线 USB 设备。写入设备的连接密钥（CK）以启动设备的身份验证。CK 是 16 个空格分隔的十六进制八位字节。

**What**：

- `/sys/bus/usb/drivers/.../new_id`

- `Date`：2011 年 10 月

- `Contact`：linux-usb@vger.kernel.org

- `Description`：向此文件写入设备 ID 将尝试向 USB 设备驱动程序动态添加新的设备 ID。这可能使驱动程序能够支持比编译时驱动程序的静态设备 ID 支持表中包含的更多硬件。设备 ID 的格式为：idVendor idProduct bInterfaceClass RefIdVendor RefIdProduct。供应商 ID 和设备 ID 字段是必需的，其余是可选的。Ref* 元组可用于告诉驱动程序对新设备使用与参考设备相同的驱动程序数据。成功添加 ID 后，驱动程序将探测设备并尝试绑定到它。例如：# echo "8086 10f5" > /sys/bus/usb/drivers/foo/new_id

**What**：

- `/sys/bus/usb/devices/.../power/usb2_hardware_lpm`

- `Date`：2011 年 9 月

- `Contact`：Lan Tianyu <tianyu.lan@intel.com>

- `Description`：可以从集线器描述符位和特定于平台的数据（如 ACPI）的组合中推断出有关给定 USB 设备是否物理固定到平台的一些信息。如果此信息可用，此文件将读取“removable”（可移动）或“fixed”（固定），否则读取“unknown”（未知）。

**What**：

- `/sys/bus/usb/devices/.../remove_id`

- `Date`：2009 年 11 月

- `Contact`：CHENG Renquan <rqcheng@smu.edu.sg>

- `Description`：向此文件写入设备 ID 将删除通过 new_id sysfs 条目动态添加的 ID。设备 ID 的格式为：idVendor idProduct。成功删除 ID 后，驱动程序将不再支持该设备。这对于确保自动探测不会将驱动程序与设备匹配很有用。例如：# echo "046d c315" > /sys/bus/usb/drivers/foo/remove_id

**What**：

- `/sys/bus/usb/devices/.../(hub interface)/portX`

- `Date`：2012 年 8 月

- `Contact`：Lan Tianyu <tianyu.lan@intel.com>

- `Description`：某些平台通过 ACPI 提供 USB 端口连接类型。此属性用于将这些信息暴露给用户空间。如果信息可用，文件将读取“hotplug”（热插拔）、“wired”（有线）和“not used”（未使用），否则读取“unknown”（未知）。

**What**：

- `/sys/bus/usb/devices/.../power/usb2_lpm_l1_timeout`

- `Date`：2013 年 5 月

- `Contact`：Mathias Nyman <mathias.nyman@linux.intel.com>

- `Description`：USB 2.0 设备可能支持延迟容忍消息传递（LTM）。它们通过在其 SuperSpeed BOS 描述符的 bmAttributes 字段中设置一位来表明其支持。如果设备设置了该位，则 ltm_capable 将读取“yes”。如果设备不支持 LTM，文件将读取“no”。该文件将存在于所有速度的 USB 设备中，对于 USB 1.1 和 USB 2.0 设备将始终读取“no”。

**What**：

- `/sys/bus/usb/devices/.../(hub interface)/portX/connect_type`

- `Date`：2013 年 1 月

- `Contact`：Lan Tianyu <tianyu.lan@intel.com>

- `Description`：某些平台通过 ACPI 提供 USB 端口连接类型。此属性用于将这些信息暴露给用户空间。如果信息可用，文件将读取“hotplug”（热插拔）、“wired”（有线）和“not used”（未使用），否则读取“unknown”（未知）。