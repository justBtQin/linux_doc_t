<markdown_document>

Intel P-state 驱动程序

--------------------

此驱动程序提供了一个接口，用于控制 SandyBridge+ Intel 处理器的 P 状态选择。该驱动程序可以根据处理器模型在两种不同模式下运行：传统模式和硬件 P 状态（HWP）模式。

在传统模式下，驱动程序为 Intel Core 处理器实现了一个带有内部调节器的缩放驱动程序。驱动程序遵循与 Transmeta 缩放驱动程序（longrun.c）相同的模型，并实现了 setpolicy()而不是 target()。实现 setpolicy()的缩放驱动程序被 cpufreq 核心假定为实现了内部调节器。选择当前 P 状态的所有逻辑都包含在驱动程序中；cpufreq 核心不使用外部调节器。

在 HWP 模式下，P 状态选择在处理器本身中实现。驱动程序在 cpufreq 核心和处理器之间提供接口，以根据用户偏好控制 P 状态选择，并向 cpufreq 核心报告频率。在这种模式下，内部调节器代码被禁用。

除了 cpufreq 核心提供的用于控制频率的接口之外，驱动程序还提供了用于控制 P 状态选择的 sysfs 文件。这些文件已添加到 /sys/devices/system/cpu/intel_pstate/ 中。

      max_perf_pct：限制驱动程序请求的最大 P 状态，以可用性能的百分比表示。可用（P 状态）性能可能会因下面描述的 no_turbo 设置而降低。

      min_perf_pct：限制驱动程序请求的最小 P 状态，以最大（非涡轮）性能水平的百分比表示。

      no_turbo：限制驱动程序选择低于涡轮频率范围的 P 状态。

      turbo_pct：显示硬件在涡轮范围内支持的总性能的百分比。此数字与涡轮是否已被禁用无关。

      num_pstates：显示硬件支持的 P 状态数量。此数字与涡轮是否已被禁用无关。

对于现代 Intel 处理器，频率由处理器本身控制，暴露给软件的 P 状态与性能级别相关。对于 Intel Core 处理器，将频率设置为单个频率的想法是虚构的。即使缩放驱动程序选择了单个 P 状态，处理器实际运行的频率也由处理器本身选择。

对于传统模式，还添加了 debugfs 文件，以允许调整内部调节器算法。这些文件位于 /sys/kernel/debug/pstate_snb/ 中。这些文件在 HWP 模式下不存在。

      deadband

      d_gain_pct

      i_gain_pct

      p_gain_pct

      sample_rate_ms

      setpoint

</markdown_document>