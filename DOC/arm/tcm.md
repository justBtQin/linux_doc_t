在 Linux 中处理 ARM TCM（紧密耦合内存）

----

作者：Linus Walleij <linus.walleij@stericsson.com>

一些 ARM SoC 具有所谓的 TCM（紧密耦合内存）。这通常只是 ARM 处理器内部的几（4 - 64）KiB 的 RAM。

由于嵌入在 CPU 内部，TCM 具有哈佛架构，因此有一个 ITCM（指令 TCM）和一个 DTCM（数据 TCM）。DTCM 不能包含任何指令，但 ITCM 实际上可以包含数据。DTCM 或 ITCM 的大小至少为 4KiB，因此典型的最小配置是 4KiB ITCM 和 4KiB DTCM。

ARM CPU 有特殊的寄存器来读出 TCM 内存的状态、物理位置和大小。arch/arm/include/asm/cputype.h 定义了一个 CPUID_TCM 寄存器，您可以从系统控制协处理器中读出它。ARM 的文档可以在 http://infocenter.arm.com 找到，搜索“TCM 状态寄存器”以查看所有 CPU 的文档。读取此寄存器，您可以确定机器中是否存在 ITCM（位 1 - 0）和/或 DTCM（位 17 - 16）。

还有一个 TCM 区域寄存器（在 ARM 网站上搜索“TCM 区域寄存器”），可以在运行时报告和修改 TCM 内存的位置和大小。这用于读出和修改 TCM 的位置和大小。请注意，这不是 MMU 表：您实际上是在移动 TCM 的物理位置。在放置它的地方，它将屏蔽 CPU 下面的任何物理 RAM，因此通常明智的做法是不要使任何物理 RAM 与 TCM 重叠。

然后可以使用 MMU 将 TCM 内存重新映射到另一个地址，但请注意，TCM 经常用于 MMU 关闭的情况。为了避免混淆，当前的 Linux 实现将 TCM 1:1 从物理内存映射到内核指定的虚拟内存中。目前，Linux 将 ITCM 映射到 0xfffe0000 及以上，将 DTCM 映射到 0xfffe8000 及以上，支持最大 32KiB 的 ITCM 和 32KiB 的 DTCM。

区域寄存器的较新版本还支持将这些 TCM 分成两个单独的存储体，例如，一个 8KiB 的 ITCM 被分成两个具有自己的控制寄存器的 4KiB 存储体。其思想是能够锁定和隐藏其中一个存储体，以供安全世界（TrustZone）使用。

TCM 用于以下几个方面：

- 需要确定性定时且不能等待缓存未命中的 FIQ 和其他中断处理程序。

- 所有外部 RAM 都设置为自刷新保持模式的空闲循环，因此只有片上 RAM 可被 CPU 访问，然后我们在 ITCM 中挂起等待中断。

- 其他意味着关闭或重新配置外部 RAM 控制器的操作。

在 <asm/tcm.h> 中有一个在 ARM 架构上使用 TCM 的接口。使用此接口，可以：

- 定义 ITCM 和 DTCM 的物理地址和大小。

- 标记要编译到 ITCM 中的函数。

- 标记要分配到 DTCM 和 ITCM 的数据和常量。

- 使用 gen_pool_create() 和 gen_pool_add() 将剩余的 TCM RAM 添加到特殊的分配池中，并为该内存提供 tcm_alloc() 和 tcm_free()。这样的堆对于在关闭设备电源域时保存设备状态等非常有用。

具有 TCM 内存的机器应从 arch/arm/Kconfig 中选择 HAVE_TCM 。需要使用 TCM 的代码应#include <asm/tcm.h>。

可以像这样标记进入 itcm 的函数：

int __tcmfunc foo(int bar);

由于这些被标记为长调用，并且您可能希望在 TCM 内部本地调用函数而不浪费空间，还有 __tcmlocalfunc 前缀，它将使调用相对。

可以像这样标记进入 dtcm 的变量：

int __tcmdata foo;

可以像这样标记常量：

int __tcmconst foo;

要将汇编程序放入 TCM，只需分别使用.section ".tcm.text" 或.section ".tcm.data"。

示例代码：

```c
#include <asm/tcm.h>
/* 未初始化数据 */
static u32 __tcmdata tcmvar;
/* 已初始化数据 */
static u32 __tcmdata tcmassigned = 0x2BADBABEU;
/* 常量 */
static const u32 __tcmconst tcmconst = 0xCAFEBABEU;
static void __tcmlocalfunc tcm_to_tcm(void)
{
	int i;
	for (i = 0; i < 100; i++)
		tcmvar ++;
}
static void __tcmfunc hello_tcm(void)
{
	/* 一些在 ITCM 中运行的抽象代码 */
	int i;
	for (i = 0; i < 100; i++) {
		tcmvar ++;
	}
	tcm_to_tcm();
}
static void __init test_tcm(void)
{
	u32 *tcmem;
	int i;
	hello_tcm();
	printk("Hello TCM 从 ITCM RAM 执行\n");
	printk("测试运行的 TCM 变量：%u @ %p\n", tcmvar, &tcmvar);
	tcmvar = 0xDEADBEEFU;
	printk("TCM 变量：0x%x @ %p\n", tcmvar, &tcmvar);
	printk("TCM 已分配变量：0x%x @ %p\n", tcmassigned, &tcmassigned);
	printk("TCM 常量：0x%x @ %p\n", tcmconst, &tcmconst);
	/* 从池中分配一些 TCM 内存 */
	tcmem = tcm_alloc(20);
	if (tcmem) {
		printk("分配了 20 字节的 TCM @ %p\n", tcmem);
		tcmem[0] = 0xDEADBEEFU;
		tcmem[1] = 0x2BADBABEU;
		tcmem[2] = 0xCAFEBABEU;
		tcmem[3] = 0xDEADBEEFU;
		tcmem[4] = 0x2BADBABEU;
		for (i = 0; i < 5; i++)
			printk("TCM tcmem[%d] = %08x\n", i, tcmem[i]);
		tcm_free(tcmem, 20);
	}
}
```