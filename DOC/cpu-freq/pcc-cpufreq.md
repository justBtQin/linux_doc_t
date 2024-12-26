<markdown_document>

# Processor Clocking Control Driver

## 简介

Processor Clocking Control (PCC) 是平台固件和 OSPM 之间的接口。它是一种在平台固件和操作系统之间协调处理器性能（即：频率）的机制。PCC 驱动程序（pcc-cpufreq）允许 OSPM 利用 PCC 接口。

操作系统利用 PCC 接口向平台固件告知操作系统对逻辑处理器所需的频率。平台固件尝试实现请求的频率。如果平台固件无法满足目标频率的请求，通常意味着功率预算条件已到位，并且正在进行“功率限制”。

## 1.1 PCC 接口

### 1.1.1 获取平均频率

此命令由 OSPM 用于查询处理器自上次完成此命令以来的运行频率。输出缓冲区指示逻辑处理器的平均未停止频率，表示为标称（即：最大）CPU 频率的百分比。输出缓冲区还表示 CPU 频率是否受到功率预算条件的限制。

### 1.1.2 设置所需频率

此命令由 OSPM 用于向平台固件传达逻辑处理器的所需频率。输出缓冲区目前被 OSPM 忽略。下一次调用“获取平均频率”将告知 OSPM 是否达到了所需的频率。

## 1.2 受影响的平台

PCC 驱动程序将在平台固件支持 PCC 接口以及相关的 PCCH()和 PCCP()方法且负责管理硬件时钟控制以提供请求的处理器性能的任何系统上加载。

目前，某些 HP ProLiant 平台实现了 PCC 接口。在这些平台上，PCC 是“默认”选择。

但是，可以通过 BIOS 设置禁用此接口。在这种情况下，就像在未实现 PCC 接口的平台上一样，PCC 驱动程序将默默地加载失败。

## 2. 驱动程序和 /sys 详细信息

当驱动程序加载时，它仅打印平台固件支持的最低和最高 CPU 频率。

PCC 驱动程序加载时会显示类似以下消息：

pcc-cpufreq: (v1.00.00) 驱动程序已加载，频率限制：1600 MHz，2933 MHz

这意味着 OPSM 可以请求 CPU 在消息中指定的限制（1600 MHz 和 2933 MHz）之间的任何频率下运行。

内部，驱动程序无需将“目标”频率转换为相应的 P 状态。

驱动程序的版本号将采用 v.xy.ab 的格式。例如：1.00.02

以下是通过 /sys 文件系统导出的一些字段的简要讨论以及它们的值如何受 PCC 驱动程序的影响：

### 2.1 scaling_available_frequencies

/sys 中未创建 scaling_available_frequencies。无需列出中间频率，因为 BIOS 将尝试在限制范围内实现调速器请求的任何频率。频率不必与 P 状态严格关联。

### 2.2 cpuinfo_transition_latency

cpuinfo_transition_latency 字段为 0。PCC 规范目前不包括公开此值的字段。

### 2.3 cpuinfo_cur_freq

A) 通常，cpuinfo_cur_freq 将显示与 scaling_available_frequencies 或 scaling_cur_freq 或 scaling_max_freq 中声明的值不同的值。这是由于最近的 Intel 处理器上可用的“涡轮加速”。如果满足某些条件，BIOS 可以实现比 OSPM 请求的稍高的速度。例如：

scaling_cur_freq ：2933000

cpuinfo_cur_freq ：3196000

B) cpuinfo_cur_freq 值存在舍入误差。由于驱动程序从 BIOS 获得当前频率作为标称频率的“百分比”（%），因此有时 scaling_cur_freq 和 cpuinfo_cur_freq 显示的值可能不匹配。例如：

scaling_cur_freq ：1600000

cpuinfo_cur_freq ：1583000

在此示例中，标称频率为 2933 MHz。驱动程序获得的当前频率，cpuinfo_cur_freq，为标称频率的 54%：

54% 的 2933 MHz = 1583 MHz

标称频率是处理器的最大频率，通常对应于 P0 P 状态的频率。

### 2.4 related_cpus

related_cpus 字段与 affected_cpus 相同。

affected_cpus ：4

related_cpus ：4

目前，PCC 驱动程序不评估 _PSD。支持 PCC 的平台未实现 SW_ALL。因此，OSPM 无需执行任何协调以确保对所有相关 CPU 请求相同的频率。

## 3. 注意事项

当前形式的“cpufreq_stats”模块无法加载并预期与 PCC 驱动程序一起工作。由于“cpufreq_stats”模块提供有关每个 P 状态的信息，因此不适用于 PCC 驱动程序。

</markdown_document>