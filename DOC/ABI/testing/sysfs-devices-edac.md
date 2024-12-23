**`/sys/devices/system/edac/mc/mc*/reset_counters`**

- **What**：这是一个只写控制文件，将给定内存控制器上的 UE 和 CE 错误的所有统计计数器清零。

- **Date**：2006 年 1 月

- **Contact**：linux-edac@vger.kernel.org

- **Description**：此写入操作将给定内存控制器上的 UE 和 CE 错误的所有统计计数器清零。清零计数器还将重置指示自上次计数器重置以来经过多长时间的计时器。这对于计算错误/时间很有用。由于计数器总是在驱动程序初始化时重置，因此没有可用的模块/内核参数。

**`/sys/devices/system/edac/mc/mc*/seconds_since_reset`**

- **What**：此属性文件显示自上次计数器重置以来经过了多少秒。

- **Date**：2006 年 1 月

- **Contact**：linux-edac@vger.kernel.org

- **Description**：此属性文件显示自上次计数器重置以来经过的秒数。可与错误计数器一起使用以测量错误率。

**`/sys/devices/system/edac/mc/mc*/mc_name`**

- **What**：此属性文件显示正在使用的内存控制器的类型。

- **Date**：2006 年 1 月

- **Contact**：linux-edac@vger.kernel.org

- **Description**：此属性文件显示正在使用的内存控制器的类型。

**`/sys/devices/system/edac/mc/mc*/size_mb`**

- **What**：此属性文件以兆字节为单位显示此内存控制器管理的内存大小。

- **Date**：2006 年 1 月

- **Contact**：linux-edac@vger.kernel.org

- **Description**：此属性文件以兆字节为单位显示此内存控制器管理的内存大小。

**`/sys/devices/system/edac/mc/mc*/ue_count`**

- **What**：此属性文件显示在此内存控制器上发生的不可纠正错误的总数。

- **Date**：2006 年 1 月

- **Contact**：linux-edac@vger.kernel.org

- **Description**：此属性文件显示在此内存控制器上发生的不可纠正错误的总数。如果设置了`panic_on_ue`，此计数器将没有机会递增，因为 EDAC 将使系统恐慌。

**`/sys/devices/system/edac/mc/mc*/ue_noinfo_count`**

- **What**：此属性文件显示在此内存控制器上发生的 UEs 的数量，没有关于哪个 DIMM 插槽有错误的信息。

- **Date**：2006 年 1 月

- **Contact**：linux-edac@vger.kernel.org

- **Description**：此属性文件显示在此内存控制器上发生的 UEs 的数量，没有关于哪个 DIMM 插槽有错误的信息。

**`/sys/devices/system/edac/mc/mc*/ce_count`**

- **What**：此属性文件显示在此内存控制器上发生的可纠正错误的总数。

- **Date**：2006 年 1 月

- **Contact**：linux-edac@vger.kernel.org

- **Description**：此属性文件显示在此内存控制器上发生的可纠正错误的总数。此计数对于检查非常重要。CE 提供了 DIMM 开始出现故障的早期指示。应监控此计数字段是否为非零值，并将此类信息报告给系统管理员。

**`/sys/devices/system/edac/mc/mc*/ce_noinfo_count`**

- **What**：此属性文件显示在此内存控制器上发生的 CEs 的数量，没有关于哪个 DIMM 插槽有错误的信息。内存有缺陷，但仍可运行，但没有信息可指示故障内存所在的插槽。

- **Date**：2006 年 1 月

- **Contact**：linux-edac@vger.kernel.org

- **Description**：此属性文件显示在此内存控制器上发生的 CEs 的数量，没有关于哪个 DIMM 插槽有错误的信息。内存有缺陷，但仍可运行，但没有信息可指示故障内存所在的插槽。此计数字段也应监控是否为非零值。

**`/sys/devices/system/edac/mc/mc*/sdram_scrub_rate`**

- **What**：读/写属性文件，用于控制内存清理。

- **Date**：2007 年 2 月

- **Contact**：linux-edac@vger.kernel.org

- **Description**：内存控制器使用的清理速率通过将最小带宽（以字节/秒为单位）写入属性文件来设置。该速率将转换为一个内部值，至少提供指定的速率。读取文件将返回实际使用的清理速率。如果配置失败或内存清理未实现，属性文件的值将为 -1。

**`/sys/devices/system/edac/mc/mc*/max_location`**

- **What**：此属性文件显示此内存控制器中最后可用内存插槽的信息。

- **Date**：2012 年 4 月

- **Contact**：Mauro Carvalho Chehab <m.chehab@samsung.com>，linux-edac@vger.kernel.org

- **Description**：此属性文件显示此内存控制器中最后可用内存插槽的信息。它被用户空间工具用于显示内存填充布局。

**`/sys/devices/system/edac/mc/mc*/(dimm|rank)*/size`**

- **What**：此属性文件将显示 DIMM 或 rank 的大小。

- **Date**：2012 年 4 月

- **Contact**：Mauro Carvalho Chehab <m.chehab@samsung.com>，linux-edac@vger.kernel.org

- **Description**：对于`dimm*/size`，这是 DIMM 内存棒的大小（以 MB 为单位）。对于`rank*/size`，这是 DIMM 内存棒一个 rank 的大小（以 MB 为单位）。对于单 rank 内存（1R），这也是 DIMM 的总大小。对于双 rank（2R）内存，这是总 DIMM 内存的一半大小。

**`/sys/devices/system/edac/mc/mc*/(dimm|rank)*/dimm_dev_type`**

- **What**：此属性文件将显示在此 DIMM 上使用的 DRAM 设备的类型。

- **Date**：2012 年 4 月

- **Contact**：Mauro Carvalho Chehab <m.chehab@samsung.com>，linux-edac@vger.kernel.org

- **Description**：此属性文件将显示在此 DIMM 上使用的 DRAM 设备的类型（例如 x1、x2、x4、x8 等）。

**`/sys/devices/system/edac/mc/mc*/(dimm|rank)*/dimm_edac_mode`**

- **What**：此属性文件将显示正在使用的错误检测和纠正类型。

- **Date**：2012 年 4 月

- **Contact**：Mauro Carvalho Chehab <m.chehab@samsung.com>，linux-edac@vger.kernel.org

- **Description**：此属性文件将显示正在使用的错误检测和纠正类型。例如：S4ECD4ED 表示具有 x4 DRAM 的 Chipkill。

**`/sys/devices/system/edac/mc/mc*/(dimm|rank)*/dimm_label`**

- **What**：此控制文件允许为此 DIMM 分配一个标签。

- **Date**：2012 年 4 月

- **Contact**：Mauro Carvalho Chehab <m.chehab@samsung.com>，linux-edac@vger.kernel.org

- **Description**：此控制文件允许为此 DIMM 分配一个标签。在模块中使用此标签，当发生错误时，输出可以在系统日志中提供 DIMM 标签。这对于隔离 UE 事件的原因在恐慌事件中至关重要。DIMM 标签必须在引导后分配，并且信息必须正确标识物理插槽及其丝印标签。此信息目前非常特定于主板，并且必须在此时在用户空间中确定。

**`/sys/devices/system/edac/mc/mc*/(dimm|rank)*/dimm_location`**

- **What**：此属性文件将显示 DIMM 或 rank 的位置（csrow/channel、branch/channel/slot 或 channel/slot）。

- **Date**：2012 年 4 月

- **Contact**：Mauro Carvalho Chehab <m.chehab@samsung.com>，linux-edac@vger.kernel.org

- **Description**：此属性文件将显示 DIMM 或 rank 的位置（csrow/channel、branch/channel/slot 或 channel/slot）。

**`/sys/devices/system/edac/mc/mc*/(dimm|rank)*/dimm_mem_type`**

- **What**：此属性文件将显示当前在此 csrow 上的内存类型。

- **Date**：2012 年 4 月

- **Contact**：Mauro Carvalho Chehab <m.chehab@samsung.com>，linux-edac@vger.kernel.org

- **Description**：此属性文件将显示当前在此 csrow 上的内存类型。通常，为缓冲或非缓冲内存（例如，Unbuffered-DDR3）。