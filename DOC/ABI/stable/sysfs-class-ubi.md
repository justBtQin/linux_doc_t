**/sys/class/ubi/**

- **What**: 属于 UBI 子系统的子目录，提供通用 UBI 信息、每个 UBI 设备信息和每个 UBI 卷信息。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/version**

- **What**: 包含最新支持的 UBI 内存中格式的版本。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubiX/**

- **What**: 描述 UBI 设备（UBI 设备 0、1 等）。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/ubiX/avail_eraseblocks**

- **What**: 可用逻辑擦除块的数量。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/ubiX/bad_peb_count**

- **What**: 底层 MTD 设备上坏物理擦除块的数量。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/ubiX/bgt_enabled**

- **What**: 如果 UBI 后台线程被禁用，则包含 ASCII "0\n"，如果启用则包含 ASCII "1\n"。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/ubiX/dev**

- **What**: 与此 UBI 设备对应的字符设备的主设备号和次设备号（格式为 <major>:<minor>）。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/ubiX/eraseblock_size**

- **What**: 此 UBI 设备可能提供的最大逻辑擦除块大小。UBI 卷可能由于对齐而具有较小的逻辑擦除块大小。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/ubiX/max_ec**

- **What**: 最大物理擦除块擦除计数器值。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/ubiX/max_vol_count**

- **What**: 此 UBI 设备可能拥有的最大卷数。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/ubiX/min_io_size**

- **What**: 最小输入/输出单元大小。所有 I/O 只能以包含的数字的分数进行。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/ubiX/mtd_num**

- **What**: 底层 MTD 设备的编号。

- **Date**: 2008 年 1 月

- **KernelVersion**: 2.6.25

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/ubiX/reserved_for_bad**

- **What**: 为坏块处理保留的物理擦除块数量。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/ubiX/total_eraseblocks**

- **What**: 底层 MTD 设备上良好（未标记为坏）物理擦除块的总数。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/ubiX/volumes_count**

- **What**: 此 UBI 设备上的卷数。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/ubiX/ubiX_Y/**

- **What**: 描述 UBI 设备 X 上的 UBI 卷（卷 0、1 等）。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/ubiX/ubiX_Y/alignment**

- **What**: 卷对齐 - 此卷的逻辑擦除块大小必须对齐的值。例如，2048 表示逻辑擦除块大小是 2048 的倍数。换句话说，卷逻辑擦除块大小是 UBI 设备逻辑擦除块大小对齐到对齐值。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/ubiX/ubiX_Y/corrupted**

- **What**: 如果 UBI 卷正常，则包含 ASCII "0\n"，如果损坏（例如由于卷更新中断），则包含 ASCII "1\n"。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/ubiX/ubiX_Y/data_bytes**

- **What**: 此卷包含的数据量。此值仅对静态卷有意义，对于动态卷，它等效于字节中的总卷大小。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/ubiX/ubiX_Y/dev**

- **What**: 与此 UBI 卷对应的字符设备的主设备号和次设备号（格式为 <major>:<minor>）。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/ubiX/ubiX_Y/name**

- **What**: 卷名称。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/ubiX/ubiX_Y/reserved_ebs**

- **What**: 为此卷保留的物理擦除块数量。等效于卷中的逻辑擦除块大小。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/ubiX/ubiX_Y/type**

- **What**: 卷类型。对于动态卷包含 ASCII "dynamic\n"，对于静态卷包含 "static\n"。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/ubiX/ubiX_Y/upd_marker**

- **What**: 如果此卷的更新标记未设置，则包含 ASCII "0\n"，如果设置则包含 "1\n"。卷更新开始时设置更新标记，更新结束时清除。因此，更新标记的存在表示卷在更新被中断时正在更新。可以使用 "corrupted" sysfs 文件检查后者。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>

**/sys/class/ubi/ubiX/ubiX_Y/usable_eb_size**

- **What**: 此卷的逻辑擦除块大小。等效于设备的逻辑擦除块大小对齐到卷对齐值。

- **Date**: 2006 年 7 月

- **KernelVersion**: 2.6.22

- **Contact**: Artem Bityutskiy <dedekind@infradead.org>