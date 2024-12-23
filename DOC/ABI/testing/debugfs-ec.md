**What**: `/sys/kernel/debug/ec/*/{gpe,use_global_lock,io}`

**Date**: 七月 2010

**Contact**: Thomas Renninger <trenn@suse.de>

**Description**:

一般信息，如哪个通用可编程中断控制器（GPE）被分配给了嵌入式控制器（EC），以及全局锁是否应该被使用。知道了 EC GPE，就可以在这里观察与 EC 相关的硬件事件的数量（XY -> /sys/kernel/debug/ec/*/gpe 中的 GPE 编号）：

/sys/firmware/acpi/interrupts/gpeXY

`io`文件是二进制的，位于这里的用户空间工具：

ftp://ftp.suse.com/pub/people/trenn/sources/ec/

应该被用于读出 256 个嵌入式控制器寄存器或向其写入。

注意：如果你不知道自己在做什么，不要向嵌入式控制器写入！之后重新启动也是一个好主意。这可能会影响你的机器的冷却方式，并且在你进行错误写入后，风扇可能不会再次启动。