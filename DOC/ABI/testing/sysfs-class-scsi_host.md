**What: /sys/class/scsi_host/hostX/isci_id**

**Date: June 2011**

**Contact: Dave Jiang <dave.jiang@intel.com>**

**Description:**

此文件包含英特尔 SCU 控制器的枚举主机 ID。英特尔（R）C600 系列芯片组 SATA/SAS 存储控制单元在单个 PCI 设备中嵌入多达两个 4 端口控制器。控制器按顺序枚举，通常意味着最低编号的 scsi_host 对应第一个控制器，但此关联不保证。'isci_id'属性明确标识控制器索引：'0'表示第一个控制器，'1'表示第二个。

**What: /sys/class/scsi_host/hostX/acciopath_status**

**Date: November 2013**

**Contact: Stephen M. Cameron <scameron@beardog.cce.hp.com>**

**Description:**

此文件包含使用 hpsa 驱动程序的 HP Smart Array RAID 控制器的“SSD 智能路径”功能的当前状态。启用 SSD 智能路径后，驱动程序可以将 I/O 请求直接发送到作为逻辑驱动器一部分的物理设备，在可能的情况下绕过控制器固件 RAID 堆栈以获得性能优势。值为'1'表示该功能已启用，控制器可以使用直接 I/O 路径到物理设备。值为零表示该功能已禁用，控制器可能不使用直接 I/O 路径到物理设备。此设置在控制器范围内，影响控制器上的所有配置逻辑驱动器。此文件可读可写。