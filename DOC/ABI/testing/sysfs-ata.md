**What**: `/sys/class/ata_...`

**Date**: August 2008

**Contact**: Gwendal Grignou `<gwendal@google.com>`

**Description**:

提供一个在 `sysfs` 中的位置，用于存储系统的 ATA 拓扑结构。这允许检索关于 ATA 对象的各种信息。

`/sys/class/ata_port` 下的文件

---------------------------------

对于每个端口，会创建一个目录 `ataX`，其中 `X` 是端口的 `ata_port_id`。设备父节点是 `ata` 主机设备。

`idle_irq`（读取）

端口空闲时接收到的中断数量[某些 `ata HBA` 仅支持]。

`nr_pmp_links`（读取）

如果连接了 SATA 端口倍增器（PM），则其后的链接数量。

`/sys/class/ata_link` 下的文件

---------------------------------

在每个端口后面，有一个 `ata_link`。如果拓扑结构中有 SATA PM，则会创建 15 个 `ata_link` 对象。

如果一个链接在端口后面，目录名称为 `linkX`，其中 `X` 是端口的 `ata_port_id`。

如果一个链接在 PM 后面，其名称为 `linkX.Y`，其中 `X` 是父端口的 `ata_port_id`，`Y` 是 PM 端口。

`hw_sata_spd_limit`

连接的 SATA 设备支持的最大速度。

`sata_spd_limit`

`libata` 施加的最大速度。

`sata_spd`

链接的当前速度[1.5、3Gps 等]。

`/sys/class/ata_device` 下的文件

---------------------------------

在每个链接后面，最多创建两个 `ata` 设备。

目录名称为 `devX[.Y].Z`，其中：

- `X` 是设备连接的端口的 `ata_port_id`，

- `Y` 是 PM 的端口（如果有），

- `Z` 是设备 ID：对于 PATA，通常有 2 个设备[0,1]，对于 SATA 只有 1 个。

`class`

设备类。对于磁盘可以是“ata”，对于数据包设备可以是“atapi”，对于 PM 可以是“pmp”，如果在链接后面未找到设备则为“none”。

`dma_mode`

设备在 DMA 模式下支持的传输模式。主要用于 PATA 设备。

`pio_mode`

设备在 PIO 模式下支持的传输模式。主要用于 PATA 设备。

`xfer_mode`

当前传输模式。

`id`

如 ATA8 7.16 和 7.17 中所述的 `IDENTIFY` 命令的缓存结果。仅在设备不是 PM 时有效。

`gscr`

PM GSCR 寄存器转储的缓存结果。有效的寄存器有：

0：SATA_PMP_GSCR_PROD_ID，

1：SATA_PMP_GSCR_REV，

2：SATA_PMP_GSCR_PORT_INFO，

32：SATA_PMP_GSCR_ERROR，

33：SATA_PMP_GSCR_ERROR_EN，

64：SATA_PMP_GSCR_FEAT，

96：SATA_PMP_GSCR_FEAT_EN，

130：SATA_PMP_GSCR_SII_GPIO

仅在设备是 PM 时有效。

`trim`

显示设备当前使用的 DSM TRIM 模式。有效值有：

unsupported：驱动器不支持 DSM TRIM

unqueued：驱动器仅支持未排队的 DSM TRIM

queued：驱动器支持排队的 DSM TRIM

forced_unqueued：已知驱动器的未排队 DSM 支持有问题，仅发送未排队的 TRIM 命令

`spdn_cnt`

由于错误，`libata` 决定降低链接速度的次数。

`ering`

设备错误环的格式化输出。