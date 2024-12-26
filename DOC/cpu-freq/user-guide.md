<markdown_document>

# CPU frequency and voltage scaling code in the Linux(TM) kernel

Linux CPUFreq

USER GUIDE

Dominik Brodowski <linux@brodo.de>

时钟缩放允许您实时更改 CPU 的时钟速度。这是一种节省电池电量的好方法，因为时钟速度越低，CPU 消耗的功率就越少。

内容：

- 1. Supported Architectures and Processors

    - 1.1 ARM

    - 1.2 x86

    - 1.3 sparc64

    - 1.4 ppc

    - 1.5 SuperH

    - 1.6 Blackfin

- 2. "Policy" / "Governor"?

    - 2.1 Policy

    - 2.2 Governor

- 3. How to change the CPU cpufreq policy and/or speed

    - 3.1 Preferred interface: sysfs

1. Supported Architectures and Processors

=========================================

1.1 ARM

-------

以下 ARM 处理器受 cpufreq 支持：

ARM Integrator

ARM-SA1100

ARM-SA1110

Intel PXA

1.2 x86

-------

以下 x86 架构的处理器受 cpufreq 支持：

AMD Elan - SC400, SC410

AMD mobile K6-2+

AMD mobile K6-3+

AMD mobile Duron

AMD mobile Athlon

AMD Opteron

AMD Athlon 64

Cyrix Media GXm

Intel mobile PIII 和某些芯片组上的 Intel mobile PIII-M

Intel Pentium 4, Intel Xeon

Intel Pentium M (Centrino)

National Semiconductors Geode GX

Transmeta Crusoe

Transmeta Efficeon

VIA Cyrix 3 / C3

某些 ACPI 2.0 兼容系统上的各种处理器 [*]

[*] 仅当“ACPI 处理器性能状态”可用于 ACPI<->BIOS 接口时。

1.3 sparc64

-----------

以下 sparc64 架构的处理器受 cpufreq 支持：

UltraSPARC-III

1.4 ppc

-------

一些“PowerBook”和“iBook2”笔记本电脑受到支持。

1.5 SuperH

----------

所有通过时钟框架支持速率舍入的 SuperH 处理器都受 cpufreq 支持。

1.6 Blackfin

------------

以下 Blackfin 处理器受 cpufreq 支持：

BF522, BF523, BF524, BF525, BF526, BF527, Rev 0.1 或更高版本

BF531, BF532, BF533, Rev 0.3 或更高版本

BF534, BF536, BF537, Rev 0.2 或更高版本

BF561, Rev 0.3 或更高版本

BF542, BF544, BF547, BF548, BF549, Rev 0.1 或更高版本

2. "Policy" / "Governor"?

==========================

一些具有 CPU 频率缩放功能的处理器可以在没有任何内核或用户干预的情况下“实时”在各种频率和工作电压之间切换。这保证了非常快速地切换到足以满足用户需求但又足够低以节省电源的频率。

2.1 Policy

----------

在这些系统上，您可以做的就是选择较低和较高的频率限制，以及您是想要更积极的节能还是更即时可用的处理能力。

2.2 Governor

------------

在所有其他 cpufreq 实现中，仍然需要设置这些边界。然后，必须选择一个“调节器”。这样的“调节器”决定处理器在边界内运行的速度。其中一个“调节器”是“用户空间”调节器。这个调节器允许用户 - 或尚未实现的用户空间程序 - 决定处理器应运行的特定速度。

3. How to change the CPU cpufreq policy and/or speed

====================================================

3.1 Preferred Interface: sysfs

------------------------------

首选接口位于 sysfs 文件系统中。如果您将其挂载在 /sys 上，cpufreq 接口位于 cpu 设备目录中的“cpufreq”子目录中（例如，对于第一个 CPU，为 /sys/devices/system/cpu/cpu0/cpufreq/）。

cpuinfo_min_freq：此文件显示处理器可以运行的最低工作频率（以 kHz 为单位）

cpuinfo_max_freq：此文件显示处理器可以运行的最高工作频率（以 kHz 为单位）

cpuinfo_transition_latency：此 CPU 在两个频率之间切换所需的时间（以纳秒为单位）。如果未知或已知过高以至于驱动程序与 ondemand 调节器不兼容，则返回 -1（CPUFREQ_ETERNAL）。使用此信息对于为内核调节器或用户空间守护程序选择适当的轮询频率很有用。请确保不要过于频繁地切换频率，以免导致性能损失。

scaling_driver：此文件显示用于设置此 CPU 频率的 cpufreq 驱动程序。

scaling_available_governors：此文件显示此内核中可用的 CPUfreq 调节器。您可以在

scaling_governor 中看到当前激活的调节器，并且通过“回显”另一个调节器的名称可以更改它。请注意，某些调节器不会加载 - 它们仅在某些特定架构或处理器上工作。

cpuinfo_cur_freq：从硬件获取的 CPU 当前频率，以 KHz 为单位。这是 CPU 实际运行的频率。

scaling_available_frequencies：可用频率列表，以 KHz 为单位。

scaling_min_freq 和

scaling_max_freq 显示当前的“策略限制”（以 kHz 为单位）。通过将新值回显到这些文件中，您可以更改这些限制。

注意：设置策略时，您需要先设置 scaling_max_freq，然后设置 scaling_min_freq。

affected_cpus：需要软件协调频率的在线 CPU 列表。

related_cpus：需要软件协调频率的在线 + 离线 CPU 列表。

scaling_driver：cpufreq 的硬件驱动程序。

scaling_cur_freq：由调节器和 cpufreq 核心确定的 CPU 当前频率，以 KHz 为单位。这是内核认为 CPU 运行的频率。

bios_limit：如果 BIOS 告诉操作系统将 CPU 限制为较低频率，用户可以从此文件中读取可用的最大频率。

这通常可能通过（通常不是有意的）BIOS 设置、通过服务处理器或其他基于 BIOS/硬件的实现触发的限制发生。

这不包括可以通过通用热驱动程序检测到的热 ACPI 限制。

如果您选择了“用户空间”调节器，它允许您将 CPU 工作频率设置为特定值，您可以从此处读取当前频率

scaling_setspeed。通过将新频率回显到此文件中，您可以更改 CPU 的速度，但只能在 scaling_min_freq 和 scaling_max_freq 的限制范围内。

</markdown_document>