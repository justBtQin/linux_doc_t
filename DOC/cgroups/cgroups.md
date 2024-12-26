<markdown_document>

# CGROUPS

-------

由 Paul Menage <menage@google.com> 基于 Documentation/cgroups/cpusets.txt 撰写

cpusets.txt 中的原始版权声明：

部分版权 (C) 2004 BULL SA。部分版权 (c) 2004 - 2006 Silicon Graphics, Inc. 由 Paul Jackson <pj@sgi.com> 修改

由 Christoph Lameter <clameter@sgi.com> 修改

# 目录：

=========

1. 控制组

  1.1 什么是 cgroups？

  1.2 为什么需要 cgroups？

  1.3 如何实现 cgroups？

  1.4 notify_on_release 是做什么的？

  1.5 clone_children 是做什么的？

  1.6 如何使用 cgroups？

2. 使用示例和语法

  2.1 基本用法

  2.2 附加进程

  2.3 按名称挂载层次结构

3. 内核 API

  3.1 概述

  3.2 同步

  3.3 子系统 API

4. 扩展属性使用

5. 问题

# 1. 控制组

=================

## 1.1 什么是 cgroups？

控制组提供了一种机制，用于将任务集及其所有未来子任务聚合/分区为具有特殊行为的层次结构组。

定义：

一个 *cgroup* 将一组任务与一个或多个子系统的一组参数相关联。

一个 *子系统* 是一个模块，它利用 cgroups 提供的任务分组功能，以特定方式处理任务组。子系统通常是一个“资源控制器”，它调度资源或应用每个 cgroup 的限制，但它可以是任何希望对一组进程采取行动的东西，例如虚拟化子系统。

一个 *层次结构* 是一组 cgroup 排列成一棵树，使得系统中的每个任务都恰好位于层次结构中的一个 cgroup 中，以及一组子系统；每个子系统在层次结构中的每个 cgroup 上都有系统特定的状态。每个层次结构都有一个与之关联的 cgroup 虚拟文件系统实例。

在任何时候，可能有多个活动的任务 cgroup 层次结构。每个层次结构都是系统中所有任务的分区。

用户级代码可以通过 cgroup 虚拟文件系统实例中的名称创建和销毁 cgroup，指定并查询任务分配到哪个 cgroup，并列出分配给 cgroup 的任务 PID。这些创建和分配仅影响与该 cgroup 文件系统实例关联的层次结构。

仅就其本身而言，cgroups 的唯一用途是用于简单的作业跟踪。其目的是让其他子系统挂钩到通用的 cgroup 支持中，为 cgroup 提供新的属性，例如计算/限制 cgroup 中的进程可以访问的资源。例如，cpusets（见 Documentation/cgroups/cpusets.txt）允许您将一组 CPU 和一组内存节点与每个 cgroup 中的任务相关联。

## 1.2 为什么需要 cgroups？

在 Linux 内核中，有多个努力提供进程聚合，主要用于资源跟踪目的。这些努力包括 cpusets、CKRM/ResGroups、UserBeanCounters 和虚拟服务器命名空间。这些都需要进程分组/分区的基本概念，新分叉的进程最终与它们的父进程位于同一组（cgroup）中。

内核 cgroup 补丁提供了有效地实现此类组所需的最小基本内核机制。它对系统快速路径的影响最小，并为特定子系统（如 cpusets）提供挂钩，以根据需要提供额外的行为。

提供多个层次结构支持，以允许在不同子系统中任务到 cgroup 的划分明显不同的情况 - 具有并行层次结构允许每个层次结构是任务的自然划分，而无需处理如果几个不相关的子系统需要被强制放入同一 cgroup 树中会出现的复杂任务组合。

在一个极端情况下，每个资源控制器或子系统可以在一个单独的层次结构中；在另一个极端情况下，所有子系统都将附加到同一个层次结构中。

作为一个可以从多个层次结构中受益的场景示例（最初由 vatsa@in.ibm.com 提出），考虑一个带有各种用户的大型大学服务器 - 学生、教授、系统任务等。该服务器的资源规划可以如下进行：

```
       CPU：          "Top cpuset"
                       /       \
               CPUSet1         CPUSet2
                  |               |
               （教授）    （学生）
               此外，（系统任务）附加到 topcpuset（以便它们可以在任何地方运行），限制为 20％
       内存：教授（50％），学生（30％），系统（20％）
       磁盘：教授（50％），学生（30％），系统（20％）
       网络：WWW 浏览（20％），网络文件系统（60％），其他（20％）
                               / \
               教授（15％）  学生（5％）
```

像 Firefox/Lynx 这样的浏览器进入 WWW 网络类，而 (k)nfsd 进入 NFS 网络类。

同时，Firefox/Lynx 将根据启动它的人（教授/学生）共享适当的 CPU/内存类。

通过能够根据不同的资源对任务进行不同的分类（通过将这些资源子系统放在不同的层次结构中），管理员可以轻松设置一个接收 exec 通知的脚本，并且根据谁正在启动浏览器，他可以

    # echo browser_pid > /sys/fs/cgroup/<restype>/<userclass>/tasks

如果只有一个层次结构，他现在可能必须为每个启动的浏览器创建一个单独的 cgroup，并将其与适当的网络和其他资源类相关联。这可能会导致此类 cgroup 的激增。

另外，假设管理员希望暂时为学生的浏览器提供增强的网络访问权限（因为是晚上，用户想要进行在线游戏：））或者为学生的一个模拟应用程序提供增强的 CPU 功率。

通过能够直接将 PID 写入资源类，只需执行以下操作：

       # echo pid > /sys/fs/cgroup/network/<new_class>/tasks

       （一段时间后）

       # echo pid > /sys/fs/cgroup/network/<orig_class>/tasks

如果没有此功能，管理员将不得不将 cgroup 拆分为多个单独的 cgroup，然后将新的 cgroup 与新的资源类相关联。

## 1.3 如何实现 cgroups？ 

控制组对内核进行了以下扩展：

 - 系统中的每个任务都有一个引用计数指针指向一个 css_set。

 - 一个 css_set 包含一组指向 cgroup_subsys_state 对象的引用计数指针，每个在系统中注册的 cgroup 子系统一个。从任务到它在每个层次结构中的 cgroup 没有直接链接，但可以通过 cgroup_subsys_state 对象中的指针来确定。这是因为访问子系统状态是预期会频繁发生且在性能关键代码中的事情，而需要任务实际 cgroup 分配的操作（特别是在 cgroup 之间移动）则不太常见。一个链表通过每个 task_struct 的 cg_list 字段使用 css_set 运行，锚定在 css_set->tasks 上。

 - 可以挂载一个 cgroup 层次结构文件系统，以便从用户空间进行浏览和操作。

 - 可以列出附加到任何 cgroup 的所有任务（按 PID）。

cgroups 的实现需要在其余内核中添加一些简单的钩子，没有在性能关键路径中：

 - 在 init/main.c 中，在系统启动时初始化根 cgroups 和初始 css_set。

 - 在 fork 和 exit 中，将任务附加和分离到其 css_set。

此外，可以挂载一个类型为“cgroup”的新文件系统，以启用浏览和修改当前已知的内核 cgroup。挂载 cgroup 层次结构时，可以指定一个以逗号分隔的子系统列表作为文件系统挂载选项。默认情况下，挂载 cgroup 文件系统会尝试挂载包含所有注册子系统的层次结构。

如果已经存在具有完全相同的子系统集的活动层次结构，它将被重用用于新的挂载。如果没有现有的层次结构匹配，并且任何请求的子系统在现有层次结构中正在使用，则挂载将以 -EBUSY 失败。否则，将激活一个新的层次结构，与请求的子系统相关联。

目前无法将新的子系统绑定到活动的 cgroup 层次结构，也无法从活动的 cgroup 层次结构中解绑子系统。这在未来可能是可能的，但充满了棘手的错误恢复问题。

当卸载 cgroup 文件系统时，如果在顶级 cgroup 下方创建了任何子 cgroup，则该层次结构即使卸载后仍将保持活动；如果没有子 cgroup，则层次结构将被停用。

没有为 cgroups 添加新的系统调用 - 所有用于查询和修改 cgroups 的支持都是通过此 cgroup 文件系统进行的。

/proc 下的每个任务都有一个名为“cgroup”的附加文件，对于每个活动层次结构，显示子系统名称和 cgroup 名称作为相对于 cgroup 文件系统根的路径。

每个 cgroup 在 cgroup 文件系统中由一个目录表示，其中包含描述该 cgroup 的以下文件：

 - tasks：附加到该 cgroup 的任务（按 PID）列表。此列表不保证已排序。将线程 ID 写入此文件会将线程移动到此 cgroup 中。

 - cgroup.procs：cgroup 中的线程组 ID 列表。此列表不保证已排序或没有重复的 TGIDs，如果需要此属性，用户空间应排序/唯一化该列表。将线程组 ID 写入此文件会将该组中的所有线程移动到此 cgroup 中。

 - notify_on_release 标志：退出时运行释放代理？

 - release_agent：用于释放通知的路径（此文件仅存在于顶级 cgroup 中）

其他子系统（如 cpusets）可能会在每个 cgroup 目录中添加其他文件。

使用 mkdir 系统调用或 shell 命令创建新的 cgroup。通过写入该 cgroup 目录中的适当文件来修改 cgroup 的属性，如上所列。

嵌套 cgroup 的命名层次结构允许将大型系统划分为嵌套的、动态可更改的“软分区”。

每个任务的附加，在 fork 时自动由该任务的任何子任务继承，到一个 cgroup 允许将系统上的工作负载组织为相关的任务集。如果必要的 cgroup 文件系统目录的权限允许，任务可以重新附加到任何其他 cgroup。

当一个任务从一个 cgroup 移动到另一个 cgroup 时，它会获得一个新的 css_set 指针 - 如果已经存在具有所需 cgroup 集合的 css_set，则会重用该组，否则会分配一个新的 css_set。通过查找哈希表来找到适当的现有 css_set。

为了允许从 cgroup 访问组成它的 css_sets（以及因此的任务），一组 cg_cgroup_link 对象形成一个晶格；每个 cg_cgroup_link 链接到其 cgrp_link_list 字段上的单个 cgroup 的 cg_cgroup_links 列表，以及其 cg_link_list 上的单个 css_set 的 cg_cgroup_links 列表。

因此，可以通过遍历引用 cgroup 的每个 css_set，并对子迭代每个 css_set 的任务集来列出 cgroup 中的任务集。

使用 Linux 虚拟文件系统（vfs）来表示 cgroup 层次结构，为 cgroup 提供了熟悉的权限和名称空间，同时只需最少的额外内核代码。

## 1.4 notify_on_release 是做什么的？ 

如果在 cgroup 中启用了 notify_on_release 标志（1），那么每当该 cgroup 中的最后一个任务离开（退出或附加到其他 cgroup）并且该 cgroup 的最后一个子 cgroup 被删除时，内核将运行由该层次结构的根目录中的“release_agent”文件的内容指定的命令，并提供被遗弃的 cgroup 的路径名（相对于 cgroup 文件系统的挂载点）。这使得能够自动删除被遗弃的 cgroup。在系统启动时，根 cgroup 中的 notify_on_release 的默认值为禁用（0）。创建时其他 cgroup 的默认值是其父级 notify_on_release 设置的当前值。cgroup 层次结构的 release_agent 路径的默认值为空。

## 1.5 clone_children 是做什么的？ 

此标志仅影响 cpuset 控制器。如果在 cgroup 中启用了 clone_children 标志（1），则新的 cpuset cgroup 将在初始化期间从父级复制其配置。

## 1.6 如何使用 cgroups？ 

要启动一个要包含在 cgroup 中的新作业，使用“cpuset”cgroup 子系统，步骤如下：

```
 1) mount -t tmpfs cgroup_root /sys/fs/cgroup
 2) mkdir /sys/fs/cgroup/cpuset
 3) mount -t cgroup -ocpuset cpuset /sys/fs/cgroup/cpuset
 4) 通过在 /sys/fs/cgroup/cpuset 虚拟文件系统中执行 mkdir 和写入（或 echo）来创建新的 cgroup。
 5) 启动一个将成为新作业“创始父亲”的任务。
 6) 通过将其 PID 写入该 cgroup 的 /sys/fs/cgroup/cpuset tasks 文件，将该任务附加到新 cgroup。
 7) 从这个创始父亲任务中 fork、exec 或 clone 作业任务。
```

例如，以下命令序列将设置一个名为“Charlie”的 cgroup，其中仅包含 CPU 2 和 3 以及内存节点 1，然后在该 cgroup 中启动一个子 shell 'sh'：

```
  mount -t tmpfs cgroup_root /sys/fs/cgroup
  mkdir /sys/fs/cgroup/cpuset
  mount -t cgroup cpuset -ocpuset /sys/fs/cgroup/cpuset
  cd /sys/fs/cgroup/cpuset
  mkdir Charlie
  cd Charlie
  /bin/echo 2-3 > cpuset.cpus
  /bin/echo 1 > cpuset.mems
  /bin/echo $$ > tasks
  sh
  # 子 shell 'sh' 现在在 cgroup Charlie 中运行
  # 下一行应显示 '/Charlie'
  cat /proc/self/cgroup
```

# 2. 使用示例和语法

============================

## 2.1 基本用法

---------------

可以通过 cgroup 虚拟文件系统创建、修改和使用 cgroup。

要挂载具有所有可用子系统的 cgroup 层次结构，请输入：

    # mount -t cgroup xxx /sys/fs/cgroup

“xxx”不会被 cgroup 代码解释，但会出现在 /proc/mounts 中，因此可以是您喜欢的任何有用的标识字符串。

注意：某些子系统在没有用户输入的情况下无法工作。例如，如果启用了 cpusets，用户将必须在可以使用该组之前为每个新创建的 cgroup 填充 cpus 和 mems 文件。

如“1.2 为什么需要 cgroups？”部分所述，您应该为要控制的每个单个资源或资源组创建不同的 cgroup 层次结构。因此，您应该在 /sys/fs/cgroup 上挂载一个 tmpfs 并为每个 cgroup 资源或资源组创建目录。

    # mount -t tmpfs cgroup_root /sys/fs/cgroup

    # mkdir /sys/fs/cgroup/rg1

要挂载仅包含 cpuset 和 memory 子系统的 cgroup 层次结构，可输入：

    # mount -t cgroup -o cpuset,memory hier1 /sys/fs/cgroup/rg1

虽然当前支持重新挂载 cgroups，但不建议使用它。重新挂载允许更改绑定的子系统和 release_agent。重新绑定几乎没有用处，因为它仅在层次结构为空时起作用，并且 release_agent 本身应替换为传统的 fsnotify。将来将删除对重新挂载的支持。

要指定层次结构的 release_agent：

    # mount -t cgroup -o cpuset,release_agent="/sbin/cpuset_release_agent" \

    xxx /sys/fs/cgroup/rg1

请注意，多次指定'release_agent'将返回失败。

请注意，仅当层次结构由单个（根）cgroup 组成时，才支持更改子系统集。支持从现有 cgroup 层次结构中任意绑定/解绑子系统的功能旨在将来实现。

然后在 /sys/fs/cgroup/rg1 下，您可以找到一个与系统中的 cgroups 树相对应的树。例如，/sys/fs/cgroup/rg1 是包含整个系统的 cgroup。

如果要更改 release_agent 的值：

    #  echo "/sbin/new_release_agent" > /sys/fs/cgroup/rg1/release_agent

也可以通过重新挂载来更改它。

如果要在 /sys/fs/cgroup/rg1 下创建一个新的 cgroup：

    # cd /sys/fs/cgroup/rg1

    # mkdir my_cgroup

现在您要对这个 cgroup 进行一些操作。 

    # cd my_cgroup

在这个目录中，您可以找到几个文件：

    # ls

cgroup.procs notify_on_release tasks

（以及附加的子系统添加的任何文件）

现在将您的 shell 附加到这个 cgroup：

    # /bin/echo $$ > tasks

您还可以通过在这个目录中使用 mkdir 来在 cgroup 内部创建 cgroups。 # 创建 my_sub_cs

要删除一个 cgroup，只需使用 rmdir：

    # rmdir my_sub_cs

如果 cgroup 正在使用中（内部有 cgroups，或有进程附加，或由其他子系统特定的引用保持活动），这将失败。

## 2.2 附加进程

-----------------------

    # /bin/echo PID > tasks

请注意，是 PID，不是 PIDs。您一次只能附加一个任务。如果有多个任务要附加，您必须一个接一个地进行：

    # /bin/echo PID1 > tasks

    # /bin/echo PID2 > tasks

	... 
	
	# /bin/echo PIDn > tasks

您可以通过回显 0 来附加当前 shell 任务：

    # echo 0 > tasks

您可以使用 cgroup.procs 文件而不是 tasks 文件一次移动一个线程组中的所有线程。将线程组中的任何任务的 PID 回显到 cgroup.procs 会将该线程组中的所有任务附加到 cgroup。将 0 写入 cgroup.procs 会将写入任务的线程组中的所有任务移动。

注意：由于每个任务在每个挂载的层次结构中始终是恰好一个 cgroup 的成员，要将一个任务从其当前 cgroup 中删除，您必须通过写入新 cgroup 的 tasks 文件将其移动到一个新的 cgroup（可能是根 cgroup）。

注意：由于某些 cgroup 子系统的一些限制，将一个进程移动到另一个 cgroup 可能会失败。

## 2.3 按名称挂载层次结构

------

在挂载 cgroups 层次结构时传递 name=<x> 选项会将给定的名称与该层次结构相关联。当挂载预先存在的层次结构时，可以使用此方法，以便通过名称而不是其活动子系统的集合来引用它。每个层次结构要么是无名的，要么有一个唯一的名称。

名称应匹配 [\w.-]+

当为新层次结构传递 name=<x> 选项时，您需要手动指定子系统；当您为子系统指定名称时，不支持在未明确指定时挂载所有子系统的旧行为。

子系统的名称出现在 /proc/mounts 和 /proc/<pid>/cgroups 中的层次结构描述的一部分中。

# 3. 内核 API

=============

## 3.1 概述

------------

每个希望挂钩到通用 cgroup 系统的内核子系统都需要创建一个 cgroup_subsys 对象。这包含各种方法，这些方法是来自 cgroup 系统的回调，以及一个将由 cgroup 系统分配的子系统 ID。

cgroup_subsys 对象中的其他字段包括：

- subsys_id：子系统的唯一数组索引，指示 cgroup->subsys[] 中此子系统应管理的条目。

- name：应初始化为唯一的子系统名称。不应超过 MAX_CGROUP_TYPE_NAMELEN。

- early_init：指示子系统在系统启动时是否需要早期初始化。

每个由系统创建的 cgroup 对象都有一个指针数组，按子系统 ID 索引；此指针完全由子系统管理；通用 cgroup 代码永远不会触摸此指针。

## 3.2 同步

-------------------

有一个全局互斥锁，cgroup_mutex，由 cgroup 系统使用。任何想要修改 cgroup 的东西都应该获取此锁。它也可以被获取以防止 cgroups 被修改，但在那种情况下，更具体的锁可能更合适。

有关更多详细信息，请参阅 kernel/cgroup.c。

子系统可以通过函数 cgroup_lock()/cgroup_unlock() 获取/释放 cgroup_mutex。

访问任务的 cgroup 指针可以通过以下方式完成：

- 持有 cgroup_mutex 时

- 持有任务的 alloc_lock（通过 task_lock()）时

- 在 rcu_read_lock() 部分内通过 rcu_dereference() 时

## 3.3 子系统 API

-----------------

每个子系统应该：

- 在 linux/cgroup_subsys.h 中添加一个条目

- 定义一个名为 <name>_subsys 的 cgroup_subsys 对象

如果一个子系统可以编译为模块，它还应该在其模块初始化调用中调用 cgroup_load_subsys()，在其退出调用中调用 cgroup_unload_subsys()。它还应该在其.c 文件中将其_subsys.module = THIS_MODULE。

每个子系统可以导出以下方法。唯一的强制方法是 css_alloc/free。任何其他为空的方法都假定是成功的无操作。

struct cgroup_subsys_state *css_alloc(struct cgroup *cgrp)

（调用者持有 cgroup_mutex）

被调用为一个 cgroup 分配一个子系统状态对象。子系统应该为传递的 cgroup 分配其子系统状态对象，成功时返回指向新对象的指针，失败时返回 ERR_PTR() 值。成功时，子系统指针应该指向一个 cgroup_subsys_state 类型的结构（通常嵌入在一个更大的特定于子系统的对象中），该结构将由 cgroup 系统初始化。请注意，这将在初始化时被调用，为该子系统创建根子系统状态；可以通过传递的 cgroup 对象具有 NULL 父对象（因为它是层次结构的根）来识别这种情况，并且这可能是初始化代码的合适位置。

int css_online(struct cgroup *cgrp)

（调用者持有 cgroup_mutex）

在 @cgrp 成功完成所有分配并对 cgroup_for_each_child/descendant_*() 迭代器可见之后被调用。子系统可以选择通过返回 -errno 来失败创建。此回调可用于实现层次结构中的可靠状态共享和传播。有关详细信息，请参阅 cgroup_for_each_descendant_pre() 上的注释。

void css_offline(struct cgroup *cgrp);

（调用者持有 cgroup_mutex）

这是 css_online() 的对应物，仅在 css_online() 在 @cgrp 上成功时被调用。这表示 @cgrp 的结束开始。@cgrp 正在被删除，子系统应该开始释放它在 @cgrp 上持有的所有引用。当所有引用都被释放时，cgroup 删除将进入下一步 - css_free()。在此回调之后，@cgrp 应该被视为对子系统已死亡。

void css_free(struct cgroup *cgrp)

（调用者持有 cgroup_mutex）

cgroup 系统即将释放 @cgrp；子系统应该释放其子系统状态对象。在调用此方法时，@cgrp 已完全未使用；@cgrp->parent 仍然有效。（注意 - 对于在为此新 cgroup 调用此子系统的 create() 方法后发生错误的新创建的 cgroup，也可以调用此方法）。

int can_attach(struct cgroup *cgrp, struct cgroup_taskset *tset)

（调用者持有 cgroup_mutex）

在将一个或多个任务移动到 cgroup 之前被调用；如果子系统返回错误，这将中止附加操作。@tset 包含要附加的任务，并且保证其中至少有一个任务。

如果任务集中有多个任务，那么：

  - 保证所有任务都来自同一个线程组

  - @tset 包含来自线程组的所有任务，无论它们是否正在切换 cgroups

  - 第一个任务是领导者

每个 @tset 条目还包含任务的旧 cgroup，并且可以使用 cgroup_taskset_for_each() 迭代器轻松跳过不切换 cgroup 的任务。请注意，此方法不会在 fork 时被调用。如果此方法返回 0（成功），则在调用者持有 cgroup_mutex 时此应保持有效，并且确保将来将调用 attach() 或 cancel_attach()。

void css_reset(struct cgroup_subsys_state *css)

（调用者持有 cgroup_mutex）

一个可选操作，应将 @css 的配置恢复到初始状态。此操作当前仅在统一层次结构中使用，当通过 "cgroup.subtree_control" 在 cgroup 上禁用子系统但由于其他子系统依赖它而应保持启用时。cgroup 核心通过删除相关的接口文件使这样的 css 不可见，并调用此回调，以便隐藏的子系统可以返回到初始的中立状态。这可以防止隐藏的 css 产生意外的资源控制，并确保在稍后再次使其可见时配置处于初始状态。

void cancel_attach(struct cgroup *cgrp, struct cgroup_taskset *tset)

（调用者持有 cgroup_mutex）

当一个任务附加操作在 can_attach() 成功后失败时被调用。一个其 can_attach() 有一些副作用的子系统应该提供此函数，以便子系统可以实现回滚。如果不需要，则不需要。此函数仅在其 can_attach() 操作成功的子系统上被调用。参数与 can_attach() 相同。

void attach(struct cgroup *cgrp, struct cgroup_taskset *tset)

（调用者持有 cgroup_mutex）

在任务已附加到 cgroup 之后被调用，以允许任何需要内存分配或阻塞的附加后活动。参数与 can_attach() 相同。

void fork(struct task_struct *task)

在一个任务被 fork 到一个 cgroup 时被调用。

void exit(struct task_struct *task)

在任务退出期间被调用。

void bind(struct cgroup *root)

（调用者持有 cgroup_mutex）

在一个 cgroup 子系统被重新绑定到不同的层次结构和根 cgroup 时被调用。当前，这仅涉及在默认层次结构（该层次结构永远没有子 cgroups）和正在创建/销毁的层次结构（因此没有子 cgroups）之间的移动。

# 4. 扩展属性使用

===========================

cgroup 文件系统在其目录和文件中支持某些类型的扩展属性。当前支持的类型是：

	- 受信任的（XATTR_TRUSTED）

	- 安全的（XATTR_SECURITY）

两者都需要 CAP_SYS_ADMIN 能力才能设置。

与 tmpfs 一样，cgroup 文件系统中的扩展属性使用内核内存存储，建议将使用量保持在最低限度。这就是不支持用户定义的扩展属性的原因，因为任何用户都可以进行设置，并且值大小没有限制。

此功能的当前已知用户是 SELinux 以限制容器中的 cgroup 使用和 systemd 用于各种元数据，如 cgroup 中的主 PID（systemd 为每个服务创建一个 cgroup）。

# 5. 问题

============

Q：这个 '/bin/echo' 是怎么回事？A：bash 的内置 'echo' 命令不会检查对 write() 的调用是否出错。如果在 cgroup 文件系统中使用它，您将无法知道命令是成功还是失败。

Q：当我附加进程时，只有行中的第一个真正被附加！A：我们每次对 write() 的调用只能返回一个错误代码。所以您也应该只放置一个 PID。

</markdown_document>