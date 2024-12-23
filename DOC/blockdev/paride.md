**Linux 和并行端口 IDE 设备**

PARIDE v1.03  (c) 1997 - 8  Grant Guenther <grant@torque.net>

# 1. 引言

由于个人计算机的并行端口接口的简单性和近乎普遍性，许多外部设备，如便携式硬盘、CD-ROM、LS - 120 和磁带驱动器，使用并行端口连接到其主机计算机。虽然一些设备（特别是扫描仪）使用临时方法通过并行端口接口传递命令和数据，但大多数外部设备实际上与内部型号相同，只是添加了一个并行端口适配器芯片。一些原始的并行端口适配器只不过是多路复用 SCSI 总线的机制。（ZIP 驱动器中使用的 Iomega PPA - 3 适配器就是这种方法的一个例子）。然而，大多数当前设计采用了不同的方法。适配器芯片在外部设备中复制一个小型 ISA 或 IDE 总线，通信协议提供用于读取和写入设备寄存器的操作，以及数据块传输功能。有时，通过并行电缆寻址的设备是一个标准的 SCSI 控制器，如 NCR 5380。“ditto”系列外部磁带驱动器使用 ISA 复制器连接软盘控制器，然后连接到软盘 - 磁带机制。然而，绝大多数外部并行端口设备现在基于标准的 IDE 类型设备，不需要中间控制器。例如，如果打开一个并行端口 CD - ROM 驱动器，会发现一个标准的 ATAPI CD - ROM 驱动器、一个电源和一个将标准 PC 并行端口电缆和标准 IDE 电缆互连的单个适配器。通常可以用任何其他使用 IDE 接口的设备交换 CD - ROM 设备。

本文档描述了 Linux 对并行端口 IDE 设备的支持。它不涵盖并行端口 SCSI 设备、“ditto”磁带驱动器或扫描仪。并行端口 IDE 子系统支持许多不同的设备，包括：

- MicroSolutions 背包式 CD - ROM

- MicroSolutions 背包式 PD/CD

- MicroSolutions 背包式硬盘

- MicroSolutions 背包式 8000t 磁带驱动器

- SyQuest EZ - 135、EZ - 230 和 SparQ 驱动器

- Avatar Shark

- Imation Superdisk LS - 120

- Maxell Superdisk LS - 120

- FreeCom Power CD

- Hewlett - Packard 5GB 和 8GB 磁带驱动器

- Hewlett - Packard 7100 和 7200 CD - RW 驱动器

以及市场上的大多数克隆和无名产品。为了支持如此广泛的设备，PARIDE，并行端口 IDE 子系统，实际上分为三个部分。有一个基本的 paride 模块，它提供一个注册表和一些用于访问并行端口的通用方法。第二个组件是一组针对每种不同类型支持设备的高级驱动程序：

- pd：IDE 磁盘

- pcd：ATAPI CD - ROM

- pf：ATAPI 磁盘

- pt：ATAPI 磁带

- pg：ATAPI 通用

（目前，pg 驱动程序仅用于 CD - R 驱动器）。高级驱动程序根据相关标准运行。PARIDE 的第三个组件是一组针对每个并行端口 IDE 适配器芯片的低级协议驱动程序。由于来自世界各地的 Linux 用户的兴趣和鼓励，几乎所有已知的适配器协议都有支持：

- aten：ATEN EH - 100 （HK）

- bpck：MicroSolutions 背包式 （US）

- comm：DataStor（旧型）“通勤者”适配器 （TW）

- dstr：DataStor EP - 2000 （TW）

- epat：Shuttle EPAT （UK）

- epia：Shuttle EPIA （UK）

- fit2：FIT TD - 2000 （US）

- fit3：FIT TD - 3000 （US）

- friq：Freecom IQ 电缆 （DE）

- frpw：Freecom Power （DE）

- kbic：KingByte KBIC - 951A 和 KBIC - 971A （TW）

- ktti：KT Technology PHd 适配器 （SG）

- on20：OnSpec 90c20 （US）

- on26：OnSpec 90c26 （US）

# 2. 使用 PARIDE 子系统

在配置 Linux 内核时，您可以选择将 PARIDE 驱动程序构建到内核中，也可以将它们构建为模块。在任何一种情况下，您都需要选择“并行端口 IDE 设备支持”以及至少一个高级驱动程序和至少一个并行端口通信协议。如果您不知道您的驱动器中使用的是哪种并行端口适配器，您可以从检查 DOS 安装软盘上的文件名和任何文本文件开始。或者，您可以查看适配器芯片本身的标记。这通常足以识别正确的设备。

实际上，您可以选择所有协议模块，并让 PARIDE 子系统为您尝试所有协议。对于上述“品牌”产品，以下是您将使用的协议和高级驱动程序：

|制造商|型号|驱动程序|协议|

|--|--|--|--|

|MicroSolutions|CD - ROM|pcd|bpck|

|MicroSolutions|PD 驱动器|pf|bpck|

|MicroSolutions|硬盘|pd|bpck|

|MicroSolutions|8000t 磁带|pt|bpck|

|SyQuest|EZ、SparQ|pd|epat|

|Imation|Superdisk|pf|epat|

|Maxell|Superdisk|pf|friq|

|Avatar|Shark|pd|epat|

|FreeCom|CD - ROM|pcd|frpw|

|Hewlett - Packard|5GB 磁带|pt|epat|

|Hewlett - Packard|7200e（CD）|pcd|epat|

|Hewlett - Packard|7200e（CD - R）|pg|epat|

## 2.1 配置内置驱动程序

我们建议您先了解驱动程序的工作原理以及如何将它们配置为可加载模块，然后再尝试编译包含这些驱动程序的内核。如果您将所有的 PARIDE 支持直接构建到内核中，并且只有一个并行端口 IDE 设备，您的内核应该会自动为您找到它。如果您有多个设备，您可能需要向引导加载程序（例如：LILO）提供一些命令行选项，如何做到这一点超出了本文档的范围。高级驱动程序接受一些命令行参数，所有这些参数都在 linux/drivers/block/paride 的源文件中有文档记录。默认情况下，每个驱动程序将自动尝试它可以找到的所有并行端口和已安装的所有协议类型，直到找到一个并行端口 IDE 适配器。一旦找到一个，探测就会停止。因此，如果您有多个设备，您需要告诉驱动程序如何识别它们。这需要指定端口地址、协议标识号，对于某些设备，还需要指定驱动器的链 ID。在系统引导期间，一些消息会显示在控制台上。像所有这样的消息一样，它们可以使用“dmesg”命令进行查看。在这些消息中，会有一些类似的行：

paride：bpck 注册为协议 0

paride：epat 注册为协议 1

这些数字在您构建具有不同协议选择的新内核之前将始终相同。您应该记录这些数字，因为您需要它们来识别设备。

例如，假设您有一个单元 ID 号为 36 的 MicroSolutions PD/CD 驱动器连接到 0x378 的并行端口，一个连接到 PD/CD 驱动器的链式端口的 SyQuest EZ - 135 和一个连接到 0x278 端口的 Imation Superdisk。您可以在引导命令中提供以下选项：

pd.drive0 = 0x378,1 pf.drive0 = 0x278,1 pf.drive1 = 0x378,0,36

在最后一个选项中，pf.drive1 配置设备 /dev/pf1，0x378 是并行端口基地址，0 是协议注册号，36 是链 ID。请注意：尽管 PARIDE 在包含“并行端口支持”选项所包含的 PARPORT 并行端口共享系统时可以工作，也可以不包含该系统工作，但如果您想在同一并行端口上使用设备链，则必须包含并启用 PARPORT。

## 2.2 将 PARIDE 作为模块加载和配置

如果将 PARIDE 驱动程序用作可加载内核模块，将更容易且更简单地理解它们。

注意 1：不建议初学者使用这些驱动程序与“kerneld”自动模块加载系统一起使用，此处未记录相关内容。

注意 2：如果将 PARPORT 支持构建为可加载模块，则 PARIDE 也必须构建为可加载模块，并且必须在加载 PARIDE 模块之前加载 PARPORT。

要使用 PARIDE，您必须首先执行：

`insmod paride`

这将加载一个基础模块，该模块为协议提供注册表等任务。

然后，加载您认为可能需要的尽可能多的协议模块。加载每个模块时，它将注册它支持的协议，并将日志消息打印到您的内核日志文件和控制台。例如：

`# insmod epat`

`paride: epat 注册为协议 0`

`# insmod kbic`

`paride: k951 注册为协议 1`

`paride: k971 注册为协议 2`

最后，您可以为您连接的每种设备加载高级驱动程序。默认情况下，每个驱动程序将自动探测单个设备，但您可以通过在加载驱动程序时提供其单独的坐标来支持最多四个类似的设备。

例如，如果您有两个无名 CD-ROM 驱动器，都使用 KingByte KBIC-951A 适配器，一个在端口 0x378，另一个在 0x3bc，您可以执行以下命令：

`# insmod pcd drive0=0x378,1 drive1=0x3bc,1`

对于大多数适配器，提供端口地址和协议号就足够了，但请查看 linux/drivers/block/paride 中的源文件以获取更多信息。（希望有一天有人会编写一些手册页！）

另一个示例，当安装 PARPORT 并且将 SyQuest EZ-135 连接到端口 0x378 时会发生以下情况：

`# insmod paride`

`paride: 版本 1.0 已安装`

`# insmod epat`

`paride: epat 注册为协议 0`

`# insmod pd`

`pd: pd 版本 1.0，主设备号 45，簇 64，优先级 0`

`pda: 在 0x378 处共享 parport1`

`pda: epat 1.0，Shuttle EPAT 芯片 c3 在 0x378，模式 5（EPP-32），延迟 1`

`pda: SyQuest EZ135A，262144 块 [128M]，（512/16/32），可移动介质`

`pda: pda1`

请注意，最后一行是通用分区表扫描器的输出 - 在这种情况下，它报告已找到一个带有一个分区的磁盘。

## 2.3 使用 PARIDE 设备

一旦加载了驱动程序，您就可以以与传统对应设备相同的方式访问 PARIDE 设备。您可能需要创建“设备特殊文件”。这是一个简单的脚本，您可以将其剪切到文件中并执行：

```bash
#!/bin/bash
#
# mkd -- 一个为 PARIDE 子系统创建设备特殊文件的脚本
#
function mkdev {
  mknod $1 $2 $3 $4 ; chmod 0660 $1 ; chown root:disk $1
}
#
function pd {
  D=$( printf \\$( printf "x%03x" $[ $1 + 97 ] ) )
  mkdev pd$D b 45 $[ $1 * 16 ]
  for P in 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15
  do mkdev pd$D$P b 45 $[ $1 * 16 + $P ]
  done
}
#
cd /dev
#
for u in 0 1 2 3 ; do pd $u ; done
for u in 0 1 2 3 ; do mkdev pcd$u b 46 $u ; done 
for u in 0 1 2 3 ; do mkdev pf$u  b 47 $u ; done 
for u in 0 1 2 3 ; do mkdev pt$u  c 96 $u ; done 
for u in 0 1 2 3 ; do mkdev npt$u c 96 $[ $u + 128 ] ; done 
for u in 0 1 2 3 ; do mkdev pg$u  c 97 $u ; done 
#
# 结束 mkd
```

有了设备文件和驱动程序，您就可以像访问任何其他 Linux 设备一样访问 PARIDE 设备。例如，要在 pcd0 中挂载 CD-ROM，请使用：

`mount /dev/pcd0 /cdrom`

如果您有一个新的 Avatar Shark 墨盒，并且驱动器是 pda，您可能会执行以下操作：

`fdisk /dev/pda`  -- 使用分区 1 类型 83 创建新的分区表

`mke2fs /dev/pda1`  -- 构建文件系统

`mkdir /shark`  -- 创建一个挂载磁盘的位置

`mount /dev/pda1 /shark`

像 Imation 超级磁盘这样的设备以相同的方式工作，除了它们没有分区表。例如，要制作一个 120MB 的软盘，您可以与 DOS 系统共享：

`mkdosfs /dev/pf0`

`mount /dev/pf0 /mnt`

## 2.4 pf 驱动程序

pf 驱动程序旨在用于并行端口 ATAPI 磁盘设备。此类中最常见的设备是 PD 驱动器和 LS-120 驱动器。传统上，这些设备的介质未分区。因此，pf 驱动程序不支持分区介质。这可能会在驱动程序的未来版本中更改。

## 2.5 使用 pt 驱动程序

并行端口 ATAPI 磁带驱动器的 pt 驱动程序是一个最小化驱动程序。它尚未支持许多标准磁带 ioctl 操作。为了获得最佳性能，应使用 32KB 的块大小。如果可以，您可能希望将并行端口延迟设置为 0。

## 2.6 使用 pg 驱动程序

pg 驱动程序可与 cdrecord 程序结合使用以创建 CD-ROM。请从 ftp://ftp.fokus.gmd.de/pub/unix/cdrecord/ 获取 cdrecord 版本 1.6.1 或更高版本。要录制 CD-R 介质，理想情况下应将并行端口设置为 EPP 模式，并将“端口延迟”设置为 0。使用这些设置，可以以 2 倍速度录制而不会出现任何缓冲区下溢。如果您无法使驱动程序在 EPP 模式下工作，请尝试使用“双向”或“PS/2”模式，仅使用 1 倍速度。

# 3. 故障排除

## 3.1 如果可以，请使用 EPP 模式

人们报告的与 PARIDE 驱动程序相关的最常见问题涉及并行端口 CMOS 设置。此时，没有一个 PARIDE 协议模块支持 ECP 模式或任何 ECP 组合模式。如果您能够这样做，请使用 CMOS 设置程序将并行端口设置为 EPP 模式。

## 3.2 检查端口延迟

一些并行端口无法可靠地以全速传输数据。为了抵消错误，PARIDE 协议模块在每次访问 i/o 端口之间引入“端口延迟”。每个协议都为此延迟设置默认值。在大多数情况下，用户可以覆盖默认值并将其设置为 0 - 从而导致传输速率略高。在某些罕见情况下（特别是在较旧的 486 系统中），默认延迟不够长。如果您遇到数据传输损坏或意外故障，您可能希望增加端口延迟。可以使用每个高级驱动程序的“driveN”参数来编程延迟。请参阅上面的注释，或阅读 linux/drivers/block/paride 中驱动程序源文件开头的注释。

## 3.3 一些驱动器需要打印机重置

市场上似乎有许多“无名”外部驱动器，它们并不总是能正确上电。我们已经注意到一些基于 OnSpec 和较旧 Freecom 适配器的驱动器存在此问题。在这些罕见情况下，通常可以通过在并行端口上发出“打印机重置”来重新初始化适配器。由于重置操作在多设备环境中可能具有破坏性，PARIDE 驱动程序不会自动执行此操作。但是，您可以通过执行以下操作强制进行打印机重置：

`insmod lp reset=1`

`rmmod lp`

如果您遇到这些边缘情况之一，您可能应该将 paride 驱动程序构建为模块，并在加载 PARIDE 驱动程序之前安排进行打印机重置。

## 3.4 如果需要帮助，请使用详细选项和 dmesg

虽然已经对这些驱动程序进行了大量测试以使其尽可能顺利地工作，但仍会出现问题。如果您确实有问题，请首先检查所有明显的事情：该驱动器在带有制造商驱动程序的 DOS 中是否工作？如果这没有提供任何有用的线索，请确保系统上仅连接一个驱动器，并且（a）启用了 PARPORT 或（b）没有其他设备驱动程序正在使用您的并行端口（在 /proc/ioports 中检查）。然后，加载适当的驱动程序（如果需要，你可以加载多个协议模块），如下所示：

```
# insmod paride
# insmod epat
# insmod bpck
# insmod kbic
...  # insmod pd verbose=1
```

（当然，使用适合你设备类型的正确驱动程序）。`verbose=1`参数将使驱动程序在尝试定位你的驱动器时记录其活动的跟踪。

使用`dmesg`来捕获所有`PARIDE`消息的日志（任何以`paride:`开头的消息，即协议模块的名称或驱动程序的名称），并将其包含在你的错误报告中。你可以通过以下两种方式提交错误报告。要么直接通过电子邮件`grant@torque.net`发送给`PARIDE`套件的作者，要么加入`linux-parport`邮件列表并在那里发布你的报告。

## 3.5 更多信息或帮助

你可以通过向以下地址发送邮件消息来加入`linux-parport`邮件列表：

`linux-parport-request@torque.net`

邮件正文内容仅包含一个单词：

`subscribe`

（不要在主题行中包含）。在执行此操作时，请确保你的邮件程序已正确设置，因为列表管理器是一个机器人，它将使用你邮件头中的回复地址订阅你。在向列表服务器发送邮件时，请删除你邮件头中可能存在的任何反垃圾邮件技巧。

你可能还会在`linux-parport`网页上找到一些有用的信息（尽管它们并不总是最新的），网址为：

`http://web.archive.org/web/*/http://www.torque.net/parport/`