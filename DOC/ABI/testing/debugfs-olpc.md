**What**: `/sys/kernel/debug/olpc-ec/cmd`

**Date**: Dec 2011

**KernelVersion**: 3.4

**Contact**: devel@lists.laptop.org

**Description**:

一个用于执行 OLPC 嵌入式控制器命令并读取其响应的通用接口。

要执行一个命令，以以下格式写入数据：CC:N A A A A

CC 是（十六进制）命令，N 是预期回复字节的数量，而 A A A A 是可选的（十六进制）参数。

要读取响应（如果有），在执行命令后从通用节点读取。十六进制回复字节将被返回，*无论它们是否来自*紧接在前的命令。