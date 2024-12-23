# Kernel Memory Layout on ARM Linux

**Russell King <rmk@arm.linux.org.uk>**

**November 17, 2005 (2.6.15)**

本文档描述了 Linux 内核用于 ARM 处理器的虚拟内存布局。它指出了哪些区域可供平台使用，哪些区域由通用代码使用。

ARM CPU 最多能够寻址 4GB 的虚拟内存空间，并且必须在用户空间进程、内核和硬件设备之间共享。

随着 ARM 架构的成熟，有必要为新设施保留某些 VM 空间区域；因此，随着时间的推移，本文档可能会保留更多的 VM 空间。

|起始地址|结束地址|用途|
|--|--|--|
|ffff8000|ffffffff|copy_user_page / clear_user_page 使用。<br>对于 SA11xx 和 Xscale，此区域用于设置微型缓存映射。|
|ffff4000|ffffffff|ARMv6 及更高版本 CPU 上的缓存别名。|
|ffff1000|ffff7fff|保留。<br>平台不得使用此地址范围。|
|ffff0000|ffff0fff|CPU 向量页。<br>如果 CPU 支持向量重定位（控制寄存器 V 位），则 CPU 向量在此处映射。|
|fffe0000|fffeffff|XScale 缓存刷新区域。<br>此区域用于在 proc-xscale.S 中刷新整个数据缓存。（XScale 没有 TCM。）|
|fffe8000|fffeffff|对于在 CPU 内部安装了 DTCM 的平台，DTCM 映射区域。|
|fffe0000|fffe7fff|对于在 CPU 内部安装了 ITCM 的平台，ITCM 映射区域。|
|ffc00000|ffefffff|固定映射区域。<br>fix_to_virt() 提供的地址将位于此处。|
|fee00000|feffffff|PCI I/O 空间的映射。<br>这是 vmalloc 空间内的静态映射。|
|VMALLOC_START|VMALLOC_END - 1|vmalloc() / ioremap() 空间。<br>vmalloc/ioremap 返回的内存将动态放置在此区域。<br>通过 iotable_init() 在此处也定位了特定于机器的静态映射。<br>VMALLOC_START 基于 high_memory 变量的值，VMALLOC_END 等于 0xff000000。|
|PAGE_OFFSET|high_memory - 1|内核直接映射的 RAM 区域。<br>此区域映射了平台的 RAM，通常以 1:1 的关系映射所有平台 RAM。|
|PKMAP_BASE|PAGE_OFFSET - 1|永久内核映射<br>将 HIGHMEM 页映射到内核空间的一种方式。|
|MODULES_VADDR|MODULES_END - 1|内核模块空间<br>通过 insmod 插入的内核模块在此处使用动态映射放置。|
|00001000|TASK_SIZE - 1|用户空间映射<br>每个线程的映射通过 mmap() 系统调用在此处放置。|
|00000000|00000fff|CPU 向量页 / 空指针陷阱<br>不支持向量重映射的 CPU 将其向量页放置在此处。内核和用户空间的空指针引用也通过此映射捕获。|

请注意，与上述区域冲突的映射可能会导致无法启动的内核，或者可能导致内核在运行时（最终）崩溃。

由于未来的 CPU 可能会影响内核映射布局，用户程序不得访问在其 0x0001000 到 TASK_SIZE 地址范围之外未映射的任何内存。如果他们希望访问这些区域，他们必须使用 open() 和 mmap() 设置自己的映射。