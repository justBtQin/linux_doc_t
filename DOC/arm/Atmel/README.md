**ARM Atmel SoCs (aka AT91)**

=========================

**介绍**

------------

本文档提供了关于在 Linux 主线（即 kernel.org 上的那个）中当前受支持的 ARM Atmel SoCs 的有用信息。需要注意的是，在整个 Linux 内核开发过程中，Atmel | SMART ARM 基于的 MPU 产品线在历史上被命名为“AT91”或“at91”，即使这个产品前缀已从 Atmel 的官方产品名称中完全消失。无论如何，文件、目录、git 树、git 分支/标签和电子邮件主题始终包含这个“at91”子字符串。

**AT91 SoCs**

---------

每个产品的文档和详细数据手册可在 Atmel 网站上获取：http://www.atmel.com。

```
  类型：
    * 基于 ARM 920 的 SoC
      - at91rm9200
        + 数据手册
          http://www.atmel.com/Images/doc1768.pdf
    * 基于 ARM 926 的 SoC
      - at91sam9260
        + 数据手册
          http://www.atmel.com/Images/doc6221.pdf
      - at91sam9xe
        + 数据手册
          http://www.atmel.com/Images/Atmel-6254-32-bit-ARM926EJ-S-Embedded-Microprocessor-SAM9XE_Datasheet.pdf
      - at91sam9261
        + 数据手册
          http://www.atmel.com/Images/doc6062.pdf
      - at91sam9263
        + 数据手册
          http://www.atmel.com/Images/Atmel_6249_32-bit-ARM926EJ-S-Microcontroller_SAM9263_Datasheet.pdf
      - at91sam9rl
        + 数据手册
          http://www.atmel.com/Images/doc6289.pdf
      - at91sam9g20
        + 数据手册
          http://www.atmel.com/Images/doc6384.pdf
      - at91sam9g45 系列
        - at91sam9g45
        - at91sam9g46
        - at91sam9m10
        - at91sam9m11（设备超集）
        + 数据手册
          http://www.atmel.com/Images/Atmel-6437-32-bit-ARM926-Embedded-Microprocessor-SAM9M11_Datasheet.pdf
      - at91sam9x5 系列（又名“5 系列”）
        - at91sam9g15
        - at91sam9g25
        - at91sam9g35
        - at91sam9x25
        - at91sam9x35
        + 数据手册（可被视为涵盖整个系列）
          http://www.atmel.com/Images/Atmel_11055_32-bit-ARM926EJ-S-Microcontroller_SAM9X35_Datasheet.pdf
      - at91sam9n12
        + 数据手册
          http://www.atmel.com/Images/Atmel_11063_32-bit-ARM926EJ-S-Microcontroller_SAM9N12CN11CN12_Datasheet.pdf
    * 基于 ARM Cortex - A5 的 SoC
      - sama5d3 系列
        - sama5d31
        - sama5d33
        - sama5d34
        - sama5d35
        - sama5d36（设备超集）
        + 数据手册
          http://www.atmel.com/Images/Atmel-11121-32-bit-Cortex-A5-Microcontroller-SAMA5D3_Datasheet.pdf
    * 基于 ARM Cortex - A5 + NEON 的 SoC
      - sama5d4 系列
        - sama5d41
        - sama5d42
        - sama5d43
        - sama5d44（设备超集）
        + 数据手册
          http://www.atmel.com/Images/Atmel-11238-32-bit-Cortex-A5-Microcontroller-SAMA5D4_Datasheet.pdf
```

**Linux 内核信息**

------------------------

Linux 内核 mach 目录：arch/arm/mach-at91

MAINTAINERS 条目是：“ARM/ATMEL AT91RM9200 AND AT91SAM ARM ARCHITECTURES”

**AT91 SoCs 和板的设备树**

------------------------------------

所有 AT91 SoC 都已转换为设备树。自 Linux 3.19 以来，这些产品必须使用此方法来引导 Linux 内核。

进行中声明：

适用于 AT91 SoCs 和板的设备树文件和设备树绑定被视为“不稳定”。要完全清楚，任何 at91 绑定都可以随时更改。因此，请确保使用从同一源树生成的设备树二进制文件和内核映像。有关“稳定”绑定/ABI 的定义，请参考 Documentation/devicetree/bindings/ABI.txt 文件。当适当时，AT91 MAINTAINERS 将删除此声明。

命名约定和最佳实践：

- SoC 设备树源包含文件以产品的官方名称命名（例如 at91sam9g20.dtsi 或 sama5d33.dtsi）。

- 设备树源包含文件（.dtsi）用于收集可在 SoC 或板之间共享的公共节点（例如 sama5d3.dtsi 或 at91sam9x5cm.dtsi）。当为特定外设或主题收集节点时，标识符必须放在文件名的末尾，用“_”分隔（例如 at91sam9x5_can.dtsi 或 sama5d3_gmac.dtsi）。

- 板设备树源文件（.dts）以“at91-”字符串为前缀，以便可以轻松识别。请注意，某些文件是此规则的历史例外（例如 sama5d3[13456]ek.dts、usb_a9g20.dts 或 animeo_ip.dts）。