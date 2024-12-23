Kernel initialisation parameters on ARM Linux

---------------------------------------------

以下文档描述了内核初始化参数结构，也称为“struct param_struct”，它用于大多数 ARM Linux 架构。

此结构用于将初始化参数从内核加载器传递到 Linux 内核本身，并且在内核初始化过程中可能是短暂的。一般来说，不应在 arch/arm/kernel/setup.c:setup_arch()之外引用它。

其中列出了许多参数，如下所述：

```
page_size
	此参数必须设置为机器的页面大小，并且将由内核检查。

nr_pages
	这是系统中内存的总页数。如果内存是分块的，那么这应该包含系统中的总页数。如果系统包含单独的 VRAM，此值不应包括此信息。

ramdisk_size
	此参数现已过时，不应使用。

flags
	各种内核标志，包括：
		位 0 - 1 = 以只读方式挂载根文件系统
		位 1 - 未使用
		位 2 - 0 = 加载 ramdisk
		位 3 - 0 = 提示 ramdisk

rootdev
	要挂载为根文件系统的设备的主/次设备号对。

video_num_cols
video_num_rows
	这两个一起描述了虚拟控制台或 VGA 控制台的字符大小。不应将它们用于任何其他目的。通常，将它们设置为标准 VGA 或 fbcon 显示的等效字符大小是一个好主意。这样可以确保所有启动消息都能正确显示。

video_x
video_y
	这描述了 VGA 控制台上光标的字符位置，否则未使用。（不应用于其他控制台类型，也不应用于其他目的）。

memc_control_reg
	用于 Acorn Archimedes 和基于 Acorn A5000 的机器的 MEMC 芯片控制寄存器。不同的架构可能会以不同的方式使用它。

sounddefault
	Acorn 机器上的默认声音设置。不同的架构可能会以不同的方式使用它。

adfsdrives
	ADFS/MFM 磁盘的数量。不同的架构可能会以不同的方式使用它。

bytes_per_char_h
bytes_per_char_v
	这些现已过时，不应使用。

pages_in_bank[4]
	系统内存每个块中的页数（用于 RiscPC）。这旨在用于从处理器的角度来看物理内存不连续的系统。

pages_in_vram
	VRAM 中的页数（用于 Acorn RiscPC）。如果无法从硬件获取视频 RAM 的大小，加载程序也可能会使用此值。

initrd_start
initrd_size
	这描述了初始 ramdisk 的内核虚拟起始地址和大小。

rd_start
	软盘上 ramdisk 映像的起始扇区地址。

system_rev
	系统修订号。

system_serial_low
system_serial_high
	系统 64 位序列号

mem_fclk_21285
	外部振荡器到 21285（桥接器）的速度，它控制内存总线、定时器和串口的速度。根据 CPU 的速度，其值可以在 0-66 MHz 之间。如果未传递参数或传递了零值，则在 21285 架构上默认值为 50 MHz。

paths[8][128]
	这些现已过时，不应使用。

commandline
	内核命令行参数。详细信息可在其他地方找到。
```