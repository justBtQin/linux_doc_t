**What**: `/sys/class/srp_remote_ports/port-<h>:<n>/delete`

**Date**: June 1, 2012

**KernelVersion**: 3.7

**Contact**: linux-scsi@vger.kernel.org, linux-rdma@vger.kernel.org

**Description**: 指示 SRP 启动器与目标断开连接，并删除从该目标导入的所有 LUN。

**What**: `/sys/class/srp_remote_ports/port-<h>:<n>/dev_loss_tmo`

**Date**: February 1, 2014

**KernelVersion**: 3.13

**Contact**: linux-scsi@vger.kernel.org, linux-rdma@vger.kernel.org

**Description**: 在观察到传输层错误后，SCSI 层将等待的秒数，然后删除目标端口。零表示立即删除。将此属性设置为“off”将禁用 dev_loss 计时器。

**What**: `/sys/class/srp_remote_ports/port-<h>:<n>/fast_io_fail_tmo`

**Date**: February 1, 2014

**KernelVersion**: 3.13

**Contact**: linux-scsi@vger.kernel.org, linux-rdma@vger.kernel.org

**Description**: 在观察到传输层错误后，SCSI 层将等待的秒数，然后使 I/O 失败。零表示立即使 I/O 失败。将此属性设置为“off”将禁用 fast_io_fail 计时器。

**What**: `/sys/class/srp_remote_ports/port-<h>:<n>/port_id`

**Date**: June 27, 2007

**KernelVersion**: 2.6.24

**Contact**: linux-scsi@vger.kernel.org

**Description**: 十六进制格式的 16 字节本地 SRP 端口标识符。例如：4c:49:4e:55:58:20:56:49:4f:00:00:00:00:00:00:00。

**What**: `/sys/class/srp_remote_ports/port-<h>:<n>/reconnect_delay`

**Date**: February 1, 2014

**KernelVersion**: 3.13

**Contact**: linux-scsi@vger.kernel.org, linux-rdma@vger.kernel.org

**Description**: 在重新连接尝试失败后，SCSI 层将等待的秒数，然后重试。将此属性设置为“off”将禁用基于时间的重新连接。

**What**: `/sys/class/srp_remote_ports/port-<h>:<n>/roles`

**Date**: June 27, 2007

**KernelVersion**: 2.6.24

**Contact**: linux-scsi@vger.kernel.org

**Description**: 远程端口的角色。要么是“SRP 启动器”，要么是“SRP 目标”。

**What**: `/sys/class/srp_remote_ports/port-<h>:<n>/state`

**Date**: February 1, 2014

**KernelVersion**: 3.13

**Contact**: linux-scsi@vger.kernel.org, linux-rdma@vger.kernel.org

**Description**: 用于与远程端口通信的传输层的状态。“运行”表示传输层正在运行；“阻塞”表示遇到传输层错误，但 fast_io_fail_tmo 计时器尚未触发；“快速失败”表示 fast_io_fail_tmo 计时器触发且 dev_loss_tmo 计时器未触发之前；“丢失”表示 dev_loss_tmo 计时器触发且端口最终被删除之前。