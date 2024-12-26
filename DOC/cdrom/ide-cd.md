<markdown_document>

# IDE-CD 驱动文档

最初由 scott snyder 撰写 <snyder@fnald0.fnal.gov>（1996 年 5 月 19 日）

接力者：Erik Andersen <andersee@debian.org>

新维护者（1998 年 10 月 19 日）：Jens Axboe <axboe@image.dk>

## 1. 介绍

---------------

ide-cd 驱动应可与所有符合 ATAPI 1.2 至 ATAPI 2.6 标准的连接到 IDE 接口的 CDROM 驱动器配合使用。请注意，一些 CDROM 供应商（包括 Mitsumi、Sony、Creative、Aztech 和 Goldstar）既生产符合 ATAPI 标准的驱动器，也生产使用专有接口的驱动器。如果您的驱动器使用其中一种专有接口，此驱动将无法与之配合使用（但其他 CDROM 驱动之一可能可以）。此驱动不适用于连接到并行端口的“ATAPI”驱动器。此外，至少有一个驱动器（CyCDROM CR520ie）连接到 IDE 端口但不是 ATAPI；此驱动也不适用于此类驱动器（但请参阅 aztcd 驱动）。

此驱动提供以下功能：

- 从数据轨道读取并挂载 ISO 9660 文件系统。

- 播放音频轨道。 周围漂浮的大多数 CDROM 播放器程序都应该可以工作；我通常使用 Workman。

- 多会话支持。

- 在支持的驱动器上，直接从音频轨道读取数字音频数据。 可以使用程序 cdda2wav 进行此操作。 然而，请注意，只有一些驱动器实际上支持此功能。

- 现在支持符合 ATAPI 2.6 草案标准的 CDROM 换盘机（例如 NEC CDR-251）。 此附加功能包括查询当前选定插槽的函数调用、查询哪些插槽包含 CD 等的函数调用。 演示此功能的示例程序附加在此文件的末尾。 三洋 3 盘换盘机（不符合标准）现在也得到支持。 请注意，驱动程序将第一个 CD 称为插槽 #0。

## 2. 安装

---------------

0. ide-cd 依赖于 ide 磁盘驱动程序。 有关 ide 驱动程序的最新信息，请参阅 Documentation/ide/ide.txt。

1. 确保将 ide 和 ide-cd 驱动程序编译到您正在使用的内核中。 在配置内核时，在题为“软盘、IDE 和其他块设备”的部分中，对以下选项选择`Y`（将直接将支持编译到内核中）或`M`（将支持编译为可加载和卸载的模块）：

    - 增强型 IDE/MFM/RLL 磁盘/CDROM/磁带/软盘支持

    - 包括 IDE/ATAPI CDROM 支持

    并对

    - 在主接口上使用旧的仅磁盘驱动程序

    选择`no`。

    根据您拥有的 IDE 接口类型，您可能需要指定其他配置选项。 请参阅 Documentation/ide/ide.txt。

2. 您还应确保 iso9660 文件系统已编译到内核中或作为可加载模块可用。 您可以通过 catting /proc/filesystems 来查看内核是否知道某个文件系统。

3. CDROM 驱动器应连接到主机的 IDE 接口上。 系统上的每个接口由一个 I/O 端口地址和一个 IRQ 号定义，标准分配为 0x1f0 和 14 用于主接口，0x170 和 15 用于辅助接口。每个接口可以控制最多两个设备，每个设备可以是硬盘、CDROM 驱动器、软盘驱动器或磁带驱动器。接口上的两个设备称为“主设备”和“从设备”；这通常可以通过驱动器上的跳线进行选择。

    Linux 以如下方式命名这些设备。 主 IDE 接口上的主设备和从设备分别称为`hda`和`hdb`。 辅助接口上的驱动器称为`hdc`和`hdd`。（位于其他位置的接口在第三个位置使用其他字母；请参阅 Documentation/ide/ide.txt。）

    如果您希望驱动程序自动找到您的 CDROM 驱动器，您应确保您的 IDE 接口使用上述主或辅助地址之一。 此外，如果 CDROM 驱动器是 IDE 接口上的唯一设备，它应跳线为“主设备”。（如果由于某些原因您无法以这种方式配置您的系统，您仍然可以使用该驱动程序。 但是，您可能需要在引导时向内核传递额外的配置信息。 有关更多信息，请参阅 Documentation/ide/ide.txt。）

4. 引导系统。 如果驱动器被识别，您应该看到一条类似以下内容的消息：

    hdb: NEC CD-ROM DRIVE:260, ATAPI CDROM drive

    如果您没有看到此消息，请参阅下面的第 5 节。

5. 您可能希望创建一个指向实际设备的符号链接 /dev/cdrom。 您可以使用以下命令执行此操作：

    ln -s  /dev/hdX  /dev/cdrom

    其中 X 应替换为指示您的驱动器安装位置的字母。

6. 您应该能够使用`dmesg`命令查看驱动程序的任何错误消息。

## 3. 基本用法

--------------

将 ISO 9660 CDROM 放入驱动器中，以 root 用户身份输入以下命令即可挂载：

mount -t iso9660 /dev/cdrom /mnt/cdrom

假设 /dev/cdrom 是指向实际设备的链接（如前一节的步骤 5 中所述），并且 /mnt/cdrom 是一个空目录。 现在，您应该能够在 /mnt/cdrom 目录下看到 CDROM 的内容。 如果要弹出 CDROM，您必须首先使用类似以下的命令卸载它：

umount /mnt/cdrom

请注意，音频 CD 无法挂载。

一些发行版在 /etc/fstab 中设置为在启动时始终尝试挂载 CDROM 文件系统。 但是，不需要以这种方式挂载 CDROM，如果您经常更换 CDROM，这可能会很麻烦。 如果您觉得这样更好，您可以随意从 /etc/fstab 中删除 cdrom 行并手动挂载 CDROM。

多会话和 photocd 光盘无需特殊处理即可正常工作。 hpcdtoppm 软件包（ftp.gwdg.de:/pub/linux/hpcdtoppm/）可能对读取 photocd 有用。

要播放音频 CD，您应该首先卸载并移除任何数据 CDROM。 然后，任何 CDROM 播放器程序都应该可以工作（workman、workbone、cdplayer 等）。

在少数驱动器上，您可以使用 cdda2wav 等程序直接读取数字音频。 我听说仅支持此功能的驱动器类型是 Sony 和 Toshiba 驱动器。 如果在不支持此功能的驱动器上尝试使用此功能，您将收到错误。

对于受支持的换盘机，您可以使用`cdchange`程序（附加在此文件的末尾）在换盘机插槽之间切换。 请注意，在尝试此操作之前，应先卸载驱动器。 该程序接受两个参数：CDROM 设备和您希望切换到的插槽号。 如果插槽号为 -1，则卸载驱动器。

## 4. 常见问题

------------------

本节讨论在尝试使用驱动程序时遇到的一些常见问题以及一些可能的解决方案。 请注意，如果您遇到问题，您可能还应查看 Documentation/ide/ide.txt 以获取有关底层 IDE 支持代码的当前信息。 其中一些项目仅适用于早期版本的驱动程序，但为了完整性而在此处提及。

在大多数情况下，您应该查看`dmesg`以获取驱动程序的任何错误。

a. 引导期间未检测到驱动器。

    - 查看上面的配置说明和 Documentation/ide/ide.txt，并检查您的硬件配置。

    - 如果您的驱动器是 IDE 接口上的唯一设备，应尽可能将其跳线为“主设备”。

    - 如果您的 IDE 接口不在 0x170 或 0x1f0 的标准地址，则需要使用 lilo 选项明确通知驱动程序。 请参阅 Documentation/ide/ide.txt。

</markdown_document># 此特性大约在内核版本 1.3.30 时被添加。

- 如果自动探测未找到你的驱动器，你可以通过使用 lilo 选项`hdX=cdrom`（其中 X 是与你的驱动器安装位置相对应的驱动器字母）来告诉驱动程序假设存在一个驱动器。请注意，如果你这样做并且看到类似以下的启动消息：

  `hdX: ATAPI cdrom (?)`

  这并不意味着驱动程序已成功检测到驱动器；相反，这意味着驱动程序未检测到驱动器，但由于你已告知它存在，所以仍在假设存在一个驱动器。如果你实际上尝试对在不存在或无响应的 I/O 地址上定义的驱动器进行 I/O 操作，你可能会收到状态值为 0xff 的错误。

- 一些 IDE 适配器在正常工作之前需要非标准的初始化序列。（如果是这种情况，通常会有一个单独的 MS-DOS 驱动程序仅用于该控制器。）声卡上的 IDE 接口通常属于这一类。在较新的 1.3.x 内核中提供了对一些需要额外初始化的接口的支持。你可能需要打开额外的内核配置选项才能使它们正常工作；请参阅 Documentation/ide/ide.txt。即使你的接口不支持，你也可以通过以下步骤使其正常工作。首先启动 MS-DOS 并加载适当的驱动程序。然后热启动 linux（即不关闭电源）。如果此方法有效，则可以通过从 MS-DOS autoexec 运行 loadlin 来实现自动化。

b. 超时/IRQ 错误。

  - 如果总是出现超时错误，驱动器的中断可能无法到达主机。

  - 在启动时，消息`IRQ probe failed (<n>)`也可能表明存在 IRQ 问题。如果<n>为零，这意味着系统在预期接收中断时（在任何可行的 IRQ 上）未从驱动器接收到中断。如果<n>为负数，这意味着系统在预期从 CDROM 驱动器接收一个中断时，在多个 IRQ 线上接收到了中断。

  - 请仔细检查你的硬件配置，以确保你的 IDE 接口的 IRQ 编号与驱动程序预期的匹配。（通常的分配是主（0x1f0）接口为 14，从（0x170）接口为 15。）还要确保你没有其他硬件可能与你正在使用的 IRQ 冲突。还要检查你的系统的 BIOS 设置；有些系统具有禁用单个 IRQ 级别的功能，我曾遇到过一个系统，其默认情况下禁用了 IRQ 15。

  - 请注意，许多 MS-DOS CDROM 驱动程序即使中断设置存在硬件问题仍能正常工作；它们显然不使用中断。

  - 如果你拥有 Pioneer DR-A24X，在启动时你将肯定会收到讨厌的错误消息，例如“irq timeout: status=0x50 { DriveReady SeekComplete }”。Pioneer DR-A24X CDROM 驱动器如今相当流行。不幸的是，当我们执行标准的 Linux ATA 磁盘驱动器探测时，这些驱动器似乎会变得非常混乱。如果你拥有其中一个驱动器，你可以通过在你的 lilo.conf 文件中添加`append="hdX=noprobe hdX=cdrom"`并再次运行 lilo（其中 X 是与你的驱动器安装位置相对应的驱动器字母）来绕过使这些 CDROM 驱动器混乱的 ATA 探测。

c. 系统挂起。

  - 如果在你尝试访问 CDROM 时系统锁定，最可能的原因是你有一个有缺陷的 IDE 适配器，它不能正确处理多个接口上的同时事务。其中最臭名昭著的是 CMD640B 芯片。通过在启动时指定`serialize`选项可以解决此问题。最近的内核在大多数情况下应该能够自动检测到需要此选项，但检测并不完全可靠。有关`serialize`选项和 CMD640B 的更多信息，请参阅 Documentation/ide/ide.txt。

  - 请注意，许多 MS-DOS CDROM 驱动程序可以与这种有缺陷的硬件一起使用，显然是因为它们从不尝试将 CDROM 操作与其他磁盘活动重叠。

d. 无法挂载 CDROM。

  - 如果从 mount 收到错误，检查`dmesg`可能会有所帮助，以查看驱动程序或文件系统是否有更具体的错误。

  - 确保驱动器中有一个 CDROM 加载，并且它是一个 ISO 9660 光盘。你不能挂载音频 CD。

  - 在驱动器中有 CDROM 且未挂载的情况下，尝试类似以下的操作：

      `cat /dev/cdrom | od | more`

    如果看到转储，那么驱动器和驱动程序可能工作正常，问题出在文件系统级别（即 CDROM 不是 ISO 9660 或文件系统结构中有错误）。

  - 如果看到`not a block device`错误，请检查设备特殊文件的定义是否正确。它们应该如下所示：

      `brw-rw----   1 root     disk       3,   0 Nov 11 18:48 /dev/hda`

      `brw-rw----   1 root     disk       3,  64 Nov 11 18:48 /dev/hdb`

      `brw-rw----   1 root     disk      22,   0 Nov 11 18:48 /dev/hdc`

      `brw-rw----   1 root     disk      22,  64 Nov 11 18:48 /dev/hdd`

    一些早期的 Slackware 版本中这些定义不正确。如果这些错误，你可以通过运行脚本 scripts/MAKEDEV.ide 来重新创建它们。（你可能需要首先使用 chmod 使其可执行。）

    如果你有一个 /dev/cdrom 符号链接，请检查它是否指向正确的设备文件。

    如果你听到人们谈论设备`hd1a`和`hd1b`，这些是现在称为 hdc 和 hdd 的旧名称。这些名称应被视为过时。

  - 如果 mount 抱怨 iso9660 文件系统不可用，但你知道它是可用的（检查 /proc/filesystems），你可能需要一个较新的 mount 版本。早期版本并不总是给出有意义的错误消息。

e. 目录列表不可预测地被截断，并且`dmesg`显示来自驱动程序的`buffer botch`错误消息。

  - 在 1.2.x 内核中的驱动程序版本中存在一个错误，可能会导致此问题。它在 1.3.0 中已修复。如果你无法升级，你可以在挂载时指定块大小为 2048 来解决此问题。（请注意，在这种情况下，你将无法直接从 CDROM 执行二进制文件。）

    如果在 1.3.0 之后的内核中看到此问题，请将其报告为一个错误。

f. 数据损坏。

  - 偶尔会观察到与 Hitachi CDR-7730 CDROM 相关的数据损坏。如果你遇到数据损坏，使用"hdx=slow"作为命令行参数可能会解决问题，但会牺牲系统性能。

## 5. cdchange.c

-------------

```c
/*
 * cdchange.c  [-v]  <device>  [<slot>]
 *
 * 此程序从更换器中的指定插槽加载 CDROM，并显示有关更换器状态的信息。在使用此程序之前，应先卸载驱动器。
 * 如果指定了 -v 标志或未指定插槽，则会显示更换器信息。
 * 基于最初由 Gerhard Zuber <zuber@berlin.snafu.de>提供的代码。Erik Andersen <andersee@debian.org>为新的统一 CDROM 驱动程序接口进行了更换器状态信息和重写。
 */
#include <stdio.h>
#include <stdlib.h>
#include <errno.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
#include <sys/ioctl.h>
#include <linux/cdrom.h>

// 主函数
int
main (int argc, char **argv)
{
    char *program;
    char *device;
    int fd;           // CD-ROM 设备的文件描述符
    int status;       // 系统调用的返回状态
    int verbose = 0;
    int slot=-1, x_slot;
    int total_slots_available;

    program = argv[0];

    ++argv;
    --argc;

    // 检查参数个数
    if (argc < 1 || argc > 3) {
        fprintf (stderr, "usage: %s [-v] <device> [<slot>]\n",
                 program);
        fprintf (stderr, "       Slots are numbered 1 -- n.\n");
        exit (1);
    }

    // 检查是否为 -v 选项
    if (strcmp (argv[0], "-v") == 0) {
        verbose = 1;
        ++argv;
        --argc;
    }

    // 获取设备名称
    device = argv[0];

    // 检查是否有指定槽位
    if (argc == 2)
        slot = atoi (argv[1]) - 1;

    // 打开设备
    fd = open(device, O_RDONLY | O_NONBLOCK);
    if (fd < 0) {
        fprintf (stderr, "%s: open failed for `%s': %s\n",
                 program, device, strerror (errno));
        exit (1);
    }

    // 检查 CD 播放器状态
    total_slots_available = ioctl (fd, CDROM_CHANGER_NSLOTS);
    if (total_slots_available <= 1 ) {
        fprintf (stderr, "%s: Device `%s' is not an ATAPI "
                "compliant CD changer.\n", program, device);
        exit (1);
    }

    // 处理指定槽位的情况
    if (slot >= 0) {
        if (slot >= total_slots_available) {
            fprintf (stderr, "Bad slot number.  "
                    "Should be 1 -- %d.\n",
                    total_slots_available);
            exit (1);
        }

        // 加载指定槽位的光盘
        slot=ioctl (fd, CDROM_SELECT_DISC, slot);
        if (slot<0) {
            fflush(stdout);
            perror ("CDROM_SELECT_DISC ");
            exit(1);
        }
    }

    // 输出当前槽位和总槽位数
    if (slot < 0 || verbose) {

        status=ioctl (fd, CDROM_SELECT_DISC, CDSL_CURRENT);
        if (status<0) {
            fflush(stdout);
            perror (" CDROM_SELECT_DISC");
            exit(1);
        }
        slot=status;

        printf ("Current slot: %d\n", slot+1);
        printf ("Total slots available: %d\n",
                total_slots_available);

        printf ("Drive status: ");
        status = ioctl (fd, CDROM_DRIVE_STATUS, CDSL_CURRENT);
        if (status<0) {
            perror(" CDROM_DRIVE_STATUS");
        } else switch(status) {
            case CDS_DISC_OK:
                printf ("Ready.\n");
                break;
            case CDS_TRAY_OPEN:
                printf ("Tray Open.\n");
                break;
            case CDS_DRIVE_NOT_READY:
                printf ("Drive Not Ready.\n");
                break;
            default:
                printf ("This Should not happen!\n");
                break;
        }

        // 遍历所有槽位并输出状态
        for (x_slot=0; x_slot<total_slots_available; x_slot++) {
            printf ("Slot %2d: ", x_slot+1);
            status = ioctl (fd, CDROM_DRIVE_STATUS, x_slot);
            if (status<0) {
                perror(" CDROM_DRIVE_STATUS");
            } else switch(status) {
                case CDS_DISC_OK:
                    printf ("Disc present.");
                    break;
                case CDS_NO_DISC: 
                    printf ("Empty slot.");
                    break;
                case CDS_TRAY_OPEN:
                    printf ("CD-ROM tray open.\n");
                    break;
                case CDS_DRIVE_NOT_READY:
                    printf ("CD-ROM drive not ready.\n");
                    break;
                case CDS_NO_INFO:
                    printf ("No Information available.");
                    break;
                default:
                    printf ("This Should not happen!\n");
                    break;
            }
            if (slot == x_slot) {
                status = ioctl (fd, CDROM_DISC_STATUS);
                if (status<0) {
                    perror(" CDROM_DISC_STATUS");
                }
                switch (status) {
                    case CDS_AUDIO:
                        printf ("\tAudio disc.\t");
                        break;
                    case CDS_DATA_1:
                    case CDS_DATA_2:
                        printf ("\tData disc type %d.\t", status-CDS_DATA_1+1);
                        break;
                    case CDS_XA_2_1:
                    case CDS_XA_2_2:
                        printf ("\tXA data disc type %d.\t", status-CDS_XA_2_1+1);
                        break;
                    default:
                        printf ("\tUnknown disc type 0x%x!\t", status);
                        break;
                }
            }
            status = ioctl (fd, CDROM_MEDIA_CHANGED, x_slot);
            if (status<0) {
                perror(" CDROM_MEDIA_CHANGED");
            }
            switch (status) {
                case 1:
                    printf ("Changed.\n");
                    break;
                default:
                    printf ("\n");
                    break;
            }
        }
    }

    // 关闭设备
    status = close (fd);
    if (status!= 0) {
        fprintf (stderr, "%s: close failed for `%s': %s\n",
                 program, device, strerror (errno));
        exit (1);
    }
 
    exit (0);
}
```

上述代码是一个 C 语言程序，用于获取 CD 播放器的信息和操作光盘。程序通过命令行参数指定 CD 设备和可选的槽位，然后打开设备并进行一系列的 I/O 控制操作（`ioctl`）来获取和设置 CD 播放器的状态。

程序主要功能包括：

1. 检查命令行参数的有效性，确保指定了正确的设备和可选的槽位。

2. 打开指定的 CD 设备，并设置为只读和非阻塞模式。

3. 获取 CD 播放器的总槽位数，并检查设备是否为 ATAPI 兼容的 CD 换片机。

4. 如果指定了槽位，将其加载到驱动器中。

5. 输出当前槽位的信息，包括槽位编号、总槽位数和驱动器状态。

6. 遍历所有槽位，输出每个槽位的状态，包括是否有光盘、托盘状态、驱动器状态等。

7. 如果当前槽位有光盘，输出光盘的类型。

8. 关闭打开的 CD 设备。

总的来说，这个程序提供了一种简单的方式来与 CD 播放器进行交互，并获取其状态信息。