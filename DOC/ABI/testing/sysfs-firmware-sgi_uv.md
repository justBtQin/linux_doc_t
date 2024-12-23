**What**: `/sys/firmware/sgi_uv/`

**Date**: 八月 2008

**Contact**: Russ Anderson <rja@sgi.com>

**Description**:

    位于 `/sys/firmware/sgi_uv` 目录中的信息包含关于 SGI UV 平台的相关内容。

    在该目录下有多个文件：

        - `partition_id`

        - `coherence_id`

    `partition_id` 条目包含分区 ID。SGI UV 系统可以被分区为多个物理机器，每个分区运行操作系统的唯一副本。每个分区都有一个唯一的分区 ID。要显示分区 ID，可使用命令：

        - `cat /sys/firmware/sgi_uv/partition_id`

    `coherence_id` 条目包含一致性 ID。分区的 SGI UV 系统可以有一个或多个一致性域。一致性 ID 指示此分区所在的一致性域。要显示一致性 ID，可使用命令：

        - `cat /sys/firmware/sgi_uv/coherence_id`