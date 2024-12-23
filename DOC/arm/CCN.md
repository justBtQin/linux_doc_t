# ARM 缓存一致性网络

==========================

CCN - 504 是一个由 11 个交叉点（XPs）组成的环形总线互连，每个交叉点支持最多两个设备端口，

因此节点（设备）0 和 1 连接到交叉点 0，节点 2 和 3 连接到交叉点 1 等。

# PMU（性能）驱动程序

-----------------

CCN 驱动程序注册了一个性能 PMU 驱动程序，该驱动程序在 sysfs 中提供了可用事件和配置选项的描述，

请参阅 /sys/bus/event_source/devices/ccn*。“format”目录描述了 perf_event_attr 结构的 config、config1 和 config2 字段的格式。“events”目录为所有记录的事件提供了配置模板，可与 perf 工具一起使用。例如，“xp_valid_flit”等同于“type=0x8,event=0x4”。其他参数必须明确指定。对于源自设备的事件，“node”定义了其索引。所有交叉点事件都需要“xp”（索引）、“port”（设备端口号）、“vc”（虚拟通道 ID）和“dir”（方向）。观察点（特殊的“event”值 0xfe）还需要比较器值（“cmp_l”和“cmp_h”）以及“mask”，即比较器掩码的索引。

掩码与事件描述分开定义（由于配置值数量有限），位于“cmp_mask”目录中，其中前 8 个可由用户配置，另外 4 个为最常见的用例硬编码。

周期计数器由“type”值 0xff 描述，不需要任何其他设置。

perf 工具使用示例：

/ # perf list | grep ccn

  ccn/cycles/                                        [Kernel PMU 事件]

<...>

  ccn/xp_valid_flit/                                 [Kernel PMU 事件]

<...>

/ # perf stat -C 0 -e ccn/cycles/,ccn/xp_valid_flit,xp=1,port=0,vc=1,dir=1/ \

                                                                       睡眠 1

驱动程序不支持采样，因此“perf record”将无法工作。还请注意，仅选择了单个 CPU（“-C 0”）- 这是因为 perf 框架尚不支持“非 CPU 相关”计数器（尚未？），因此系统范围的会话（“-a”）将尝试（并且在大多数情况下会失败）为每个 CPU 设置相同的事件。