**What**: `/sys/kernel/uids/<uid>/cpu_shares`

**Date**: 十二月 2007

**Contact**: Dhaval Giani <dhaval@linux.vnet.ibm.com>，Srivatsa Vaddagiri <vatsa@linux.vnet.ibm.com>

**Description**:

`/sys/kernel/uids/<uid>/cpu_shares` 可调整参数用于设置用户被允许的 CPU 带宽。这是一个成比例的值。这意味着如果有两个用户登录，每个用户具有相等数量的份额，那么他们将获得相等的 CPU 带宽。另一个例子是，如果用户 A 的份额 = 1024 且用户 B 的份额 = 2048，用户 B 将获得用户 A 两倍的 CPU 带宽。更多细节请参考 Documentation/scheduler/sched-design-CFS.txt