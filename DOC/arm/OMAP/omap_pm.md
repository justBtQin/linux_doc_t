# OMAP PM 接口

=====================

本文档描述了临时的 OMAP PM 接口。驱动程序作者使用这些函数将最小延迟或吞吐量约束传达给内核电源管理代码。随着时间的推移，目的是将 OMAP PM 接口的功能合并到 Linux PM QoS 代码中。

驱动程序需要表达 PM 参数，这些参数：

- 支持 TI SRF 中存在的电源管理参数范围；

- 将驱动程序与底层 PM 参数实现分离，无论是 TI SRF 还是 Linux PM QoS 或 Linux 延迟框架或其他；

- 以基本单位（例如延迟和吞吐量）而不是特定于 OMAP 或特定 OMAP 变体的单位来指定 PM 参数；

- 允许与其他架构（例如 DaVinci）共享的驱动程序以不会影响非 OMAP 系统的方式添加这些约束；

- 可以立即实现，对其他架构的干扰最小。

本文档提出了 OMAP PM 接口，包括以下五个用于驱动程序代码的电源管理函数：

1. 设置最大 MPU 唤醒延迟：

   (*pdata->set_max_mpu_wakeup_lat)(struct device *dev, unsigned long t)

2. 设置最大设备唤醒延迟：

   (*pdata->set_max_dev_wakeup_lat)(struct device *dev, unsigned long t)

3. 设置最大系统 DMA 传输开始延迟（CORE pwrdm）：

   (*pdata->set_max_sdma_lat)(struct device *dev, long t)

4. 设置设备所需的最小总线吞吐量：

   (*pdata->set_min_bus_tput)(struct device *dev, u8 agent_id, unsigned long r)

5. 返回设备丢失上下文的次数

   (*pdata->get_dev_context_loss_count)(struct device *dev)

所有 OMAP PM 接口函数的进一步文档可以在 arch/arm/plat-omap/include/mach/omap-pm.h 中找到。

# OMAP PM 层旨在是临时的

---------------------------------------------

目的是最终 Linux PM QoS 层应该支持 OMAP3 中存在的电源管理功能范围。当这发生时，使用 OMAP PM 接口的现有驱动程序可以修改为使用 Linux PM QoS 代码；并且 OMAP PM 接口可以消失。

# 驱动程序对 OMAP PM 函数的使用

-------------------------------------

如上述示例中的“pdata”所示，这些函数通过驱动程序.platform_data 结构中的函数指针暴露给驱动程序。函数指针由 board-*.c 文件初始化，以指向相应的 OMAP PM 函数：.set_max_dev_wakeup_lat 将指向 omap_pm_set_max_dev_wakeup_lat()等。不支持这些函数的其他架构应该将这些函数指针设置为 NULL。驱动程序应该使用以下习惯用法：

        if (pdata->set_max_dev_wakeup_lat)

            (*pdata->set_max_dev_wakeup_lat)(dev, t);

这些函数最常见的用法可能是指定从中断发生到设备可访问的最大时间。为了实现这一点，驱动程序编写者应该使用 set_max_mpu_wakeup_lat()函数来限制 MPU 唤醒延迟，并使用 set_max_dev_wakeup_lat()函数来限制设备唤醒延迟（从 clk_enable()到可访问性）。例如，

        /* 限制 MPU 唤醒延迟 */

        if (pdata->set_max_mpu_wakeup_lat)

            (*pdata->set_max_mpu_wakeup_lat)(dev, tc);

        /* 限制设备电源域唤醒延迟 */

        if (pdata->set_max_dev_wakeup_lat)

            (*pdata->set_max_dev_wakeup_lat)(dev, td);

        /* 此示例中的总唤醒延迟：(tc + td) */

可以通过再次调用该函数并使用新值来覆盖 PM 参数。可以通过使用 t 参数为 -1 调用该函数来删除设置（除了 set_max_bus_tput()的情况，应该使用 r 参数为 0 调用）。上面的第五个函数 omap_pm_get_dev_context_loss_count()旨在作为一种优化，允许驱动程序确定设备是否丢失了其内部上下文。如果上下文已丢失，驱动程序必须在继续之前恢复其内部上下文。

# 其他专用接口函数

-------------------------------------

上面列出的五个函数旨在可供任何设备驱动程序使用。DSPBridge 和 CPUFreq 有一些特殊要求。DSPBridge 以 OPP ID 的形式表示目标 DSP 性能级别。CPUFreq 以 MPU 频率的形式表示目标 MPU 性能级别。OMAP PM 接口包含用于这些特殊情况的函数，以将输入信息（OPPs/MPU 频率）转换为底层电源管理实现所需的形式：

6. (*pdata->dsp_get_opp_table)(void)

7. (*pdata->dsp_set_min_opp)(u8 opp_id)

8. (*pdata->dsp_get_opp)(void)

9. (*pdata->cpu_get_freq_table)(void)

10. (*pdata->cpu_set_freq)(unsigned long f)

11. (*pdata->cpu_get_freq)(void)

# 为平台定制 OPP

============================

定义 CONFIG_PM 应该为该芯片启用 OPP 层，并且 OPP 表的注册应该自动进行。然而，在特殊情况下，可能需要调整默认的 OPP 表，例如：

 * 启用默认情况下被禁用但可以在平台上启用的默认 OPP

 * 在平台上禁用不受支持的 OPP

 * 定义并添加自定义的 opp 表条目

在这些情况下，板文件需要执行以下额外步骤：

arch/arm/mach-omapx/board-xyz.c

	#include "pm.h"

	.... 	static void __init omap_xyz_init_irq(void)

	{

		.... 		/* 初始化默认表 */

		omapx_opp_init();

		/* 对默认值进行定制 */

		.... 	}

注意：omapx_opp_init 将是 omap3_opp_init 或根据 omap 系列的要求而定。