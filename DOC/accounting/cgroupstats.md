Control Groupstats 受 [http://lkml.org/lkml/2007/4/11/187](http://lkml.org/lkml/2007/4/11/187) 处的讨论启发，并实现了 Andrew Morton 在 [http://lkml.org/lkml/2007/4/11/263](http://lkml.org/lkml/2007/4/11/263) 中建议的 per cgroup 统计信息。

Per cgroup 统计信息基础架构重用了 taskstats 接口的代码。向 cgroups 注册了一组新的 cgroup 操作，包括特定于 cgroups 的命令和属性。通过向 cgroupstats 结构添加成员，应该很容易扩展 per cgroup 统计信息。

当前 cgroupstats 的模型是拉取模式，添加推送模式（在有趣事件上发布统计信息）应该非常容易。目前，用户空间通过传递 cgroup 路径请求统计信息。关于 cgroup 中所有任务状态的统计信息将返回给用户空间。

注意：我们目前依赖延迟记账来提取关于被 I/O 阻塞的任务的信息。如果禁用了 CONFIG_TASK_DELAY_ACCT，此信息将不可用。

为了提取 cgroup 统计信息，已经开发了一个与 getdelays.c 非常相似的实用程序，该实用程序的示例输出如下：

```
~/balbir/cgroupstats #./getdelays  -C "/sys/fs/cgroup/a"
sleeping 1, blocked 0, running 1, stopped 0, uninterruptible 0
```

```
~/balbir/cgroupstats #./getdelays  -C "/sys/fs/cgroup"
sleeping 155, blocked 0, running 1, stopped 0, uninterruptible 2
```