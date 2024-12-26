<markdown_document>

Memory Resource Controller(Memcg) 实现备忘录。

最后更新：2010/2

基础内核版本：基于 2.6.33-rc7-mm（34 候选版本）。

由于 VM 变得越来越复杂（原因之一是 memcg……），memcg 的行为也很复杂。这是一份关于 memcg 内部行为的文档。请注意，实现细节可能会有所更改。

(*) 关于 API 的主题应在 Documentation/cgroups/memory.txt 中）

0. 如何记录使用情况？

使用 2 个对象。

page_cgroup……每页一个对象。在启动或内存热插拔时分配。在内存热移除时释放。

swap_cgroup……每个 swp_entry 一个条目。在 swapon()时分配。在 swapoff()时释放。

page_cgroup 有 USED 位，并且不会对一个 page_cgroup 进行双重计数。仅当一个已计费的页面被交换出时，才使用 swap_cgroup。

1. 计费

一个页面/swp_entry 可能在以下情况下被计费（usage += PAGE_SIZE）：

mem_cgroup_try_charge()

2. 取消计费

一个页面/swp_entry 可能通过以下方式被取消计费（usage -= PAGE_SIZE）：

mem_cgroup_uncharge()

在页面的引用计数降至 0 时调用。

mem_cgroup_uncharge_swap()

在 swp_entry 的引用计数降至 0 时调用。交换的计费消失。

3. 计费-提交-取消

Memcg 页面分两步计费：

mem_cgroup_try_charge()

mem_cgroup_commit_charge() 或 mem_cgroup_cancel_charge()

在 try_charge()时，没有标志说明“此页面已计费”。此时，usage += PAGE_SIZE。

在 commit()时，页面与 memcg 相关联。

在 cancel()时，简单地 usage -= PAGE_SIZE。

在以下解释中，我们假设 CONFIG_MEM_RES_CTRL_SWAP=y。

4. 匿名

匿名页面在以下情况下新分配：

- 缺页进入 MAP_ANONYMOUS 映射。

- 写时复制。

4.1 交换入。

在交换入时，页面从交换缓存中取出。有 2 种情况。

(a) 如果交换缓存是新分配并读取的，它没有计费。

(b) 如果交换缓存已被进程映射，它已经被计费。

4.2 交换出。

在交换出时，典型的状态转换如下。

(a) 添加到交换缓存。（标记为 SwapCache）

swp_entry 的引用计数 += 1。

(b) 完全解除映射。

swp_entry 的引用计数 += 页面表项的数量。

(c) 写回交换。

(d) 从交换缓存中删除。（从 SwapCache 中移除）

swp_entry 的引用计数 -= 1。

最后，在任务退出时，

(e) 调用 zap_pte()，swp_entry 的引用计数 -= 1 -> 0。

5. 页面缓存

在 add_to_page_cache_locked()时对页面缓存计费。

逻辑非常清晰。（关于迁移，见下文）

注意：__remove_from_page_cache()由 remove_from_page_cache()和 __remove_mapping()调用。

6. Shmem（tmpfs）页面缓存

理解 shmem 的页面状态转换的最佳方法是阅读 mm/shmem.c。

但对 memcg 围绕 shmem 的行为的简要解释将有助于理解逻辑。

Shmem 的页面（仅叶页面，不是直接/间接块）可以在以下位置：

- shmem 的 inode 的基数树中。

- 交换缓存中。

- 同时在基数树和交换缓存中。这在交换入和交换出时发生。

在以下情况下计费：

- 一个新页面添加到 shmem 的基数树中。

- 一个交换页面被读取。（将计费从 swap_cgroup 移动到 page_cgroup）

7. 页面迁移

mem_cgroup_migrate()

8. LRU

每个 memcg 都有自己的私有 LRU。现在，它的处理由全局 VM 控制（意味着在全局 zone->lru_lock 下处理）。

围绕 memcg 的 LRU 的几乎所有例程都由 zone->lru_lock()下的全局 LRU 的列表管理函数调用。

一个特殊的函数是 mem_cgroup_isolate_pages()。它扫描 memcg 的私有 LRU 并调用 __isolate_lru_page()从 LRU 中提取一个页面。

（通过 __isolate_lru_page()，页面从全局和私有 LRU 中都被移除。）

9. 典型测试。

用于竞争情况的测试。

9.1 给 memcg 设置小限制。

当进行竞争情况测试时，将 memcg 的限制设置得非常小而不是 GB 是一个很好的测试。在 xKB 或 xxMB 限制下的测试中发现了许多竞争情况。

（GB 下的内存行为和 MB 下的内存行为显示出非常不同的情况。）

9.2 Shmem

从历史上看，memcg 的 shmem 处理很差，我们在这里看到了一些问题。这是因为 shmem 是页面缓存但可以是交换缓存。使用 shmem/tmpfs 进行测试总是一个好的测试。

9.3 迁移

对于 NUMA，迁移是另一个特殊情况。要进行简单的测试，cpuset 很有用。以下是一个进行迁移的示例脚本。

mount -t cgroup -o cpuset none /opt/cpuset

mkdir /opt/cpuset/01

echo 1 > /opt/cpuset/01/cpuset.cpus

echo 0 > /opt/cpuset/01/cpuset.mems

echo 1 > /opt/cpuset/01/cpuset.memory_migrate

mkdir /opt/cpuset/02

echo 1 > /opt/cpuset/02/cpuset.cpus

echo 1 > /opt/cpuset/02/cpuset.mems

echo 1 > /opt/cpuset/02/cpuset.memory_migrate

在上述设置中，当你将一个任务从 01 移动到 02 时，将发生从节点 0 到节点 1 的页面迁移。以下是一个迁移所有在 cpuset 下的任务的脚本。

--

move_task()

{

for pid in $1

do

/bin/echo $pid >$2/tasks 2>/dev/null

echo -n $pid

echo -n " "

done

echo END

}

G1_TASK=`cat ${G1}/tasks`

G2_TASK=`cat ${G2}/tasks`

move_task "${G1_TASK}" ${G2} &

--

9.4 内存热插拔。

内存热插拔测试是一个很好的测试。

要离线内存，执行以下操作。

# echo offline > /sys/devices/system/memory/memoryXXX/state

（XXX 是内存的位置）

这也是测试页面迁移的一种简单方法。

9.5 mkdir/rmdir

在使用层次结构时，应该进行 mkdir/rmdir 测试。

使用以下类似的测试。

echo 1 >/opt/cgroup/01/memory/use_hierarchy

mkdir /opt/cgroup/01/child_a

mkdir /opt/cgroup/01/child_b

为 01 设置限制。

为 01/child_b 添加限制。

在 child_a 和 child_b 下运行作业。

在作业运行时随机创建/删除以下组。

/opt/cgroup/01/child_a/child_aa

/opt/cgroup/01/child_b/child_bb

/opt/cgroup/01/child_c

在新组中运行新作业也是一个好主意。

9.6 与其他子系统一起挂载。

与其他子系统一起挂载是一个很好的测试，因为与其他 cgroup 子系统存在竞争和锁依赖关系。

例如：

# mount -t cgroup none /cgroup -o cpuset,memory,cpu,devices

并在其下进行任务移动、mkdir、rmdir 等操作。

9.7 swapoff。

除了交换的管理是 memcg 的复杂部分之一外，在 swapoff 时的交换入调用路径与通常的交换入路径不同。

值得明确测试。

例如，以下测试是好的。

（Shell-A）

# mount -t cgroup none /cgroup -o memory

# mkdir /cgroup/test

# echo 40M > /cgroup/test/memory.limit_in_bytes

# echo 0 > /cgroup/test/tasks

在其下运行 malloc(100M)程序。你将看到 60M 的交换。

（Shell-B）

# 将 /cgroup/test 中的所有任务移动到 /cgroup

# /sbin/swapoff -a

# rmdir /cgroup/test

# 杀死 malloc 任务。

当然，tmpfs 与 swapoff 的测试也应该进行。

9.8 OOM-Killer

由 memcg 的限制引起的内存不足将杀死 memcg 下的任务。当使用层次结构时，层次结构下的任务将被内核杀死。

在这种情况下，不应调用 panic_on_oom，其他组中的任务也不应被杀死。

在 memcg 下引起 OOM 并不困难，如下所示。

案例 A）当你可以进行 swapoff 时

#swapoff -a

#echo 50M > /memory.limit_in_bytes

运行 51M 的 malloc

案例 B）当你使用内存+交换限制时

#echo 50M > memory.limit_in_bytes

#echo 50M > memory.memsw.limit_in_bytes

运行 51M 的 malloc

9.9 在任务迁移时移动计费

与任务相关联的计费可以随着任务迁移而移动。

（Shell-A）

#mkdir /cgroup/A

#echo $$ >/cgroup/A/tasks

在 /cgroup/A 中运行使用一些内存的程序。

（Shell-B）

#mkdir /cgroup/B

#echo 1 >/cgroup/B/memory.move_charge_at_immigrate

#echo "group A 中运行的程序的 pid" >/cgroup/B/tasks

你可以通过读取 A 和 B 的 *.usage_in_bytes 或 memory.stat 来查看计费已被移动。

请参阅 Documentation/cgroups/memory.txt 的 8.2 节，了解应写入 move_charge_at_immigrate 的值。

9.10 内存阈值

内存控制器使用 cgroups 通知 API 实现内存阈值。你可以使用 tools/cgroup/cgroup_event_listener.c 来测试它。

（Shell-A）创建 cgroup 并运行事件监听器

# mkdir /cgroup/A

#./cgroup_event_listener /cgroup/A/memory.usage_in_bytes 5M

（Shell-B）将任务添加到 cgroup 并尝试分配和释放内存

# echo $$ >/cgroup/A/tasks

# a="$(dd if=/dev/zero bs=1M count=10)"

# a=

每次跨越阈值时，你将从 cgroup_event_listener 看到消息。

使用 /cgroup/A/memory.memsw.usage_in_bytes 来测试 memsw 阈值。

测试根 cgroup 也是一个好主意。

</markdown_document>