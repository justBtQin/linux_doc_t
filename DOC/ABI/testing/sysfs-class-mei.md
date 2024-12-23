**What**：`/sys/class/mei/`

**Date**：2014 年 5 月

**KernelVersion**：3.17

**Contact**：Tomas Winkler <tomas.winkler@intel.com>

**Description**：

`mei/` 类子目录属于 `mei` 设备类

**What**：`/sys/class/mei/meiN/`

**Date**：2014 年 5 月

**KernelVersion**：3.17

**Contact**：Tomas Winkler <tomas.winkler@intel.com>

**Description**：

对于每个探测到的 `mei` 设备，都会创建 `/sys/class/mei/meiN` 目录

**What**：`/sys/class/mei/meiN/fw_status`

**Date**：2014 年 11 月

**KernelVersion**：3.19

**Contact**：Tomas Winkler <tomas.winkler@intel.com>

**Description**：显示 `fw` 状态寄存器的内容

ME FW 将其状态信息写入 `fw` 状态寄存器，以供 BIOS 和操作系统监控 `fw` 的健康状况。该寄存器包含运行状态、电源管理状态、错误代码等。寄存器的解码方式取决于 PCH 或 SoC 的世代。此外，根据世代的不同，寄存器的数量在 1 到 6 个之间变化。