		Summary of HDIO_ ioctl calls.
		============================

		Edward A. Falk <efalk@google.com>

		November, 2004

# HD/IDE 层支持的 ioctl(2) 调用

此文档试图描述 HD/IDE 层所支持的 ioctl(2) 调用。在 Linux 2.6 中，它们大体上在 `drivers/ide/ide.c` 和 `drivers/block/scsi_ioctl.c` 中实现。

ioctl 的值在 `<linux/hdreg.h>` 中列出。在撰写本文时，它们如下：

**ioctl 会将参数指针传递到用户空间：**

 - `HDIO_GETGEO`  获取设备几何信息
 - `HDIO_GET_UNMASKINTR`  获取当前的未屏蔽设置
 - `HDIO_GET_MULTCOUNT`  获取当前的 IDE 块模式设置
 - `HDIO_GET_QDMA`  获取使用-qdma 标志
 - `HDIO_SET_XFER`  通过 proc 设置传输速率
 - `HDIO_OBSOLETE_IDENTITY`  已过时，请勿使用
 - `HDIO_GET_KEEPSETTINGS`  获取保持设置-在重置时的标志
 - `HDIO_GET_32BIT`  获取当前的 io_32bit 设置
 - `HDIO_GET_NOWERR`  获取忽略写入错误标志
 - `HDIO_GET_DMA`  获取使用-dma 标志
 - `HDIO_GET_NICE`  获取 nice 标志
 - `HDIO_GET_IDENTITY`  获取 IDE 识别信息
 - `HDIO_GET_WCACHE`  获取写缓存模式 开/关
 - `HDIO_GET_ACOUSTIC`  获取声学值
 - `HDIO_GET_ADDRESS`  获取扇区寻址模式
 - `HDIO_GET_BUSSTATE`  获取硬件接口的总线状态
 - `HDIO_TRISTATE_HWIF`  执行通道三态
 - `HDIO_DRIVE_RESET`  执行设备重置
 - `HDIO_DRIVE_TASKFILE`  执行原始任务文件
 - `HDIO_DRIVE_TASK`  执行任务和特殊的驱动器命令
 - `HDIO_DRIVE_CMD`  执行特殊的驱动器命令
 - `HDIO_DRIVE_CMD_AEB`  `HDIO_DRIVE_TASK`

**ioctl 会传递非指针值：**

 - `HDIO_SET_MULTCOUNT`  更改 IDE 块模式
 - `HDIO_SET_UNMASKINTR`  在 I/O 期间允许其他中断
 - `HDIO_SET_KEEPSETTINGS`  在重置时保持 ioctl 设置
 - `HDIO_SET_32BIT`  更改 io_32bit 标志
 - `HDIO_SET_NOWERR`  更改忽略写入错误标志
 - `HDIO_SET_DMA`  更改使用-dma 标志
 - `HDIO_SET_PIO_MODE`  将接口重新配置为新速度
 - `HDIO_SCAN_HWIF`  注册并（重新）扫描接口
 - `HDIO_SET_NICE`  设置 nice 标志
 - `HDIO_UNREGISTER_HWIF`  注销接口
 - `HDIO_SET_WCACHE`  更改写缓存启用/禁用
 - `HDIO_SET_ACOUSTIC`  更改声学行为
 - `HDIO_SET_BUSSTATE`  设置硬件接口的总线状态
 - `HDIO_SET_QDMA`  更改使用-qdma 标志
 - `HDIO_SET_ADDRESS`  更改 lba 寻址模式

 - `HDIO_SET_IDE_SCSI`  设置 scsi 仿真模式 开/关
 - `HDIO_SET_SCSI_IDE`  尚未实现

以下信息是通过阅读内核源代码确定的。随着时间的推移，可能会进行一些修正。

# 通用说明：

 - 除非另有说明，所有的 ioctl 调用在成功时返回 0，在错误时返回 -1，并将 `errno` 设置为适当的值。

 - 除非另有说明，如果在向或从用户地址空间复制数据时尝试失败，所有的 ioctl 调用都会返回 -1 并将 `errno` 设置为 `EFAULT`。

 - 除非另有说明，所有的数据结构和常量都在 `<linux/hdreg.h>` 中定义。

# HDIO_GETGEO - 获取设备几何信息

 - 使用方法：

   `struct hd_geometry geom;`
   `ioctl(fd, HDIO_GETGEO, &geom);`

 - 输入：无

 - 输出：

   `hd_geometry` 结构，包含：

   `heads`  磁头数量
   `sectors`  每磁道扇区数量
   `cylinders`  柱面数量，对 65536 取模
   `start`  此分区的起始扇区。

 - 错误返回：

   `EINVAL`  如果设备不是磁盘驱动器或软盘驱动器，或者用户传递了空指针

 - 备注：

   对于现代磁盘驱动器来说并不是特别有用，因为它们的几何信息无论如何都是一种礼貌性的虚构。如今，现代驱动器纯粹通过扇区编号（lba 寻址）进行寻址，驱动器的几何信息是一种抽象，实际上可能会发生变化。当前（截至 2004 年 11 月），几何值是“bios”值 - 大概是 Linux 首次启动时驱动器具有的值。

   此外，`hd_geometry` 中的 `cylinders` 字段是一个无符号短整型，这意味着在大多数架构上，对于具有超过 65535 个磁道的驱动器，此 ioctl 将不会返回有意义的值。

   `start` 字段是无符号长整型，这意味着对于超过 219GB 大小的磁盘，它将不包含有意义的值。

# HDIO_GET_UNMASKINTR - 获取当前未屏蔽设置

 - 使用方法：

   `long val;`
   `ioctl(fd, HDIO_GET_UNMASKINTR, &val);`

 - 输入：无

 - 输出：
   驱动器当前未屏蔽设置的值

# HDIO_SET_UNMASKINTR - 允许在 I/O 期间的其他中断

 - 使用方法：

   `unsigned long val;`
   `ioctl(fd, HDIO_SET_UNMASKINTR, val);`

 - 输入：
   未屏蔽标志的新值

 - 输出：无

 - 错误返回：

   `EINVAL`  （`bdev!= bdev->bd_contains`）（不确定这意味着什么）
   `EACCES`  访问被拒绝：需要 `CAP_SYS_ADMIN`
   `EINVAL`  值超出范围 [0 1]
   `EBUSY`  控制器繁忙

# HDIO_GET_MULTCOUNT - 获取当前 IDE 块模式设置

 - 使用方法：

   `long val;`
   `ioctl(fd, HDIO_GET_MULTCOUNT, &val);`

 - 输入：无

 - 输出：
   当前 IDE 块模式设置的值。这控制了驱动器每次中断将传输的扇区数量。

# HDIO_SET_MULTCOUNT - 更改 IDE 块模式

 - 使用方法：

   `int val;`
   `ioctl(fd, HDIO_SET_MULTCOUNT, val);`

 - 输入：
   IDE 块模式设置的新值。这控制了驱动器每次中断将传输的扇区数量。

 - 输出：无

 - 错误返回：

   `EINVAL`  （`bdev!= bdev->bd_contains`）（不确定这意味着什么）
   `EACCES`  访问被拒绝：需要 `CAP_SYS_ADMIN`
   `EINVAL`  值超出磁盘支持的范围。
   `EBUSY`  控制器繁忙或块模式已经设置。
   `EIO`  驱动器不接受新的块模式。

 - 备注：

   源代码注释：

   这与 `driver->do_special` 紧密交织在一起，在进行全面的个性重写之前不要再触碰。

   如果块模式已经设置，此 ioctl 将因 `EBUSY` 失败

# HDIO_GET_QDMA - 获取使用-qdma 标志

 - 截至 2.6.8.1 未实现

# HDIO_SET_XFER - 通过 proc 设置传输速率

 - 截至 2.6.8.1 未实现

# HDIO_OBSOLETE_IDENTITY - 已过时，请勿使用

 - 与 `HDIO_GET_IDENTITY` 相同（见下文），但它仅返回驱动器标识信息的前 142 个字节。

# HDIO_GET_IDENTITY - 获取 IDE 识别信息

 - 使用方法：

   `unsigned char identity[512];`
   `ioctl(fd, HDIO_GET_IDENTITY, identity);`

 - 输入：无

 - 输出：

   ATA 驱动器标识信息。有关完整描述，请参阅 ATA 规范中的 `IDENTIFY DEVICE` 和 `IDENTIFY PACKET DEVICE` 命令。

 - 错误返回：

   `EINVAL`  （`bdev!= bdev->bd_contains`）（不确定这意味着什么）
   `ENOMSG`  `IDENTIFY DEVICE` 信息不可用

 - 备注：

   返回在探测驱动器时获取的信息。其中一些信息可能会发生变化，并且此 ioctl 不会重新探测驱动器以更新信息。

   此信息也可以从 `/proc/ide/hdX/identify` 获得

# HDIO_GET_KEEPSETTINGS - 获取保持设置-在重置时的标志

 - 使用方法：

   `long val;`
   `ioctl(fd, HDIO_GET_KEEPSETTINGS, &val);`

 - 输入：无

 - 输出：
   当前“保持设置”标志的值

 - 备注：

   设置时，表示内核应在驱动器重置后恢复设置。

# HDIO_SET_KEEPSETTINGS - 在重置时保持 ioctl 设置

 - 使用方法：

   `long val;`
   `ioctl(fd, HDIO_SET_KEEPSETTINGS, val);`

 - 输入：
   保持设置标志的新值

 - 输出：无

 - 错误返回：

   `EINVAL`  （`bdev!= bdev->bd_contains`）（不确定这意味着什么）
   `EACCES`  访问被拒绝：需要 `CAP_SYS_ADMIN`
   `EINVAL`  值超出范围 [0 1]
   `EBUSY`  控制器繁忙

# HDIO_GET_32BIT - 获取当前 io_32bit 设置

 - 使用方法：

   `long val;`
   `ioctl(fd, HDIO_GET_32BIT, &val);`

 - 输入：无

 - 输出：
   当前 io_32bit 设置的值

 - 备注：

   0 = 16 位，1 = 32 位，2,3 = 32 位+同步

# HDIO_GET_NOWERR - 获取忽略写入错误标志

 - 使用方法：

   `long val;`
   `ioctl(fd, HDIO_GET_NOWERR, &val);`

 - 输入：无

 - 输出：
   当前忽略写入错误标志的值

# HDIO_GET_DMA - 获取使用-dma 标志

 - 使用方法：

   `long val;`
   `ioctl(fd, HDIO_GET_DMA, &val);`

 - 输入：无

 - 输出：
   当前使用-dma 标志的值

# HDIO_GET_NICE - 获取 nice 标志

 - 使用方法：

   `long nice;`
   `ioctl(fd, HDIO_GET_NICE, &nice);`

 - 输入：无

 - 输出：

   驱动器的“nice”值。

 - 备注：

   每个驱动器的标志，用于确定系统何时将更多带宽分配给共享相同 IDE 总线的其他设备。
   请参阅 `<linux/hdreg.h>`，靠近符号 `IDE_NICE_DSC_OVERLAP`。

# HDIO_SET_NICE - 设置 nice 标志

 - 使用方法：

   `unsigned long nice;`
   `...`
   `ioctl(fd, HDIO_SET_NICE, nice);`

 - 输入：
   nice 标志的位掩码。

 - 输出：无

 - 错误返回：

   `EACCES`  访问被拒绝：需要 `CAP_SYS_ADMIN`
   `EPERM`  除了 `DSC_OVERLAP` 和 `NICE_1` 之外的标志被设置。
   `EPERM`  指定了 `DSC_OVERLAP` 但驱动器不支持

 - 备注：

   此 ioctl 根据用户提供的值设置 `DSC_OVERLAP` 和 `NICE_1` 标志。

   Nice 标志列在 `<linux/hdreg.h>` 中，从 `IDE_NICE_DSC_OVERLAP` 开始。这些值表示移位。

# HDIO_GET_WCACHE - 获取写缓存模式 开/关

 - 使用方法：

   `long val;`
   `ioctl(fd, HDIO_GET_WCACHE, &val);`

 - 输入：无

 - 输出：
   当前写缓存模式的值

# HDIO_GET_ACOUSTIC - 获取声学值

 - 使用方法：

   `long val;`
   `ioctl(fd, HDIO_GET_ACOUSTIC, &val);`

 - 输入：无

 - 输出：
   当前声学设置的值

 - 备注：

   请参阅 `HDIO_SET_ACOUSTIC`

# HDIO_GET_ADDRESS

 - 使用方法：

   `long val;`
   `ioctl(fd, HDIO_GET_ADDRESS, &val);`

 - 输入：无

 - 输出：
   当前寻址模式的值：
   0 = 28 位
   1 = 48 位
   2 = 48 位执行 28 位
   3 = 64 位

# HDIO_GET_BUSSTATE - 获取硬件接口的总线状态

 - 使用方法：

   `long state;`
   `ioctl(fd, HDIO_SCAN_HWIF, &state);`

 - 输入：无

 - 输出：
   当前 IDE 总线的电源状态。为 `BUSSTATE_OFF`、`BUSSTATE_ON` 或 `BUSSTATE_TRISTATE` 之一

 - 错误返回：
   `EACCES`  访问被拒绝：需要 `CAP_SYS_ADMIN`

# HDIO_SET_BUSSTATE - 设置硬件接口的总线状态

 - 使用方法：

   `int state;`
   `...`
   `ioctl(fd, HDIO_SCAN_HWIF, state);`

 - 输入：
   期望的 IDE 电源状态。为 `BUSSTATE_OFF`、`BUSSTATE_ON` 或 `BUSSTATE_TRISTATE` 之一

 - 输出：无

 - 错误返回：

   `EACCES`  访问被拒绝：需要 `CAP_SYS_RAWIO`
   `EOPNOTSUPP`  硬件接口不支持总线电源控制

# HDIO_TRISTATE_HWIF - 执行通道三态

 - 截至 2.6.8.1 未实现。 请参阅 `HDIO_SET_BUSSTATE`

# HDIO_DRIVE_RESET - 执行设备重置

 - 使用方法：

   `int args[3]`
   `...`
   `ioctl(fd, HDIO_DRIVE_RESET, args);`

 - 输入：无

 - 输出：无

 - 错误返回：

   `EACCES`  访问被拒绝：需要 `CAP_SYS_ADMIN`
   `ENXIO`  无此设备：物理设备已死或 `ctl_addr == 0`
   `EIO`  I/O 错误：重置超时或硬件错误

 - 备注：

   在当前 I/O 操作完成后立即在设备上执行重置。

   如果适用，执行 ATAPI 软重置，否则在控制器上执行 ATA 软重置。

# HDIO_DRIVE_TASKFILE - 执行原始任务文件

 - 注意：如果您手头没有 ANSI ATA 规范的副本，您可能应该忽略此 ioctl。

 - 直接通过写入驱动器的“任务文件”寄存器来执行 ATA 磁盘命令。需要 `ADMIN` 和 `RAWIO` 访问权限。

 - 使用方法：

   `struct {`
   `    ide_task_request_t req_task;`
   `    u8 outbuf[OUTPUT_SIZE];`
   `    u8 inbuf[INPUT_SIZE];`
   `} task;`
   `memset(&task.req_task, 0, sizeof(task.req_task));`
   `task.req_task.out_size = sizeof(task.outbuf);`
   `task.req_task.in_size = sizeof(task.inbuf);`
   `...`
   `ioctl(fd, HDIO_DRIVE_TASKFILE, &task);`
   `...`

 - 输入：

   （有关传递给 ioctl 的内存区域的详细信息，请参阅下文。）

   `io_ports[8]`  要写入任务文件寄存器的值
   `hob_ports[8]`  高阶字节，用于扩展命令。
   `out_flags`  指示哪些寄存器有效的标志
   `in_flags`  指示哪些寄存器应返回的标志
   `data_phase`  请参阅下文
   `req_cmd`  要执行的命令类型
   `out_size`  输出缓冲区的大小，字节
   `outbuf`  要传输到磁盘的数据缓冲区
   `inbuf`  要从磁盘接收的数据缓冲区（请参阅 [1]）

 - 输出：

   `io_ports[]`  在任务文件寄存器中返回的值
   `hob_ports[]`  高阶字节，用于扩展命令。
   `out_flags`  指示哪些寄存器有效的标志（请参阅 [2]）
   `in_flags`  指示哪些寄存器应返回
   `outbuf`  要传输到磁盘的数据缓冲区（请参阅 [1]）
   `inbuf`  要从磁盘接收的数据缓冲区

 - 错误返回：

   `EACCES`  `CAP_SYS_ADMIN` 或 `CAP_SYS_RAWIO` 权限未设置。
   `ENOMSG`  设备不是磁盘驱动器。
   `ENOMEM`  无法为任务分配内存
   `EFAULT`  `req_cmd == TASKFILE_IN_OUT` （截至 2.6.8 未实现）
   `EPERM`  `req_cmd == TASKFILE_MULTI_OUT` 并且驱动器的多计数尚未设置。
   `EIO`  驱动器命令失败。

 - 备注：

   [1] 仔细阅读以下注释。此 ioctl 充满了陷阱。使用此 ioctl 时应极度谨慎。一个错误很容易损坏数据或使系统挂起。

   [2] 即使未使用，输入和输出缓冲区也会从用户复制并写回用户。

   [3] 如果 `out_flags` 和 `in_flags` 中的一个或多个位被设置，以下值将用于 `in_flags.all` 并在完成时写回。

   `* IDE_TASKFILE_STD_IN_FLAGS | (IDE_HOB_STD_IN_FLAGS << 8)`  如果驱动器支持 LBA48
   `* IDE_TASKFILE_STD_IN_FLAGS`  如果 `CHS/LBA28`

   输入和输出标志的关联会根据字节序翻转；幸运的是，`TASKFILE` 仅使用 `inflags.b.data` 位并忽略所有其他位。最终结果是，在任何字节序的机器上，除了在完成时修改 `in_flags` 之外，它没有其他效果。

   [4] `SELECT` 的默认值是 `(0xa0|DEV_bit|LBA_bit)`，除非是每个端口四个驱动器的芯片组。对于每个端口四个驱动器的芯片组，对于第一对是 `(0xa0|DEV_bit|LBA_bit)`，对于第二对是 `(0x80|DEV_bit|LBA_bit)`。

   [5] 传递给 ioctl 的参数是一个指向内存区域的指针，该区域包含一个 `ide_task_request_t` 结构，后跟一个可选的要传输到驱动器的数据缓冲区，后跟一个可选的从驱动器接收数据的缓冲区。

   命令通过 `ide_task_request_t` 结构传递给磁盘驱动器，该结构包含以下字段：

   `io_ports[8]`  任务文件寄存器的值
   `hob_ports[8]`  扩展命令的高阶字节
   `out_flags`  指示 `io_ports[]` 和 `hob_ports[]` 数组中哪些条目包含有效值。类型为 `ide_reg_valid_t`。
   `in_flags`  指示 `io_ports[]` 和 `hob_ports[]` 数组中哪些条目在返回时应包含有效值
   `data_phase`  请参阅下文
   `req_cmd`  命令类型，见下文
   `out_size`  输出（用户->驱动器）缓冲区大小，字节
   `in_size`  输入（驱动器->用户）缓冲区大小，字节

   当 `out_flags` 为零时，将加载以下寄存器。

   `HOB_FEATURE`  如果驱动器支持 LBA48
   `HOB_NSECTOR`  如果驱动器支持 LBA48
   `HOB_SECTOR`  如果驱动器支持 LBA48
   `HOB_LCYL`  如果驱动器支持 LBA48
   `HOB_HCYL`  如果驱动器支持 LBA48
   `FEATURE`
   `NSECTOR`
   `SECTOR`
   `LCYL`
   `HCYL`
   `SELECT`  首先，如果 LBA48 被启用则与 0xE0 进行掩码操作，否则与 0xEF 进行掩码操作；然后，或上 `SELECT` 的默认值。

   如果 `out_flags` 中的任何位被设置，将加载以下寄存器。

   `HOB_DATA`  如果 `out_flags.b.data` 被设置。在小端机器上，`HOB_DATA` 将在 `DD8-DD15` 上传输，在大端机器上则在 `DD0-DD7` 上传输。
   `DATA`  如果 `out_flags.b.data` 被设置。在小端机器上，`DATA` 将在 `DD0-DD7` 上传输，在大端机器上则在 `DD8-DD15` 上传输。
   `HOB_NSECTOR`  如果 `out_flags.b.nsector_hob` 被设置
   `HOB_SECTOR`  如果 `out_flags.b.sector_hob` 被设置
   `HOB_LCYL`  如果 `out_flags.b.lcyl_hob` 被设置
   `HOB_HCYL`  如果 `out_flags.b.hcyl_hob` 被设置
   `FEATURE`  如果 `out_flags.b.feature` 被设置
   `NSECTOR`  如果 `out_flags.b.nsector` 被设置
   `SECTOR`  如果 `out_flags.b.sector` 被设置
   `LCYL`  如果 `out_flags.b.lcyl` 被设置
   `HCYL`  如果 `out_flags.b.hcyl` 被设置
   `SELECT`  或上 `SELECT` 的默认值并加载，无论 `out_flags.b.select` 如何。

   任务文件寄存器在命令完成后从驱动器读回 `{io|hob}_ports[]`，前提是满足以下条件之一；否则，将原封不动地写回原始值。

   1. 驱动器命令失败（`EIO`）。
   2. `out_flags` 中有一个或多个位被设置。
   3. 请求的数据阶段是 `TASKFILE_NO_DATA`。

   `HOB_DATA`  如果 `in_flags.b.data` 被设置。它将在小端机器上包含 `DD8-DD15`，在大端机器上包含 `DD0-DD7`。
   `DATA`  如果 `in_flags.b.data` 被设置。它将在小端机器上包含 `DD0-DD7`，在大端机器上包含 `DD8-DD15`。
   `HOB_FEATURE`  如果驱动器支持 LBA48
   `HOB_NSECTOR`  如果驱动器支持 LBA48
   `HOB_SECTOR`  如果驱动器支持 LBA48
   `HOB_LCYL`  如果驱动器支持 LBA48
   `HOB_HCYL`  如果驱动器支持 LBA48
   `NSECTOR`
   `SECTOR`
   `LCYL`
   `HCYL`

   `data_phase` 字段描述要执行的数据传输。值为以下之一：

   `TASKFILE_IN`
   `TASKFILE_MULTI_IN`
   `TASKFILE_OUT`
   `TASKFILE_MULTI_OUT`
   `TASKFILE_IN_OUT`
   `TASKFILE_IN_DMA`
   `TASKFILE_IN_DMAQ`  == `IN_DMA` （排队不支持）
   `TASKFILE_OUT_DMA`
   `TASKFILE_OUT_DMAQ`  == `OUT_DMA` （排队不支持）
   `TASKFILE_P_IN`  未实现
   `TASKFILE_P_IN_DMA`  未实现
   `TASKFILE_P_IN_DMAQ`  未实现
   `TASKFILE_P_OUT`  未实现
   `TASKFILE_P_OUT_DMA`  未实现
   `TASKFILE_P_OUT_DMAQ`  未实现

   `req_cmd` 字段对命令类型进行分类。它可以是以下之一：

   `IDE_DRIVE_TASK_NO_DATA`
   `IDE_DRIVE_TASK_SET_XFER`  未实现
   `IDE_DRIVE_TASK_IN`
   `IDE_DRIVE_TASK_OUT`  未实现
   `IDE_DRIVE_TASK_RAW_WRITE`

   [6] 除非重置所有位，否则不要访问 `{in|out}_flags->all`。始终访问各个位字段。`->all` 值会根据字节序翻转。出于同样的原因，不要使用 `hdreg.h` 中定义的 `IDE_{TASKFILE|HOB}_STD_{OUT|IN}_FLAGS` 常量。

# HDIO_DRIVE_CMD - 执行特殊驱动器命令

 - 注意：如果您手头没有 ANSI ATA 规范的副本，您可能应该忽略此 ioctl。

 - 使用方法：

   `u8 args[4 + XFER_SIZE];`
   `...`
   `ioctl(fd, HDIO_DRIVE_CMD, args);`

 - 输入：

   除 `WIN_SMART` 以外的命令
   `args[0]`  `COMMAND`
   `args[1]`  `NSECTOR`
   `args[2]`  `FEATURE`
   `args[3]`  `NSECTOR`

   `WIN_SMART`
   `args[0]`  `COMMAND`
   `args[1]`  `SECTOR`
   `args[2]`  `FEATURE`
   `args[3]`  `NSECTOR`

 - 输出：

   `args[]` 缓冲区将填充寄存器值，后跟磁盘返回的任何数据。
   `args[0]`  状态
   `args[1]`  错误
   `args[2]`  `NSECTOR`
   `args[3]`  未定义
   `args[4 + ]`  `NSECTOR * 512` 字节的命令返回的数据。

 - 错误返回：

   `EACCES`  访问被拒绝：需要 `CAP_SYS_RAWIO`
   `ENOMEM`  无法为任务分配内存
   `EIO`  驱动器报告错误

 - 备注：

   [1] 对于除 `WIN_SMART` 以外的命令，`args[1]` 应等于 `args[3]`。`SECTOR`、`LCYL` 和 `HCYL` 未定义。对于 `WIN_SMART`，`0x4f` 和 `0xc2` 分别加载到 `LCYL` 和 `HCYL` 中。在这两种情况下，`SELECT` 将包含驱动器的默认值。请参考 `HDIO_DRIVE_TASKFILE` 注释中的 `SELECT` 默认值。

   [2] 如果 `NSECTOR` 值大于零并且驱动器在中断命令时设置 `DRQ`，则从设备读取 `NSECTOR * 512` 字节到 `NSECTOR` 后面的区域。在上述示例中，该区域将是 `args[4..4 + XFER_SIZE]`。无论 `HDIO_SET_32BIT` 设置如何，都使用 16 位 PIO。

   [3] 如果 `COMMAND == WIN_SETFEATURES` 并且 `FEATURE == SETFEATURES_XFER` 并且 `NSECTOR >= XFER_SW_DMA_0` 并且驱动器支持任何 DMA 模式，IDE 驱动程序将尝试相应地调整驱动器的传输模式。

# HDIO_DRIVE_TASK - 执行任务和特殊驱动器命令

 - 注意：如果您手头没有 ANSI ATA 规范的副本，您可能应该忽略此 ioctl。

 - 使用方法：

   `u8 args[7];`
   `...`
   `ioctl(fd, HDIO_DRIVE_TASK, args);`

 - 输入：

   任务文件寄存器值：
   `args[0]`  `COMMAND`
   `args[1]`  `FEATURE`
   `args[2]`  `NSECTOR`
   `args[3]`  `SECTOR`
   `args[4]`  `LCYL`
   `args[5]`  `HCYL`
   `args[6]`  `SELECT`

 - 输出：

   任务文件寄存器值：
   `args[0]`  状态
   `args[1]`  错误
   `args[2]`  `NSECTOR`
   `args[3]`  `SECTOR`
   `args[4]`  `LCYL`
   `args[5]`  `HCYL`
   `args[6]`  `SELECT`

 - 错误返回：

   `EACCES`  访问被拒绝：需要 `CAP_SYS_RAWIO`
   `ENOMEM`  无法为任务分配内存
   `ENOMSG`  设备不是磁盘驱动器。
   `EIO`  驱动器命令失败。

 - 备注：

   [1] `SELECT` 寄存器的 `DEV` 位（`0x10`）被忽略，并使用驱动器的适当值。所有其他位均未更改地使用。

# HDIO_DRIVE_CMD_AEB - `HDIO_DRIVE_TASK`

 - 截至 2.6.8.1 未实现

# HDIO_SET_32BIT - 更改 io_32bit 标志

 - 使用方法：

   `int val;`
   `ioctl(fd, HDIO_SET_32BIT, val);`

 - 输入：
   io_32bit 标志的新值

 - 输出：无

 - 错误返回：

   `EINVAL`  （`bdev!= bdev->bd_contains`）（不确定这意味着什么）
   `EACCES`  访问被拒绝：需要 `CAP_SYS_ADMIN`
   `EINVAL`  值超出范围 [0 3]
   `EBUSY`  控制器繁忙

# HDIO_SET_NOWERR - 更改忽略写入错误标志

 - 使用方法：

   `int val;`
   `ioctl(fd, HDIO_SET_NOWERR, val);`

 - 输入：
   忽略写入错误标志的新值。用于忽略 `WRERR_STAT`

 - 输出：无

 - 错误返回：

   `EINVAL`  （`bdev!= bdev->bd_contains`）（不确定这意味着什么）
   `EACCES`  访问被拒绝：需要 `CAP_SYS_ADMIN`
   `EINVAL`  值超出范围 [0 1]
   `EBUSY`  控制器繁忙

# HDIO_SET_DMA - 更改使用-dma 标志

 - 使用方法：

   `long val;`
   `ioctl(fd, HDIO_SET_DMA, val);`

 - 输入：
   使用-dma 标志的新值

 - 输出：无

 - 错误返回：

   `EINVAL`  （`bdev!= bdev->bd_contains`）（不确定这意味着什么）
   `EACCES`  访问被拒绝：需要 `CAP_SYS_ADMIN`
   `EINVAL`  值超出范围 [0 1]
   `EBUSY`  控制器繁忙

# HDIO_SET_PIO_MODE - 重新配置接口到新速度

 - 使用方法：

   `long val;`
   `ioctl(fd, HDIO_SET_PIO_MODE, val);`

 - 输入：
   新的接口速度。

 - 输出：无

 - 错误返回：

   `EINVAL`  （`bdev!= bdev->bd_contains`）（不确定这意味着什么）
   `EACCES`  访问被拒绝：需要 `CAP_SYS_ADMIN`
   `EINVAL`  值超出范围 [0 255]
   `EBUSY`  控制器繁忙

# HDIO_SCAN_HWIF - 注册并（重新）扫描接口

 - 使用方法：

   `int args[3]`
   `...`
   `ioctl(fd, HDIO_SCAN_HWIF, args);`

 - 输入：
   `args[0]`  要探测的 I/O 地址
   `args[1]`  要探测的控制地址
   `args[2]`  IRQ 编号

 - 输出：无

 - 错误返回：

   `EACCES`  访问被拒绝：需要 `CAP_SYS_RAWIO`
   `EIO`  探测失败。

 - 备注：

   此 ioctl 初始化磁盘控制器的地址和 IRQ，探测驱动器，并根据需要创建 `/proc/ide` 接口。

# HDIO_UNREGISTER_HWIF - 注销接口

 - 使用方法：

   `int index;`
   `ioctl(fd, HDIO_UNREGISTER_HWIF, index);`

 - 输入：
   要注销的硬件接口的索引

 - 输出：无

 - 错误返回：

   `EACCES`  访问被拒绝：需要 `CAP_SYS_RAWIO`

 - 备注：

   此 ioctl 从内核中删除硬件接口。

   当前（2.6.8），如果接口上的任何驱动器繁忙，此 ioctl 将默默失败。

# HDIO_SET_WCACHE - 更改写缓存启用/禁用

 - 使用方法：

   `int val;`
   `ioctl(fd, HDIO_SET_WCACHE, val);`

 - 输入：
   写缓存启用的新值

 - 输出：无

 - 错误返回：

   `EINVAL`  （`bdev!= bdev->bd_contains`）（不确定这意味着什么）
   `EACCES`  访问被拒绝：需要 `CAP_SYS_ADMIN`
   `EINVAL`  值超出范围 [0 1]
   `EBUSY`  控制器繁忙

# HDIO_SET_ACOUSTIC - 更改声学行为

 - 使用方法：

   `int val;`
   `ioctl(fd, HDIO_SET_ACOUSTIC, val);`

 - 输入：
   驱动器声学设置的新值

 - 输出：无

 - 错误返回：

   `EINVAL`  （`bdev!= bdev->bd_contains`）（不确定这意味着什么）
   `EACCES`  访问被拒绝：需要 `CAP_SYS_ADMIN`
   `EINVAL`  值超出范围 [0 254]
   `EBUSY`  控制器繁忙

# HDIO_SET_QDMA - 更改使用-qdma 标志

 - 截至 2.6.8.1 未实现

# HDIO_SET_ADDRESS - 更改 lba 寻址模式

 - 使用方法：

   `int val;`
   `ioctl(fd, HDIO_SET_ADDRESS, val);`

 - 输入：
   寻址模式的新值
   0 = 28 位
   1 = 48 位
   2 = 48 位执行 28 位

 - 输出：无

 - 错误返回：

   `EINVAL`  （`bdev!= bdev->bd_contains`）（不确定这意味着什么）
   `EACCES`  访问被拒绝：需要 `CAP_SYS_ADMIN`
   `EINVAL`  值超出范围 [0 2]
   `EBUSY`  控制器繁忙
   `EIO`  驱动器不支持 lba48 模式。

# HDIO_SET_IDE_SCSI

 - 使用方法：

   `long val;`
   `ioctl(fd, HDIO_SET_IDE_SCSI, val);`

 - 输入：
   scsi 仿真模式的新值（？）

 - 输出：无

 - 错误返回：

   `EINVAL`  （`bdev!= bdev->bd_contains`）（不确定这意味着什么）
   `EACCES`  访问被拒绝：需要 `CAP_SYS_ADMIN`
   `EINVAL`  值超出范围 [0 1]
   `EBUSY`  控制器繁忙

# HDIO_SET_SCSI_IDE

 - 截至 2.6.8.1 未实现

