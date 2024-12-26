<markdown_document>

# 内核中支持多个 CPU 空闲级别

cpuidle 调节器

cpuidle 调节器是在任何给定时间决定进入何种空闲状态的策略例程。cpuidle 核心对调节器使用不同的回调函数。

- enable()：为特定设备启用调节器。

- disable()：为特定设备禁用调节器。

- select()：选择要进入的空闲状态。

- reflect()：在从空闲状态返回后调用，调节器可用于一些记录保存。

可以同时注册多个调节器，用户可以使用 /sysfs 接口（在启用时）在不同调节器之间切换。为了方便开发者轻松尝试不同的调节器，支持多个调节器部分。默认情况下，根据内核配置和平台，cpuidle 将选择最优的调节器。

接口：

    extern int cpuidle_register_governor(struct cpuidle_governor *gov);
    
    struct cpuidle_governor

</markdown_document>