**What**：`/config/usb-gadget/gadget/functions/mass_storage.name`

**Date**：2013 年 10 月

**KernelVersion**：3.13

**Description**：

    该属性：

    - `stall` - 设置为允许功能停止批量端点。在一些已知无法正常工作的 USB 设备上被禁用。您应该将其设置为 `true` 。

    - `num_buffers` - 管道缓冲区的数量。有效数字为 2 到 4。仅在设置了 `CONFIG_USB_GADGET_DEBUG_FILES` 时可用。

**What**：`/config/usb-gadget/gadget/functions/mass_storage.name/lun.name`

**Date**：2013 年 10 月

**KernelVersion**：3.13

**Description**：

    该属性：

    - `file` - LUN 的后备文件的路径。如果 LUN 未标记为可移动，则是必需的。

    - `ro` - 指定对 LUN 的访问应为只读的标志。如果启用了 CD-ROM 仿真以及无法以读写模式打开“文件名”时，这是隐含的。

    - `removable` - 指定 LUN 应被指示为可移动的标志。

    - `cdrom` - 指定 LUN 应被报告为 CD-ROM 的标志。

    - `nofua` - 指定 SCSI WRITE(10,12) 中的 FUA 标志 