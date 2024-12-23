**What**：`/sys/class/pps/`

**Date**：2008 年 2 月

**Contact**：Rodolfo Giometti <giometti@linux.it>

**Description**：

`/sys/class/pps/` 目录将包含文件和目录，为 PPS 源提供统一的接口。

**What**：`/sys/class/pps/ppsX/`

**Date**：2008 年 2 月

**Contact**：Rodolfo Giometti <giometti@linux.it>

**Description**：

`/sys/class/pps/ppsX/` 目录与系统中的第 X 个 PPS 源相关。每个目录将包含用于管理和控制其 PPS 源的文件。

**What**：`/sys/class/pps/ppsX/assert`

**Date**：2008 年 2 月

**Contact**：Rodolfo Giometti <giometti@linux.it>

**Description**：

`/sys/class/pps/ppsX/assert` 文件以 `<secs>.<nsec>#<sequence>` 的形式报告第 X 个源的断言事件和断言序列号。

如果源没有断言事件，此文件的内容为空。

**What**：`/sys/class/pps/ppsX/clear`

**Date**：2008 年 2 月

**Contact**：Rodolfo Giometti <giometti@linux.it>

**Description**：

`/sys/class/pps/ppsX/clear` 文件以 `<secs>.<nsec>#<sequence>` 的形式报告第 X 个源的清除事件和清除序列号。

如果源没有清除事件，此文件的内容为空。

**What**：`/sys/class/pps/ppsX/mode`

**Date**：2008 年 2 月

**Contact**：Rodolfo Giometti <giometti@linux.it>

**Description**：

`/sys/class/pps/ppsX/mode` 文件以十六进制编码报告第 X 个源的工作模式。

请参考 `linux/include/linux/pps.h` 获取更多信息。

**What**：`/sys/class/pps/ppsX/echo`

**Date**：2008 年 2 月

**Contact**：Rodolfo Giometti <giometti@linux.it>

**Description**：

`/sys/class/pps/ppsX/echo` 文件报告第 X 个源是否支持“回显”功能。

**What**：`/sys/class/pps/ppsX/name`

**Date**：2008 年 2 月

**Contact**：Rodolfo Giometti <giometti@linux.it>

**Description**：

`/sys/class/pps/ppsX/name` 文件报告第 X 个源的名称。

**What**：`/sys/class/pps/ppsX/path`

**Date**：2008 年 2 月

**Contact**：Rodolfo Giometti <giometti@linux.it>

**Description**：

`/sys/class/pps/ppsX/path` 文件报告与第 X 个源连接的设备的路径名。

如果源未与任何设备连接，此文件的内容为空。