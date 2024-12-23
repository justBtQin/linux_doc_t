
**What**: `/sys/devices/cpu/events/`

 - `/sys/devices/cpu/events/branch-misses`

 - `/sys/devices/cpu/events/cache-references`

 - `/sys/devices/cpu/events/cache-misses`

 - `/sys/devices/cpu/events/stalled-cycles-frontend`

 - `/sys/devices/cpu/events/branch-instructions`

 - `/sys/devices/cpu/events/stalled-cycles-backend`

 - `/sys/devices/cpu/events/instructions`

 - `/sys/devices/cpu/events/cpu-cycles`

**Date**: 2013/01/08

**Contact**: Linux 内核邮件列表 <linux-kernel@vger.kernel.org>

**Description**: 通用性能监测事件

一系列可能被许多/大多数 CPU 支持的性能监测事件。可以使用 `perf(1)` 工具来监测这些事件。

每个文件的内容看起来像：

`event=0xNNNN`

其中 'N' 是十六进制数字，数字 `0xNNNN` 显示了由文件的“基本名称”标识的 `perf` 事件的“原始代码”。

**What**: `/sys/bus/event_source/devices/<pmu>/events/<event>`

**Date**: 2014/02/24

**Contact**: Linux 内核邮件列表 <linux-kernel@vger.kernel.org>

**Description**: 针对运行系统的每个 PMU 特定的性能监测事件

在 `events` 目录中的每个文件（除了其中一些带有“.”的文件，如 `.unit` 和 `.scale`）描述了由 `<pmu>` 支持的单个性能监测事件。文件的名称就是事件的名称。

文件内容：

`<term>[=<value>][,<term>[=<value>]]...`

其中 `<term>` 是 `/sys/bus/event_source/devices/<pmu>/format/` 下列出的术语之一，`<value>` 是一个十六进制格式的数字，带有 `0x` 前缀（仅小写）。

如果 `<term>` 单独指定（没有分配的值），则暗示为该 `<term>` 分配 `0x1`。

示例（这些行中的每一行都将在一个单独的文件中）：

`event=0x2abc`

`event=0x423,inv,cmask=0x3`

`domain=0x1,offset=0x8,starting_index=0xffff`

`domain=0x1,offset=0x8,core=?`

每个分配都表示要分配给传递给 `perf_open` 系统调用的 `perf_event` 结构中的特定位集（由 `<term>` 对应的格式文件定义）的值。

对于最后一个示例，用户选择特定事件时需要提供替换“?”的值。

这被称为“事件参数化”。事件参数具有“param=?”的格式。

**What**: `/sys/bus/event_source/devices/<pmu>/events/<event>.unit`

**Date**: 2014/02/24

**Contact**: Linux 内核邮件列表 <linux-kernel@vger.kernel.org>

**Description**: `Perf` 事件单位

一个字符串，指定 `<event>`（乘以 `<event>.scale` 后）表示的英文复数数字单位。

示例：

`Joules`

**What**: `/sys/bus/event_source/devices/<pmu>/events/<event>.scale`

**Date**: 2014/02/24

**Contact**: Linux 内核邮件列表 <linux-kernel@vger.kernel.org>

**Description**: `Perf` 事件缩放因子

一个字符串，表示以科学记数法表示的浮点数，与从内核接收的事件计数相乘，以匹配 `<event>.unit` 文件中指定的单位。

示例：

`2.3283064365386962890625e-10`

这是为了避免在内核中执行浮点运算。