**What**：`/sys/class/c2port/`

**Date**：2008 年 10 月

**Contact**：Rodolfo Giometti <giometti@linux.it>

**Description**：

`/sys/class/c2port/` 目录将包含文件和目录，为 C2 端口接口提供统一的接口。

**What**：`/sys/class/c2port/c2portX`

**Date**：2008 年 10 月

**Contact**：Rodolfo Giometti <giometti@linux.it>

**Description**：

`/sys/class/c2port/c2portX/` 目录与系统中的第 X 个 C2 端口相关。每个目录将包含用于管理和控制其 C2 端口的文件。

**What**：`/sys/class/c2port/c2portX/access`

**Date**：2008 年 10 月

**Contact**：Rodolfo Giometti <giometti@linux.it>

**Description**：

`/sys/class/c2port/c2portX/access` 文件启用从系统对 C2 端口的访问。在将此条目设置为 0 之前，无法发送命令。

**What**：`/sys/class/c2port/c2portX/dev_id`

**Date**：2008 年 10 月

**Contact**：Rodolfo Giometti <giometti@linux.it>

**Description**：

`/sys/class/c2port/c2portX/dev_id` 文件显示连接的微型设备的设备 ID。

**What**：`/sys/class/c2port/c2portX/flash_access`

**Date**：2008 年 10 月

**Contact**：Rodolfo Giometti <giometti@linux.it>

**Description**：

`/sys/class/c2port/c2portX/flash_access` 文件启用对连接的微型设备的板载闪存的访问。在将此条目设置为 0 之前，无法发送命令。

**What**：`/sys/class/c2port/c2portX/flash_block_size`

**Date**：2008 年 10 月

**Contact**：Rodolfo Giometti <giometti@linux.it>

**Description**：

`/sys/class/c2port/c2portX/flash_block_size` 文件显示连接的微型设备的板载闪存块大小。

**What**：`/sys/class/c2port/c2portX/flash_blocks_num`

**Date**：2008 年 10 月

**Contact**：Rodolfo Giometti <giometti@linux.it>

**Description**：

`/sys/class/c2port/c2portX/flash_blocks_num` 文件显示连接的微型设备的板载闪存块数量。

**What**：`/sys/class/c2port/c2portX/flash_data`

**Date**：2008 年 10 月

**Contact**：Rodolfo Giometti <giometti@linux.it>

**Description**：

`/sys/class/c2port/c2portX/flash_data` 文件导出连接的微型设备的板载闪存的内容。

**What**：`/sys/class/c2port/c2portX/flash_erase`

**Date**：2008 年 10 月

**Contact**：Rodolfo Giometti <giometti@linux.it>

**Description**：

`/sys/class/c2port/c2portX/flash_erase` 文件在连接的微型设备的板载闪存上执行“擦除”命令。

**What**：`/sys/class/c2port/c2portX/flash_erase`

**Date**：2008 年 10 月

**Contact**：Rodolfo Giometti <giometti@linux.it>

**Description**：

`/sys/class/c2port/c2portX/flash_erase` 文件显示连接的微型设备的板载闪存大小。

**What**：`/sys/class/c2port/c2portX/reset`

**Date**：2008 年 10 月

**Contact**：Rodolfo Giometti <giometti@linux.it>

**Description**：

`/sys/class/c2port/c2portX/reset` 文件在连接的微型设备上执行“重置”命令。

**What**：`/sys/class/c2port/c2portX/rev_id`

**Date**：2008 年 10 月

**Contact**：Rodolfo Giometti <giometti@linux.it>

**Description**：

`/sys/class/c2port/c2portX/rev_id` 文件显示连接的微型设备的修订 ID。