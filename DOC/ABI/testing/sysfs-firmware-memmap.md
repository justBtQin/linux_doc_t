**What**: /sys/firmware/memmap/

**Date**: 六月 2008

**Contact**: Bernhard Walle <bernhard.walle@gmx.de>

**Description**:

在所有平台上，固件提供了一个内存映射，内核会读取该映射。来自该内存映射的资源在内核资源树中注册，并通过 /proc/iomem（以及其他资源）暴露给用户空间。

然而，在大多数体系结构中，内核本身会在之后修改固件提供的内存映射，要么是因为内核将该内存映射与其他信息合并，要么只是因为用户通过命令行覆盖了该内存映射。

kexec 需要原始的固件提供的内存映射来设置要使用 kexec 启动的内核的参数段。此外，原始内存映射对于调试很有用。出于这个原因，/sys/firmware/memmap 是一个向用户空间提供原始内存映射的接口。

其结构如下：在 /sys/firmware/memmap 下有子目录，其名称为条目编号：

    /sys/firmware/memmap/0

    /sys/firmware/memmap/1

    /sys/firmware/memmap/2

    /sys/firmware/memmap/3

    …

最大数量取决于固件提供的内存映射条目的数量。顺序只是固件提供的顺序。

每个目录包含三个文件：

start：起始地址（带有“0x”前缀的十六进制数）。

end：结束地址，包括在内（无论固件提供的是包含还是排他范围）。

type：条目的类型为字符串。以下是有效类型的列表。

例如：

    /sys/firmware/memmap/0/start

    /sys/firmware/memmap/0/end

    /sys/firmware/memmap/0/type

    /sys/firmware/memmap/1/start

    …

当前存在以下类型：

- 系统内存

- ACPI 表

- ACPI 非易失性存储

- 保留

以下 shell 代码片段可用于以人类可读的格式显示该内存映射：

-------------------- 8< ----------------------------------------

#!/bin/bash

cd /sys/firmware/memmap

for dir in * ; do

    start=$(cat $dir/start)

    end=$(cat $dir/end)

    type=$(cat $dir/type)

    printf "%016x-%016x (%s)\n" $start $[ $end +1] "$type"

done

-------------------- >8 ----------------------------------------