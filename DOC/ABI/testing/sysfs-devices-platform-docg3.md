**What**: /sys/devices/platform/docg3/f[0-3]_dps[01]_is_keylocked

**Date**: 2011 年 11 月

**KernelVersion**: 3.3

**Contact**: Robert Jarzmik <robert.jarzmik@free.fr>

**Description**: 显示地板（0 到 4）、保护区（0 或 1）是否被密钥锁定。每个 docg3 芯片（或地板）有 2 个保护区，可覆盖其任何部分，块对齐，称为 DPS。该保护嵌入了是否阻止读取、写入或两者的信息。结果为：

0 -> DPS 未被密钥锁定

1 -> DPS 被密钥锁定

**Users**: 目前未确定任何用户。

**What**: /sys/devices/platform/docg3/f[0-3]_dps[01]_protection_key

**Date**: 2011 年 11 月

**KernelVersion**: 3.3

**Contact**: Robert Jarzmik <robert.jarzmik@free.fr>

**Description**: 输入地板（0 到 4）、保护区（0 或 1）的保护密钥。每个 docg3 芯片（或地板）有 2 个保护区，可覆盖其任何部分，块对齐，称为 DPS。该保护嵌入了是否阻止读取、写入或两者的信息。保护密钥是 8 字节的字符串（值 0-255）。输入正确的值可切换锁定，可通过 f[0-3]_dps[01]_is_keylocked 观察。可能的值为：

- 8 字节

典型值为：

- "00000000"

- "12345678"

**Users**: 目前未确定任何用户。