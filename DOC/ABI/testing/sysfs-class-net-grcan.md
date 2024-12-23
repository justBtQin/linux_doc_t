**What**: `/sys/class/net/<iface>/grcan/enable0`

**Date**:  October 2012

**KernelVersion**: 3.8

**Contact**: Andreas Larsson <andreas@gaisler.com>

**Description**:

硬件配置物理接口 0。此文件读取和写入配置寄存器的“启用 0”位。可能的值：0 或 1。有关详细信息，请参阅 GRLIB IP 核心库文档的 GRCAN 章节。默认值为 0 或由模块参数 `grcan.enable0` 设置，并且可以在 `/sys/module/grcan/parameters/enable0` 处读取。

**What**: `/sys/class/net/<iface>/grcan/enable1`

**Date**:  October 2012

**KernelVersion**: 3.8

**Contact**: Andreas Larsson <andreas@gaisler.com>

**Description**:

硬件配置物理接口 1。此文件读取和写入配置寄存器的“启用 1”位。可能的值：0 或 1。有关详细信息，请参阅 GRLIB IP 核心库文档的 GRCAN 章节。默认值为 0 或由模块参数 `grcan.enable1` 设置，并且可以在 `/sys/module/grcan/parameters/enable1` 处读取。

**What**: `/sys/class/net/<iface>/grcan/select`

**Date**:  October 2012

**KernelVersion**: 3.8

**Contact**: Andreas Larsson <andreas@gaisler.com>

**Description**:

配置要使用的物理接口。可能的值：0 或 1。有关详细信息，请参阅 GRLIB IP 核心库文档的 GRCAN 章节。默认值为 0 或由模块参数 `grcan.select` 设置，并且可以在 `/sys/module/grcan/parameters/select` 处读取。