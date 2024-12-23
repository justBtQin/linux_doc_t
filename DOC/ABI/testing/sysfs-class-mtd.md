**/sys/class/mtd/**

 - **Date**：2009 年 4 月

 - **KernelVersion**：2.6.29

 - **Contact**：linux-mtd@lists.infradead.org

 - **Description**：`mtd/`类子目录属于 MTD 子系统（MTD 核心）。

**/sys/class/mtd/mtdX/**

 - **Date**：2009 年 4 月

 - **KernelVersion**：2.6.29

 - **Contact**：linux-mtd@lists.infradead.org

 - **Description**：`/sys/class/mtd/mtd{0,1,2,3,...}`目录对应于每个`/dev/mtdX`字符设备。这些可能代表物理/模拟闪存设备、闪存设备上的分区或串联的闪存设备。

**/sys/class/mtd/mtdXro/**

 - **Date**：2009 年 4 月

 - **KernelVersion**：2.6.29

 - **Contact**：linux-mtd@lists.infradead.org

 - **Description**：这些目录为`/sys/class/mtd/mtdX/`提供相应的只读设备节点。

**/sys/class/mtd/mtdX/dev**

 - **Date**：2009 年 4 月

 - **KernelVersion**：2.6.29

 - **Contact**：linux-mtd@lists.infradead.org

 - **Description**：与此 MTD 设备对应的字符设备的主设备号和次设备号（以`<major>:<minor>`格式）。这是读写设备，所以`<minor>`将是偶数。

**/sys/class/mtd/mtdXro/dev**

 - **Date**：2009 年 4 月

 - **KernelVersion**：2.6.29

 - **Contact**：linux-mtd@lists.infradead.org

 - **Description**：与此 MTD 设备的只读变体对应的字符设备的主设备号和次设备号（以`<major>:<minor>`格式）。在这种情况下，`<minor>`将是奇数。

**/sys/class/mtd/mtdX/erasesize**

 - **Date**：2009 年 4 月

 - **KernelVersion**：2.6.29

 - **Contact**：linux-mtd@lists.infradead.org

 - **Description**：设备的“主要”擦除大小。如果`numeraseregions`为零，则这是整个设备的擦除块大小。否则，可以使用`MEMGETREGIONCOUNT`/`MEMGETREGIONINFO`ioctl 来确定实际的擦除块布局。

**/sys/class/mtd/mtdX/flags**

 - **Date**：2009 年 4 月

 - **KernelVersion**：2.6.29

 - **Contact**：linux-mtd@lists.infradead.org

 - **Description**：表示设备标志的十六进制值，或在一起：

 - 0x0400：`MTD_WRITEABLE` - 设备可写

 - 0x0800：`MTD_BIT_WRITEABLE` - 可以翻转单个位

 - 0x1000：`MTD_NO_ERASE` - 无需擦除

 - 0x2000：`MTD_POWERUP_LOCK` - 复位后始终锁定

**/sys/class/mtd/mtdX/name**

 - **Date**：2009 年 4 月

 - **KernelVersion**：2.6.29

 - **Contact**：linux-mtd@lists.infradead.org

 - **Description**：设备或分区的人类可读 ASCII 名称。这将与`/proc/mtd`中的名称匹配。

**/sys/class/mtd/mtdX/numeraseregions**

 - **Date**：2009 年 4 月

 - **KernelVersion**：2.6.29

 - **Contact**：linux-mtd@lists.infradead.org

 - **Description**：对于具有可变擦除块大小的设备，这提供了擦除区域的总数。否则，它将读回为零。

**/sys/class/mtd/mtdX/oobsize**

 - **Date**：2009 年 4 月

 - **KernelVersion**：2.6.29

 - **Contact**：linux-mtd@lists.infradead.org

 - **Description**：每页的 OOB 字节数。

**/sys/class/mtd/mtdX/size**

 - **Date**：2009 年 4 月

 - **KernelVersion**：2.6.29

 - **Contact**：linux-mtd@lists.infradead.org

 - **Description**：设备/分区的总大小，以字节为单位。

**/sys/class/mtd/mtdX/type**

 - **Date**：2009 年 4 月

 - **KernelVersion**：2.6.29

 - **Contact**：linux-mtd@lists.infradead.org

 - **Description**：以下 ASCII 字符串之一，表示设备类型：
 
 - `absent`

 - `ram`

 - `rom`

 - `nor`

 - `nand`

 - `mlc-nand`

 - `dataflash`

 - `ubi`

 - `unknown`

**/sys/class/mtd/mtdX/writesize**

 - **Date**：2009 年 4 月

 - **KernelVersion**：2.6.29

 - **Contact**：linux-mtd@lists.infradead.org

 - **Description**：最小可写闪存单元大小。这将始终是一个正整数。对于 NOR 闪存，它是 1（即使可以清除单个位）。对于 NAND 闪存，它是一个 NAND 页（或半页或四分之一页）。对于 ECC NOR，它是 ECC 块大小。

**/sys/class/mtd/mtdX/ecc_strength**

 - **Date**：2012 年 4 月

 - **KernelVersion**：3.4

 - **Contact**：linux-mtd@lists.infradead.org

 - **Description**：设备在覆盖 ECC 步骤的每个区域内能够纠正的最大位错误数（请参阅`ecc_step_size`）。这将始终是一个非负整数。对于没有任何 ECC 能力的设备，它是 0。

**/sys/class/mtd/mtdX/bitflip_threshold**

 - **Date**：2012 年 4 月

 - **KernelVersion**：3.4

 - **Contact**：linux-mtd@lists.infradead.org

 - **Description**：这允许用户检查和调整`mtd_read()`和`mtd_read_oob()`中`mtd`返回`-EUCLEAN`的标准。如果在包括 ECC 步骤的任何单个区域上纠正的最大位错误数（由驱动程序报告）等于或超过此值，则返回`-EUCLEAN`。否则，如果没有错误，则返回 0。更高层（例如，UBI）使用此返回代码作为擦除块可能降级并应作为标记为坏的候选的指示。初始值可以由闪存设备驱动程序指定。如果没有，则默认值是`ecc_strength`。此功能的引入对`-EUCLEAN`返回代码的含义带来了细微的变化。以前，它被解释为简单地“纠正了一个或多个位错误”。它的新解释可以表述为“在包括 ECC 步骤的一个或多个区域上纠正了危险的高位错误数”。“危险的高”的精确定义可以由用户使用`bitflip_threshold`进行调整。但是，除非用户知道自己在做什么并且对其设备的属性有深入了解，否则不鼓励这样做。一般来说，`bitflip_threshold`应该足够低以检测真正的擦除块降级，但又足够高以避免在发生粘性位翻转的设备上持续返回`-EUCLEAN`的值的后果。请注意，如果`bitflip_threshold`超过`ecc_strength`，则读取操作永远不会返回`-EUCLEAN`。相反，如果`bitflip_threshold`为零，则总是返回`-EUCLEAN`，除非有硬错误。这通常仅适用于具有 ECC 能力的 NAND 闪存设备。对于没有 ECC 能力的设备（即`ecc_strength`为零的设备），它将被忽略。

**/sys/class/mtd/mtdX/ecc_step_size**

 - **Date**：2013 年 5 月

 - **KernelVersion**：3.10

 - **Contact**：linux-mtd@lists.infradead.org

 - **Description**：ECC 覆盖的单个区域的大小，称为 ECC 步长。设备在每个写入大小区域内可能有几个大小相等的 ECC 步长。它将始终是一个非负整数。对于没有任何 ECC 能力的设备，它是 0。

**/sys/class/mtd/mtdX/ecc_failures**

 - **Date**：2014 年 6 月

 - **KernelVersion**：3.17

 - **Contact**：linux-mtd@lists.infradead.org

 - **Description**：此设备的 ECC 报告的失败次数。通常，这些失败与失败的读取操作相关。它将始终是一个非负整数。对于没有任何 ECC 能力的设备，它是 0。

**/sys/class/mtd/mtdX/corrected_bits**

 - **Date**：2014 年 6 月

 - **KernelVersion**：3.17

 - **Contact**：linux-mtd@lists.infradead.org

 - **Description**：通过设备的 ECC 纠正的位的数量。它将始终是一个非负整数。对于没有任何 ECC 能力的设备，它是 0。

**/sys/class/mtd/mtdX/bad_blocks**

 - **Date**：2014 年 6 月

 - **KernelVersion**：3.17

 - **Contact**：linux-mtd@lists.infradead.org

 - **Description**：在此分区中标记为坏的块的数量（如果有）。

**/sys/class/mtd/mtdX/bbt_blocks**

 - **Date**：2014 年 6 月

 - **KernelVersion**：3.17

 - **Contact**：linux-mtd@lists.infradead.org

 - **Description**：在此分区中标记为保留的块的数量（如果有）。这些通常用于存储闪存中的坏块表（BBT）。

**/sys/class/mtd/mtdX/offset**

 - **Date**：2015 年 3 月

 - **KernelVersion**：4.1

 - **Contact**：linux-mtd@lists.infradead.org

 - **Description**：对于分区，该分区相对于主设备起始位置的偏移量（以字节为单位）。此属性在主设备上不存在，因此可以用于区分分区和非分区的设备。