**What**: /sys/bus/fcoe/

**Date**: 八月 2012

**KernelVersion**: TBD

**Contact**: Robert Love <robert.w.love@intel.com>, devel@open-fcoe.org

**Description**: FCoE 总线。此目录中的属性为控制接口。

**Attributes**:

- ctlr_create: 'FCoE 控制器'实例创建接口。向此文件写入一个<ifname>将分配并填充 sysfs 中的一个 fcoe_ctlr_device（ctlr_X）。用户然后可以配置每个端口的设置，最后写入 fcoe_ctlr_device 的'start'属性以开始内核的发现和登录过程。

- ctlr_destroy: 'FCoE 控制器'实例删除接口。向此文件写入一个 fcoe_ctlr_device 的 sysfs 名称将使该 fcoe_ctlr_device 从结构中注销或其他连接的 FCoE 设备。它还将释放为此 fcoe_ctlr_device 分配的所有内核内存以及与之相关的任何结构，包括 scsi_host。

**What**: /sys/bus/fcoe/devices/ctlr_X

**Date**: 三月 2012

**KernelVersion**: TBD

**Contact**: Robert Love <robert.w.love@intel.com>, devel@open-fcoe.org

**Description**: fcoe 总线上的'FCoE 控制器'实例。FCoE 控制器现在有一个三阶段创建过程。1）将接口名称写入 ctlr_create；2）配置 FCoE 控制器（ctlr_X）；3）启用 FCoE 控制器以开始发现和登录。通过将其名称（即 ctlr_X）写入 ctlr_delete 文件来销毁 FCoE 控制器。

**Attributes**:

- fcf_dev_loss_tmo: 设备丢失超时周期（见下文）。更改此值将更改此控制器发现的所有 FCF 的 dev_loss_tmo。

- mode: 显示或更改 FCoE 控制器的模式。可能的模式为'Fabric'和'VN2VN'。如果 FCoE 控制器以'Fabric'模式启动，则启动 FIP FCF 发现并最终尝试进行结构登录。如果 FCoE 控制器以'VN2VN'模式启动，则执行 FIP VN2VN 发现和登录。一个 FCoE 控制器一次只能支持一种模式。

- enabled: FCoE 控制器是否启用或禁用。0 表示禁用，1 表示启用。向此文件写入 0 或 1 将启用或禁用 FCoE 控制器。

- lesb/link_fail: 链路错误状态块（LESB）链路故障计数。

- lesb/vlink_fail: 链路错误状态块（LESB）虚拟链路故障计数。

- lesb/miss_fka: 链路错误状态块（LESB）错过的 FCoE 初始化协议（FIP）保持活动（FKA）。

- lesb/symb_err: 链路错误状态块（LESB）符号错误计数。

- lesb/err_block: 链路错误状态块（LESB）块错误计数。

- lesb/fcs_error: 链路错误状态块（LESB）光纤通道服务错误计数。

**Notes**: ctlr_X（从 0 开始的全局递增）

**What**: /sys/bus/fcoe/devices/fcf_X

**Date**: 三月 2012

**KernelVersion**: TBD

**Contact**: Robert Love <robert.w.love@intel.com>, devel@open-fcoe.org

**Description**: fcoe 总线上的'FCoE FCF'实例。FCF 是光纤通道转发器，是一种可以接受 FCoE（以太网）数据包、解包并将嵌入的光纤通道帧转发到 FC 结构中的 FCoE 交换机。它还可以取出出站 FC 帧并将其打包在以太网数据包中，以在以太网段上发送到其目的地。

**Attributes**:

- fabric_name: 标识 FCF 服务的结构。

- switch_name: 标识 FCF。

- priority: 同一结构上其他 FCF 中的交换机优先级。

- selected: 1 表示已选择该交换机用于使用；0 表示该交换机将不被使用。

- fc_map: 光纤通道映射

- vfid: 虚拟结构 ID

- mac: FCF 的 MAC 地址

- fka_peroid: FIP 保持活动周期

- fabric_state: 内部内核状态

"Unknown" - 初始化值

"Disconnected" - 与 FCF/结构无链接

"Connected" - 主机已连接到 FCF

"Deleted" - FCF 正在从系统中删除

- dev_loss_tmo: 此 FCF 的设备丢失超时周期

**Notes**: 类似于 FC 传输的设备丢失基础设施存在于 fcoe_sysfs 中。这很好，因为链接抖动的适配器不会不断增加用于标识发现的 FCF 的计数。FCF 将处于“Disconnected”状态，直到计时器到期且 FCF 变为“Deleted”或 FCF 被重新发现并变为“Connected”。

**Users**: 此接口的第一个用户将是 fcoeadm 应用程序，通常在 fcoe-utils 包中打包。