<markdown_document>

# CPU frequency and voltage scaling code in the Linux(TM) kernel

Linux CPUFreq

CPUFreq Governors

- information for users and developers -

Dominik Brodowski <linux@brodo.de>

some additions and corrections by Nico Golde <nico@ngolde.de>

时钟缩放允许您动态更改 CPU 的时钟速度。这是一种节省电池电量的好方法，因为时钟速度越低，CPU 消耗的电量就越少。

内容：

---------

1. 什么是 CPUFreq 调节器？

2. Linux 内核中的调节器

2.1 性能

2.2 省电

2.3 用户空间

2.4 按需

2.5 保守

2.6 交互式

3. CPUfreq 核心中的调节器接口

1. 什么是 CPUFreq 调节器？ ==============================

大多数 cpufreq 驱动程序（实际上，除了 longrun 之外的所有驱动程序），甚至大多数 CPU 频率缩放算法，都只允许将 CPU 设置为一个频率。为了提供动态频率缩放，cpufreq 核心必须能够将“目标频率”告诉这些驱动程序。因此，这些特定的驱动程序将被转换为提供“->target/target_index”调用，而不是现有的“->setpolicy”调用。对于“longrun”，一切保持不变。

如何决定在 CPUfreq 策略中应该使用哪个频率？这是通过使用“cpufreq 调节器”来完成的。本补丁中已经有两个 - 它们是已经存在的“powersave”和“performance”，分别将频率静态设置为最低或最高频率。在不久的将来，至少还有两个这样的调节器准备添加，但可能会有更多，因为周围有各种不同的动态频率缩放理论和模型。使用 cpufreq 为缩放调节器提供的这种通用接口，可以对这些调节器进行广泛的测试，并为每个特定用途选择最好的调节器。

基本上，它是以下流程图：

CPU 可以独立设置为切换频率 | CPU 只能设置为特定频率

"CPUfreq 策略"

由频率限制（policy->{min,max}）组成

和要使用的 CPUfreq 调节器

/ \

/ \

/ 调节器决定（动态或静态）

/ 要在 policy->{min,max} 的限制内设置的 target_freq

/ \

/  使用 ->setpolicy 调用，设置限制和 "策略"。  使用 ->target/target_index 调用，设置最接近 target_freq 的频率。  确保它在 policy->{min,max} 内

2. Linux 内核中的调节器

================================

2.1 性能

---------------

CPUfreq 调节器“performance”将 CPU 静态设置为 scaling_min_freq 和 scaling_max_freq 范围内的最高频率。

2.2 省电

-------------

CPUfreq 调节器“powersave”将 CPU 静态设置为 scaling_min_freq 和 scaling_max_freq 范围内的最低频率。

2.3 用户空间

-------------

CPUfreq 调节器“userspace”允许用户或任何以 UID“root”运行的用户空间程序通过在 CPU 设备目录中提供一个 sysfs 文件“scaling_setspeed”来将 CPU 设置为特定频率。

2.4 按需

------------

CPUfreq 调节器“ondemand”根据当前使用情况设置 CPU。为此，CPU 必须具有非常快速地切换频率的能力。有许多 sysfs 文件可访问的参数：

sampling_rate：以微秒（10^-6 秒）为单位测量，这是您希望内核查看 CPU 使用情况并就频率做出决策的频率。通常，此值设置为“10000”或更高。其默认值为（与 users-guide.txt 比较）：transition_latency * 1000

请注意，transition latency 以纳秒为单位，sampling_rate 以微秒为单位，因此默认情况下您将获得相同的 sysfs 值。应始终根据 transition latency 调整采样率

要将采样率设置为 transition latency 的 750 倍（如前所述，默认值为 1000），请在 bash 中执行以下操作：

echo `$(($(cat cpuinfo_transition_latency) * 750 / 1000)) \

    >ondemand/sampling_rate

sampling_rate_min：

采样率受硬件转换延迟限制：transition_latency * 100

或内核限制：

如果设置了 CONFIG_NO_HZ_COMMON，则限制为 10ms 固定。如果未设置 CONFIG_NO_HZ_COMMON 或使用 nohz=off 引导参数，则限制取决于 CONFIG_HZ 选项：

HZ=1000：min=20000us（20ms）

HZ=250：min=80000us（80ms）

HZ=100：min=200000us（200ms）

显示并使用内核和硬件延迟限制的最高值作为最小采样率。

up_threshold：定义在“sampling_rate”的采样之间，CPU 平均使用率需要达到多少才能使内核决定是否应提高频率。例如，当将其设置为默认值“95”时，这意味着在检查间隔之间，CPU 需要平均使用超过 95％才能决定需要提高 CPU 频率。

ignore_nice_load：此参数的值为“0”或“1”。设置为“0”（其默认值）时，所有进程都计入“cpu 利用率”值。设置为“1”时，以“nice”值运行的进程将不参与决定是否提高 CPU 频率的过程。如果您在笔记本电脑上运行 CPU 密集型计算，并且不在乎它需要多长时间才能完成，因为您可以“nice”它并防止它参与决定是否提高 CPU 频率，那么这很有用。

sampling_down_factor：此参数控制内核在以最高速度运行时决定何时降低频率的速率。设置为 1（默认值）时，无论当前时钟速度如何，都会在相同的时间间隔内重新评估负载。但是，当设置为大于 1（例如 100）时，它会在 CPU 因高负载而处于最高速度时，将重新评估负载的调度间隔用作乘数。通过减少负载评估的开销并帮助 CPU 在真正繁忙时保持最高速度，而不是在速度之间来回切换，从而提高性能。此可调参数对较低速度/较低 CPU 负载下的行为没有影响。

powersave_bias：此参数的值介于 0 到 1000 之间。它定义了将从目标中减去的目标频率的百分比（乘以 10）值。例如，当设置为 100 - 10％时，当 ondemand 调节器的目标为 1000 MHz 时，它将目标设置为 1000 MHz - （1000 MHz 的 10％）= 900 MHz 。默认情况下，此值设置为 0（禁用）。当加载 AMD 频率敏感度省电偏差驱动程序 - drivers/cpufreq/amd_freq_sensitivity.c 时，此参数定义了工作负载频率敏感度阈值，在该阈值下将选择较低的频率，而不是 ondemand 调节器的原始目标。频率敏感度是硬件报告的值（在 AMD Family 16h 处理器及以上），介于 0 到 100％之间，告诉软件当频率变化时 CPU 上运行的工作负载的性能将如何变化。敏感度为 0％（内存/IO 绑定）的工作负载在更高的核心频率下不会表现得更好，而敏感度为 100％（CPU 绑定）的工作负载在频率越高时表现越好。加载驱动程序时，此值默认为 400 - 对于敏感度值低于 40％的运行工作负载的 CPU，将选择较低的频率。卸载驱动程序或写入 0 将禁用此功能。

2.5 保守

----------------

CPUfreq 调节器“conservative”，与“ondemand”调节器非常相似，根据当前使用情况设置 CPU。它的行为差异在于，当 CPU 有任何负载时，它会优雅地提高和降低 CPU 速度，而不是立即跳转到最大速度。这种行为在电池供电环境中更合适。该调节器通过 sysfs 以与“ondemand”调节器相同的方式进行调整，并添加了：

freq_step：此描述了 CPU 频率应平稳增加和减少的百分比步骤。默认情况下，CPU 频率将以最大 CPU 频率的 5％增量增加。您可以将此值更改为 0 到 100 之间的任何值，其中“0”将有效地将 CPU 锁定在某个速度，而不管其负载如何，而“100”在理论上将使其行为与“ondemand”调节器相同。

down_threshold：与“ondemand”调节器的“up_threshold”相同，但方向相反。例如，当将其设置为默认值“20”时，这意味着如果 CPU 使用率在样本之间需要低于 20％，则频率将降低。

sampling_down_factor：与“ondemand”调节器中的功能相似。但是在“conservative”中，它控制内核在以任何速度运行时决定何时降低频率的速率。仍会在每个采样率时评估频率增加的负载。

2.6 交互式

---------------

CPUfreq 调节器“interactive”专为对延迟敏感的交互式工作负载而设计。该调节器根据使用情况设置 CPU 速度，类似于“ondemand”和“conservative”调节器，但具有不同的可配置行为。

该调节器的可调值为：

target_loads：用于调整速度的 CPU 负载值，以将当前 CPU 负载调整为接近该值。通常，目标负载越低，调节器将越频繁地提高 CPU 速度，以使负载低于目标。格式为单个目标负载，可选地后跟成对的 CPU 速度和要达到或高于这些速度的 CPU 负载。速度和相关目标负载之间可以使用冒号以提高可读性。例如：

   85 1000000:90 1700000:99

目标 CPU 负载为 85％以下速度为 1GHz，90％在或高于 1GHz，直到 1.7GHz 及以上，在该速度下目标负载为 99％。如果指定了速度，则这些速度必须按升序出现。较高的目标负载值通常指定为较高的速度，即目标负载值也通常按升序出现。默认情况下，所有速度的目标负载为 90％。

min_sample_time：在降低速度之前，在当前频率下花费的最小时间。默认值为 80000 uS。

hispeed_freq：在 CPU 负载达到 go_hispeed_load 中指定的值时，初始提升到的中间“高速”。如果负载在 above_hispeed_delay 中指定的时间内保持较高，则速度可能会进一步提高。默认值是在调节器初始化时策略允许的最大速度。

go_hispeed_load：提升到 hispeed_freq 的 CPU 负载。默认值为 99％。

above_hispeed_delay：当速度在或高于 hispeed_freq 时，等待此长时间后再根据持续的高负载提高速度。格式为单个延迟值，可选地后跟成对的 CPU 速度和在或高于这些速度时要使用的延迟。速度和相关延迟之间可以使用冒号以提高可读性。例如：

   80000 1300000:200000 1500000:40000

使用延迟 80000 uS 直到 CPU 速度为 1.3 GHz，在此速度下延迟 200000 uS 直到速度为 1.5 GHz，在此速度（及以上）下延迟 40000 uS。如果指定了速度，则这些速度必须按升序出现。默认值为 20000 uS。

timer_rate：当 CPU 不空闲时重新评估 CPU 负载的采样率。使用可延迟定时器，以便在有其他事情需要运行之前，CPU 不会从空闲状态唤醒以服务此定时器。（当未以最低速度运行时，允许延迟此定时器的最大时间可通过 timer_slack 进行配置。）默认值为 20000 uS。

timer_slack：在以高于最低速度运行时，将处理调节器采样定时器的延迟时间延长到 timer_rate 之外的最大时间。对于在 CPU 以高于最低速度运行时在空闲时消耗额外功率的平台，这限制了在重新评估负载并降低速度之前定时器将被延迟的时间。例如，如果 timer_rate 为 20000uS 且 timer_slack 为 10000uS，则在不是最低速度时，定时器将被延迟最多 30msec。值为 -1 表示在所有速度下无限期地延迟定时器。默认值为 80000 uS。

boost：如果非零，立即将所有 CPU 的速度提升到至少 hispeed_freq，直到将零写入此属性。如果为零，则允许 CPU 速度根据负载像往常一样降至 hispeed_freq 以下。默认值为零。

boostpulse：每次写入时，立即将所有 CPU 的速度提升到 hispeed_freq，至少持续 boostpulse_duration 所指定的时间，之后速度将像往常一样根据负载下降。

boostpulse_duration：在写入 boostpulse 时将 CPU 速度保持在 hispeed_freq 的时间长度，然后允许速度根据负载像往常一样下降。默认值为 80000 uS。

3. CPUfreq 核心中的调节器接口

=============================================

新的调节器必须使用“cpufreq_register_governor”向 CPUfreq 核心注册自己。必须传递给该函数的 struct cpufreq_governor 必须包含以下值：

governor->name - 此调节器的唯一名称

governor->governor - 调节器回调函数

governor->owner -.THIS_MODULE 用于调节器模块（如果合适）

调节器->governor 回调函数将使用当前（或要设置的）CPU 的 cpufreq_policy 结构和一个无符号整数事件进行调用。当前定义了以下事件：

CPUFREQ_GOV_START：此调节器应开始为 CPU policy->cpu 履行职责

CPUFREQ_GOV_STOP：此调节器应结束为 CPU policy->cpu 履行的职责

CPUFREQ_GOV_LIMITS：CPU policy->cpu 的限制已更改为 policy->min 和 policy->max。

如果您的驱动程序需要其他“事件”，仅使用 cpufreq_governor_l(unsigned int cpu, unsigned int event) 调用来确保正确的锁定。

CPUfreq 调节器可以使用以下两个函数之一调用 CPU 处理器驱动程序：

int cpufreq_driver_target(struct cpufreq_policy *policy,

                                 unsigned int target_freq,

                                 unsigned int relation);

int __cpufreq_driver_target(struct cpufreq_policy *policy,

                                   unsigned int target_freq,

                                   unsigned int relation);

当然，target_freq 必须在 policy->min 和 policy->max 之内。这两个函数有什么区别？当您的调节器仍在调用调节器->governor 的直接代码路径中时，cpufreq 核心中仍持有 per-CPU cpufreq 锁，并且无需再次锁定它（实际上，这将导致死锁）。

</markdown_document><markdown_document>

仅在以下情况下使用`__cpufreq_driver_target`。在所有其他情况下（例如，当存在每秒钟唤醒一次的“守护进程化”函数时），在将命令传递给`cpufreq`处理器驱动程序之前，使用`cpufreq_driver_target`来锁定每个 CPU 的`cpufreq`锁。

```
以上内容已按照要求转换为 markdown 格式并翻译为中文。
```