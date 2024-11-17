# 设备树的待办事项列表

##  === 一般结构 ===
-  将节点和属性结构的自定义列表切换为 `(h)list_head`

##  === CONFIG_OF_DYNAMIC ===
-  针对树的更新切换到 RCU，并摆脱全局自旋锁
-  为 `CONFIG_OF_DYNAMIC` 记录节点的生命周期
-  始终在 `of_attach_node()` 时设置 `->full_name`
-  `pseries`：从 `arch/powerpc/platforms/pseries/dlpar.c` 中去除开放编码的树修改