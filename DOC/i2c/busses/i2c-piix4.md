# 内核驱动程序 `i2c-piix4`

**支持的适配器**：
  * Intel 82371AB PIIX4 和 PIIX4E
  * Intel 82443MX（440MX）
    数据表：可在 Intel 网站上公开获取
  * ServerWorks OSB4、CSB5、CSB6、HT-1000 和 HT-1100 南桥
    数据表：仅可通过 ServerWorks 的保密协议获取
  * ATI IXP200、IXP300、IXP400、SB600、SB700 和 SB800 南桥
    数据表：未公开
    SB700 寄存器参考可在以下位置获取：
    http://support.amd.com/us/Embedded_TechDocs/43009_sb7xx_rrg_pub_1.00.pdf
  * AMD SP5100（在某些服务器主板上发现的 SB700 衍生产品）
    数据表：可在 AMD 网站上公开获取
    http://support.amd.com/us/Embedded_TechDocs/44413.pdf
  * AMD Hudson-2、ML、CZ
    数据表：未公开
  * Standard Microsystems（SMSC）SLC90E66（Victory66）南桥
    数据表：可在 SMSC 网站上公开获取 http://www.smsc.com

**作者**：
    Frodo Looijaard <frodol@dds.nl>
    Philip Edelbrock <phil@netroedge.com>

**模块参数**

* force：整数
  强制启用 PIIX4。危险！
* force_addr：整数
  在给定地址强制启用 PIIX4。极其危险！

**描述**

PIIX4（正确名称为 82371AB）是一款具有众多功能的 Intel 芯片。其中之一是实现 PCI 总线。它的一个次要功能是实现系统管理总线。这是一个真正的 SMBus - 您不能在 I2C 级别访问它。好消息是它原生理解 SMBus 命令，您不必担心时序问题。坏消息是连接到它的非 SMBus 设备可能会极大地混淆它。是的，已知会发生这种情况...

执行 `lspci -v` 并查看是否包含如下条目：

```
0000:00:02.3 Bridge: Intel Corp. 82371AB/EB/MB PIIX4 ACPI (rev 02)
	     Flags: medium devsel, IRQ 9
```

总线和设备编号可能不同，但功能编号必须相同（与许多 PCI 设备一样，PIIX4 包含多个不同的“功能”，可以视为单独的设备）。如果您找到这样的条目，您就拥有一个 PIIX4 SMBus 控制器。

在某些计算机上（特别是某些戴尔计算机），SMBus 默认被禁用。如果您使用 `insmod` 参数 `force=1`，内核模块将尝试启用它。这非常危险！如果 BIOS 没有为该模块设置正确的地址，您可能会遇到大麻烦（读取：崩溃，数据损坏等）。仅在万不得已时尝试（例如首先尝试 BIOS 更新），并先进行备份！更危险的选项是 `force_addr=<IOPORT>`。这不仅会像 `force` 那样启用 PIIX4，还会设置新的基本 I/O 端口地址。PIIX4 的 SMBus 部分需要 8 个这样的地址范围才能正常工作。如果这些地址已被其他设备保留，您将陷入大麻烦！如果您不确定自己在做什么，请不要使用此选项！

PIIX4E 只是 PIIX4 的新版本；它也受支持。PIIX/PIIX3 未实现 SMBus 或 I2C 总线，因此您不能在这些主板上使用此驱动程序。

ServerWorks 南桥、Intel 440MX 和 Victory66 在 I2C/SMBus 支持方面与 PIIX4 相同。

AMD SB700、SB800、SP5100 和 Hudson-2 芯片组实现了两个与 PIIX4 兼容的 SMBus 控制器。如果您的 BIOS 初始化了辅助控制器，此驱动程序将其检测为“辅助 SMBus 主机控制器”。

如果您拥有 Force CPCI735 主板或其他基于 OSB4 的系统，您可能需要更改 SMBus 中断选择寄存器，以使 SMBus 控制器使用 SMI 模式。

1) 使用 `lspci` 命令并找到带有 SMBus 控制器的 PCI 设备：
   ```
   00:0f.0 ISA bridge: ServerWorks OSB4 South Bridge (rev 4f)
   ```
   对于不同的芯片组，行可能会有所不同。请查阅驱动程序源代码以获取所有可能的 PCI ID（并使用 `lspci -n` 进行匹配）。假设设备位于 00:0f.0。
2) 现在您只需要更改 0xD2 寄存器的值。首先使用以下命令获取它：
   `lspci -xxx -s 00:0f.0`
   如果值为 0x3，则需要将其更改为 0x1
   `setpci -s 00:0f.0 d2.b=1`

请注意，并非在所有情况下都需要这样做，仅在 SMBus 无法正常工作时才需要。

**特定硬件问题**

此驱动程序将拒绝在具有 Intel PIIX4 SMBus 的 IBM 系统上加载。其中一些机器具有连接到 SMBus 的 RFID EEPROM（24RF08），由于状态机错误，可能容易损坏。这些大多是 Thinkpad 笔记本电脑，但台式机系统也可能受到影响。我们没有所有受影响系统的列表，因此唯一安全的解决方案是阻止在所有 IBM 系统上访问 SMBus（使用 DMI 数据检测）。

有关更多信息，请阅读：
http://www.lm-sensors.org/browser/lm-sensors/trunk/README