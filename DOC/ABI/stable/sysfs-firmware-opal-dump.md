**What**: `/sys/firmware/opal/dump`

**Date**: Feb 2014

**Contact**: Stewart Smith <stewart@linux.vnet.ibm.com>

**Description**:

  - 此目录通过 OPAL 固件接口公开了与 FSP 和平台转储进行交互的接口。

  - 此仅适用于 powerpc/powernv 平台。

  - `initiate_dump`：向其写入“1”时，我们将启动转储。读取此文件以获取支持的命令。

  - `0xXX-0xYYYY`：用于转储类型为 0xXX 且 ID 为 0xYYYY（十六进制）的目录。此目录的名称不应依赖于这种格式，仅应确保在所有转储中是唯一的。要确定转储的类型和 ID，请使用`id`和`type`文件。不要依赖任何特定大小的转储类型或转储 ID。

  - 每个转储都有以下文件：

    - `id`：转储 ID 的 ASCII 表示形式（十六进制，例如“0x01”）。

    - `type`：转储类型的 ASCII 表示形式，格式为“0x%x %s”，其中 ID 为十六进制，转储类型的描述（或“unknown”）。当无法从固件获取类型时，使用类型“0xffffffff unknown”。例如“0x02 System/Platform Dump”。

    - `dump`：包含转储的二进制文件。转储的大小就是此文件的大小。

  - `acknowledge`：向其写入“ack”时，我们将确认已将转储检索到服务处理器。然后它将删除转储，使转储不可访问。读取此文件将获取支持的操作列表。