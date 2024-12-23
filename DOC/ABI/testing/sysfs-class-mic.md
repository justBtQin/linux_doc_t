**什么：** `/sys/class/mic/`

**日期：** 2013 年 10 月

**内核版本：** 3.13

**联系人：** Sudeep Dutt <sudeep.dutt@intel.com>

**描述：**

mic 类目录属于英特尔 MIC 设备，并提供每个 MIC 设备的信息。英特尔 MIC 设备是基于英特尔 Many Integrated Core (MIC) 架构的 PCIe 外形尺寸附加协处理器卡，运行 Linux OS。

**什么：** `/sys/class/mic/mic(x)`

**日期：** 2013 年 10 月

**内核版本：** 3.13

**联系人：** Sudeep Dutt <sudeep.dutt@intel.com>

**描述：**

目录 `/sys/class/mic/mic0`、`/sys/class/mic/mic1` 等表示 MIC 设备（0、1 等）。每个目录都有特定于该 MIC 设备的信息。

**什么：** `/sys/class/mic/mic(x)/family`

**日期：** 2013 年 10 月

**内核版本：** 3.13

**联系人：** Sudeep Dutt <sudeep.dutt@intel.com>

**描述：**

提供英特尔 MIC 设备的协处理器系列信息。例如 - "x100"

**什么：** `/sys/class/mic/mic(x)/stepping`

**日期：** 2013 年 10 月

**内核版本：** 3.13

**联系人：** Sudeep Dutt <sudeep.dutt@intel.com>

**描述：**

提供英特尔 MIC 设备的硅片步进信息。例如 - "A0" 或 "B0"

**什么：** `/sys/class/mic/mic(x)/state`

**日期：** 2013 年 10 月

**内核版本：** 3.13

**联系人：** Sudeep Dutt <sudeep.dutt@intel.com>

**描述：**

读取时，此条目提供英特尔 MIC 设备在卡 OS 上下文中的当前状态。可能读取的值为：

"offline" - MIC 设备已准备好引导卡 OS。在 OSPM 恢复后读取此条目时，如果卡先前在 OSPM 挂起期间关闭，则必须将 "boot" 写入此条目。

"online" - MIC 设备已启动卡 OS 引导。

"shutting_down" - 卡 OS 正在关闭。

"reset_failed" - MIC 设备复位失败。

"suspending" - MIC 设备当前正在准备挂起。读取此条目时，必须将 "suspend" 写入 state sysfs 条目，以确保卡在 OSPM 挂起期间关闭。

"suspended" - MIC 设备已挂起。

写入时，此 sysfs 条目根据卡 OS 的当前状态触发不同的状态更改操作。可接受的值为：

"boot" - 引导由固件、ramdisk、cmdline 和 bootmode sysfs 条目组合指定的卡 OS 映像。

"reset" - 启动设备复位。

"shutdown" - 启动卡 OS 关闭。

"suspend" - 启动卡 OS 关闭并将卡标记为已挂起。

**什么：** `/sys/class/mic/mic(x)/shutdown_status`

**日期：** 2013 年 10 月

**内核版本：** 3.13

**联系人：** Sudeep Dutt <sudeep.dutt@intel.com>

**描述：**

英特尔 MIC 设备在运行期间运行 Linux OS。此 OS 可能由于各种原因关闭。读取时，此条目提供卡 OS 关闭的原因状态。可能的值为：

"nop" - 关闭状态不适用，当卡 OS 为 "online" 时

"crashed" - 由于硬件或软件崩溃而关闭。

"halted" - 由于停止命令而关闭。

"poweroff" - 由于关闭命令而关闭。

"restart" - 由于重新启动命令而关闭。

**什么：** `/sys/class/mic/mic(x)/cmdline`

**日期：** 2013 年 10 月

**内核版本：** 3.13

**联系人：** Sudeep Dutt <sudeep.dutt@intel.com>

**描述：**

英特尔 MIC 设备在运行期间运行 Linux OS。在引导此卡 OS 之前，可以传递内核命令行选项来配置其中的各种功能，类似于自启动机器。读取时，此条目提供用于引导卡 OS 的当前内核命令行选项的信息。可以写入此条目以更改现有内核命令行选项。通常，用户希望读取当前命令行选项，附加新选项或修改现有选项，然后将整个内核命令行写回此条目。

**什么：** `/sys/class/mic/mic(x)/firmware`

**日期：** 2013 年 10 月

**内核版本：** 3.13

**联系人：** Sudeep Dutt <sudeep.dutt@intel.com>

**描述：**

读取时，此 sysfs 条目提供在 /lib/firmware/ 下可找到要在卡上引导的固件映像的路径名。可以写入此条目以更改 /lib/firmware/ 下的固件映像位置。

**什么：** `/sys/class/mic/mic(x)/ramdisk`

**日期：** 2013 年 10 月

**内核版本：** 3.13

**联系人：** Sudeep Dutt <sudeep.dutt@intel.com>

**描述：**

读取时，此 sysfs 条目提供在 /lib/firmware/ 下可找到在卡 OS 引导期间使用的 ramdisk 映像的路径名。可以写入此条目以更改 /lib/firmware/ 下的 ramdisk 映像位置。

**什么：** `/sys/class/mic/mic(x)/bootmode`

**日期：** 2013 年 10 月

**内核版本：** 3.13

**联系人：** Sudeep Dutt <sudeep.dutt@intel.com>

**描述：**

读取时，此 sysfs 条目提供卡的当前引导模式。此 sysfs 条目可以用以下有效字符串写入：

a) linux - 引导 Linux 映像。

b) elf - 引导用于闪存更新的 elf 映像。

**什么：** `/sys/class/mic/mic(x)/log_buf_addr`

**日期：** 2013 年 10 月

**内核版本：** 3.13

**联系人：** Sudeep Dutt <sudeep.dutt@intel.com>

**描述：**

英特尔 MIC 设备在运行期间运行 Linux OS。出于调试目的和早期内核引导消息，用户可以通过 debugfs 访问卡 OS 日志缓冲区。读取时，此条目提供可以读取卡 OS 日志缓冲区的缓冲区的内核虚拟地址。此条目由主机配置守护进程写入以设置日志缓冲区地址。要写入的正确日志缓冲区地址可以在卡 OS 的 System.map 文件中找到。

**什么：** `/sys/class/mic/mic(x)/log_buf_len`

**日期：** 2013 年 10 月

**内核版本：** 3.13

**联系人：** Sudeep Dutt <sudeep.dutt@intel.com>

**描述：**

英特尔 MIC 设备在运行期间运行 Linux OS。出于调试目的和早期内核引导消息，用户可以通过 debugfs 访问卡 OS 日志缓冲区。读取时，此条目提供可以读取卡 OS 日志缓冲区长度的内核虚拟地址。此条目由主机配置守护进程写入以设置日志缓冲区长度地址。要写入的正确日志缓冲区长度地址可以在卡 OS 的 System.map 文件中找到。