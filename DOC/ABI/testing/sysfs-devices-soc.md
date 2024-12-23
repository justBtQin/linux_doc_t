**What**：`/sys/devices/socX`

**Date**：2012 年 1 月

**contact**：Lee Jones <lee.jones@linaro.org>

**Description**：

`/sys/devices/` 目录为运行平台上的每个片上系统（SoC）设备都包含一个子目录。通过读取 sysfs 文件可以获取每个 SoC 的相关信息。只有平台实现了此功能时才可用。为每个 SoC 创建的目录还将包含通常位于 `/sys/devices/platform` 中的设备的信息。已达成共识，如果存在 SoC 设备，其支持的设备更适合作为该 SoC 的子项出现。

**What**：`/sys/devices/socX/machine`

**Date**：2012 年 1 月

**contact**：Lee Jones <lee.jones@linaro.org>

**Description**：

所有 SoC 共有的只读属性。包含 SoC 机器名称（例如 Ux500）。

**What**：`/sys/devices/socX/family`

**Date**：2012 年 1 月

**contact**：Lee Jones <lee.jones@linaro.org>

**Description**：

所有 SoC 共有的只读属性。包含 SoC 系列名称（例如 DB8500）。

**What**：`/sys/devices/socX/soc_id`

**Date**：2012 年 1 月

**contact**：Lee Jones <lee.jones@linaro.org>

**Description**：

大多数 SoC 支持的只读属性。对于意法爱立信的芯片，此属性包含 SoC 序列号。

**What**：`/sys/devices/socX/revision`

**Date**：2012 年 1 月

**contact**：Lee Jones <lee.jones@linaro.org>

**Description**：

大多数 SoC 支持的只读属性。包含 SoC 的制造修订号。

**What**：`/sys/devices/socX/process`

**Date**：2012 年 1 月

**contact**：Lee Jones <lee.jones@linaro.org>

**Description**：

意法爱立信的硅片支持的只读属性。包含硅芯片的制造工艺。

**What**：`/sys/bus/soc`

**Date**：2012 年 1 月

**contact**：Lee Jones <lee.jones@linaro.org>

**Description**：

`/sys/bus/soc/` 目录包含大多数总线通常期望的子文件夹。`/sys/bus/soc/devices` 特别值得关注，因为它为系统上找到的每个 SoC 设备都包含一个符号链接。每个符号链接都指向上述 `/sys/devices/socX` 设备。