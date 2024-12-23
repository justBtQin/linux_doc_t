**What**: /sys/fs/o2cb/（原为 /sys/o2cb）

**Date**: 12 月 2005 年

**KernelVersion**: 2.6.16

**Contact**: ocfs2-devel@oss.oracle.com

**Description**: Ocfs2-tools 通过“interface-revision”查看版本信息。每个 logmask/文件控制一组调试打印，并且可以使用字符串“allow”、“deny”或“off”写入。读取该文件将返回当前状态。

**Users**: ocfs2-tools。将提议的更改邮件发送至 ocfs2-devel@oss.oracle.com 就足够了。