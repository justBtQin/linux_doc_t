<markdown_document>

# cgroup freezer 的用途

cgroup freezer 对于批量作业管理系统非常有用，它可以启动和停止一组任务，以便根据系统管理员的需求来调度机器的资源。这种类型的程序通常在 HPC 集群中用于调度对整个集群的访问。

cgroup freezer 使用 cgroups 来描述要由批量作业管理系统启动/停止的任务集。它还提供了一种启动和停止组成作业的任务的方法。

cgroup freezer 对于检查点正在运行的任务组也很有用。冻结器允许检查点代码通过尝试将 cgroup 中的任务强制进入静止状态来获得任务的一致映像。一旦任务处于静止状态，另一个任务可以遍历 /proc 或调用内核接口来收集关于静止任务的信息。如果发生可恢复的错误，检查点的任务可以稍后重新启动。这也允许通过将收集到的信息复制到另一个节点并在那里重新启动任务，将检查点的任务在集群中的节点之间迁移。

使用 SIGSTOP 和 SIGCONT 序列并不总是足以在用户空间中停止和恢复任务。这两个信号在我们希望冻结的任务内部是可观察的。虽然 SIGSTOP 不能被捕获、阻塞或忽略，但它可以通过等待或 ptrace 父任务来看到。SIGCONT 尤其不适合，因为它可以被任务捕获。任何设计用于观察 SIGSTOP 和 SIGCONT 的程序都可能因尝试使用 SIGSTOP 和 SIGCONT 来停止和恢复任务而出现问题。我们可以使用嵌套的 bash 外壳来演示这个问题：

```
$ echo $$
16644
$ bash
$ echo $$
16690
```

从第二个不相关的 bash 外壳：

```
$ kill -SIGSTOP 16690
$ kill -SIGCONT 16690
```
（在这一点上，16690 退出并导致 16644 也退出）

这是因为 bash 可以观察到这两个信号，并选择如何响应它们。

另一个捕获并响应这些信号的程序的例子是 gdb。实际上，任何设计用于使用 ptrace 的程序都可能在这种停止和恢复任务的方法上有问题。

相比之下，cgroup freezer 使用内核冻结器代码来防止冻结/解冻周期对被冻结的任务可见。这允许上面的 bash 示例和 gdb 按预期运行。

cgroup freezer 是分层的。冻结一个 cgroup 会冻结该 cgroup 及其所有后代 cgroup 中的所有任务。每个 cgroup 都有自己的状态（自身状态）和从父级继承的状态（父级状态）。如果两个状态都是 THAWED，则 cgroup 是 THAWED。

以下是 cgroupfs 文件由 cgroup freezer 创建：

- freezer.state：可读可写。

  读取时，返回 cgroup 的有效状态 - "THAWED"、"FREEZING" 或 "FROZEN"。这是自身状态和父级状态的组合。如果任何一个处于冻结状态，cgroup 就是冻结的（FREEZING 或 FROZEN）。当属于 cgroup 及其后代的所有任务都冻结时，FREEZING cgroup 转换为 FROZEN 状态。请注意，在向 cgroup 或其后代之一添加新任务后，直到新任务被冻结，cgroup 会从 FROZEN 恢复为 FREEZING。

  写入时，设置 cgroup 的自身状态。允许两个值 - "FROZEN" 和 "THAWED"。如果写入 FROZEN，cgroup（如果尚未处于冻结状态）将与其所有后代 cgroup 一起进入 FREEZING 状态。

  如果写入 THAWED，cgroup 的自身状态将更改为 THAWED。请注意，如果父级状态仍在冻结，有效状态可能不会更改为 THAWED。如果 cgroup 的有效状态变为 THAWED，则由于该 cgroup 而处于冻结状态的所有后代也将离开冻结状态。

- freezer.self_freezing：只读。

  显示自身状态。如果自身状态为 THAWED，则为 0；否则为 1。

  此值为 1 当且仅当最后一次写入 freezer.state 为 "FROZEN"。

- freezer.parent_freezing：只读。

  显示父级状态。如果 cgroup 的任何祖先都未冻结，则为 0；否则为 1。

根 cgroup 是不可冻结的，并且上述接口文件不存在。

使用示例：
```
# mkdir /sys/fs/cgroup/freezer
# mount -t cgroup -ofreezer freezer /sys/fs/cgroup/freezer
# mkdir /sys/fs/cgroup/freezer/0
# echo $some_pid > /sys/fs/cgroup/freezer/0/tasks

获取冻结子系统的状态：
# cat /sys/fs/cgroup/freezer/0/freezer.state
THAWED

冻结容器中的所有任务：
# echo FROZEN > /sys/fs/cgroup/freezer/0/freezer.state
# cat /sys/fs/cgroup/freezer/0/freezer.state
FREEZING
# cat /sys/fs/cgroup/freezer/0/freezer.state
FROZEN

解冻容器中的所有任务：
# echo THAWED > /sys/fs/cgroup/freezer/0/freezer.state
# cat /sys/fs/cgroup/freezer/0/freezer.state
THAWED

这是在简单场景中对用户空间任务应该做正确事情的基本机制。
```