<markdown_document>

Linux(TM)内核中的 CPU 频率和电压缩放代码

Linux CPU 频率

CPU 驱动程序

- 给开发者的信息 -

时钟缩放允许你动态改变 CPU 的时钟速度。这是一种节省电池电量的好方法，因为时钟速度越低，CPU 消耗的电量就越少。

内容：

---------

1. 做什么？

1.1 初始化

1.2 每个 CPU 的初始化

1.3 验证

1.4 target/target_index 或 setpolicy？

1.5 target/target_index

1.6 setpolicy

1.7 get_intermediate 和 target_intermediate

2. 频率表帮助器

1. 做什么？=============

所以，你刚刚获得了一个带有数据表的全新 CPU/芯片组，并希望为这个 CPU/芯片组添加 cpufreq 支持？很好。这里有一些关于必要步骤的提示：

1.1 初始化

------------------

首先，在一个__initcall 级别 7（module_init()）或更高的函数中，检查这个内核是否在正确的 CPU 和芯片组上运行。如果是，使用 cpufreq_register_driver()向 CPUfreq 核心注册一个 struct cpufreq_driver。

这个 struct cpufreq_driver 应该包含什么？

cpufreq_driver.name - 这个驱动程序的名称。

cpufreq_driver.init - 指向每个 CPU 初始化函数的指针。

cpufreq_driver.verify - 指向“验证”函数的指针。

cpufreq_driver.setpolicy 或

cpufreq_driver.target/target_index - 下面会说明它们的区别。

并且可选地：

cpufreq_driver.exit - 指向在 CPU 热插拔过程的 CPU_POST_DEAD 阶段调用的每个 CPU 清理函数的指针。

cpufreq_driver.stop_cpu - 指向在 CPU 热插拔过程的 CPU_DOWN_PREPARE 阶段调用的每个 CPU 停止函数的指针。

cpufreq_driver.resume - 指向一个在中断被禁用时调用的每个 CPU 恢复函数，并且在通过调用 ->target/target_index 或 ->setpolicy 恢复预挂起频率和/或策略之前调用。

cpufreq_driver.attr - 指向一个以 NULL 结尾的“struct freq_attr”列表的指针，这些“struct freq_attr”允许将值导出到 sysfs。

cpufreq_driver.get_intermediate 和 target_intermediate - 用于在改变 CPU 频率时切换到稳定频率。

1.2 每个 CPU 的初始化

--------------------------

每当一个新的 CPU 向设备模型注册，或者 cpufreq 驱动程序注册自己后，每个 CPU 的初始化函数 cpufreq_driver.init 会被调用。它接受一个 struct cpufreq_policy *policy 作为参数。现在该做什么？

如果需要，在你的 CPU 上激活 CPUfreq 支持。

然后，驱动程序必须填写以下值：

policy->cpuinfo.min_freq 和

policy->cpuinfo.max_freq - 这个 CPU 支持的最小和最大频率（以 kHz 为单位）

policy->cpuinfo.transition_latency - 在这个 CPU 上在两个频率之间切换所需的时间（以纳秒为单位）（如果合适，否则指定 CPUFREQ_ETERNAL）

policy->cur - 这个 CPU 的当前工作频率（如果合适）

policy->min、

policy->max、

policy->policy 以及，如果需要，

policy->governor - 必须包含这个 CPU 的“默认策略”。过一会儿，

cpufreq_driver.verify 和要么 cpufreq_driver.setpolicy 要么

cpufreq_driver.target/target_index 会用这些值被调用。

对于设置这些值中的一些（cpuinfo.min[max]_freq、policy->min[max]），频率表帮助器可能会有帮助。关于它们的更多信息请参见第 2 节。

SMP 系统通常对一组 CPU 有相同的时钟源。对于这些，.init()只会为第一个在线的 CPU 调用一次。在这里，.init()例程必须用共享时钟的所有可能的 CPU（在线+离线）的掩码初始化 policy->cpus。然后核心会将这个掩码复制到 policy->related_cpus 并将 policy->cpus 重置为只包含在线的 CPU。

1.3 验证

------------

当用户决定设置一个新的策略（由“policy、governor、min、max”组成）时，必须验证这个策略，以便可以纠正不兼容的值。为了验证这些值，频率表帮助器和/或

cpufreq_verify_within_limits(struct cpufreq_policy *policy, unsigned int min_freq, unsigned int max_freq)函数可能会有帮助。关于频率表帮助器的细节请参见第 2 节。

你需要确保至少一个有效的频率（或工作范围）在 policy->min 和 policy->max 之内。如果需要，首先增加 policy->max，如果这不是解决方案，再减小 policy->min。

1.4 target/target_index 或 setpolicy？----------------------------

大多数 cpufreq 驱动程序，甚至大多数 CPU 频率缩放算法只允许将 CPU 设置为一个频率。对于这些，你使用 ->target/target_index 调用。

一些支持 cpufreq 的处理器会自己在某些限制之间切换频率。这些应该使用 ->setpolicy 调用。

1.5. target/target_index

-------------

target_index 调用有两个参数：struct cpufreq_policy *policy 和 unsigned int index（到暴露的频率表中的索引）。

当在这里被调用时，CPUfreq 驱动程序必须设置新的频率。实际频率必须由 freq_table[index].frequency 确定。

在出现错误的情况下，它应该总是恢复到之前的频率（即 policy->restore_freq），即使我们之前切换到了中间频率。

已弃用：

----------

target 调用有三个参数：struct cpufreq_policy *policy、unsigned int target_frequency、unsigned int relation。

当在这里被调用时，CPUfreq 驱动程序必须设置新的频率。实际频率必须使用以下规则确定：

- 接近“target_freq”

- policy->min <= new_freq <= policy->max（这必须是有效的！！！）

- 如果 relation==CPUFREQ_REL_L，尝试选择一个不低于 target_freq 的新频率。（“L 表示最低，但不低于”）

- 如果 relation==CPUFREQ_REL_H，尝试选择一个不高于 target_freq 的新频率。（“H 表示最高，但不高于”）

这里频率表帮助器也可以帮助你 - 关于细节请参见第 2 节。

1.6 setpolicy

---------------

setpolicy 调用只接受一个 struct cpufreq_policy *policy 作为参数。你需要将处理器内或芯片组内动态频率切换的下限设置为 policy->min，上限设置为 policy->max，并且 - 如果支持 - 当 policy->policy 是 CPUFREQ_POLICY_PERFORMANCE 时选择一个性能导向的设置，当是 CPUFREQ_POLICY_POWERSAVE 时选择一个省电导向的设置。也可以参考 drivers/cpufreq/longrun.c 中的参考实现。

1.7 get_intermediate 和 target_intermediate

--------------------------------------------

仅对于没有设置 target_index()和 CPUFREQ_ASYNC_NOTIFICATION 的驱动程序。

get_intermediate 应该返回平台希望切换到的稳定中间频率，而 target_intermediate()应该在跳转到与“index”对应的频率之前将 CPU 设置为该频率。核心会负责发送通知，驱动程序不必在 target_intermediate()或 target_index()中处理它们。

如果驱动程序不希望为某些目标频率切换到中间频率，可以从 get_intermediate()返回'0'。在这种情况下，核心将直接调用 ->target_index()。

注意：如果出现故障，->target_index()应该恢复到 policy->restore_freq，因为核心会为该情况发送通知。

2. 频率表帮助器

==========================

由于大多数 cpufreq 处理器只允许被设置为几个特定的频率，一个带有一些函数的“频率表”可以帮助处理器驱动程序的一些工作。这样的“频率表”由一个 struct cpufreq_frequency_table 条目数组组成，带有你想要使用的任何“driver_data”值，以及对应的“frequency”中的频率。在表的末尾，你需要添加一个频率设置为 CPUFREQ_TABLE_END 的 cpufreq_frequency_table 条目。并且如果你想在表中跳过一个条目，将频率设置为 CPUFREQ_ENTRY_INVALID。条目不需要按升序排列。

通过调用 cpufreq_frequency_table_cpuinfo(struct cpufreq_policy *policy,

					struct cpufreq_frequency_table *table);

cpuinfo.min_freq 和 cpuinfo.max_freq 值被检测到，并且 policy->min 和 policy->max 被设置为相同的值。这在每个 CPU 的初始化阶段很有帮助。

int cpufreq_frequency_table_verify(struct cpufreq_policy *policy,

                                   struct cpufreq_frequency_table *table);

确保至少一个有效的频率在 policy->min 和 policy->max 之内，并且满足所有其他标准。这在 ->verify 调用中很有帮助。

int cpufreq_frequency_table_target(struct cpufreq_policy *policy,

                                   struct cpufreq_frequency_table *table,

                                   unsigned int target_freq,

                                   unsigned int relation,

                                   unsigned int *index);

是 ->target 阶段的相应频率表帮助器。只需将值传递给这个函数，unsigned int index 将返回频率表条目的编号，该条目包含 CPU 应该被设置到的频率。

以下宏可以用作 cpufreq_frequency_table 的迭代器：

cpufreq_for_each_entry(pos, table) - 迭代频率表的所有条目。

cpufreq-for_each_valid_entry(pos, table) - 迭代所有条目，排除 CPUFREQ_ENTRY_INVALID 频率。使用参数“pos” - 一个 cpufreq_frequency_table *作为循环游标，和“table” - 你想要迭代的 cpufreq_frequency_table *。

例如：

	struct cpufreq_frequency_table *pos, *driver_freq_table;

	cpufreq_for_each_entry(pos, driver_freq_table) {

		/* 对 pos 做一些事情 */

		pos->frequency =... 	}

</markdown_document>