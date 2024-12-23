**What**: `/sys/bus/event_source/devices/hv_gpci/interface/collect_privileged`

**Date**: February 2014

**Contact**: Linux on PowerPC Developer List <linuxppc-dev@lists.ozlabs.org>

**Description**: '0' 表示管理程序配置为禁止访问其他客户机正在累积的事件计数器以及物理域事件计数器。'1' 表示允许该访问。

**What**: `/sys/bus/event_source/devices/hv_gpci/interface/ga`

**Date**: February 2014

**Contact**: Linux on PowerPC Developer List <linuxppc-dev@lists.ozlabs.org>

**Description**: 0 或 1。指示我们是否有权访问“GA”事件（在 arch/powerpc/perf/hv-gpci.h 中列出）。

**What**: `/sys/bus/event_source/devices/hv_gpci/interface/expanded`

**Date**: February 2014

**Contact**: Linux on PowerPC Developer List <linuxppc-dev@lists.ozlabs.org>

**Description**: 0 或 1。指示我们是否有权访问“EXPANDED”事件（在 arch/powerpc/perf/hv-gpci.h 中列出）。

**What**: `/sys/bus/event_source/devices/hv_gpci/interface/lab`

**Date**: February 2014

**Contact**: Linux on PowerPC Developer List <linuxppc-dev@lists.ozlabs.org>

**Description**: 0 或 1。指示我们是否有权访问“LAB”事件（在 arch/powerpc/perf/hv-gpci.h 中列出）。

**What**: `/sys/bus/event_source/devices/hv_gpci/interface/version`

**Date**: February 2014

**Contact**: Linux on PowerPC Developer List <linuxppc-dev@lists.ozlabs.org>

**Description**: 一个表示管理程序报告支持的 gpci 接口版本的数字。

**What**: `/sys/bus/event_source/devices/hv_gpci/interface/kernel_version`

**Date**: February 2014

**Contact**: Linux on PowerPC Developer List <linuxppc-dev@lists.ozlabs.org>

**Description**: 一个表示内核已知的 gpci 接口的最新版本的数字。