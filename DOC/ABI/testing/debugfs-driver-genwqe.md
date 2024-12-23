**What**: /sys/kernel/debug/genwqe/genwqe<n>_card/ddcb_info

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: DDCB 队列转储，用于调试排队问题。

**What**: /sys/kernel/debug/genwqe/genwqe<n>_card/curr_regs

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: 当前错误寄存器转储。仅对 PF 可用。

**What**: /sys/kernel/debug/genwqe/genwqe<n>_card/curr_dbg_uid0

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: UID0（单元 ID 0）的内部芯片状态。仅对 PF 可用。

**What**: /sys/kernel/debug/genwqe/genwqe<n>_card/curr_dbg_uid1

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: UID1 的内部芯片状态。仅对 PF 可用。

**What**: /sys/kernel/debug/genwqe/genwqe<n>_card/curr_dbg_uid2

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: UID2 的内部芯片状态。仅对 PF 可用。

**What**: /sys/kernel/debug/genwqe/genwqe<n>_card/prev_regs

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: 上次卡复位前的错误寄存器转储。仅对 PF 可用。

**What**: /sys/kernel/debug/genwqe/genwqe<n>_card/prev_dbg_uid0

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: 卡复位前 UID0 的内部芯片状态。仅对 PF 可用。

**What**: /sys/kernel/debug/genwqe/genwqe<n>_card/prev_dbg_uid1

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: 卡复位前 UID1 的内部芯片状态。仅对 PF 可用。

**What**: /sys/kernel/debug/genwqe/genwqe<n>_card/prev_dbg_uid2

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: 卡复位前 UID2 的内部芯片状态。仅对 PF 可用。

**What**: /sys/kernel/debug/genwqe/genwqe<n>_card/info

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibus.com

**Description**: 位流版本和软件版本的综合摘要。使用的位流和位流时钟信息。

**What**: /sys/kernel/debug/genwqe/genwqe<n>_card/err_inject

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: 注入错误情况的可能性，以确保驱动程序的错误处理代码正常工作。

**What**: /sys/kernel/debug/genwqe/genwqe<n>_card/vf<0..14>_jobtimeout_msec

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: 默认 VF 超时 250ms。测试可能需要 1000ms。使用 0 将使用卡的默认值（无论其为何值）。超时取决于系统中可用卡的最大数量和允许的最大队列大小。驱动程序确保在 VF 启用之前进行设置。飞行中更改超时是不可能的。仅对 PF 可用。

**What**: /sys/kernel/debug/genwqe/genwqe<n>_card/jobtimer

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: 转储 PF 和 VF 的作业超时寄存器值。仅对 PF 可用。

**What**: /sys/kernel/debug/genwqe/genwqe<n>_card/queue_working_time

**Date**: Dec 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: 转储 PF 和 VF 的队列工作时间寄存器值。仅对 PF 可用。