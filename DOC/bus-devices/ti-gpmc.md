<markdown_document>

# GPMC (通用内存控制器)：

=========================================

GPMC 是一个统一的内存控制器，专门用于连接外部内存设备，例如：

- 异步静态随机存储器（如内存和专用集成电路设备）。

- 异步、同步和页模式突发 NOR 闪存设备以及 NAND 闪存。

- 伪静态随机存储器设备。

GPMC 可在德州仪器的 SoC（基于 OMAP）上找到。

IP 细节：http://www.ti.com/lit/pdf/spruh73 第 7.1 节。

# GPMC 通用时序计算：

================================

GPMC 有一些时序需要进行编程，以确保外设的正常运行，而外设本身又有另一组时序。为了使外设与 GPMC 配合工作，外设的时序需要转换为 GPMC 能够理解的形式。转换的方式取决于所连接的外设。此外，某些 GPMC 时序还依赖于 GPMC 时钟频率。因此，开发了一个通用时序例程来满足上述要求。通用例程提供了一种从 GPMC 外设时序计算 GPMC 时序的通用方法。需要使用外设连接到 GPMC 的数据手册中的时序来更新 struct gpmc_device_timings 字段。一些外设时序可以以时间或周期的形式提供，已经提供了处理这种情况的规定（请参考 struct gpmc_device_timings 定义）。可能会出现外设数据手册中指定的时序在时序结构中不存在的情况，在这种情况下，尝试将外设时序与可用的时序相关联。如果不起作用，则根据外设的要求添加一个新字段，告知通用时序例程如何处理它，并确保它不会破坏任何现有的内容。然后，可能会出现外设数据手册未提及 struct gpmc_device_timings 的某些字段的情况，将这些条目清零。

通用时序例程已在多个 onenand 和 tusb6010 外设上进行了验证，能够正常工作。

需要注意的是：通用时序例程是基于对 GPMC 时序、外设时序、可用的自定义时序例程的理解而开发的，是一种逆向工程，没有大多数数据手册和硬件（确切地说，主线中支持的没有自定义时序例程的那些），而是通过仿真实现的。

GPMC 时序对外设时序的依赖：

[<gpmc_timing>: <peripheral timing1>, <peripheral timing2>...]

1. 公共

cs_on: t_ceasu

adv_on: t_avdasu, t_ceavd

2. 同步公共

sync_clk: clk

page_burst_access: t_bacc

clk_activation: t_ces, t_avds

3. 读异步复用

adv_rd_off: t_avdp_r

oe_on: t_oeasu, t_aavdh

access: t_iaa, t_oe, t_ce, t_aa

rd_cycle: t_rd_cycle, t_cez_r, t_oez

4. 读异步非复用

adv_rd_off: t_avdp_r

oe_on: t_oeasu

access: t_iaa, t_oe, t_ce, t_aa

rd_cycle: t_rd_cycle, t_cez_r, t_oez

5. 读同步复用

adv_rd_off: t_avdp_r, t_avdh

oe_on: t_oeasu, t_ach, cyc_aavdh_oe

access: t_iaa, cyc_iaa, cyc_oe

rd_cycle: t_cez_r, t_oez, t_ce_rdyz

6. 读同步非复用

adv_rd_off: t_avdp_r

oe_on: t_oeasu

access: t_iaa, cyc_iaa, cyc_oe

rd_cycle: t_cez_r, t_oez, t_ce_rdyz

7. 写异步复用

adv_wr_off: t_avdp_w

we_on, wr_data_mux_bus: t_weasu, t_aavdh, cyc_aavhd_we

we_off: t_wpl

cs_wr_off: t_wph

wr_cycle: t_cez_w, t_wr_cycle

8. 写异步非复用

adv_wr_off: t_avdp_w

we_on, wr_data_mux_bus: t_weasu

we_off: t_wpl

cs_wr_off: t_wph

wr_cycle: t_cez_w, t_wr_cycle

9. 写同步复用

adv_wr_off: t_avdp_w, t_avdh

we_on, wr_data_mux_bus: t_weasu, t_rdyo, t_aavdh, cyc_aavhd_we

we_off: t_wpl, cyc_wpl

cs_wr_off: t_wph

wr_cycle: t_cez_w, t_ce_rdyz

10. 写同步非复用

adv_wr_off: t_avdp_w

we_on, wr_data_mux_bus: t_weasu, t_rdyo

we_off: t_wpl, cyc_wpl

cs_wr_off: t_wph

wr_cycle: t_cez_w, t_ce_rdyz

注意：许多 GPMC 时序都依赖于其他 GPMC 时序（一些 GPMC 时序完全依赖于其他 GPMC 时序，这就是为什么上述某些 GPMC 时序缺失的原因），这将导致外设时序间接依赖于上述未提及的 GPMC 时序，有关更多详细信息，请参考时序例程。要了解这些外设时序对应什么，请参阅 struct gpmc_device_timings 定义中的解释。对于 GPMC 时序，请参考 IP 细节（上述链接）。

</markdown_document>