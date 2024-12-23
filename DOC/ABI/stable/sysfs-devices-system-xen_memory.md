**What**: `/sys/devices/system/xen_memory/xen_memory0/max_retry_count`

**Date**: May 2011

**KernelVersion**: 2.6.39

**Contact**: Konrad Rzeszutek Wilk <konrad.wilk@oracle.com>

**Description**: 气球驱动程序在放弃之前尝试增加气球的最大次数。另请参阅下面的“retry_count”。值为零表示永远重试，是默认值。

**What**: `/sys/devices/system/xen_memory/xen_memory0/max_schedule_delay`

**Date**: May 2011

**KernelVersion**: 2.6.39

**Contact**: Konrad Rzeszutek Wilk <konrad.wilk@oracle.com>

**Description**: “schedule_delay”（见下文）将增加到的限制。默认值为 32 秒。

**What**: `/sys/devices/system/xen_memory/xen_memory0/retry_count`

**Date**: May 2011

**KernelVersion**: 2.6.39

**Contact**: Konrad Rzeszutek Wilk <konrad.wilk@oracle.com>

**Description**: 气球驱动程序尝试增加气球大小的当前次数。默认值为 1。当 max_retry_count 为零（无限制）时，这意味着驱动程序将尝试以“schedule_delay”延迟重试。

**What**: `/sys/devices/system/xen_memory/xen_memory0/schedule_delay`

**Date**: May 2011

**KernelVersion**: 2.6.39

**Contact**: Konrad Rzeszutek Wilk <konrad.wilk@oracle.com>

**Description**: 每次尝试增加气球之间等待的时间（以秒为单位）。每次气球无法增加时，“schedule_delay”将增加（直到达到“max_schedule_delay”，此时它将使用最大值）。

**What**: `/sys/devices/system/xen_memory/xen_memory0/target`

**Date**: April 2008

**KernelVersion**: 2.6.26

**Contact**: Konrad Rzeszutek Wilk <konrad.wilk@oracle.com>

**Description**: 要将此域的内存预留调整到的目标页数。

**What**: `/sys/devices/system/xen_memory/xen_memory0/target_kb`

**Date**: April 2008

**KernelVersion**: 2.6.26

**Contact**: Konrad Rzeszutek Wilk <konrad.wilk@oracle.com>

**Description**: 与上面的 target 相同，只是值以 KiB 为单位。

**What**: `/sys/devices/system/xen_memory/xen_memory0/info/current_kb`

**Date**: April 2008

**KernelVersion**: 2.6.26

**Contact**: Konrad Rzeszutek Wilk <konrad.wilk@oracle.com>

**Description**: 此域的内存预留的当前大小（以 KiB 为单位）。

**What**: `/sys/devices/system/xen_memory/xen_memory0/info/high_kb`

**Date**: April 2008

**KernelVersion**: 2.6.26

**Contact**: Konrad Rzeszutek Wilk <konrad.wilk@oracle.com>

**Description**: 气球中的高端内存量（以 KiB 为单位）。

**What**: `/sys/devices/system/xen_memory/xen_memory0/info/low_kb`

**Date**: April 2008

**KernelVersion**: 2.6.26

**Contact**: Konrad Rzeszutek Wilk <konrad.wilk@oracle.com>

**Description**: 气球中的低端（或正常）内存量（以 KiB 为单位）。