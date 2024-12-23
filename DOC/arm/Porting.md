取自 http://lists.arm.linux.org.uk/pipermail/linux-arm-kernel/2001-July/004064.html 的列表存档

# 初始定义

-------------------

以下符号定义依赖于你知道 `__virt_to_phys()` 为你的机器所做的转换。这个宏将传递的虚拟地址转换为物理地址。通常，它只是：

`phys = virt - PAGE_OFFSET + PHYS_OFFSET`

# 解压缩器符号

--------------------

`ZTEXTADDR`

解压缩器的起始地址。在这里谈论虚拟或物理地址没有意义，因为在调用解压缩器代码时，MMU 将关闭。你通常在这个地址调用内核以开始引导它。这不一定位于 RAM 中，它可以在闪存或其他只读或可读写寻址的介质中。

`ZBSSADDR`

解压缩器的零初始化工作区的起始地址。此地址必须指向 RAM。解压缩器将为你初始化这个区域为零。同样，MMU 将关闭。

`ZRELADDR`

这是解压缩后的内核将被写入并最终执行的地址。以下约束必须有效：

`__virt_to_phys(TEXTADDR) == ZRELADDR`

内核的初始部分经过精心编码以实现位置无关性。

`INITRD_PHYS`

放置初始 RAM 磁盘的物理地址。仅在你使用 `bootpImage` 东西时相关（仅在旧的 `struct param_struct` 上有效）。

`INITRD_VIRT`

初始 RAM 磁盘的虚拟地址。以下约束必须有效：

`__virt_to_phys(INITRD_VIRT) == INITRD_PHYS`

`PARAMS_PHYS`

`struct param_struct` 或标签列表的物理地址，为内核提供关于其执行环境的各种参数。

# 内核符号

--------------

`PHYS_OFFSET`

第一部分 RAM 的物理起始地址。

`PAGE_OFFSET`

第一部分 RAM 的虚拟起始地址。在内核引导阶段，虚拟地址 `PAGE_OFFSET` 将被映射到物理地址 `PHYS_OFFSET`，以及你提供的任何其他映射。此值应与 `TASK_SIZE` 相同。

`TASK_SIZE`

用户进程的最大大小（以字节为单位）。由于用户空间始终从零开始，这是用户进程可以访问的最大地址 + 1。用户空间堆栈从这个地址向下增长。

任何低于 `TASK_SIZE` 的虚拟地址都被视为用户进程区域，因此由内核在每个进程的基础上动态管理。我将此称为用户段。

高于 `TASK_SIZE` 的任何内容对所有进程都是通用的。我将此称为内核段。

（换句话说，你不能将 IO 映射放在 `TASK_SIZE` 以下，因此也不能放在 `PAGE_OFFSET` 以下）。

`TEXTADDR`

内核的虚拟起始地址，通常为 `PAGE_OFFSET + 0x8000`。这是内核映像最终所在的位置。对于最新的内核，它必须位于 128MB 区域的 32768 字节处。以前的内核在此处设置了 256MB 的限制。

`DATAADDR`

内核数据段的虚拟地址。在使用解压缩器时不得定义。

`VMALLOC_START`

`VMALLOC_END`

包围 `vmalloc()` 区域的虚拟地址。在这个区域中不能有任何静态映射；`vmalloc` 将覆盖它们。这些地址也必须在内核段中（见上文）。通常，`vmalloc()` 区域从最后一个虚拟 RAM 地址（使用变量 `high_memory` 找到）上方的 `VMALLOC_OFFSET` 字节开始。

`VMALLOC_OFFSET`

通常合并到 `VMALLOC_START` 中的偏移量，以在虚拟 RAM 和 `vmalloc` 区域之间提供一个空洞。我们这样做是为了允许捕获越界内存访问（例如，某些东西写入映射内存映射的末尾）。通常设置为 8MB。

# 架构特定宏

----------------------------

`BOOT_MEM(pram,pio,vio)`

`pram` 指定 RAM 的物理起始地址。必须始终存在，并且应该与 `PHYS_OFFSET` 相同。

`pio` 是包含用于 `arch/arm/kernel/debug-armv.S` 中的调试宏的 8MB 区域的物理地址。

`vio` 是 8MB 调试区域的虚拟地址。

预计调试区域将由后续代码中的架构特定代码重新初始化（通过 `MAPIO` 函数）。

`BOOT_PARAMS`

与 `PARAMS_PHYS` 相同，请参阅 `PARAMS_PHYS`。

`FIXUP(func)`

在内存子系统初始化之前运行的特定于机器的修复程序。

`MAPIO(func)`

映射 IO 区域（包括上面的调试区域）的特定于机器的函数。

`INITIRQ(func)`

初始化中断的特定于机器的函数。