<markdown_document>

# CPU frequency and voltage scaling code in the Linux(TM) kernel

## Linux    CPUFreq

## CPUFreq    Core

Dominik Brodowski <linux@brodo.de>

David Kimdon <dwhedon@debian.org>

时钟缩放允许你动态更改 CPU 的时钟速度。这是一种节省电池电量的好方法，因为时钟速度越低，CPU 消耗的电量就越少。

内容：

- 1. CPUFreq core and interfaces

- 2. CPUFreq notifiers

- 3. CPUFreq Table Generation with Operating Performance Point (OPP)

1. 一般信息

=======================

CPUFreq 核心代码位于 drivers/cpufreq/cpufreq.c 中。这个 cpufreq 代码为 CPUFreq 架构驱动程序（实际执行频率转换的代码部分）以及“通知器”提供了一个标准化接口。这些是需要被告知策略更改（例如 ACPI 等热模块）或所有频率更改（例如计时代码）甚至需要强制某些速度限制（例如 ARM 架构上的 LCD 驱动程序）的设备驱动程序或内核的其他部分。此外，内核的“常量”loops_per_jiffy 会在此处随频率变化而更新。

引用计数通过 cpufreq_get_cpu 和 cpufreq_put_cpu 完成，这确保了 cpufreq 处理器驱动程序正确注册到核心，并且在调用 cpufreq_put_cpu 之前不会被卸载。

2. CPUFreq 通知器

====================

CPUFreq 通知器符合标准的内核通知器接口。有关通知器的详细信息，请参阅 linux/include/linux/notifier.h。

有两种不同的 CPUFreq 通知器 - 策略通知器和转换通知器。

2.1 CPUFreq 策略通知器

----------------------------

当打算设置新策略时会通知这些通知器。对于策略转换，每个 CPUFreq 策略通知器会被调用三次：

1. 在 CPUFREQ_ADJUST 期间，如果所有 CPUFreq 通知器认为有必要，它们可以更改限制 - 可能是热考虑或硬件限制。

2. 在 CPUFREQ_INCOMPATIBLE 期间，只能进行更改以避免硬件故障。

3. 在 CPUFREQ_NOTIFY 期间，所有通知器都会被告知新策略 - 如果在这个阶段之前两个硬件驱动程序未能就新策略达成一致，不兼容的硬件将被关闭，并通知用户。

阶段在通知器的第二个参数中指定。

第三个参数，一个 void *指针，指向一个包含五个值的 struct cpufreq_policy：cpu、min、max、policy 和 max_cpu_freq。min 和 max 是新策略的下限和上限频率（以 kHz 为单位），policy 是新策略，cpu 是受影响的 CPU 编号；max_cpu_freq 是支持的最大 CPU 频率。此值仅用于参考目的。

2.2 CPUFreq 转换通知器

--------------------------------

当 CPUfreq 驱动程序切换 CPU 核心频率并且此更改有任何外部影响时，会通知这些通知器两次。

第二个参数指定阶段 - CPUFREQ_PRECHANGE 或 CPUFREQ_POSTCHANGE。

第三个参数是一个包含以下值的 struct cpufreq_freqs：

cpu - 受影响的 CPU 编号

old - 旧频率

new - 新频率

3. CPUFreq Table Generation with Operating Performance Point (OPP)

==================================================================

有关 OPP 的详细信息，请参阅 Documentation/power/opp.txt

dev_pm_opp_init_cpufreq_table - cpufreq 框架通常使用 cpufreq_frequency_table_cpuinfo 初始化，该函数提供了可用于操作的频率列表。此函数提供了一个现成的转换例程，将 OPP 层关于可用频率的内部信息转换为可直接提供给 cpufreq 的格式。

**注意：不要在中断上下文中使用此函数。**

示例：

soc_pm_init()

{

    /* 执行一些操作 */

    r = dev_pm_opp_init_cpufreq_table(dev, &freq_table);

    if (!r)

        cpufreq_frequency_table_cpuinfo(policy, freq_table);

    /* 执行其他操作 */

}

**注意：只有在启用了 CONFIG_CPU_FREQ 并且还启用了 CONFIG_PM_OPP 的情况下，此函数才可用。**

dev_pm_opp_free_cpufreq_table - 释放由 dev_pm_opp_init_cpufreq_table 分配的表

</markdown_document>