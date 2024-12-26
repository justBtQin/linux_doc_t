<markdown_document>

# CPU Accounting Controller

CPU 记账控制器用于使用 cgroups 对任务进行分组，并统计这些任务组的 CPU 使用情况。

CPU 记账控制器支持多层次组。一个记账组会累积其所有子组以及直接存在于其组中的任务的 CPU 使用情况。

可以通过首先挂载 cgroup 文件系统来创建记账组。

`# mount -t cgroup -ocpuacct none /sys/fs/cgroup`

通过上述步骤，初始的或父记账组在 /sys/fs/cgroup 中变得可见。在启动时，该组包含系统中的所有任务。/sys/fs/cgroup/tasks 列出了此 cgroup 中的任务。/sys/fs/cgroup/cpuacct.usage 给出了此组获得的 CPU 时间（以纳秒为单位），实际上就是系统中所有任务获得的 CPU 时间。

可以在父组 /sys/fs/cgroup 下创建新的记账组。

```
# cd /sys/fs/cgroup
# mkdir g1
# echo $$ > g1/tasks
```

上述步骤创建了一个新组 g1，并将当前的 shell 进程（bash）移动到其中。此 bash 及其子进程消耗的 CPU 时间可以从 g1/cpuacct.usage 获得，并且也会累积在 /sys/fs/cgroup/cpuacct.usage 中。

cpuacct.stat 文件列出了一些统计信息，这些统计信息将 cgroup 获得的 CPU 时间进一步划分为用户时间和系统时间。目前支持以下统计信息：

- user：cgroup 中的任务在用户模式下花费的时间。

- system：cgroup 中的任务在内核模式下花费的时间。

user 和 system 以 USER_HZ 单位表示。

cpuacct 控制器使用 percpu_counter 接口来收集用户时间和系统时间。这有两个副作用：

- 理论上可能会看到用户时间和系统时间的错误值。这是因为 32 位系统上的 percpu_counter_read() 对并发写入不安全。

- 由于 percpu_counter 的批处理性质，可能会看到用户时间和系统时间的稍微过时的值。

</markdown_document>