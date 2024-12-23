**What**: `/sys/bus/coresight/devices/<memory_map>.tmc/trigger_cntr`

**Date**: 2014 年 11 月

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (可读写)在触发事件后，通过停止格式化程序，在存储了一定数量的字后，禁用对跟踪 RAM 的写访问。随着该驱动程序的成熟，预计将添加此驱动程序的其他接口。