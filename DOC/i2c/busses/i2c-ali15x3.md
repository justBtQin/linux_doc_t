以下是转换成 Markdown 格式后的内容：

# 内核驱动程序 `i2c-ali15x3`

**支持的适配器**：
  * Acer Labs, Inc. ALI 1533 和 1543C（南桥）
    数据表：现处于保密协议下
    http://www.ali.com.tw/

**作者**：
    Frodo Looijaard <frodol@dds.nl>
    Philip Edelbrock <phil@netroedge.com>
    Mark D. Studebaker <mdsxyz123@yahoo.com>

**模块参数**

* force_addr：整数
  初始化 I2C 控制器的基地址

**注释**

`force_addr` 参数对于不在 BIOS 中设置地址的主板很有用。不执行 PCI 强制；设备仍必须在 `lspci` 中存在。除非驱动程序抱怨基地址未设置，否则不要使用此参数。

示例：`modprobe i2c-ali15x3 force_addr=0xe800`

SMBus 在 ASUS P5A 主板上会周期性挂起，只能通过电源循环来清除。原因未知（见下面的“问题”部分）。

**描述**

这是 Acer Labs Inc.（ALI）M1541 和 M1543C 南桥的 SMB 主机控制器的驱动程序。

M1543C 是台式机系统的南桥。
M1541 是便携式系统的南桥。
它们是以下 ALI 芯片组的一部分：

 * “Aladdin Pro 2”包括具有 AGP 和 100MHz CPU 前端总线的 M1621 Slot 1 北桥
 * “Aladdin V”包括具有 AGP 和 100MHz CPU 前端总线的 M1541 Socket 7 北桥
  一些 Aladdin V 主板：
    Asus P5A
    Atrend ATC-5220
    BCM/GVC VP1541
    Biostar M5ALA
    Gigabyte GA-5AX （** 通常不起作用，因为 BIOS 未启用 7101 设备！ **）
    Iwill XA100 Plus
    Micronics C200
    Microstar (MSI) MS-5169

  * “Aladdin IV”包括具有高达 83.3 MHz 主机总线的 M1541 Socket 7 北桥

有关这些芯片的概述，请参阅 http://www.acerlabs.com 。此时，网站上的完整数据表受密码保护，但是如果您联系圣何塞的 ALI 办公室，他们可能会给您密码。

M1533/M1543C 设备在 PCI 总线上显示为四个独立的设备。`lspci` 的输出将显示类似于以下内容：

  00:02.0 USB 控制器：Acer Laboratories Inc. M5237（修订版 03）
  00:03.0 桥：Acer Laboratories Inc. M7101      <= 这是我们需要的
  00:07.0 ISA 桥：Acer Laboratories Inc. M1533（修订版 c3）
  00:0f.0 IDE 接口：Acer Laboratories Inc. M5229（修订版 c1）

**重要**
** 如果您的主板上有 M1533 或 M1543C，并且您得到
** “ali15x3: 错误：无法检测到 ali15x3！”
** 那么运行 `lspci` 。
** 如果您看到 1533 和 5229 设备，但没有 7101 设备，
** 那么您必须在 BIOS 中启用 ACPI、PMU、SMB 或类似的东西
** 如果找不到 M7101 设备，驱动程序将无法工作。

SMB 控制器是 M7101 设备的一部分，这是一个符合 ACPI 的电源管理单元（PMU）。

为了使 SMB 工作，必须启用整个 M7101 设备。不能仅启用 SMB 本身。SMB 和 ACPI 具有单独的 I/O 空间。我们确保启用 SMB。我们不处理 ACPI。

**功能**

此驱动程序仅控制 SMB 主机。M15X3 上的 SMB 从控制器未启用。此驱动程序不使用中断。

**问题**

此驱动程序仅请求 SMB 寄存器的 I/O 空间。它不使用 ACPI 区域。

在 ASUS P5A 主板上，有多个报告称 SMBus 会挂起，并且只能通过关闭计算机电源来解决。在例如高 CPU 负载或夏季等主板变热的情况下，情况似乎更糟。此主板可能存在电气问题。
在 P5A 上，W83781D 传感器芯片同时在 ISA 和 SMBus 上。因此，通常可以通过仅在 ISA 总线上访问 W83781D 来避免 SMBus 挂起。