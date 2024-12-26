<markdown_document>

# 快速入门

---------------------

- 在块设备部分选择数据包支持，在文件系统部分选择 UDF 支持。

- 编译并安装内核和模块，然后重新启动。

- 需要 udftools 包（pktsetup、mkudffs、cdrwtool）。从 http://sourceforge.net/projects/linux-udf/ 下载。

- 抓取一张新的 CD-RW 光盘并对其进行格式化（假设 CD-RW 是 hdc，请根据实际情况进行替换）：

```
# cdrwtool -d /dev/hdc -q
```

- 设置你的刻录机：

```
# pktsetup dev_name /dev/hdc
```

- 现在你可以挂载 /dev/pktcdvd/dev_name 并将文件复制到其中。享受吧！

```
# mount /dev/pktcdvd/dev_name /cdrom -t udf -o rw,noatime
```

## DVD-RW 介质的数据包写入

如果 DVD-RW 光盘处于所谓的“受限覆盖”模式，那么它们可以像 CD-RW 光盘一样进行写入。要将光盘置于受限覆盖模式，请运行：

```
# dvd+rw-format /dev/hdc
```

然后，你可以以与使用 CD-RW 光盘相同的方式使用该光盘：

```
# pktsetup dev_name /dev/hdc
# mount /dev/pktcdvd/dev_name /cdrom -t udf -o rw,noatime
```

## DVD+RW 介质的数据包写入

根据 DVD+RW 规范，支持 DVD+RW 光盘的驱动器应实现“具有 2KB 粒度的真正随机写入”，这意味着应该可以在这样的光盘上放置任何块大小 >= 2KB 的文件系统。例如，应该可以执行以下操作：

```
# dvd+rw-format /dev/hdc （仅在光盘从未格式化时需要）
# mkudffs /dev/hdc
# mount /dev/hdc /cdrom -t udf -o rw,noatime
```

然而，一些驱动器不遵循规范，期望主机在 32KB 边界处执行对齐写入。其他驱动器遵循规范，但如果写入不是 32KB 对齐，则会出现性能问题。

这两个问题都可以通过使用 pktcdvd 驱动程序来解决，该驱动程序始终生成对齐写入。

```
# dvd+rw-format /dev/hdc
# pktsetup dev_name /dev/hdc
# mkudffs /dev/pktcdvd/dev_name
# mount /dev/pktcdvd/dev_name /cdrom -t udf -o rw,noatime
```

## DVD-RAM 介质的数据包写入

DVD-RAM 光盘是随机可写的，因此不需要使用 pktcdvd 驱动程序。然而，使用 pktcdvd 驱动程序可以以与 DVD+RW 介质相同的方式提高性能。

## 注意事项

- CD-RW 介质通常不能被重写超过大约 1000 次，因此为了避免对介质造成不必要的磨损，你应该始终使用 noatime 挂载选项。

- 缺陷管理（即自动重新映射坏扇区）尚未实现，因此如果光盘磨损，你很可能会遇到至少一些文件系统损坏。

- 由于 pktcdvd 驱动程序使光盘看起来像一个具有 2KB 块大小的常规块设备，因此你可以在光盘上放置任何你喜欢的文件系统。例如，运行：

```
# /sbin/mke2fs /dev/pktcdvd/dev_name
```
以在光盘上创建 ext2 文件系统。

## 使用 pktcdvd sysfs 接口

自 Linux 2.6.20 以来，pktcdvd 模块具有 sysfs 接口，可以通过它进行控制。例如，“pktcdvd”工具使用此接口。（请参阅 http://tom.ist-im-web.de/download/pktcdvd ）

“pktcdvd”的工作方式类似于“pktsetup”，例如：

```
# pktcdvd -a dev_name /dev/hdc
# mkudffs /dev/pktcdvd/dev_name
# mount -t udf -o rw,noatime /dev/pktcdvd/dev_name /dvdram
# cp files /dvdram
# umount /dvdram
# pktcdvd -r dev_name
```

有关 sysfs 接口的描述，请查看文件：

```
Documentation/ABI/testing/sysfs-class-pktcdvd
```

## 使用 pktcdvd debugfs 接口

要以人类可读的形式读取 pktcdvd 设备信息，请执行：

```
# cat /sys/kernel/debug/pktcdvd/pktcdvd[0-7]/info
```

有关 debugfs 接口的描述，请查看文件：

```
Documentation/ABI/testing/debugfs-pktcdvd
```

## 链接

有关 DVD 写入的更多信息，请参阅 http://fy.chalmers.se/~appro/linux/DVD+RW/。

</markdown_document>