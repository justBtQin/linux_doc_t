# 一、/sys/block/dm-<num>/dm/name

- **What**：/sys/block/dm-<num>/dm/name

- **Date**：January 2009

- **KernelVersion**：2.6.29

- **Contact**：dm-devel@redhat.com

- **Description**：Device-mapper 设备名称。包含映射设备名称的只读字符串。

- **Users**：util-linux, device-mapper udev rules

# 二、/sys/block/dm-<num>/dm/uuid

- **What**：/sys/block/dm-<num>/dm/uuid

- **Date**：January 2009

- **KernelVersion**：2.6.29

- **Contact**：dm-devel@redhat.com

- **Description**：Device-mapper 设备 UUID。包含 DM-UUID 的只读字符串，如果未设置 DM-UUID，则为空字符串。

- **Users**：util-linux, device-mapper udev rules

# 三、/sys/block/dm-<num>/dm/suspended

- **What**：/sys/block/dm-<num>/dm/suspended

- **Date**：June 2009

- **KernelVersion**：2.6.31

- **Contact**：dm-devel@redhat.com

- **Description**：Device-mapper 设备挂起状态。设备挂起时包含值 1，否则包含 0。只读属性。

- **Users**：util-linux, device-mapper udev rules

# 四、/sys/block/dm-<num>/dm/rq_based_seq_io_merge_deadline

- **What**：/sys/block/dm-<num>/dm/rq_based_seq_io_merge_deadline

- **Date**：March 2015

- **KernelVersion**：4.1

- **Contact**：dm-devel@redhat.com

- **Description**：允许控制作为合理合并候选的请求在请求队列中可以排队的时间长度。此截止时间的分辨率为微秒（范围从 1 到 100000 微秒）。将此属性设置为 0（默认值）将禁用基于请求的 DM 的合并启发式算法和相关的额外记账。此属性不适用于基于 bio 的 DM 设备，因此对于它们，它将始终报告 0。

# 五、/sys/block/dm-<num>/dm/use_blk_mq

- **What**：/sys/block/dm-<num>/dm/use_blk_mq

- **Date**：March 2015

- **KernelVersion**：4.1

- **Contact**：dm-devel@redhat.com

- **Description**：基于请求的 Device-mapper blk-mq I/O 路径模式。如果设备正在使用 blk-mq，则包含值 1，否则包含 0。只读属性。