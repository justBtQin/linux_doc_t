**What**：`state`

**Date**：2010 年 9 月

**KernelVersion**：2.6.37

**Contact**：Vernon Mauery <vernux@us.ibm.com>

**Description**：`state`文件提供了一种在高级实时模式（PRTM）中切换以及查询当前状态的方法。0 表示 PRTM 关闭，1 表示 PRTM 启用

**Users**：ibm-prtm 用户空间守护进程使用此接口。

**What**：`version`

**Date**：2010 年 9 月

**KernelVersion**：2.6.37

**Contact**：Vernon Mauery <vernux@us.ibm.com>

**Description**：`version`文件提供了一种查询存在于扩展 BIOS 数据区（EBDA）中的 RTL 表版本的方法。

**Users**：ibm-prtm 用户空间守护进程使用此接口。 