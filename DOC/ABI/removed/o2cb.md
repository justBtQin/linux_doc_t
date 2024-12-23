**What**: /sys/o2cb 符号链接

**Date**: 2011 年 5 月

**KernelVersion**: 3.0

**Contact**: ocfs2-devel@oss.oracle.com

**Description**: 这是一个符号链接：/sys/o2cb 指向 /sys/fs/o2cb。当知道在 /sys/fs/o2cb 中查找的新 ocfs2-tools 版本足够普遍时，该符号链接将被删除。不要编写新软件来此处查找，它应该尝试 /sys/fs/o2cb 代替。

**Users**: ocfs2-tools。将提议的更改邮件发送至 ocfs2-devel@oss.oracle.com 就足够了。