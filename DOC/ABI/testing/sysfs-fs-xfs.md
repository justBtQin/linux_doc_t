**What**: /sys/fs/xfs/<disk>/log/log_head_lsn

**Date**: July 2014

**KernelVersion**: 3.17

**Contact**: xfs@oss.sgi.com

**Description**:

    日志当前头部的日志序列号（LSN）。LSN 以“cycle:基本块”格式导出。

**Users**: xfstests

**What**: /sys/fs/xfs/<disk>/log/log_tail_lsn

**Date**: July 2014

**KernelVersion**: 3.17

**Contact**: xfs@oss.sgi.com

**Description**:

    日志当前尾部的日志序列号（LSN）。LSN 以“cycle:基本块”格式导出。

**What**: /sys/fs/xfs/<disk>/log/reserve_grant_head

**Date**: July 2014

**KernelVersion**: 3.17

**Contact**: xfs@oss.sgi.com

**Description**:

    日志保留授予头的当前状态。它表示所有当前未完成事务的总日志保留。授予头以“cycle:字节”格式导出。

**Users**: xfstests

**What**: /sys/fs/xfs/<disk>/log/write_grant_head

**Date**: July 2014

**KernelVersion**: 3.17

**Contact**: xfs@oss.sgi.com

**Description**:

    日志写入授予头的当前状态。它表示所有当前未完成事务的总日志保留，包括由于滚动事务而导致的重新授予。授予头以“cycle:字节”格式导出。

**Users**: xfstests