**What**: `/sys/class/genwqe/genwqe<n>_card/version`

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: 唯一的比特流标识，例如 '0000000330336283.00000000475a4950'。

**What**: `/sys/class/genwqe/genwqe<n>_card/appid`

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: 标识当前活动的卡应用程序，例如用于压缩/解压缩的 'GZIP'。

**What**: `/sys/class/genwqe/genwqe<n>_card/type`

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: 卡的类型，例如 'GenWQE5-A7'。

**What**: `/sys/class/genwqe/genwqe<n>_card/curr_bitstream`

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: 当前活动的比特流。1 是默认值，0 是备份。

**What**: `/sys/class/genwqe/genwqe<n>_card/next_bitstream`

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: 用于设置要使用的下一个比特流的接口。

**What**: `/sys/class/genwqe/genwqe<n>_card/reload_bitstream`

**Date**: May 2014

**Contact**: klebers@linux.vnet.ibm.com

**Description**: 触发 PCIe 卡重置以重新加载比特流的接口。

`sudo sh -c 'echo 1 > /sys/class/genwqe/genwqe0_card/reload_bitstream'`

如果成功，卡将以设置在 'next_bitstream' 上的比特流返回。

**What**: `/sys/class/genwqe/genwqe<n>_card/tempsens`

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: 读取卡的温度感测寄存器的接口。

**What**: `/sys/class/genwqe/genwqe<n>_card/freerunning_timer`

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: 读取卡的自由运行定时器的接口。用于性能和利用率测量。

**What**: `/sys/class/genwqe/genwqe<n>_card/queue_working_time`

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: 读取队列工作时间的接口。用于性能和利用率测量。

**What**: `/sys/class/genwqe/genwqe<n>_card/state`

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: 卡的状态："unused"（未使用）、"used"（已使用）、"error"（错误）。

**What**: `/sys/class/genwqe/genwqe<n>_card/base_clock`

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: 卡的基本时钟频率。

**What**: `/sys/class/genwqe/genwqe<n>_card/device/sriov_numvfs`

**Date**: Oct 2013

**Contact**: haver@linux.vnet.ibm.com

**Description**: 启用 VF（1..15）：

`sudo sh -c 'echo 15 > /sys/bus/pci/devices/0000:1b:00.0/sriov_numvfs'`

禁用 VF：将 0 写入相同的 sysfs 条目。