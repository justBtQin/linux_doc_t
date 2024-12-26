<markdown_document>

# Linux on the CRIS architecture

这是将 Linux 移植到 Axis Communications ETRAX 100LX、ETRAX FS 和 ARTPEC - 3 嵌入式网络 CPU 上的版本。

关于 CRIS 和 ETRAX 的更多信息，请参阅以下内容。为了编译此版本，您需要一个支持 ETRAX 芯片系列的 gcc 版本。有关如何下载编译器和在为 ETRAX 平台构建和启动软件时有用的其他工具的更多信息，请参阅此链接：

http://developer.axis.com/wiki/doku.php?id=axis:install-howto-2_20

什么是 CRIS？

--------------

CRIS 是“精简指令集代码”的缩写。它是 Axis Communication AB 系列嵌入式网络 CPU（称为 ETRAX）中的 CPU 架构。

ETRAX 100LX 芯片

--------------------

如需参考，请参阅以下链接：

http://www.axis.com/products/dev_etrax_100lx/index.htm

ETRAX 100LX 是一款 100 MIPS 处理器，具有 8kB 缓存、MMU 和广泛的内置接口，所有接口都具有现代的分散/收集 DMA。

内存接口：

- SRAM

- NOR - flash/ROM

- EDO 或页模式 DRAM

- SDRAM

I/O 接口：

- 一个 10/100 Mbit/s 以太网控制器

- 四个串行端口（最高 6 Mbit/s）

- 两个用于多媒体编解码器等的同步串行端口

- USB 主机控制器和 USB 从设备

- ATA

- SCSI

- 两个并行端口

- 两个通用 8 位端口

（由于芯片引脚复用，并非所有接口都可同时使用）

ETRAX 100LX 是 CRISv10 架构。

ETRAX FS 和 ARTPEC - 3 芯片

-------------------------------

ETRAX FS 是一款 200MHz 的 32 位 RISC 处理器，具有片上 16kB 的 I - cache 和 16kB 的 D - cache，以及广泛的设备接口，包括多个高速串行端口和集成的 USB 1.1 PHY。

ARTPEC - 3 是 ETRAX FS 的变体，具有 Axis Communications 网络摄像机使用的附加 IO 单元。

有关更多信息，请参阅以下链接：

http://www.axis.com/products/dev_etrax_fs/index.htm

ETRAX FS 和 ARTPEC - 3 都是 CRISv32 架构。

启动日志

-------

仅作为示例，这是在带有 ETRAX 100LX 的板上启动 Linux 2.4 的调试输出。显示的 BogoMIPS 值小了 5 倍：）最后，您可以看到一些用户模式程序启动，如 telnet 和 ftp 守护进程。

```

Linux version 2.4.1 (bjornw@godzilla.axis.se) (gcc version 2.96 20000427 (experimental)) #207 Wed Feb 21 15:48:15 CET 2001
ROM fs in RAM, size 1376256 bytes
Setting up paging and the MMU.
On node 0 totalpages: 2048
zone(0): 2048 pages.
zone(1): 0 pages.
zone(2): 0 pages.
Linux/CRIS port on ETRAX 100LX (c) 2001 Axis Communications AB
Kernel command line: 
Calibrating delay loop... 19.91 BogoMIPS
Memory: 13872k/16384k available (587k kernel code, 2512k reserved, 44k data, 24k init)
kmem_create: Forcing size word alignment - vm_area_struct
kmem_create: Forcing size word alignment - filp
Dentry-cache hash table entries: 2048 (order: 1, 16384 bytes)
Buffer-cache hash table entries: 2048 (order: 0, 8192 bytes)
Page-cache hash table entries: 2048 (order: 0, 8192 bytes)
kmem_create: Forcing size word alignment - kiobuf
kmem_create: Forcing size word alignment - bdev_cache
Inode-cache hash table entries: 1024 (order: 0, 8192 bytes)
kmem_create: Forcing size word alignment - inode_cache
POSIX conformance testing by UNIFIX
Linux NET4.0 for Linux 2.4
Based upon Swansea University Computer Society NET3.039
Starting kswapd v1.8
kmem_create: Forcing size word alignment - file lock cache
kmem_create: Forcing size word alignment - blkdev_requests
block: queued sectors max/low 9109kB/3036kB, 64 slots per queue
ETRAX 100LX 10/100MBit ethernet v2.0 (c) 2000 Axis Communications AB
eth0 initialized
eth0: changed MAC to 00:40:8C:CD:00:00
ETRAX 100LX serial-driver $Revision: 1.7 $, (c) 2000 Axis Communications AB
ttyS0 at 0xb0000060 is a builtin UART with DMA
ttyS1 at 0xb0000068 is a builtin UART with DMA
ttyS2 at 0xb0000070 is a builtin UART with DMA
ttyS3 at 0xb0000078 is a builtin UART with DMA
Axis flash mapping: 200000 at 50000000
Axis flash: Found 1 x16 CFI device at 0x0 in 16 bit mode
 Amd/Fujitsu Extended Query Table v1.0 at 0x0040
Axis flash: JEDEC Device ID is 0xC4. Assuming broken CFI table.
Axis flash: Swapping erase regions for broken CFI table.
number of CFI chips: 1
 Using default partition table
I2C driver v2.2, (c) 1999-2001 Axis Communications AB
ETRAX 100LX GPIO driver v2.1, (c) 2001 Axis Communications AB
NET4: Linux TCP/IP 1.0 for NET4.0
IP Protocols: ICMP, UDP, TCP
kmem_create: Forcing size word alignment - ip_dst_cache
IP: routing cache hash table of 1024 buckets, 8Kbytes
TCP: Hash tables configured (established 2048 bind 2048)
NET4: Unix domain sockets 1.0/SMP for Linux NET4.0.
VFS: Mounted root (cramfs filesystem) readonly.
Init starts up...
Mounted none on /proc ok.
Setting up eth0 with ip 10.13.9.116 and mac 00:40:8c:18:04:60
eth0: changed MAC to 00:40:8C:18:04:60
Setting up lo with ip 127.0.0.1
Default gateway is 10.13.9.1
Hostname is bbox1
Telnetd starting, using port 23.
  using /bin/sash as shell.
sftpd[15]: sftpd $Revision: 1.7 $ starting up
```

以下是一些 /proc 条目的外观：

```
17# cd /proc
17# cat cpuinfo
cpu             : CRIS
cpu revision    : 10
cpu model       : ETRAX 100LX
cache size      : 8 kB
fpu             : no
mmu             : yes
ethernet        : 10/100 Mbps
token ring      : no
scsi            : yes
ata             : yes
usb             : yes
bogomips        : 99.84
```

17# cat meminfo

```
        total:    used:    free:  shared: buffers:  cached:
Mem:   7028736   925696  6103040   114688        0   229376
Swap:        0        0        0
MemTotal:         6864 kB
MemFree:          5960 kB
MemShared:         112 kB
Buffers:             0 kB
Cached:            224 kB
Active:            224 kB
Inact_dirty:         0 kB
Inact_clean:         0 kB
Inact_target:        0 kB
HighTotal:           0 kB
HighFree:            0 kB
LowTotal:         6864 kB
LowFree:          5960 kB
SwapTotal:           0 kB
SwapFree:            0 kB
```
17# ls -l /bin

-rwxr-xr-x 1 342 100 10356 1 月 01 日 00:00 ifconfig

-rwxr-xr-x 1 342 100 17548 1 月 01 日 00:00 init

-rwxr-xr-x 1 342 100 9488 1 月 01 日 00:00 route

-rwxr-xr-x 1 342 100 46036 1 月 01 日 00:00 sftpd

-rwxr-xr-x 1 342 100 48104 1 月 01 日 00:00 sh

-rwxr-xr-x 1 342 100 16252 1 月 01 日 00:00 telnetd

</markdown_document>