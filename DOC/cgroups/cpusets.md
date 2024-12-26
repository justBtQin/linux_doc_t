<markdown_document>

CPUSETS

-------

版权所有 (C) 2004 BULL SA。作者：Simon.Derr@bull.net

部分版权 (c) 2004 - 2006 Silicon Graphics, Inc.。由 Paul Jackson <pj@sgi.com> 修改

由 Christoph Lameter <clameter@sgi.com> 修改

由 Paul Menage <menage@google.com> 修改

由 Hidetoshi Seto <seto.hidetoshi@jp.fujitsu.com> 修改

内容：

=========

# 1. Cpusets

  ## 1.1 什么是 cpusets？

  Cpusets 提供了一种机制，用于将一组 CPU 和内存节点分配给一组任务。在本文档中，“内存节点”指的是包含内存的在线节点。

  Cpusets 将任务的 CPU 和内存放置限制在其当前 cpusets 内的资源中。它们形成一个嵌套层次结构，在虚拟文件系统中可见。这些是在大型系统上管理动态作业放置所需的基本挂钩，超出了已经存在的内容。

  Cpusets 使用文档 /Documentation/cgroups/cgroups.txt 中描述的通用 cgroup 子系统。

  任务使用 sched_setaffinity(2) 系统调用将 CPU 包含在其 CPU 亲和性掩码中，并使用 mbind(2) 和 set_mempolicy(2) 系统调用将内存节点包含在其内存策略中，这些请求都通过该任务的 cpusets 进行过滤，过滤掉不在该 cpusets 中的任何 CPU 或内存节点。调度程序不会在其 cpus_allowed 向量中不允许的 CPU 上调度任务，内核页面分配器也不会在请求任务的 mems_allowed 向量中不允许的节点上分配页面。

  用户级代码可以通过 cgroup 虚拟文件系统中的名称创建和销毁 cpusets，管理这些 cpusets 的属性和权限，以及哪些 CPU 和内存节点分配给每个 cpusets，指定并查询任务分配到哪个 cpusets，并列出分配给 cpusets 的任务 pid。

  ## 1.2 为什么需要 cpusets？

  大型计算机系统的管理，具有许多处理器 (CPU)、复杂的内存缓存层次结构和具有非均匀访问时间 (NUMA) 的多个内存节点，给进程的有效调度和内存放置带来了额外的挑战。

  通常，只需让操作系统在请求任务之间自动共享可用的 CPU 和内存资源，适度大小的系统就可以以足够的效率运行。

  但是，对于从仔细的处理器和内存放置中受益更多的大型系统，以减少内存访问时间和争用，并且通常代表客户的更大投资，可以从将作业明确放置在系统的适当大小的子集上受益。

  这在以下方面尤其有价值：

    * 运行多个相同 Web 应用程序实例的 Web 服务器，

    * 运行不同应用程序的服务器（例如，Web 服务器和数据库），或

    * 运行具有苛刻性能特征的大型 HPC 应用程序的 NUMA 系统。

  这些子集或“软分区”必须能够动态调整，随着作业组合的变化，而不会影响其他并发执行的作业。当内存位置更改时，正在运行的作业页面的位置也可能会移动。

  内核 cpusets 补丁提供了有效实现此类子集所需的最小基本内核机制。它利用 Linux 内核中现有的 CPU 和内存放置设施，以避免对关键调度程序或内存分配器代码造成任何额外影响。

  ## 1.3 如何实现 cpusets？

  Cpusets 提供了一种 Linux 内核机制，用于限制一个进程或一组进程使用哪些 CPU 和内存节点。

  Linux 内核已经有一对机制来指定一个任务可以在哪些 CPU 上调度（sched_setaffinity）以及它可以在哪些内存节点上获取内存（mbind，set_mempolicy）。

  Cpusets 对这两种机制进行了如下扩展：

 - Cpusets 是内核已知的允许的 CPU 和内存节点的集合。

 - 系统中的每个任务都通过任务结构中的指针附加到一个 cpusets，该指针指向一个引用计数的 cgroup 结构。

 - 对 sched_setaffinity 的调用仅过滤到该任务的 cpusets 中允许的那些 CPU。

 - 对 mbind 和 set_mempolicy 的调用仅过滤到该任务的 cpusets 中允许的那些内存节点。

 - 根 cpusets 包含所有系统的 CPU 和内存节点。

 - 对于任何 cpusets，可以定义包含父 CPU 和内存节点资源子集的子 cpusets。

 - cpusets 的层次结构可以安装在 /dev/cpuset 上，以便从用户空间进行浏览和操作。

 - 一个 cpusets 可以被标记为独占，这确保没有其他 cpusets（除了直接祖先和后代）可以包含任何重叠的 CPU 或内存节点。

 - 您可以列出附加到任何 cpusets 的所有任务（按 pid）。

  cpusets 的实现需要在其余内核中添加一些简单的挂钩，没有一个在性能关键路径中：

 - 在 init/main.c 中，在系统启动时初始化根 cpusets。

 - 在 fork 和 exit 中，将任务附加到其 cpusets 或从其 cpusets 分离。

 - 在 sched_setaffinity 中，通过该任务的 cpusets 中允许的内容来屏蔽请求的 CPU。

 - 在 sched.c migrate_live_tasks() 中，如果可能，在其 cpusets 允许的 CPU 内继续迁移任务。

 - 在 mbind 和 set_mempolicy 系统调用中，通过该任务的 cpusets 中允许的内容来屏蔽请求的内存节点。

 - 在 page_alloc.c 中，将内存限制为允许的节点。

 - 在 vmscan.c 中，将页面回收限制为当前 cpusets。

  您应该挂载“cgroup”文件系统类型，以启用浏览和修改内核当前已知的 cpusets。对于 cpusets 没有添加新的系统调用 - 所有用于查询和修改 cpusets 的支持都是通过此 cpusets 文件系统进行的。

  每个任务的 /proc/<pid>/status 文件有四行添加的行，以两种格式显示任务的 cpus_allowed（它可以在哪些 CPU 上调度）和 mems_allowed（它可以在哪些内存节点上获取内存），如下例所示：

```
  Cpus_allowed:   ffffffff,ffffffff,ffffffff,ffffffff
  Cpus_allowed_list:      0-127
  Mems_allowed:   ffffffff,ffffffff
  Mems_allowed_list:      0-63
```

  每个 cpusets 在 cgroup 文件系统中的一个目录中表示，该目录包含（在标准 cgroup 文件之上）描述该 cpusets 的以下文件：

 - cpuset.cpus：该 cpusets 中的 CPU 列表

 - cpuset.mems：该 cpusets 中的内存节点列表

 - cpuset.memory_migrate 标志：如果设置，将页面移动到 cpusets 节点

 - cpuset.cpu_exclusive 标志：CPU 放置是否独占？

 - cpuset.mem_exclusive 标志：内存放置是否独占？

 - cpuset.mem_hardwall 标志：内存分配是否硬限制？

 - cpuset.memory_pressure：cpuset 中分页压力的度量

 - cpuset.memory_spread_page 标志：如果设置，在允许的节点上均匀分布页面缓存

 - cpuset.memory_spread_slab 标志：如果设置，在允许的节点上均匀分布 slab 缓存

 - cpuset.sched_load_balance 标志：如果设置，在该 cpusets 内的 CPU 上进行负载平衡

 - cpuset.sched_relax_domain_level：迁移任务时的搜索范围

  此外，只有根 cpusets 有以下文件：

 - cpuset.memory_pressure_enabled 标志：是否计算内存压力？

  使用 mkdir 系统调用或 shell 命令创建新的 cpusets。通过写入上述 cpusets 目录中的适当文件来修改 cpusets 的属性，例如其标志、允许的 CPU 和内存节点以及附加的任务。

  嵌套 cpusets 的命名层次结构允许将大型系统划分为嵌套的、动态可更改的“软分区”。

  每个任务的附加，在 fork 时自动继承给该任务的任何子任务，到一个 cpusets 允许将系统上的工作负载组织到相关的任务集中，使得每个集都被限制为使用特定 cpusets 的 CPU 和内存节点。如果必要的 cpusets 文件系统目录的权限允许，一个任务可以重新附加到任何其他 cpusets。

  这种“大规模”系统的管理与使用 sched_setaffinity、mbind 和 set_mempolicy 系统调用在单个任务和内存区域上进行的详细放置平滑集成。

  以下规则适用于每个 cpusets：

 - 其 CPU 和内存节点必须是其父节点的子集。

 - 除非其父节点是独占的，否则它不能被标记为独占。

 - 如果其 CPU 或内存是独占的，则它们不得与任何兄弟节点重叠。

  这些规则以及 cpusets 的自然层次结构，使得能够有效地执行独占保证，而无需每次任何 cpusets 更改时都扫描所有 cpusets 以确保没有任何内容与独占 cpusets 重叠。此外，使用 Linux 虚拟文件系统 (vfs) 来表示 cpusets 层次结构，为 cpusets 提供了熟悉的权限和名称空间，并且只需最少的额外内核代码。

  根（top_cpuset）cpusets 中的 cpus 和 mems 文件是只读的。cpus 文件使用 CPU 热插拔通知程序自动跟踪 cpu_online_mask 的值，mems 文件使用 cpuset_track_online_nodes() 挂钩自动跟踪 node_states[N_MEMORY] 的值 - 即包含内存的节点。

  ## 1.4 什么是独占 cpusets？

  如果一个 cpusets 是 CPU 或内存独占的，则除了直接祖先或后代之外，没有其他 cpusets 可以共享相同的 CPU 或内存节点。

  一个 cpusets 是 cpuset.mem_exclusive *或* cpuset.mem_hardwall 是“硬限制”的，即它限制了内核为页面、缓冲区和其他通常由内核跨多个用户共享的数据分配。所有 cpusets，无论是否硬限制，都限制了用户空间的内存分配。这使得可以配置一个系统，以便几个独立的作业可以共享公共的内核数据，如文件系统页面，同时将每个作业的用户分配隔离在其自己的 cpusets 中。为此，构造一个大的 mem_exclusive cpusets 来容纳所有作业，并为每个单独的作业构造非 mem_exclusive 的子 cpusets。只有少量典型的内核内存，例如来自中断处理程序的请求，才被允许即使在 mem_exclusive cpusets 之外也被获取。

  ## 1.5 什么是内存压力？

  cpusets 的内存压力提供了一个简单的每个 cpusets 度量，用于度量 cpusets 中的任务尝试在 cpusets 的节点上释放使用内存以满足额外内存请求的速率。

  这使批量管理器能够监控在专用 cpusets 中运行的作业，以有效地检测该作业导致的内存压力级别。

  这在运行广泛提交作业的严格管理系统上以及在紧密耦合、长时间运行、大规模并行科学计算作业中都很有用，如果这些作业开始使用超过分配给它们的更多内存，它们将严重无法满足所需的性能目标。

  此机制为批量管理器提供了一种非常经济的方式来监控 cpusets 的内存压力迹象。由批量管理器或其他用户代码决定对此采取什么措施并采取行动。

  ==> 除非通过将“1”写入特殊文件 /dev/cpuset/memory_pressure_enabled 来启用此功能，否则 __alloc_pages() 的重新平衡代码中的此指标的挂钩简化为仅注意 cpuset_memory_pressure_enabled 标志为零。因此，只有启用此功能的系统才会计算该指标。

  为什么是每个 cpusets 的运行平均值：

    因为此仪表是每个 cpusets 的，而不是每个任务或 mm 的，所以在大型系统上，批量调度程序监控此指标所施加的系统负载会大幅降低，因为可以避免每次查询集时扫描任务列表。

    因为此仪表是运行平均值，而不是累加计数器，所以批量调度程序可以通过一次读取检测内存压力，而不必读取并累积一段时间的结果。

    因为此仪表是每个 cpusets 的，而不是每个任务或 mm 的，所以批量调度程序可以通过一次读取获得关键信息，即 cpusets 中的内存压力，而不必在 cpusets 中的所有（动态变化）任务集上查询和累积结果。

  每个 cpusets 保持一个简单的数字滤波器（每个 cpusets 需要一个自旋锁和 3 个字的数据），并由附加到该 cpusets 的任何任务更新，如果它进入同步（直接）页面回收代码。

  每个 cpusets 文件提供一个整数，表示由于 cpusets 中的任务导致的直接页面回收的最近（半衰期为 10 秒）速率，单位为每秒尝试的回收次数乘以 1000。

  ## 1.6 什么是内存分布？

  每个 cpusets 有两个布尔标志文件，用于控制内核为文件系统缓冲区和相关内核数据结构分配页面的位置。它们被称为“cpuset.memory_spread_page”和“cpuset.memory_spread_slab”。

  如果每个 cpusets 的布尔标志文件“cpuset.memory_spread_page”被设置，则内核将在故障任务允许使用的所有节点上均匀分布文件系统缓冲区（页面缓存），而不是更喜欢将这些页面放在任务正在运行的节点上。

  如果每个 cpusets 的布尔标志文件“cpuset.memory_spread_slab”被设置，则内核将在故障任务允许使用的所有节点上均匀分布一些与文件系统相关的 slab 缓存，例如 inodes 和 dentries，而不是更喜欢将这些页面放在任务正在运行的节点上。

  这些标志的设置不会影响任务的匿名数据段或堆栈段页面。

  默认情况下，两种内存分布都关闭，并且内存页面在任务正在运行的本地节点上分配，除非可能受到任务的 NUMA 内存策略或 cpusets 配置的修改，只要有足够的可用空闲内存页面。

  创建新的 cpusets 时，它们继承其父 cpusets 的内存分布设置。

  设置内存分布会导致受影响的页面或 slab 缓存的分配忽略任务的 NUMA 内存策略并进行分布。使用 mbind() 或 set_mempolicy() 调用设置 NUMA 内存策略的任务不会注意到这些调用因包含任务的内存分布设置而发生的任何变化。

如果内存扩展被关闭，那么当前指定的 NUMA 内存策略将再次应用于内存页面分配。

`cpuset.memory_spread_page` 和 `cpuset.memory_spread_slab` 都是布尔标志文件。默认情况下，它们包含 "0"，这意味着该 cpuset 的该功能已关闭。如果向该文件写入 "1"，则会打开该命名功能。

实现很简单。

设置标志 `cpuset.memory_spread_page` 会为属于该 cpuset 或随后加入该 cpuset 的每个任务打开一个 per-process 标志 PFA_SPREAD_PAGE。页面缓存的页面分配调用会被修改，以对这个 PFA_SPREAD_PAGE 任务标志进行内联检查，如果设置了该标志，就会调用一个新例程 `cpuset_mem_spread_node()`，该例程会返回用于分配的首选节点。

类似地，设置 `cpuset.memory_spread_slab` 会打开标志 PFA_SPREAD_SLAB，经过适当标记的 slab 缓存将从 `cpuset_mem_spread_node()` 返回的节点分配页面。

`cpuset_mem_spread_node()` 例程也很简单。它使用每个任务的转子 `cpuset_mem_spread_rotor` 的值来选择当前任务的 `mems_allowed` 中的下一个节点，以用于分配。

这种内存放置策略在其他上下文中也被称为轮询或交错。

这种策略可以为需要将线程本地数据放置在相应节点上，但需要访问大型文件系统数据集以跨作业 cpuset 中的多个节点进行分布以便适应的作业提供显著的改进。如果没有这种策略，特别是对于可能有一个线程读取数据集的作业，作业 cpuset 中节点之间的内存分配可能会变得非常不均匀。

## 1.7 什么是 sched_load_balance？

内核调度程序（kernel/sched/core.c）会自动进行负载平衡。如果一个 CPU 未充分利用，在该 CPU 上运行的内核代码将在诸如 cpusets 和 sched_setaffinity 等放置机制的约束下，在其他更过载的 CPU 上查找任务并将这些任务移动到自身。

负载平衡的算法成本及其对关键共享内核数据结构（如任务列表）的影响，随着要平衡的 CPU 数量的增加，增长速度超过线性。因此，调度程序支持将系统的 CPU 划分为多个调度域，以便它仅在每个调度域内进行负载平衡。每个调度域覆盖系统中的一些 CPU 子集；没有两个调度域重叠；一些 CPU 可能不在任何调度域中，因此不会进行负载平衡。

简而言之，在两个较小的调度域之间进行平衡的成本低于在一个大的调度域中进行平衡的成本，但这样做意味着两个域之一中的过载不会被平衡到另一个域。

默认情况下，有一个调度域覆盖所有 CPU，包括使用内核启动时间的 "isolcpus=" 参数标记为隔离的 CPU。然而，隔离的 CPU 不会参与负载平衡，并且除非明确分配，否则不会在其上运行任务。

这种在所有 CPU 上的默认负载平衡不适用于以下两种情况：

- 在大型系统中，跨许多 CPU 的负载平衡成本很高。如果系统使用 cpusets 将独立作业放置在不同的 CPU 集上，则不需要完全负载平衡。

- 支持某些 CPU 上的实时的系统需要最小化这些 CPU 上的系统开销，包括在不需要时避免任务负载平衡。

当 per-cpuset 标志 "cpuset.sched_load_balance" 被启用（默认设置）时，它请求该 cpuset 允许的 'cpuset.cpus' 中的所有 CPU 都包含在一个单独的调度域中，确保负载平衡可以将一个任务（不受 sched_setaffinity 固定的任务）从该 cpuset 中的任何 CPU 移动到任何其他 CPU。

当 per-cpuset 标志 "cpuset.sched_load_balance" 被禁用时，调度程序将避免在该 cpuset 中的 CPU 之间进行负载平衡，除非由于某些重叠的 cpuset 已启用 "sched_load_balance" 而必须进行负载平衡。

例如，如果顶级 cpuset 启用了标志 "cpuset.sched_load_balance"，则调度程序将有一个覆盖所有 CPU 的调度域，并且其他任何 cpuset 中 "cpuset.sched_load_balance" 标志的设置都无关紧要，因为我们已经在进行完全负载平衡。

因此，在上述两种情况下，顶级 cpuset 标志 "cpuset.sched_load_balance" 应被禁用，只有一些较小的子 cpuset 启用此标志。

当这样做时，通常不希望在顶级 cpuset 中留下任何未固定的可能使用大量 CPU 的任务，因为这些任务可能会被人为地限制在某些 CPU 子集中，具体取决于后代 cpuset 中此标志设置的细节。即使这样的任务可以在其他 CPU 中使用空闲的 CPU 周期，内核调度程序也可能不会考虑将该任务平衡到该未充分利用的 CPU 的可能性。

当然，固定到特定 CPU 的任务可以留在禁用 "cpuset.sched_load_balance" 的 cpuset 中，因为这些任务无论如何都不会移动到其他地方。

这里在 cpusets 和调度域之间存在阻抗不匹配。cpusets 是分层的和嵌套的。调度域是扁平的；它们不重叠，每个 CPU 最多在一个调度域中。

调度域必须是扁平的，因为跨部分重叠的 CPU 集进行负载平衡可能会带来超出我们理解范围的不稳定动态。因此，如果两个部分重叠的 cpuset 都启用了标志 'cpuset.sched_load_balance'，那么我们将形成一个包含两者的单个调度域。我们不会将任务移动到其 cpuset 之外的 CPU，但调度程序负载平衡代码可能会浪费一些计算周期来考虑这种可能性。

这种不匹配就是为什么在哪些 cpuset 启用了标志 "cpuset.sched_load_balance" 与调度域配置之间没有简单的一对一关系。如果一个 cpuset 启用了该标志，它将在其所有 CPU 上进行平衡，但如果它禁用了该标志，只有在没有其他重叠的 cpuset 启用该标志的情况下，才会确保没有负载平衡。

如果两个 cpuset 的 'cpuset.cpus' 允许部分重叠，并且只有其中一个启用了此标志，则另一个可能会发现其任务仅在重叠的 CPU 上部分进行负载平衡。这只是前面几段中给出的顶级 cpuset 示例的一般情况。在一般情况下，就像顶级 cpuset 情况一样，不要在这样部分负载平衡的 cpuset 中留下可能使用大量 CPU 的任务，因为由于缺乏对其他 CPU 的负载平衡，它们可能会被人为地限制在允许它们的某些 CPU 子集中。

在 "cpuset.isolcpus" 中的 CPU 被 isolcpus= 内核启动选项排除在负载平衡之外，并且无论任何 cpuset 中的 "cpuset.sched_load_balance" 值如何，它们都不会进行负载平衡。

### 1.7.1 sched_load_balance 实现细节

per-cpuset 标志 'cpuset.sched_load_balance' 默认启用（与大多数 cpuset 标志相反）。当为一个 cpuset 启用时，内核将确保它可以在该 cpuset 中的所有 CPU 上进行负载平衡（确保该 cpuset 的 cpus_allowed 中的所有 CPU 都在同一个调度域中）。

如果两个重叠的 cpuset 都启用了 'cpuset.sched_load_balance'，那么它们将（必须）都在同一个调度域中。

如果像默认情况一样，顶级 cpuset 启用了 'cpuset.sched_load_balance'，那么根据上述内容，这意味着有一个覆盖整个系统的单个调度域，而不管其他任何 cpuset 设置。

内核向用户空间承诺它将在可以的情况下避免负载平衡。它将选择尽可能精细的调度域粒度分区，同时仍为允许给启用了 'cpuset.sched_load_balance' 的 cpuset 的任何 CPU 集提供负载平衡。

内核 cpuset 到调度程序的内部接口将系统中负载平衡的 CPU 的分区从 cpuset 代码传递到调度程序代码。这个分区是一组 CPU 的子集（表示为一个 `struct cpumask` 数组），成对不相交，覆盖所有必须进行负载平衡的 CPU。

cpuset 代码构建一个新的这样的分区并将其传递给调度程序调度域设置代码，以便在以下情况下必要时重新构建调度域：

- 具有非空 CPU 的 cpuset 的 'cpuset.sched_load_balance' 标志更改，

- 或者具有此标志启用的 cpuset 中的 CPU 加入或离开，

- 或者具有非空 CPU 且此标志启用的 cpuset 的 'cpuset.sched_relax_domain_level' 值更改，

- 或者具有非空 CPU 且此标志启用的 cpuset 被删除，

- 或者一个 CPU 被离线/在线。

这个分区确切地定义了调度程序应该设置哪些调度域 - 每个分区中的元素（`struct cpumask`）对应一个调度域。

调度程序记住当前活动的调度域分区。当从 cpuset 代码调用调度程序例程 `partition_sched_domains()` 以更新这些调度域时，它会将请求的新分区与当前分区进行比较，并根据每个更改更新其调度域，删除旧的并添加新的。

## 1.8 什么是 sched_relax_domain_level？

在调度域中，调度程序以两种方式迁移任务；在时钟周期上进行定期负载平衡，以及在某些调度事件时。

当一个任务被唤醒时，调度程序尝试将该任务移动到空闲的 CPU 上。例如，如果一个任务 A 在 CPU X 上运行并在同一 CPU X 上激活另一个任务 B，并且如果 CPU Y 是 X 的兄弟节点且处于空闲状态，那么调度程序将任务 B 迁移到 CPU Y，以便任务 B 可以在 CPU Y 上开始，而无需在 CPU X 上等待任务 A。

并且如果一个 CPU 的运行队列中没有任务，该 CPU 将在即将空闲之前尝试从其他繁忙的 CPU 拉取额外的任务以帮助它们。

当然，找到可移动任务和/或空闲 CPU 需要一些搜索成本，调度程序可能不会每次都搜索域中的所有 CPU。实际上，在某些体系结构中，事件的搜索范围限制在 CPU 所在的同一套接字或节点中，而时钟周期上的负载平衡则搜索所有。

例如，假设 CPU Z 离 CPU X 相对较远。即使 CPU Z 处于空闲状态，而 CPU X 和其兄弟节点处于繁忙状态，调度程序也不能将唤醒的任务 B 从 X 迁移到 Z，因为它超出了其搜索范围。结果，CPU X 上的任务 B 需要等待任务 A 或等待下一个时钟周期的负载平衡。对于某些特殊情况下的应用程序，等待 1 个时钟周期可能太长。

`cpuset.sched_relax_domain_level` 文件允许您根据需要请求更改此搜索范围。该文件采用整数值，理想情况下表示搜索范围的级别大小，否则初始值为 -1，表示 cpuset 没有请求。

```
-1 ：无请求。使用系统默认值或遵循其他请求。
0 ：不搜索。
1 ：搜索兄弟节点（一个核心中的超线程）。
2 ：搜索一个包中的核心。
3 ：搜索一个节点中的 CPU [在非 NUMA 系统上为系统范围]
4 ：在节点块中搜索节点 [在 NUMA 系统上]
5 ：在系统范围内搜索 [在 NUMA 系统上]
```

系统默认值取决于体系结构。可以使用 relax_domain_level= 启动参数更改系统默认值。

此文件是 per-cpuset 的，并影响 cpuset 所属的调度域。因此，如果一个 cpuset 的 'cpuset.sched_load_balance' 被禁用，则 'cpuset.sched_relax_domain_level' 无效，因为该 cpuset 没有所属的调度域。

如果多个 cpuset 重叠并因此形成一个单独的调度域，则使用其中的最大值。请注意，如果一个请求为 0，而其他请求为 -1，则使用 0。

请注意，修改此文件将具有好坏两方面的影响，是否可接受取决于您的情况。如果不确定，请不要修改此文件。

如果您的情况是：

- 由于您的特殊应用程序行为或对 CPU 缓存等的特殊硬件支持，每个 CPU 之间的迁移成本可以被认为相当小（对于您而言）。

- 搜索成本对您没有影响（或者您可以通过管理 cpuset 来使其足够小等）。

- 即使牺牲缓存命中率等，也需要延迟。那么增加 'sched_relax_domain_level' 将对您有益。

## 1.9 如何使用 cpusets？

为了最小化 cpusets 对关键内核代码（如调度程序）的影响，并且由于内核不支持一个任务直接更新另一个任务的内存放置，因此更改其 cpuset CPU 或内存节点放置，或更改为哪个 cpuset 一个任务附加的影响是微妙的。

如果一个 cpuset 的内存节点被修改，那么对于附加到该 cpuset 的每个任务，下次内核尝试为该任务分配一页内存时，内核将注意到该任务的 cpuset 中的更改，并更新其 per-task 内存放置，以保持在新 cpuset 的内存放置范围内。如果该任务正在使用内存策略 MPOL_BIND，并且它所绑定的节点与它的新 cpuset 重叠，那么该任务将继续使用 MPOL_BIND 节点的任何子集，这些子集仍然在新 cpuset 中允许。如果该任务正在使用 MPOL_BIND，并且现在其 MPOL_BIND 节点在新 cpuset 中都不被允许，那么该任务将基本上被视为 MPOL_BIND 绑定到新 cpuset（即使其 NUMA 放置，通过 get_mempolicy() 查询，不会改变）。如果一个任务从一个 cpuset 移动到另一个 cpuset，那么内核将根据上述情况调整该任务的内存放置，下次内核尝试为该任务分配一页内存时。

如果一个 cpuset 的 'cpuset.cpus' 被修改，那么该 cpuset 中的每个任务的允许 CPU 放置将立即更改。类似地，如果一个任务的 pid 被写入另一个 cpuset 的 'cpuset.tasks' 文件，那么其允许的 CPU 放置将立即更改。如果这样的任务已经使用 sched_setaffinity() 调用将其绑定到其 cpuset 的某个子集，则该任务将被允许在其新 cpuset 中允许的任何 CPU 上运行，从而否定先前 sched_setaffinity() 调用的效果。

总之，其 cpuset 被更改的任务的内存放置由内核在下次为该任务分配页面时更新，而处理器放置立即更新。

通常，一旦分配了一页（给定主内存的物理页面），只要该页面保持分配状态，它就会留在分配给它的任何节点上，即使 cpusets 内存放置策略 'cpuset.mems' 随后发生变化。

如果 cpuset 标志文件 'cpuset.memory_migrate' 设置为 true，则当

任务附加到该 cpuset 时，该任务在其先前 cpuset 的节点上分配的任何页面都将迁移到该任务的新 cpuset。在这些迁移操作中，如果可能，页面在 cpuset 内的相对位置将被保留。例如，如果页面位于先前 cpuset 的第二个有效节点上，则该页面将被放置在新 cpuset 的第二个有效节点上。

同样，如果 'cpuset.memory_migrate' 设置为 true，并且该 cpuset 的 'cpuset.mems' 文件被修改，则分配给该 cpuset 中的任务且位于 'cpuset.mems' 的先前设置的节点上的页面将被移动到 'mems' 的新设置中的节点。

未在任务的先前 cpuset 中或在 cpuset 的先前 'cpuset.mems' 设置中的页面将不会被移动。

上述情况有一个例外。如果使用热插拔功能删除当前分配给 cpuset 的所有 CPU，则该 cpuset 中的所有任务都将被移动到具有非空 cpu 的最近祖先。但是，如果 cpuset 与另一个对任务附加有某些限制的 cgroup 子系统绑定，则某些（或所有）任务的移动可能会失败。在这种失败情况下，这些任务将保留在原始 cpuset 中，并且内核将自动更新其 cpus_allowed 以允许所有在线 CPU。当可用内存热插拔功能用于删除内存节点时，预计也会有类似的例外情况。通常，内核更倾向于违反 cpuset 放置，而不是使已将其所有允许的 CPU 或内存节点脱机的任务挨饿。

上述情况还有第二个例外。GFP_ATOMIC 请求是内核内部分配，必须立即得到满足。如果 GFP_ATOMIC 分配失败，内核可能会丢弃一些请求，甚至在极少数情况下恐慌。如果在当前任务的 cpuset 内无法满足请求，则我们放宽 cpuset，并在任何可以找到内存的地方查找它。违反 cpuset 比给内核带来压力更好。

要开始在 cpuset 中包含的新作业，步骤如下：

```
1. mkdir /sys/fs/cgroup/cpuset
2. mount -t cgroup -ocpuset cpuset /sys/fs/cgroup/cpuset
3. 通过在 /sys/fs/cgroup/cpuset 虚拟文件系统中执行 mkdir 和写入（或 echo）来创建新的 cpuset。
4. 启动将成为新作业的“创始父亲”的任务。
5. 通过将其 pid 写入该 cpuset 的 /sys/fs/cgroup/cpuset tasks 文件，将该任务附加到新的 cpuset。
6. 从这个创始父亲任务中 fork、exec 或 clone 作业任务。
```

例如，以下命令序列将设置一个名为“Charlie”的 cpuset，其中仅包含 CPU 2 和 3 以及内存节点 1，然后在该 cpuset 中启动一个子 shell“sh”：

```
mount -t cgroup -ocpuset cpuset /sys/fs/cgroup/cpuset
cd /sys/fs/cgroup/cpuset
mkdir Charlie
cd Charlie
/bin/echo 2-3 > cpuset.cpus
/bin/echo 1 > cpuset.mems
/bin/echo $$ > tasks
sh
```

子 shell“sh”现在在 cpuset Charlie 中运行

下一行应显示“/Charlie”

    cat /proc/self/cpuset

有几种查询或修改 cpuset 的方法：

- 通过 cpuset 文件系统直接使用 shell 中的各种 cd、mkdir、echo、cat、rmdir 命令，或其在 C 中的等效命令。

- 通过 C 库 libcpuset。

- 通过 C 库 libcgroup。    (http://sourceforge.net/projects/libcg/)

- 通过 python 应用程序 cset。    (http://code.google.com/p/cpuset/)

sched_setaffinity 调用也可以在 shell 提示符下使用 SGI 的 runon 或 Robert Love 的 taskset 进行。mbind 和 set_mempolicy 调用可以在 shell 提示符下使用 numactl 命令（Andi Kleen 的 numa 包的一部分）进行。

# 2. 使用示例和语法

============================

## 2.1 基本用法

---------------

可以通过 cpuset 虚拟文件系统创建、修改和使用 cpuset。

要挂载它，请输入：

    # mount -t cgroup -o cpuset cpuset /sys/fs/cgroup/cpuset

然后在 /sys/fs/cgroup/cpuset 下，您可以找到一个与系统中 cpuset 的树相对应的树。例如，/sys/fs/cgroup/cpuset 是包含整个系统的 cpuset。

如果要在 /sys/fs/cgroup/cpuset 下创建一个新的 cpuset：

    # cd /sys/fs/cgroup/cpuset

    # mkdir my_cpuset

现在您想对这个 cpuset 进行一些操作。

    # cd my_cpuset

在这个目录中，您可以找到几个文件：

    # ls

cgroup.clone_children  cpuset.memory_pressure

cgroup.event_control   cpuset.memory_spread_page

cgroup.procs           cpuset.memory_spread_slab

cpuset.cpu_exclusive   cpuset.mems

cpuset.cpus            cpuset.sched_load_balance

cpuset.mem_exclusive   cpuset.sched_relax_domain_level

cpuset.mem_hardwall    notify_on_release

cpuset.memory_migrate  tasks

读取它们将为您提供有关此 cpuset 状态的信息：它可以使用的 CPU 和内存节点、正在使用它的进程、它的属性。通过写入这些文件，您可以操作 cpuset。

设置一些标志：

    # /bin/echo 1 > cpuset.cpu_exclusive

添加一些 CPU：

    # /bin/echo 0-7 > cpuset.cpus

添加一些内存：

    # /bin/echo 0-7 > cpuset.mems

现在将您的 shell 附加到这个 cpuset：

    # /bin/echo $$ > tasks

您还可以在这个目录中使用 mkdir 在 cpuset 内部创建 cpuset。

    # mkdir my_sub_cs

要删除一个 cpuset，只需使用 rmdir：

    # rmdir my_sub_cs

如果 cpuset 在使用中（内部有 cpuset 或有进程附加），此操作将失败。

请注意，由于历史原因，“cpuset”文件系统作为 cgroup 文件系统的包装器存在。

命令

mount -t cpuset X /sys/fs/cgroup/cpuset

等效于

mount -t cgroup -ocpuset,noprefix X /sys/fs/cgroup/cpuset

echo "/sbin/cpuset_release_agent" > /sys/fs/cgroup/cpuset/release_agent

## 2.2 添加/删除 CPU

------------------------

在 cpuset 目录中的 cpus 或 mems 文件中写入时，使用以下语法：

    # /bin/echo 1-4 > cpuset.cpus		-> 将 cpu 列表设置为 cpu 1,2,3,4

    # /bin/echo 1,2,3,4 > cpuset.cpus	-> 将 cpu 列表设置为 cpu 1,2,3,4

要将一个 CPU 添加到 cpuset 中，写入包含要添加的 CPU 的新 CPU 列表。要将 6 添加到上述 cpuset 中：

    # /bin/echo 1-4,6 > cpuset.cpus	-> 将 cpu 列表设置为 cpu 1,2,3,4,6

类似地，要从 cpuset 中删除一个 CPU，写入不包含要删除的 CPU 的新 CPU 列表。

要删除所有 CPU：

    # /bin/echo "" > cpuset.cpus		-> 清除 cpu 列表

## 2.3 设置标志

-----------------

语法非常简单：

    # /bin/echo 1 > cpuset.cpu_exclusive 	-> 设置标志 'cpuset.cpu_exclusive'

    # /bin/echo 0 > cpuset.cpu_exclusive 	-> 取消设置标志 'cpuset.cpu_exclusive'

## 2.4 附加进程

-----------------------

    # /bin/echo PID > tasks

请注意，是 PID，而不是 PIDs。您一次只能附加一个任务。如果有多个任务要附加，您必须一个接一个地进行：

    # /bin/echo PID1 > tasks

    # /bin/echo PID2 > tasks

	... # /bin/echo PIDn > tasks

# 3. 问题

============

Q：关于这个“/bin/echo”是怎么回事？

A：bash 的内置“echo”命令不会检查对 write()的调用是否出错。如果在 cpuset 文件系统中使用它，您将无法确定命令是成功还是失败。

Q：当我附加进程时，只有行中的第一个进程真正被附加！

A：我们每次对 write()的调用只能返回一个错误代码。因此，您也应该只放置一个 pid。

# 4. 联系

==========

网站：http://www.bullopensource.org/cpuset

</markdown_document>