**What**：`/sys/fs/ocfs2/`

**Date**：2008 年 4 月

**Contact**：ocfs2-devel@oss.oracle.com

**Description**：

`/sys/fs/ocfs2` 目录包含 ocfs2 工具用于与文件系统交互的调节旋钮。

**What**：`/sys/fs/ocfs2/max_locking_protocol`

**Date**：2008 年 4 月

**Contact**：ocfs2-devel@oss.oracle.com

**Description**：

`/sys/fs/ocfs2/max_locking_protocol` 文件显示文件系统支持的 ocfs2 锁定版本。此版本涵盖了 ocfs2 如何在集群节点之间使用分布式锁定。

协议版本有主版本号和次版本号。两个集群节点如果具有相同的主版本号和重叠的次版本号就可以互操作，例如，版本为 1.10 的节点可以与版本为 1.8 的节点互操作，只要两者都使用 1.8 协议。

从此文件读取将返回一行，主版本号和次版本号由点连接，例如 "1.10"。

此文件为只读。该值被编译到驱动程序中。

**What**：`/sys/fs/ocfs2/loaded_cluster_plugins`

**Date**：2008 年 4 月

**Contact**：ocfs2-devel@oss.oracle.com

**Description**：

`/sys/fs/ocfs2/loaded_cluster_plugins` 文件描述了支持 ocfs2 集群操作的可用插件。在集群中使用 ocfs2 需要一个集群插件。目前有两个可用的插件：

* 'o2cb' - ocfs2 自诞生以来一直使用的经典 o2cb 集群栈。

* 'user' - 与 fs/dlm 结合支持用户空间集群软件的插件。

从此文件读取将返回所有已加载插件的名称，每行一个。

此文件为只读。其内容可能会随着插件的加载或删除而改变。

**What**：`/sys/fs/ocfs2/active_cluster_plugin`

**Date**：2008 年 4 月

**Contact**：ocfs2-devel@oss.oracle.com

**Description**：

`/sys/fs/ocfs2/active_cluster_plugin` 显示文件系统当前正在使用的集群插件。活动插件也会出现在 `loaded_cluster_plugins` 文件中。一次只能使用一个插件。

从此文件读取将在一行上返回活动插件的名称。

此文件为只读。哪个插件处于活动状态取决于正在使用的集群栈。当所有文件系统卸载并且集群栈更改时，内容可能会改变。

**What**：`/sys/fs/ocfs2/cluster_stack`

**Date**：2008 年 4 月

**Contact**：ocfs2-devel@oss.oracle.com

**Description**：

`/sys/fs/ocfs2/cluster_stack` 文件包含当前 ocfs2 集群栈的名称。此值由用户空间工具在使集群栈上线时设置。

集群栈名称的长度为 4 个字符。

当使用 'o2cb' 集群栈时，'o2cb' 集群插件处于活动状态。所有其他集群栈使用 'user' 集群插件。

从此文件读取将在一行上返回当前集群栈的名称。

向此文件写入新的栈名称会更改当前集群栈，除非有挂载的 ocfs2 文件系统。如果有挂载的文件系统，更改栈的尝试将返回错误。

**Users**：

ocfs2-tools <ocfs2-tools-devel@oss.oracle.com> 