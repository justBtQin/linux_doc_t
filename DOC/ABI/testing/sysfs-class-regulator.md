**/sys/class/regulator/.../state**

- **What**：/sys/class/regulator/.../state

- **Date**：April 2008

- **KernelVersion**：2.6.26

- **Contact**：Liam Girdwood <lrg@slimlogic.co.uk>

- **Description**：一些调节器目录将包含一个名为“state”的字段。此字段报告调节器启用控制，对于可以报告输入值的调节器。它将是以下字符串之一：

    - “enabled”：调节器输出打开并向系统供电（假设没有错误阻止它）。

    - “disabled”：调节器输出关闭且不向系统供电（除非某些非 Linux 控制已启用它）。

    - “unknown”：软件无法确定状态，或报告的状态无效。

    注意：此字段可与微伏或微安结合使用，以确定配置的调节器输出电平。

**/sys/class/regulator/.../status**

- **What**：/sys/class/regulator/.../status

- **Description**：一些调节器目录将包含一个名为“status”的字段。此字段报告当前调节器状态，对于可以报告输出值的调节器。它将是以下字符串之一：

    - “off”：调节器未向系统供电。

    - “on”：调节器向系统供电，并且调节器无法报告详细的操作模式。

    - “error”：表示调节异常状态，例如由于热关机而被禁用，或由于输入电源问题而电压不稳定。

    - “fast”、“normal”、“idle”和“standby”都是详细的调节器操作模式（在其他地方描述）。它们暗示“on”，但提供更多细节。

    注意：调节器状态是许多输入的函数，不限于来自 Linux 的控制输入。例如，实际负载可能触发“error”状态；或者调节器可能由其他用户启用，即使 Linux 未启用它。

**/sys/class/regulator/.../type**

- **What**：/sys/class/regulator/.../type

- **Date**：April 2008

- **KernelVersion**：2.6.26

- **Contact**：Liam Girdwood <lrg@slimlogic.co.uk>

- **Description**：每个调节器目录将包含一个名为“type”的字段。此字段保存调节器类型。它将是以下字符串之一：

    - “voltage”：调节器输出电压可由软件控制。

    - “current”：调节器输出电流限制可由软件控制。

    - “unknown”：软件无法控制电压或电流限制。

**/sys/class/regulator/.../microvolts**

- **What**：/sys/class/regulator/.../microvolts

- **Date**：April 2008

- **KernelVersion**：2.6.26

- **Contact**：Liam Girdwood <lrg@slimlogic.co.uk>

- **Description**：一些调节器目录将包含一个名为“microvolts”的字段。此字段保存调节器输出电压设置，以微伏（即 E-6 伏特）为单位，对于可以报告电压控制输入的调节器。

    注意：此值不应用于确定调节器输出电压电平，因为此值在调节器启用或禁用时相同。

**/sys/class/regulator/.../microamps**

- **What**：/sys/class/regulator/.../microamps

- **Date**：April 2008

- **KernelVersion**：2.6.26

- **Contact**：Liam Girdwood <lrg@slimlogic.co.uk>

- **Description**：一些调节器目录将包含一个名为“microamps”的字段。此字段保存调节器输出电流限制设置，以微安（即 E-6 安培）为单位，对于可以报告电流限制控制输入的调节器。

    注意：此值不应用于确定调节器输出电流电平，因为此值在调节器启用或禁用时相同。

**/sys/class/regulator/.../opmode**

- **What**：/sys/class/regulator/.../opmode

- **Date**：April 2008

- **KernelVersion**：2.6.26

- **Contact**：Liam Girdwood <lrg@slimlogic.co.uk>

- **Description**：一些调节器目录将包含一个名为“opmode”的字段。此字段保存当前调节器操作模式，对于可以报告控制输入值的调节器。操作模式值可以是以下字符串之一：

    - “fast”

    - “normal”

    - “idle”

    - “standby”

    - “unknown”

    这些模式在 include/linux/regulator/consumer.h 中描述。

    注意：此值不应用于确定调节器输出操作模式，因为此值在调节器启用或禁用时相同。“status”属性可用于确定实际模式。

**/sys/class/regulator/.../min_microvolts**

- **What**：/sys/class/regulator/.../min_microvolts

- **Date**：April 2008

- **KernelVersion**：2.6.26

- **Contact**：Liam Girdwood <lrg@slimlogic.co.uk>

- **Description**：一些调节器目录将包含一个名为“min_microvolts”的字段。此字段保存此域的最小安全工作调节器输出电压设置，以微伏为单位，对于支持电压约束的调节器。

    注意：如果平台代码未定义电源域的最小微伏约束，则此将返回字符串“constraint not defined”。

**/sys/class/regulator/.../max_microvolts**

- **What**：/sys/class/regulator/.../max_microvolts

- **Date**：April 2008

- **KernelVersion**：2.6.26

- **Contact**：Liam Girdwood <lrg@slimlogic.co.uk>

- **Description**：一些调节器目录将包含一个名为“max_microvolts”的字段。此字段保存此域的最大安全工作调节器输出电压设置，以微伏为单位，对于支持电压约束的调节器。

    注意：如果平台代码未定义电源域的最大微伏约束，则此将返回字符串“constraint not defined”。

**/sys/class/regulator/.../min_microamps**

- **What**：/sys/class/regulator/.../min_microamps

- **Date**：April 2008

- **KernelVersion**：2.6.26

- **Contact**：Liam Girdwood <lrg@slimlogic.co.uk>

- **Description**：一些调节器目录将包含一个名为“min_microamps”的字段。此字段保存此域的最小安全工作调节器输出电流限制设置，以微安为单位，对于支持电流约束的调节器。

    注意：如果平台代码未定义电源域的最小微安约束，则此将返回字符串“constraint not defined”。

**/sys/class/regulator/.../max_microamps**

- **What**：/sys/class/regulator/.../max_microamps

- **Date**：April 2008

- **KernelVersion**：2.6.26

- **Contact**：Liam Girdwood <lrg@slimlogic.co.uk>

- **Description**：一些调节器目录将包含一个名为“max_microamps”的字段。此字段保存此域的最大安全工作调节器输出电流限制设置，以微安为单位，对于支持电流约束的调节器。

    注意：如果平台代码未定义电源域的最大微安约束，则此将返回字符串“constraint not defined”。

**/sys/class/regulator/.../name**

- **What**：/sys/class/regulator/.../name

- **Date**：October 2008

- **KernelVersion**：2.6.28

- **Contact**：Liam Girdwood <lrg@slimlogic.co.uk>

- **Description**：每个调节器目录将包含一个名为“name”的字段。此字段保存一个用于显示目的的标识符字符串。

    注意：如果平台或调节器驱动程序未提供合适的名称，则此将为空。

**/sys/class/regulator/.../num_users**

- **What**：/sys/class/regulator/.../num_users

- **Date**：April 2008

- **KernelVersion**：2.6.26

- **Contact**：Liam Girdwood <lrg@slimlogic.co.uk>

- **Description**：每个调节器目录将包含一个名为“num_users”的字段。此字段保存对此调节器调用 regulator_enable()的消费设备的数量。

**/sys/class/regulator/.../requested_microamps**

- **What**：/sys/class/regulator/.../requested_microamps

- **Date**：April 2008

- **KernelVersion**：2.6.26

- **Contact**：Liam Girdwood <lrg@slimlogic.co.uk>

- **Description**：一些调节器目录将包含一个名为“requested_microamps”的字段。此字段保存此调节器的所有消费设备请求的总负载电流，以微安为单位。

**/sys/class/regulator/.../parent**

- **What**：/sys/class/regulator/.../parent

- **Date**：April 2008

- **KernelVersion**：2.6.26

- **Contact**：Liam Girdwood <lrg@slimlogic.co.uk>

- **Description**：一些调节器目录将包含一个名为“parent”的链接。如果存在父调节器或供应调节器，则此链接指向它。**What: /sys/class/regulator/.../suspend_mem_microvolts**

**Date: May 2008**

**KernelVersion: 2.6.26**

**Contact: Liam Girdwood <lrg@slimlogic.co.uk>**

**Description:**

一些调节器目录将包含一个名为`suspend_mem_microvolts`的字段。当系统挂起到内存时，对于实现挂起电压配置约束的电压调节器，此字段保存该域的调节器输出电压设置（以微伏为单位）。

**What: /sys/class/regulator/.../suspend_disk_microvolts**

**Date: May 2008**

**KernelVersion: 2.6.26**

**Contact: Liam Girdwood <lrg@slimlogic.co.uk>**

**Description:**

一些调节器目录将包含一个名为`suspend_disk_microvolts`的字段。当系统挂起到磁盘时，对于实现挂起电压配置约束的电压调节器，此字段保存该域的调节器输出电压设置（以微伏为单位）。

**What: /sys/class/regulator/.../suspend_standby_microvolts**

**Date: May 2008**

**KernelVersion: 2.6.26**

**Contact: Liam Girdwood <lrg@slimlogic.co.uk>**

**Description:**

一些调节器目录将包含一个名为`suspend_standby_microvolts`的字段。当系统挂起到待机状态时，对于实现挂起电压配置约束的电压调节器，此字段保存该域的调节器输出电压设置（以微伏为单位）。

**What: /sys/class/regulator/.../suspend_mem_mode**

**Date: May 2008**

**KernelVersion: 2.6.26**

**Contact: Liam Girdwood <lrg@slimlogic.co.uk>**

**Description:**

一些调节器目录将包含一个名为`suspend_mem_mode`的字段。当系统挂起到内存时，对于实现挂起模式配置约束的调节器，此字段保存该域的调节器操作模式设置。

**What: /sys/class/regulator/.../suspend_disk_mode**

**Date: May 2008**

**KernelVersion: 2.6.26**

**Contact: Liam Girdwood <lrg@slimlogic.co.uk>**

**Description:**

一些调节器目录将包含一个名为`suspend_disk_mode`的字段。当系统挂起到磁盘时，对于实现挂起模式配置约束的调节器，此字段保存该域的调节器操作模式设置。

**What: /sys/class/regulator/.../suspend_standby_mode**

**Date: May 2008**

**KernelVersion: 2.6.26**

**Contact: Liam Girdwood <lrg@slimlogic.co.uk>**

**Description:**

一些调节器目录将包含一个名为`suspend_standby_mode`的字段。当系统挂起到待机状态时，对于实现挂起模式配置约束的调节器，此字段保存该域的调节器操作模式设置。

**What: /sys/class/regulator/.../suspend_mem_state**

**Date: May 2008**

**KernelVersion: 2.6.26**

**Contact: Liam Girdwood <lrg@slimlogic.co.uk>**

**Description:**

一些调节器目录将包含一个名为`suspend_mem_state`的字段。当挂起到内存时，对于实现挂起配置约束的调节器，此字段保存调节器的操作状态。

这将是“state”属性报告的相同字符串之一。

**What: /sys/class/regulator/.../suspend_disk_state**

**Date: May 2008**

**KernelVersion: 2.6.26**

**Contact: Liam Girdwood <lrg@slimlogic.co.uk>**

**Description:**

一些调节器目录将包含一个名为`suspend_disk_state`的字段。当挂起到磁盘时，对于实现挂起配置约束的调节器，此字段保存调节器的操作状态。

这将是“state”属性报告的相同字符串之一。

**What: /sys/class/regulator/.../suspend_standby_state**

**Date: May 2008**

**KernelVersion: 2.6.26**

**Contact: Liam Girdwood <lrg@slimlogic.co.uk>**

**Description:**

一些调节器目录将包含一个名为`suspend_standby_state`的字段。当挂起到待机状态时，对于实现挂起配置约束的调节器，此字段保存调节器的操作状态。

这将是“state”属性报告的相同字符串之一。

**What: /sys/class/regulator/.../bypass**

**Date: September 2012**

**KernelVersion: 3.7**

**Contact: Mark Brown <broonie@opensource.wolfsonmicro.com>**

**Description:**

一些调节器目录将包含一个名为`bypass`的字段。此指示设备是否处于旁路模式。

此将是以下字符串之一：

“enabled”

“disabled”

“unknown”

“enabled”表示调节器处于旁路模式。

“disabled”表示调节器正在调节。

“unknown”表示软件无法确定状态，或报告的状态无效。