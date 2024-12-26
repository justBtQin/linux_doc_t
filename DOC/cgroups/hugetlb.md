<markdown_document>

# HugeTLB 控制器

HugeTLB 控制器允许限制每个控制组的 HugeTLB 使用量，并在页面错误期间强制执行控制器限制。由于 HugeTLB 不支持页面回收，在页面错误时强制执行限制意味着，如果应用程序尝试访问超出其限制的 HugeTLB 页面，它将收到 SIGBUS 信号。这要求应用程序事先知道其使用需要多少 HugeTLB 页面。

可以通过首先挂载 cgroup 文件系统来创建 HugeTLB 控制器。

`# mount -t cgroup -o hugetlb none /sys/fs/cgroup`

执行上述步骤后，初始或父级 HugeTLB 组在 /sys/fs/cgroup 中可见。在启动时，该组包含系统中的所有任务。/sys/fs/cgroup/tasks 列出了此 cgroup 中的任务。

可以在父组 /sys/fs/cgroup 下创建新组。

```
# cd /sys/fs/cgroup
# mkdir g1
# echo $$ > g1/tasks
```

上述步骤创建了一个新组 g1，并将当前 shell 进程（bash）移动到其中。

控制文件的简要总结：

hugetlb.<hugepagesize>.limit_in_bytes  # 设置/显示“hugepagesize”HugeTLB 使用量的限制

hugetlb.<hugepagesize>.max_usage_in_bytes  # 显示记录的“hugepagesize”HugeTLB 使用量的最大值

hugetlb.<hugepagesize>.usage_in_bytes  # 显示“hugepagesize”HugeTLB 的当前使用量

hugetlb.<hugepagesize>.failcnt  # 显示由于 HugeTLB 限制而导致的分配失败次数

对于支持两种大页大小（16M 和 16G）的系统，控制文件包括：

hugetlb.16GB.limit_in_bytes

hugetlb.16GB.max_usage_in_bytes

hugetlb.16GB.usage_in_bytes

hugetlb.16GB.failcnt

hugetlb.16MB.limit_in_bytes

hugetlb.16MB.max_usage_in_bytes

hugetlb.16MB.usage_in_bytes

hugetlb.16MB.failcnt

</markdown_document>