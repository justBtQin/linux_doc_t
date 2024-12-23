# Intel Assabet（SA-1110 评估）板

============================================

请参阅：

http://developer.intel.com

还有 John G Dorsey <jd5q@andrew.cmu.edu> 的一些说明：

http://www.cs.cmu.edu/~wearable/software/assabet.html

# 构建内核

-------------------

要使用当前默认设置构建内核：

`make assabet_config`

`make oldconfig`

`make zImage`

生成的内核映像应在`linux/arch/arm/boot/zImage`中可用。

# 安装引导加载程序

-----------------------

有几个能够在 Assabet 上引导 Linux 的引导加载程序可用：

BLOB（http://www.lartmaker.nl/lartware/blob/）

   BLOB 是 LART 项目中使用的引导加载程序。 一些贡献的补丁已合并到 BLOB 中，以添加对 Assabet 的支持。

Compaq 的 Bootldr + John Dorsey 为 Assabet 支持提供的补丁

（http://www.handhelds.org/Compaq/bootldr.html）

（http://www.wearablegroup.org/software/bootldr/）

   Bootldr 是 Compaq 为 iPAQ Pocket PC 开发的引导加载程序。 John Dorsey 已生成附加补丁，以添加对 Assabet 和 JFFS 文件系统的支持。

RedBoot（http://sources.redhat.com/redboot/）

   RedBoot 是 Red Hat 基于 eCos RTOS 硬件抽象层开发的引导加载程序。 它在许多其他硬件平台中支持 Assabet。

目前，RedBoot 是推荐的选择，因为它是唯一具有网络支持且维护最活跃的引导加载程序。

下面显示了如何使用 RedBoot 引导 Linux 的简要示例。 但首先，您需要将 RedBoot 安装到闪存中。 可以从以下位置获得已知可工作的预编译 RedBoot 二进制文件：

ftp://ftp.netwinder.org/users/n/nico/

ftp://ftp.arm.linux.org.uk/pub/linux/arm/people/nico/

ftp://ftp.handhelds.org/pub/linux/arm/sa-1100-patches/

查找`redboot-assabet*.tgz`。 一些安装信息在`redboot-assabet*.txt`中提供。

# 初始 RedBoot 配置

-----------------------------

这里使用的命令在在线的《RedBoot 用户指南》（http://sources.redhat.com/ecos/docs.html）中有解释。 请参考它以获取解释。

如果您有 CF 网络卡（我的 Assabet 套件包含来自 Socket Communications Inc. 的 CF + LP-E），您应该强烈考虑使用它进行 TFTP 文件传输。 您必须在 RedBoot 运行之前插入它，因为它无法动态检测它。

要初始化闪存目录：

`fis init -f`

要初始化非易失性设置，例如您是否要使用 BOOTP 或静态 IP 地址等，请使用此命令：

`fconfig -i`

# 将内核映像写入闪存

---------------------------------

首先，必须将内核映像加载到 RAM 中。 如果您在 TFTP 服务器上有`zImage`文件：

`load zImage -r -b 0x100000`

如果您更愿意使用串行端口上的 Y-Modem 上传：

`load -m ymodem -r -b 0x100000`

要将其写入闪存：

`fis create "Linux kernel" -b 0x100000 -l 0xc0000`

# 引导内核

------------------

内核仍然需要一个文件系统才能引导。 可以如下加载 ramdisk 映像：

`load ramdisk_image.gz -r -b 0x800000`

同样，可以使用 Y-Modem 上传代替 TFTP，只需将文件名替换为`-y ymodem`。

现在可以从闪存中检索内核，如下所示：

`fis load "Linux kernel"`

或如前所述加载。 要引导内核：

`exec -b 0x100000 -l 0xc0000`

ramdisk 映像也可以存储到闪存中，但如下面所述，对于闪存文件系统有更好的解决方案。

# 使用 JFFS2

-----------

使用 JFFS2（第二个日志闪存文件系统）可能是将可写文件系统存储到闪存中的最方便方法。 JFFS2 与负责低级闪存管理的 MTD 层结合使用。 有关 Linux MTD 的更多信息可在线在：

http://www.linux-mtd.infradead.org/。 同一站点提供了有关创建 JFFS/JFFS2 映像的一些信息的 JFFS 指南。

例如，可以从用于预编译 RedBoot 映像的相同 FTP 站点检索示例 JFFS2 映像。

要加载此文件：

`load sample_img.jffs2 -r -b 0x100000`

结果应如下所示：

RedBoot> load sample_img.jffs2 -r -b 0x100000

Raw file loaded 0x00100000-0x00377424

现在我们必须知道未分配闪存的大小：

`fis free`

结果：

RedBoot> fis free

  0x500E0000..0x503C0000

上述值可能因文件系统的大小和闪存的类型而异。 请参阅下面的用法示例，并注意适当替换您自己的值。

我们必须确定一些值：

未分配闪存的大小：0x503c0000 - 0x500e0000 = 0x2e0000

文件系统映像的大小：0x00377424 - 0x00100000 = 0x277424

我们当然希望适合文件系统映像，但我们也希望为它提供所有剩余的闪存空间。 要写入它：

`fis unlock -f 0x500E0000 -l 0x2e0000`

`fis erase -f 0x500E0000 -l 0x2e0000`

`fis write -b 0x100000 -l 0x277424 -f 0x500E0000`

`fis create "JFFS2" -n -f 0x500E0000 -l 0x2e0000`

现在，一旦 Linux 在引导过程中发现它们是什么，文件系统就与 MTD“分区”相关联。 从 Redboot 中，`fis list`命令显示它们：

```
RedBoot> fis list
Name              FLASH addr  Mem addr    Length      Entry point
RedBoot           0x50000000  0x50000000  0x00020000  0x00000000
RedBoot config    0x503C0000  0x503C0000  0x00020000  0x00000000
FIS directory     0x503E0000  0x503E0000  0x00020000  0x00000000
Linux kernel      0x50020000  0x00100000  0x000C0000  0x00000000
JFFS2             0x500E0000  0x500E0000  0x002E0000  0x00000000
```

然而，Linux 应该显示类似以下内容：

```
SA1100 flash: probing 32-bit flash bus
SA1100 flash: Found 2 x16 devices at 0x0 in 32-bit mode
Using RedBoot partition definition
Creating 5 MTD partitions on "SA1100 flash":
0x00000000-0x00020000 : "RedBoot"
0x00020000-0x000e0000 : "Linux kernel"
0x000e0000-0x003c0000 : "JFFS2"
0x003c0000-0x003e0000 : "RedBoot config"
0x003e0000-0x00400000 : "FIS directory"
```

这里重要的是我们感兴趣的分区的位置，即第三个分区。 在 Linux 中，这对应于`/dev/mtdblock2`。 因此，要使用闪存中的内核及其根文件系统引导 Linux，我们需要此 RedBoot 命令：

`fis load "Linux kernel"`

`exec -b 0x100000 -l 0xc0000 -c "root=/dev/mtdblock2"`

当然，可以使用除 JFFS 之外的其他文件系统，例如 cramfs 等。 您可能希望通过 NFS 引导根文件系统等。 也可以，有时更方便的是在从 ramdisk 或 NFS 引导时直接从 Linux 内部闪存文件系统。 Linux MTD 存储库也有许多处理闪存的工具，例如擦除它。 然后可以将 JFFS2 直接挂载在新擦除的分区上，并直接复制文件。 等等...

# RedBoot 脚本编写

-----------------

如果每次重新启动 Assabet 都必须输入上述所有命令，那么它们就不是很有用。 因此，可以使用 RedBoot 的脚本功能自动化引导过程。

例如，我使用以下命令通过网络上的 TFTP 服务器引导 Linux，同时获取内核和 ramdisk 映像：

```
RedBoot> fconfig
Run script at boot: false true
Boot script:
Enter script, terminate with empty line
>> load zImage -r -b 0x100000
>> load ramdisk_ks.gz -r -b 0x800000
>> exec -b 0x100000 -l 0xc0000
>>
Boot script timeout (1000ms resolution): 3
Use BOOTP for network configuration: true
GDB connection port: 9000
Network debug at boot time: false
Update RedBoot non-volatile configuration - are you sure (y/n)? y
```

然后，只需等待登录提示符，即可重新启动 Assabet。

```
Nicolas Pitre
nico@fluxnic.net
2001 年 6 月 12 日
```

# rmk 树中外围设备的状态（更新于 2001 年 10 月 14 日）

-------------------------------------------------------

Assabet：

串口：

无线电：TX、RX、CTS、DSR、DCD、RI

   PM：未测试。`COM`：

- `TX`

- `RX`

- `CTS`

- `DSR`

- `DCD`

- `RTS`

- `DTR`

- `PM`

`PM`：

- 未测试。

`I2C`：

- 已实现，但未完全测试。

`L3`：

- 已完全测试，通过。

`PM`：

- 未测试。

`Video`：

- `LCD`：已完全测试。

`PM`：

- （LCD 连接 neponset 时不喜欢被消隐）

- `Video out`：未完全测试。

`Audio`：

- `UDA1341`：

    - `Playback`：已完全测试，通过。

    - `Record`：已实现，但未测试。

    - `PM`：未测试。

- `UCB1200`：

    - `Audio play`：已实现，但未大量测试。

    - `Audio rec`：已实现，但未大量测试。

    - `Telco audio play`：已实现，但未大量测试。

    - `Telco audio rec`：已实现，但未大量测试。

    - `POTS control`：无

    - `Touchscreen`：是

    - `PM`：未测试。

`Other`：

- `PCMCIA`：

    - `LPE`：已完全测试，通过。

    - `USB`：无

- `IRDA`：

    - `SIR`：已完全测试，通过。

    - `FIR`：已完全测试，通过。

    - `PM`：未测试。

`Neponset`：

`Serial ports`：

- `COM1,2`：`TX`、`RX`、`CTS`、`DSR`、`DCD`、`RTS`、`DTR`

`PM`：未测试。

- `USB`：已实现，但未大量测试。

- `PCMCIA`：已实现，但未大量测试。

`PM`：未测试。

- `CF`：已实现，但未大量测试。

`PM`：未测试。

更多内容可在 -np（Nicolas Pitre 的）树中找到。