**/sys/devices/system/memory**

 - **What**：/sys/devices/system/memory

 - **Date**：2008 年 6 月

 - **Contact**：Badari Pulavarty <pbadari@us.ibm.com>

 - **Description**：`/sys/devices/system/memory` 包含内核内存块内部状态的快照。文件可以动态添加或删除，以表示热添加/删除操作。

 - **Users**：热插拔内存添加/删除工具，http://www.ibm.com/developerworks/wikis/display/LinuxP/powerpc-utils

**/sys/devices/system/memory/memoryX/removable**

 - **What**：/sys/devices/system/memory/memoryX/removable

 - **Date**：2008 年 6 月

 - **Contact**：Badari Pulavarty <pbadari@us.ibm.com>

 - **Description**：文件 `/sys/devices/system/memory/memoryX/removable` 指示此内存块是否可移除。这对于用户级代理在尝试可能昂贵的热移除内存操作之前确定内存的可移除部分很有用。

 - **Users**：热插拔内存移除工具，http://www.ibm.com/developerworks/wikis/display/LinuxP/powerpc-utils

**/sys/devices/system/memory/memoryX/phys_device**

 - **What**：/sys/devices/system/memory/memoryX/phys_device

 - **Date**：2008 年 9 月

 - **Contact**：Badari Pulavarty <pbadari@us.ibm.com>

 - **Description**：文件 `/sys/devices/system/memory/memoryX/phys_device` 为只读，旨在显示物理内存设备的名称。目前实现不完整。

**/sys/devices/system/memory/memoryX/phys_index**

 - **What**：/sys/devices/system/memory/memoryX/phys_index

 - **Date**：2008 年 9 月

 - **Contact**：Badari Pulavarty <pbadari@us.ibm.com>

 - **Description**：文件 `/sys/devices/system/memory/memoryX/phys_index` 为只读，包含十六进制的节 ID，相当于内存节目录名称中包含的十进制 X。

**/sys/devices/system/memory/memoryX/state**

 - **What**：/sys/devices/system/memory/memoryX/state

 - **Date**：2008 年 9 月

 - **Contact**：Badari Pulavarty <pbadari@us.ibm.com>

 - **Description**：文件 `/sys/devices/system/memory/memoryX/state` 为读写。读取时，其内容显示内存节的在线/离线状态。写入时，根用户可以使用以下命令切换可移除内存节的在线/离线状态（请参阅上述“removable”文件描述）。

```
# echo online > /sys/devices/system/memory/memoryX/state
# echo offline > /sys/devices/system/memory/memoryX/state
```

例如，如果 `/sys/devices/system/memory/memory22/removable` 包含值 1 且 `/sys/devices/system/memory/memory22/state` 包含字符串“online”，则根用户可以执行以下命令将该节离线。

```
# echo offline > /sys/devices/system/memory/memory22/state
```

 - **Users**：热插拔内存移除工具，http://www.ibm.com/developerworks/wikis/display/LinuxP/powerpc-utils

**/sys/devices/system/memory/memoryX/valid_zones**

 - **What**：/sys/devices/system/memory/memoryX/valid_zones

 - **Date**：2014 年 7 月

 - **Contact**：Zhang Zhen <zhenzhang.zhang@huawei.com>

 - **Description**：文件 `/sys/devices/system/memory/memoryX/valid_zones` 为只读，旨在显示此内存块可以上线到哪个区域。

**/sys/devices/system/memoryX/nodeY**

 - **What**：/sys/devices/system/memoryX/nodeY

 - **Date**：2009 年 10 月

 - **Contact**：Linux Memory Management list <linux-mm@kvack.org>

 - **Description**：当 CONFIG_NUMA 启用时，一个指向相应 NUMA 节点目录的符号链接。例如，为节点 0 上的内存节 9 创建以下符号链接：`/sys/devices/system/memory/memory9/node0 -> ../../node/node0`

**/sys/devices/system/node/nodeX/memoryY**

 - **What**：/sys/devices/system/node/nodeX/memoryY

 - **Date**：2008 年 9 月

 - **Contact**：Gary Hade <garyhade@us.ibm.com>

 - **Description**：当 CONFIG_NUMA 启用时，`/sys/devices/system/node/nodeX/memoryY` 是一个指向相应 `/sys/devices/system/memory/memoryY` 内存节目录的符号链接。例如，为节点 0 上的内存节 9 创建以下符号链接：`/sys/devices/system/node/node0/memory9 -> ../../memory/memory9`