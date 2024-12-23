# 裸机互斥的投票锁

======================================

投票锁，或“vlocks”提供了一种简单的低级互斥机制，对内存系统有合理但最小的要求。

这些旨在用于在其他情况下非相干的 CPU 之间协调关键活动，在硬件不提供其他机制支持且普通自旋锁无法使用的情况下。

vlocks 利用内存系统对单个内存位置写入提供的原子性。为了仲裁，每个 CPU 通过将唯一编号存储到公共内存位置来“为自己投票”。当所有投票都投完时，在该内存位置看到的最终值标识获胜者。

为了确保选举在有限时间内产生明确的结果，CPU 只有在没有选择获胜者且选举似乎尚未开始的情况下才会首先进入选举。

# 算法

---------

用一些伪代码来解释 vlocks 算法是最简单的方法：

```
    int currently_voting[NR_CPUS] = { 0, };
	int last_vote = -1; /* 尚未投票 */
    bool vlock_trylock(int this_cpu)
    {
        /* 表示我们投票的意愿 */
        currently_voting[this_cpu] = 1;
        if (last_vote!= -1) {
            /* 已经有其他 CPU 投票 */
            currently_voting[this_cpu] = 0;
            return false; /* 不是我们自己 */
        }
        /* 提议我们自己 */
        last_vote = this_cpu;
        currently_voting[this_cpu] = 0;
        /* 然后等待直到其他所有 CPU 都完成投票 */
        for_each_cpu(i) {
            while (currently_voting[i]!= 0)
                /* 等待 */;
        }
        /* 结果 */
        if (last_vote == this_cpu)
            return true; /* 我们获胜 */
        return false;
    }
    bool vlock_unlock(void)
    {
        last_vote = -1;
    }
```

currently_voting[]数组为 CPU 提供了一种确定选举是否正在进行的方法，其作用类似于 Lamport 的面包店算法[1]中的“进入”数组。

然而，一旦选举开始，底层的内存系统原子性就用于选择获胜者。这避免了需要静态优先级规则作为决胜局，或任何可能溢出的计数器。

只要 last_vote 变量对所有 CPU 都是全局可见的，一旦每个 CPU 都清除了其 currently_voting 标志，它将只包含一个不会改变的值。

# 特性和限制

------------------------

* vlocks 不旨在公平。在竞争情况下，最后一个尝试获取锁的 CPU 最有可能获胜。

  因此，vlocks 最适合需要选择唯一获胜者但实际上哪个 CPU 获胜并不重要的情况。

* 与其他类似机制一样，vlocks 不能很好地扩展到大量的 CPU。

  如果需要，可以在投票层次结构中级联 vlocks 以实现更好的扩展，如下面针对 4096 个 CPU 的假设示例：

  ```
    /* 第一级：本地选举 */
    my_town = towns[(this_cpu >> 4) & 0xf];
    I_won = vlock_trylock(my_town, this_cpu & 0xf);
    if (I_won) {
        /* 我们赢得了城镇选举，让我们争取州的选举 */
        my_state = states[(this_cpu >> 8) & 0xf];
        I_won = vlock_lock(my_state, this_cpu & 0xf));
        if (I_won) {
            /* 等等 */
            I_won = vlock_lock(the_whole_country, this_cpu & 0xf];
            if (I_won) {
                /*... */
            }
            vlock_unlock(the_whole_country);
        }
        vlock_unlock(my_state);
    }
    vlock_unlock(my_town);
```

# ARM 实现

------------------

当前的 ARM 实现[2]包含了一些超出基本算法的优化：

* 通过将 currently_voting 数组的成员紧密打包在一起，我们可以在一个事务中读取整个数组（前提是可能竞争锁的 CPU 数量足够小）。这减少了需要到外部内存的往返次数。

  在 ARM 实现中，这意味着我们可以使用单个加载和比较：
```
	LDR	Rt, [Rn]
    CMP	Rt, #0
```

... 代替等效于以下的代码：

```
    LDRB	Rt, [Rn]
    CMP	Rt, #0
    LDRBEQ	Rt, [Rn, #1]
    CMPEQ	Rt, #0
    LDRBEQ	Rt, [Rn, #2]
    CMPEQ	Rt, #0
    LDRBEQ	Rt, [Rn, #3]
    CMPEQ	Rt, #0
```

  这减少了快速路径延迟，并在竞争情况下潜在地减少了总线争用。

  该优化依赖于 ARM 内存系统保证不同大小的重叠内存访问之间的一致性，类似于许多其他架构。请注意，我们不在乎 currently_voting 的哪个元素出现在 Rt 的哪些位中，因此在这个优化中不需要担心字节序。

  如果有太多的 CPU 以至于无法在一个事务中读取 currently_voting 数组，那么仍然需要多个事务。对于这种情况，实现使用简单的字大小加载循环。事务的数量仍然比逐个加载字节所需的数量要少。

  原则上，我们可以通过使用 LDRD 或 LDM 进行进一步聚合，但为了保持代码简单，在初始实现中并未尝试。

* vlocks 当前仅用于在尚未启用其缓存的 CPU 之间进行协调。这意味着实现删除了在缓存内存中执行算法时所需的许多屏障。

  由于一个 CPU 的缓存回写会覆盖其他 CPU 写入的值，因此 currently_voting 数组的打包在缓存内存中不起作用，除非所有竞争锁的 CPU 都是缓存一致的。（尽管如果所有 CPU 都是缓存一致的，您可能应该使用适当的自旋锁代替。）

* 用于 last_vote 变量的“尚未投票”值为 0（而不是伪代码中的 -1）。这允许静态分配的 vlocks 通过将它们放在.bss 中而隐式初始化为解锁状态。

  为了设置此变量，会向每个 CPU 的 ID 添加一个偏移量，以便没有 CPU 使用其 ID 的值 0。

# 脚注

--------

最初由 Linaro Limited 的 Dave Martin 创建和记录，用于基于 ARM 的 big.LITTLE 平台，非常感谢 Nicolas Pitre 和 Achin Gupta 的审查和输入。感谢 Nicolas 从相关邮件线程中提取了大部分文本并编写了伪代码。

版权 (C) 2012-2013  Linaro Limited

根据 GNU 通用公共许可证第 2 版的条款分发，如 linux/COPYING 中所定义。

# 参考资料

----------

[1] Lamport, L. "A New Solution of Dijkstra's Concurrent Programming

    Problem", Communications of the ACM 17, 8 (August 1974), 453-455.

    http://en.wikipedia.org/wiki/Lamport%27s_bakery_algorithm

[2] linux/arch/arm/common/vlock.S, www.kernel.org.