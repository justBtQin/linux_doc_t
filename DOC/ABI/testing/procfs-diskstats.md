**What**：`/proc/diskstats`

**Date**：2008 年 2 月

**Contact**：Jerome Marchand <jmarchan@redhat.com>

**Description**：

`/proc/diskstats`文件显示块设备的 I/O 统计信息。每行包含以下 14 个字段：

```
1 - 主设备号
2 - 次设备号
3 - 设备名称
4 - 成功完成的读操作次数
5 - 合并的读操作次数
6 - 读取的扇区数
7 - 读操作花费的时间（毫秒）
8 - 完成的写操作次数
9 - 合并的写操作次数
10 - 写入的扇区数
11 - 写操作花费的时间（毫秒）
12 - 当前正在进行的 I/O 操作数量
13 - 进行 I/O 操作花费的时间（毫秒）
14 - 加权的进行 I/O 操作花费的时间（毫秒）
```

更多细节请参考 Documentation/iostats.txt 