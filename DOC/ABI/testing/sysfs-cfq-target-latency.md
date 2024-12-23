**What**：`/sys/block/<device>/iosched/target_latency`

**Date**：2012 年 3 月

**contact**：Tao Ma <boyu.mt@taobao.com>

**Description**：

当用户将 `/sys/block/<device>/scheduler` 设置为 `cfq` 时，`/sys/block/<device>/iosched/target_latency` 才存在。它包含了对 `cfq` 的估计延迟时间。`cfq` 将使用它来计算每个任务使用的时间片。 