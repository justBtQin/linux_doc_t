# The struct taskstats

本文档包含对`struct taskstats`字段的解释。`struct taskstats`中有以下三组不同的字段：

1. 通用和基本统计字段

    如果设置了`CONFIG_TASKSTATS`，则启用任务统计接口，并为任务的`do_exit()`收集通用字段和基本统计字段以进行交付。

2. 延迟统计字段

    这些字段位于`/* Delay accounting fields start */`和`/* Delay accounting fields end */`之间。如果设置了`CONFIG_TASK_DELAY_ACCT`，则收集它们的值。

3. 扩展统计字段

    这些字段位于`/* Extended accounting fields start */`和`/* Extended accounting fields end */`之间。如果设置了`CONFIG_TASK_XACCT`，则收集它们的值。

4. 每个任务和每个线程的上下文切换计数统计

5. SMT 机器的时间统计

6. 用于内存回收的扩展延迟统计字段

未来的扩展应将字段添加到`struct taskstats`的末尾，并且不应更改结构内每个字段的相对位置。

```c
struct taskstats {
1. 通用和基本统计字段：
    /* 此结构的版本号。此字段始终设置为
     * TAKSTATS_VERSION，它在`<linux/taskstats.h>`中定义。
     * 每次更改结构时，该值应递增。
     */
    __u16 version;

    /* 任务的退出代码。 */
    __u32 ac_exitcode;  /* 退出状态 */

    /* 任务的统计标志，如`<linux/acct.h>`中所定义,定义的值为 AFORK、ASU、ACOMPAT、ACORE 和 AXSIG。*/

    __u8 ac_flag;  /* 记录标志 */

    /* 任务的`task_nice()`值。 */
    __u8 ac_nice;  /* task_nice */

    /* 启动此任务的命令的名称。 */
    char ac_comm[TS_COMM_LEN];  /* 命令名称 */

    /* 在`task->policy`字段中设置的调度策略。 */
    __u8 ac_sched;  /* 调度策略 */
    __u8 ac_pad[3];
    __u32 ac_uid;  /* 用户 ID */
    __u32 ac_gid;  /* 组 ID */
    __u32 ac_pid;  /* 进程 ID */
    __u32 ac_ppid;  /* 父进程 ID */

    /* 任务开始的时间，自 1970 年以来的[秒]。 */
    __u32 ac_btime;  /* 开始时间[自 1970 年以来的秒] */

    /* 任务的经过时间，以[微秒]为单位。 */
    __u64 ac_etime;  /* 经过时间[微秒] */

    /* 任务的用户 CPU 时间，以[微秒]为单位。 */
    __u64 ac_utime;  /* 用户 CPU 时间[微秒] */

    /* 任务的系统 CPU 时间，以[微秒]为单位。 */
    __u64 ac_stime;  /* 系统 CPU 时间[微秒] */

    /* 任务的次要页面错误计数，如`task->min_flt`中所设置。 */
    __u64 ac_minflt;  /* 次要页面错误计数 */

    /* 任务的主要页面错误计数，如`task->maj_flt`中所设置。 */
    __u64 ac_majflt;  /* 主要页面错误计数 */

2. 延迟统计字段：

    /* 延迟统计字段开始
     *
     * 直到注释“Delay accounting fields end”的所有值，
     * 只有在启用延迟统计时才可用，即使最后几个字段不是延迟
     *
     * xxx_count 是记录的延迟值的数量
     * xxx_delay_total 是相应的累积延迟（纳秒）
     *
     * xxx_delay_total 在溢出时回绕到零
     * xxx_count 无论溢出都会递增
     */

    /* 可运行时等待 CPU 的延迟计数，延迟总和不进行原子更新*/
    __u64 cpu_count;
    __u64 cpu_delay_total;

    /* 以下四个字段使用`task->delays->lock`进行原子更新 */
    /* 等待同步块 I/O 完成的延迟
     * 不考虑 I/O 提交中的延迟
     */
	__u64 blkio_count;
    __u64 blkio_delay_total;

    /* 等待页面错误 I/O（仅交换）的延迟 */
    __u64 swapin_count;
    __u64 swapin_delay_total;

    /* cpu 的“墙上时钟”运行时间
     * 在某些架构上，该值将根据虚拟化引起的内核窃取的 CPU 时间进行调整。
     * 该值是累积的，以纳秒为单位，没有相应的计数，并且在溢出时默默地回绕到零
     */

    __u64 cpu_run_real_total;

    /* cpu 的“虚拟”运行时间
     * 使用内核看到的时间间隔，即不考虑虚拟化引起的内核非自愿等待。
     * 该值是累积的，以纳秒为单位，没有相应的计数，并且在溢出时默默地回绕到零
     */
    __u64 cpu_run_virtual_total;

    /* 延迟统计字段结束 */
    /* version 1 结束于此 */

3. 扩展统计字段
    /* 扩展统计字段开始 */
    /* 任务持续时间内累积的 RSS 使用量，以 MBytes-usecs 为单位。
     * 每次将一个刻度计入任务的系统时间时，当前的 rss 使用量将添加到该计数器中。因此，最终我们将拥有内存使用量乘以系统时间。因此，可以计算每个系统时间单位的平均使用量。
     */
    __u64 coremem;  /* 累积的 RSS 使用量（MB-usec） */

    /* 任务持续时间内累积的虚拟内存使用量。
     * 与上面的`acct_rss_mem1`相同，只是我们跟踪 VM 使用量。
     */

    __u64 virtmem;  /* 累积的 VM 使用量（MB-usec） */

    /* 任务持续时间内 RSS 使用量的高水位标记，以 KBytes 为单位。 */
    __u64 hiwater_rss;  /* RSS 使用量的高水位标记 */

    /* 任务持续时间内 VM 使用量的高水位标记，以 KBytes 为单位。 */
    __u64 hiwater_vm;  /* 高水位虚拟内存使用量 */

    /* 以下四个字段是任务的 I/O 统计信息。 */
    __u64 read_char;  /* 读取的字节数 */
    __u64 write_char;  /* 写入的字节数 */
    __u64 read_syscalls;  /* 读取系统调用次数 */
    __u64 write_syscalls;  /* 写入系统调用次数 */
    /* 扩展统计字段结束 */

4. 每个任务和每个线程的统计信息
    __u64 nvcsw;  /* 自愿上下文切换计数器 */
    __u64 nivcsw;  /* 非自愿上下文切换计数器 */

5. SMT 机器的时间统计
    __u64 ac_utimescaled;  /* 按频率等缩放的用户时间 */
    __u64 ac_stimescaled;  /* 按频率等缩放的系统时间 */
    __u64 cpu_scaled_run_real_total;  /* 缩放后的 cpu_run_real_total */

6. 用于内存回收的扩展延迟统计字段
    /* 等待内存回收的延迟 */
    __u64 freepages_count;
    __u64 freepages_delay_total;
}
```