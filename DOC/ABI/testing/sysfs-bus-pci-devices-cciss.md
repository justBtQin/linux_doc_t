**Where: /sys/bus/pci/devices/<dev>/ccissX/cXdY/model**

- **Date**: March 2009

- **Kernel Version**: 2.6.30

- **Contact**: iss_storagedev@hp.com

- **Description**: 显示逻辑驱动器 Y 的 SCSI INQUIRY 页 0 型号（控制器 X 的）。

**Where: /sys/bus/pci/devices/<dev>/ccissX/cXdY/rev**

- **Date**: March 2009

- **Kernel Version**: 2.6.30

- **Contact**: iss_storagedev@hp.com

- **Description**: 显示逻辑驱动器 Y 的 SCSI INQUIRY 页 0 修订版（控制器 X 的）。

**Where: /sys/bus/pci/devices/<dev>/ccissX/cXdY/unique_id**

- **Date**: March 2009

- **Kernel Version**: 2.6.30

- **Contact**: iss_storagedev@hp.com

- **Description**: 显示逻辑驱动器 Y 的 SCSI INQUIRY 页 83 序列号（控制器 X 的）。

**Where: /sys/bus/pci/devices/<dev>/ccissX/cXdY/vendor**

- **Date**: March 2009

- **Kernel Version**: 2.6.30

- **Contact**: iss_storagedev@hp.com

- **Description**: 显示逻辑驱动器 Y 的 SCSI INQUIRY 页 0 供应商（控制器 X 的）。

**Where: /sys/bus/pci/devices/<dev>/ccissX/cXdY/block:cciss!cXdY**

- **Date**: March 2009

- **Kernel Version**: 2.6.30

- **Contact**: iss_storagedev@hp.com

- **Description**: 指向 /sys/block/cciss!cXdY 的符号链接。

**Where: /sys/bus/pci/devices/<dev>/ccissX/rescan**

- **Date**: August 2009

- **Kernel Version**: 2.6.31

- **Contact**: iss_storagedev@hp.com

- **Description**: 启动控制器的重新扫描以发现逻辑驱动器拓扑结构变化。

**Where: /sys/bus/pci/devices/<dev>/ccissX/cXdY/lunid**

- **Date**: August 2009

- **Kernel Version**: 2.6.31

- **Contact**: iss_storagedev@hp.com

- **Description**: 显示用于寻址控制器 X 的逻辑驱动器 Y 的 8 字节 LUN ID。

**Where: /sys/bus/pci/devices/<dev>/ccissX/cXdY/raid_level**

- **Date**: August 2009

- **Kernel Version**: 2.6.31

- **Contact**: iss_storagedev@hp.com

- **Description**: 显示控制器 X 的逻辑驱动器 Y 的 RAID 级别。

**Where: /sys/bus/pci/devices/<dev>/ccissX/cXdY/usage_count**

- **Date**: August 2009

- **Kernel Version**: 2.6.31

- **Contact**: iss_storagedev@hp.com

- **Description**: 显示控制器 X 的逻辑驱动器 Y 的使用计数（打开次数）。

**Where: /sys/bus/pci/devices/<dev>/ccissX/resettable**

- **Date**: February 2011

- **Kernel Version**: 2.6.38

- **Contact**: iss_storagedev@hp.com

- **Description**: 值为 1 表示控制器可以响应 reset_devices 内核参数。值为 0 表示 reset_devices 不能被响应。这是为了允许，例如，kexec 工具在将不可重置的设备指定为转储设备时能够警告用户，因为 kdump 需要重置设备才能可靠地工作。

**Where: /sys/bus/pci/devices/<dev>/ccissX/transport_mode**

- **Date**: July 2011

- **Kernel Version**: 3.0

- **Contact**: iss_storagedev@hp.com

- **Description**: 值为“simple”表示控制器已置于“simple 模式”。值为“performant”表示控制器已置于“performant 模式”。