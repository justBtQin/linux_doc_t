**/sys/bus/css/devices/.../type**

- **What**: 表示设备的类型信息。

- **Date**: 2008 年 3 月。

- **Contact**: Cornelia Huck <cornelia.huck@de.ibm.com>，linux-s390@vger.kernel.org。

- **Description**: 包含硬件报告的子通道类型。此属性对于所有子通道类型都存在。

**/sys/bus/css/devices/.../modalias**

- **What**: 包含通过 uevents 报告的模块别名。

- **Date**: 2008 年 3 月。

- **Contact**: Cornelia Huck <cornelia.huck@de.ibm.com>，linux-s390@vger.kernel.org。

- **Description**: 以 css:t<type> 的格式包含模块别名，对于所有子通道类型都存在。

**/sys/bus/css/drivers/io_subchannel/.../chpids**

- **What**: 包含此子通道使用的通道路径的 ID。

- **Date**: 2002 年 12 月。

- **Contact**: Cornelia Huck <cornelia.huck@de.ibm.com>，linux-s390@vger.kernel.org。

- **Description**: 包含通道子系统在子通道识别期间报告的通道路径的 ID。注意：这是一个 I/O 子通道特定的属性。用户：s390-tools，HAL。

**/sys/bus/css/drivers/io_subchannel/.../pimpampom**

- **What**: 包含通道子系统上次由通用 I/O 层查询时报告的 PIM/PAM/POM 值。

- **Date**: 2002 年 12 月。

- **Contact**: Cornelia Huck <cornelia.huck@de.ibm.com>，linux-s390@vger.kernel.org。

- **Description**: 包含通道子系统上次由通用 I/O 层查询时报告的 PIM/PAM/POM 值（这意味着此属性不一定与通道子系统中的当前值同步）。注意：这是一个 I/O 子通道特定的属性。用户：s390-tools，HAL。