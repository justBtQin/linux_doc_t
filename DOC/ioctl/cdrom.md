		Summary of CDROM ioctl calls.
		============================

		Edward A. Falk <efalk@google.com>

		November, 2004

# CDROM 层支持的 ioctl(2) 调用

此文档试图描述 CDROM 层所支持的 ioctl(2) 调用。在 Linux 2.6 中，它们大体上在 `drivers/cdrom/cdrom.c` 和 `drivers/block/scsi_ioctl.c` 中实现。

ioctl 的值在 `<linux/cdrom.h>` 中列出。在撰写本文时，它们如下：

 - `CDROMPAUSE`  暂停音频操作
 - `CDROMRESUME`  恢复暂停的音频操作
 - `CDROMPLAYMSF`  播放音频 MSF（`struct cdrom_msf`）
 - `CDROMPLAYTRKIND`  播放音频轨道/索引（`struct cdrom_ti`）
 - `CDROMREADTOCHDR`  读取 TOC 头部（`struct cdrom_tochdr`）
 - `CDROMREADTOCENTRY`  读取 TOC 条目（`struct cdrom_tocentry`）
 - `CDROMSTOP`  停止 CDROM 驱动器
 - `CDROMSTART`  启动 CDROM 驱动器
 - `CDROMEJECT`  弹出 CDROM 介质
 - `CDROMVOLCTRL`  控制输出音量（`struct cdrom_volctrl`）
 - `CDROMSUBCHNL`  读取子通道数据（`struct cdrom_subchnl`）
 - `CDROMREADMODE2`  读取 CDROM 模式 2 数据（2336 字节）（`struct cdrom_read`）
 - `CDROMREADMODE1`  读取 CDROM 模式 1 数据（2048 字节）（`struct cdrom_read`）
 - `CDROMREADAUDIO`  （`struct cdrom_read_audio`）
 - `CDROMEJECT_SW`  启用(1)/禁用(0) 自动弹出
 - `CDROMMULTISESSION`  获取多会话磁盘的最后一个会话的起始地址（`struct cdrom_multisession`）
 - `CDROM_GET_MCN`  获取“通用产品代码”（如果可用）（`struct cdrom_mcn`）
 - `CDROM_GET_UPC`  已弃用，请使用 `CDROM_GET_MCN` 代替。
 - `CDROMRESET`  硬重置驱动器
 - `CDROMVOLREAD`  获取驱动器的音量设置（`struct cdrom_volctrl`）
 - `CDROMREADRAW`  以原始模式读取数据（2352 字节）（`struct cdrom_read`）
 - `CDROMREADCOOKED`  以处理模式读取数据
 - `CDROMSEEK`  查找 msf 地址
 - `CDROMPLAYBLK`  仅 SCSI-CD，（`struct cdrom_blk`）
 - `CDROMREADALL`  读取全部 2646 字节
 - `CDROMGETSPINDOWN` 返回 4 位的盘片停止旋转值
 - `CDROMSETSPINDOWN` 设置 4 位的盘片停止旋转值
 - `CDROMCLOSETRAY`  `CDROMEJECT` 的对应操作
 - `CDROM_SET_OPTIONS` 设置行为选项
 - `CDROM_CLEAR_OPTIONS` 清除行为选项
 - `CDROM_SELECT_SPEED` 设置 CD-ROM 速度
 - `CDROM_SELECT_DISC` 选择光盘（对于光盘库）
 - `CDROM_MEDIA_CHANGED` 检查介质是否已更改
 - `CDROM_DRIVE_STATUS` 获取托盘位置等
 - `CDROM_DISC_STATUS` 获取光盘类型等
 - `CDROM_CHANGER_NSLOTS` 获取插槽数量
 - `CDROM_LOCKDOOR`  锁定或解锁门
 - `CDROM_DEBUG`  打开/关闭调试消息
 - `CDROM_GET_CAPABILITY` 获取能力
 - `CDROMAUDIOBUFSIZ` 设置音频缓冲区大小
 - `DVD_READ_STRUCT`  读取结构
 - `DVD_WRITE_STRUCT` 写入结构
 - `DVD_AUTH`  认证
 - `CDROM_SEND_PACKET` 向驱动器发送数据包
 - `CDROM_NEXT_WRITABLE` 获取下一个可写入块
 - `CDROM_LAST_WRITTEN` 获取光盘上最后写入的块

以下信息是通过阅读内核源代码确定的。随着时间的推移，可能会进行一些修正。

# 通用说明：

 - 除非另有说明，所有的 ioctl 调用在成功时返回 0，在错误时返回 -1，并将 `errno` 设置为适当的值。（一些 ioctl 会返回非负的数据值。）

 - 除非另有说明，如果在向或从用户地址空间复制数据时尝试失败，所有的 ioctl 调用都会返回 -1 并将 `errno` 设置为 `EFAULT`。

 - 个别驱动程序可能返回此处未列出的错误代码。

 - 除非另有说明，所有的数据结构和常量都在 `<linux/cdrom.h>` 中定义。

# CDROMPAUSE  - 暂停音频操作

 - 使用方法：

   `ioctl(fd, CDROMPAUSE, 0);`

 - 输入：无

 - 输出：无

 - 错误返回：

   `ENOSYS`  CD 驱动器不支持音频功能。

# CDROMRESUME  - 恢复暂停的音频操作

 - 使用方法：

   `ioctl(fd, CDROMRESUME, 0);`

 - 输入：无

 - 输出：无

 - 错误返回：

   `ENOSYS`  CD 驱动器不支持音频功能。

# CDROMPLAYMSF  - 播放音频 MSF（`struct cdrom_msf`）

 - 使用方法：

   `struct cdrom_msf msf;`
   `ioctl(fd, CDROMPLAYMSF, &msf);`

 - 输入：
   `cdrom_msf` 结构，描述要播放的音乐片段

 - 输出：无

 - 错误返回：

   `ENOSYS`  CD 驱动器不支持音频功能。

 - 备注：

   `MSF` 代表分钟-秒-帧
   `LBA` 代表逻辑块地址

   片段被描述为开始和结束时间，每个时间都被描述为分钟:秒:帧。一帧是 1/75 秒。

# CDROMPLAYTRKIND - 播放音频轨道/索引（`struct cdrom_ti`）

 - 使用方法：

   `struct cdrom_ti ti;`
   `ioctl(fd, CDROMPLAYTRKIND, &ti);`

 - 输入：
   `cdrom_ti` 结构，描述要播放的音乐片段

 - 输出：无

 - 错误返回：

   `ENOSYS`  CD 驱动器不支持音频功能。

 - 备注：
   片段被描述为开始和结束时间，每个时间都被描述为一个轨道和一个索引。

# CDROMREADTOCHDR - 读取 TOC 头部（`struct cdrom_tochdr`）

 - 使用方法：

   `cdrom_tochdr header;`
   `ioctl(fd, CDROMREADTOCHDR, &header);`

 - 输入：
   `cdrom_tochdr` 结构

 - 输出：
   `cdrom_tochdr` 结构

 - 错误返回：

   `ENOSYS`  CD 驱动器不支持音频功能。

# CDROMREADTOCENTRY - 读取 TOC 条目（`struct cdrom_tocentry`）

 - 使用方法：

   `struct cdrom_tocentry entry;`
   `ioctl(fd, CDROMREADTOCENTRY, &entry);`

 - 输入：
   `cdrom_tocentry` 结构

 - 输出：
   `cdrom_tocentry` 结构

 - 错误返回：

   `ENOSYS`  CD 驱动器不支持音频功能。
   `EINVAL`  `entry.cdte_format` 不是 `CDROM_MSF` 或 `CDROM_LBA`
   `EINVAL`  请求的轨道超出范围
   `EIO`  读取 TOC 时 I/O 错误

 - 备注：
   `TOC` 代表目录表
   `MSF` 代表分钟-秒-帧
   `LBA` 代表逻辑块地址

# CDROMSTOP - 停止 CDROM 驱动器

 - 使用方法：

   `ioctl(fd, CDROMSTOP, 0);`

 - 输入：无

 - 输出：无

 - 错误返回：

   `ENOSYS`  CD 驱动器不支持音频功能。

 - 备注：
   此 ioctl 的准确解释取决于设备，但大多数似乎会使驱动器停止旋转。

# CDROMSTART - 启动 CDROM 驱动器

 - 使用方法：

   `ioctl(fd, CDROMSTART, 0);`

 - 输入：无

 - 输出：无

 - 错误返回：

   `ENOSYS`  CD 驱动器不支持音频功能。

 - 备注：
   此 ioctl 的准确解释取决于设备，但大多数似乎会使驱动器旋转起来和/或关闭托盘。其他设备完全忽略此 ioctl。

# CDROMEJECT - 弹出 CDROM 介质

 - 使用方法：

   `ioctl(fd, CDROMEJECT, 0);`

 - 输入：无

 - 输出：无

 - 错误返回：

   `ENOSYS`  CD 驱动器不具备弹出功能
   `EBUSY`  其他进程正在访问驱动器，或门被锁定

 - 备注：
   请见下面的 `CDROM_LOCKDOOR`。

# CDROMCLOSETRAY - `CDROMEJECT` 的对应操作

 - 使用方法：

   `ioctl(fd, CDROMCLOSETRAY, 0);`

 - 输入：无

 - 输出：无

 - 错误返回：

   `ENOSYS`  CD 驱动器不具备关闭托盘功能
   `EBUSY`  其他进程正在访问驱动器，或门被锁定

 - 备注：
   请见下面的 `CDROM_LOCKDOOR`。

# CDROMVOLCTRL - 控制输出音量（`struct cdrom_volctrl`）

 - 使用方法：

   `struct cdrom_volctrl volume;`
   `ioctl(fd, CDROMVOLCTRL, &volume);`

 - 输入：
   包含多达 4 个通道音量的 `cdrom_volctrl` 结构。

 - 输出：无

 - 错误返回：

   `ENOSYS`  CD 驱动器不支持音频功能。

# CDROMVOLREAD - 获取驱动器的音量设置（`struct cdrom_volctrl`）

 - 使用方法：

   `struct cdrom_volctrl volume;`
   `ioctl(fd, CDROMVOLREAD, &volume);`

 - 输入：无

 - 输出：
   当前的音量设置。

 - 错误返回：

   `ENOSYS`  CD 驱动器不支持音频功能。

# CDROMSUBCHNL - 读取子通道数据（`struct cdrom_subchnl`）

 - 使用方法：

   `struct cdrom_subchnl q;`
   `ioctl(fd, CDROMSUBCHNL, &q);`

 - 输入：
   `cdrom_subchnl` 结构

 - 输出：
   `cdrom_subchnl` 结构

 - 错误返回：

   `ENOSYS`  CD 驱动器不支持音频功能。
   `EINVAL`  格式不是 `CDROM_MSF` 或 `CDROM_LBA`

 - 备注：
   格式在返回时会转换为 `CDROM_MSF`

# CDROMREADRAW - 以原始模式读取数据（2352 字节）（`struct cdrom_read`）

 - 使用方法：

   `union {`
   `    struct cdrom_msf msf;        /* 输入 */`
   `    char buffer[CD_FRAMESIZE_RAW]; /* 返回 */`
   `} arg;`
   `ioctl(fd, CDROMREADRAW, &arg);`

 - 输入：
   指示要读取地址的 `cdrom_msf` 结构。只有起始值是有效的。

 - 输出：
   写入用户提供地址的数据。

 - 错误返回：

   `EINVAL`  地址小于 0，或 `msf` 小于 0:2:0
   `ENOMEM`  内存不足

 - 备注：
   截至 2.6.8.1，`<linux/cdrom.h>` 中的注释表明此 ioctl 接受 `cdrom_read` 结构，但实际源代码读取 `cdrom_msf` 结构并将数据缓冲区写入相同的地址。

   `MSF` 值通过以下公式转换为 `LBA` 值：

   `lba = (((m * CD_SECS) + s) * CD_FRAMES + f) - CD_MSF_OFFSET;`

# CDROMREADMODE1 - 读取 CDROM 模式 1 数据（2048 字节）（`struct cdrom_read`）

 - 备注：
   与 `CDROMREADRAW` 相同，除了块大小为 `CD_FRAMESIZE`（2048）字节

# CDROMREADMODE2 - 读取 CDROM 模式 2 数据（2336 字节）（`struct cdrom_read`）

 - 备注：
   与 `CDROMREADRAW` 相同，除了块大小为 `CD_FRAMESIZE_RAW0`（2336）字节

# CDROMREADAUDIO - （`struct cdrom_read_audio`）

 - 使用方法：

   `struct cdrom_read_audio ra;`
   `ioctl(fd, CDROMREADAUDIO, &ra);`

 - 输入：
   包含读取起始点和长度的 `cdrom_read_audio` 结构

 - 输出：
   音频数据，返回到 `ra` 指示的缓冲区

 - 错误返回：

   `EINVAL`  格式不是 `CDROM_MSF` 或 `CDROM_LBA`
   `EINVAL`  `nframes` 不在范围 [1 75] 内
   `ENXIO`  驱动器没有队列（可能意味着无效的 `fd`）
   `ENOMEM`  内存不足

# CDROMEJECT_SW - 启用(1)/禁用(0) 自动弹出

 - 使用方法：

   `int val;`
   `ioctl(fd, CDROMEJECT_SW, val);`

 - 输入：
   指定自动弹出标志的标志。

 - 输出：无

 - 错误返回：

   `ENOSYS`  驱动器不具备弹出功能。
   `EBUSY`  门被锁定

# CDROMMULTISESSION - 获取多会话磁盘的最后一个会话的起始地址（`struct cdrom_multisession`）

 - 使用方法：

   `struct cdrom_multisession ms_info;`
   `ioctl(fd, CDROMMULTISESSION, &ms_info);`

 - 输入：
   包含所需格式的 `cdrom_multisession` 结构。

 - 输出：
   `cdrom_multisession` 结构会被填充最后一个会话的信息。

 - 错误返回：

   `EINVAL`  格式不是 `CDROM_MSF` 或 `CDROM_LBA`

# CDROM_GET_MCN - 获取“通用产品代码”（如果可用）（`struct cdrom_mcn`）

 - 使用方法：

   `struct cdrom_mcn mcn;`
   `ioctl(fd, CDROM_GET_MCN, &mcn);`

 - 输入：无

 - 输出：
   通用产品代码

 - 错误返回：

   `ENOSYS`  驱动器不具备读取 MCN 数据的能力。

 - 备注：
   源代码注释指出：

   以下函数已实现，尽管很少有音频光盘提供通用产品代码信息，这应该只是盒子上的介质目录编号。请注意，光盘上代码的编写方式并非在所有光盘上都统一！

# CDROM_GET_UPC - `CDROM_GET_MCN` （已弃用）

 - 截至 2.6.8.1 未实现

# CDROMRESET - 硬重置驱动器

 - 使用方法：

   `ioctl(fd, CDROMRESET, 0);`

 - 输入：无

 - 输出：无

 - 错误返回：

   `EACCES`  访问被拒绝：需要 `CAP_SYS_ADMIN`
   `ENOSYS`  驱动器不具备重置功能。

# CDROMREADCOOKED - 以处理模式读取数据

 - 使用方法：

   `u8 buffer[CD_FRAMESIZE]`
   `ioctl(fd, CDROMREADCOOKED, buffer);`

 - 输入：无

 - 输出：
   2048 字节的数据，“处理”模式。

 - 备注：
   并非在所有驱动器上都实现。

# CDROMREADALL - 读取全部 2646 字节

 - 与 `CDROMREADCOOKED` 相同，但读取 2646 字节。

# CDROMSEEK - 查找 msf 地址

 - 使用方法：

   `struct cdrom_msf msf;`
   `ioctl(fd, CDROMSEEK, &msf);`

 - 输入：
   要查找的 MSF 地址。

 - 输出：无

# CDROMPLAYBLK - 仅 SCSI-CD，（`struct cdrom_blk`）

 - 使用方法：

   `struct cdrom_blk blk;`
   `ioctl(fd, CDROMPLAYBLK, &blk);`

 - 输入：
   要播放的区域

 - 输出：无

# CDROMGETSPINDOWN

 - 使用方法：

   `char spindown;`
   `ioctl(fd, CDROMGETSPINDOWN, &spindown);`

 - 输入：无

 - 输出：
   当前 4 位的盘片停止旋转值。

# CDROMSETSPINDOWN

 - 使用方法：

   `char spindown`
   `ioctl(fd, CDROMSETSPINDOWN, &spindown);`

 - 输入：
   用于控制盘片停止旋转的 4 位值（此处需要更多细节）

 - 输出：无

# CDROM_SET_OPTIONS - 设置行为选项

 - 使用方法：

   `int options;`
   `ioctl(fd, CDROM_SET_OPTIONS, options);`

 - 输入：
   驱动器选项的新值。是以下的逻辑“或”：
   `CDO_AUTO_CLOSE` 首次打开(2)时关闭托盘
   `CDO_AUTO_EJECT` 最后一次释放时打开托盘
   `CDO_USE_FFLAGS` 打开时使用 `O_NONBLOCK` 信息
   `CDO_LOCK` 打开文件时锁定托盘
   `CDO_CHECK_TYPE` 打开时检查数据类型

 - 输出：
   在 ioctl 返回值中返回结果的选项设置。错误时返回 -1。

 - 错误返回：

   `ENOSYS`  驱动器不支持所选的选项。

# CDROM_CLEAR_OPTIONS - 清除行为选项

 - 与 `CDROM_SET_OPTIONS` 相同，除了所选选项被关闭。

# CDROM_SELECT_SPEED - 设置 CD-ROM 速度

 - 使用方法：

   `int speed;`
   `ioctl(fd, CDROM_SELECT_SPEED, speed);`

 - 输入：
   新的驱动器速度。

 - 输出：无

 - 错误返回：

   `ENOSYS`  驱动器不支持速度选择。

# CDROM_SELECT_DISC - 选择光盘（对于光盘库）

 - 使用方法：

   `int disk;`
   `ioctl(fd, CDROM_SELECT_DISC, disk);`

 - 输入：
   要加载到驱动器中的光盘。

 - 输出：无

 - 错误返回：

   `EINVAL`  光盘编号超出驱动器容量

# CDROM_MEDIA_CHANGED - 检查介质是否已更改

 - 使用方法：

   `int slot;`
   `ioctl(fd, CDROM_MEDIA_CHANGED, slot);`

 - 输入：
   要测试的插槽编号，对于光盘库以外的设备始终为零。也可以是特殊值 `CDSL_NONE` 或 `CDSL_CURRENT`

 - 输出：
   Ioctl 返回值为 0 或 1，取决于介质是否已更改，错误时返回 -1。

 - 错误返回：

   `ENOSYS`  驱动器无法检测介质更改
   `EINVAL`  插槽编号超出驱动器容量
   `ENOMEM`  内存不足

# CDROM_DRIVE_STATUS - 获取托盘位置等

 - 使用方法：

   `int slot;`
   `ioctl(fd, CDROM_DRIVE_STATUS, slot);`

 - 输入：
   要测试的插槽编号，对于光盘库以外的设备始终为零。也可以是特殊值 `CDSL_NONE` 或 `CDSL_CURRENT`

 - 输出：
   Ioctl 返回值将是以下来自 `<linux/cdrom.h>` 的值之一：

   `CDS_NO_INFO`  信息不可用。
   `CDS_NO_DISC`
   `CDS_TRAY_OPEN`
   `CDS_DRIVE_NOT_READY`
   `CDS_DISC_OK`
   `-1`  错误

 - 错误返回：

   `ENOSYS`  驱动器无法检测驱动器状态
   `EINVAL`  插槽编号超出驱动器容量
   `ENOMEM`  内存不足

# CDROM_DISC_STATUS - 获取光盘类型等

 - 使用方法：

   `ioctl(fd, CDROM_DISC_STATUS, 0);`

 - 输入：无

 - 输出：
   Ioctl 返回值将是以下来自 `<linux/cdrom.h>` 的值之一：

   `CDS_NO_INFO`
   `CDS_AUDIO`
   `CDS_MIXED`
   `CDS_XA_2_2`
   `CDS_XA_2_1`
   `CDS_DATA_1`

 - 错误返回：目前无

 - 备注：
   源代码注释指出：

   好，问题从这里开始。CDROM_DISC_STATUS ioctl 的当前接口存在缺陷。它做出了错误的假设，即光盘要么全是 `CDS_DATA_1`，要么全是 `CDS_AUDIO` 等。不幸的是，虽然这种情况经常发生，但光盘上有一些数据轨道和一些音频轨道的情况也非常常见。仅仅因为我愿意，我声明以下是应对的最佳方式。如果光盘上有任何数据轨道，它将被返回为数据光盘。如果它有任何 XA 轨道，我将返回它为 XA 轨道。现在我可以通过将这些返回与上述返回组合来简化此接口，但这更清楚地表明了当前接口的问题。可惜这个接口不是设计为使用位掩码... - Erik

   嗯，现在我们有了选项 `CDS_MIXED`：混合类型的光盘。用户级程序员可能会觉得这个 ioctl 不是很有用。
   --- david

# CDROM_CHANGER_NSLOTS - 获取插槽数量

 - 使用方法：

   `ioctl(fd, CDROM_CHANGER_NSLOTS, 0);`

 - 输入：无

 - 输出：
   Ioctl 返回值将是 CD 换盘器中的插槽数量。对于非多盘设备通常为 1。

 - 错误返回：无

# CDROM_LOCKDOOR - 锁定或解锁门

 - 使用方法：

   `int lock;`
   `ioctl(fd, CDROM_LOCKDOOR, lock);`

 - 输入：
   门锁定标志，1 = 锁定，0 = 解锁

 - 输出：无

 - 错误返回：

   `EDRIVE_CANT_DO_THIS` 门锁定功能不支持。
   `EBUSY`  尝试解锁时，多个用户打开了驱动器且不是 `CAP_SYS_ADMIN`

 - 备注：
   截至 2.6.8.1，锁定标志是全局锁定，意味着所有 CD 驱动器将一起锁定或解锁。这可能是一个错误。

   `EDRIVE_CANT_DO_THIS` 值在 `<linux/cdrom.h>` 中定义，当前（2.6.8.1）与 `EOPNOTSUPP` 相同

# CDROM_DEBUG - 打开/关闭调试消息

 - 使用方法：

   `int debug;`
   `ioctl(fd, CDROM_DEBUG, debug);`

 - 输入：
   CDROM 调试标志，0 = 禁用，1 = 启用

 - 输出：
   Ioctl 返回值将是新的调试标志。

 - 错误返回：

   `EACCES`  访问被拒绝：需要 `CAP_SYS_ADMIN`

# CDROM_GET_CAPABILITY - 获取能力

 - 使用方法：

   `ioctl(fd, CDROM_GET_CAPABILITY, 0);`

 - 输入：无

 - 输出：
   Ioctl 返回值是当前设备的能力标志。参见 `CDC_CLOSE_TRAY`、`CDC_OPEN_TRAY` 等。

# CDROMAUDIOBUFSIZ - 设置音频缓冲区大小

 - 使用方法：

   `int arg;`
   `ioctl(fd, CDROMAUDIOBUFSIZ, val);`

 - 输入：
   新的音频缓冲区大小

 - 输出：
   Ioctl 返回值是新的音频缓冲区大小，错误时为 -1。

 - 错误返回：

   `ENOSYS`  此驱动程序不支持。

 - 备注：
   并非所有驱动程序都支持。

# DVD_READ_STRUCT - 读取结构

 - 使用方法：

   `dvd_struct s;`
   `ioctl(fd, DVD_READ_STRUCT, &s);`

 - 输入：
   `dvd_struct` 结构，包含：
   `type`  指定所需的信息，为以下之一：`DVD_STRUCT_PHYSICAL`、`DVD_STRUCT_COPYRIGHT`、`DVD_STRUCT_DISCKEY`、`DVD_STRUCT_BCA`、`DVD_STRUCT_MANUFACT`
   `physical.layer_num`  所需的层，从 0 索引
   `copyright.layer_num`  所需的层，从 0 索引
   `disckey.agid`

 - 输出：
   `dvd_struct` 结构，包含：
   `physical`  对于 `type == DVD_STRUCT_PHYSICAL`
   `copyright`  对于 `type == DVD_STRUCT_COPYRIGHT`
   `disckey.value`  对于 `type == DVD_STRUCT_DISCKEY`
   `bca.{len,value}`  对于 `type == DVD_STRUCT_BCA`
   `Manufact.{len,valu}`  对于 `type == DVD_STRUCT_MANUFACT`

 - 错误返回：

   `EINVAL`  `physical.layer_num` 超过层数
   `EIO`  从驱动器收到无效响应

# DVD_WRITE_STRUCT - 写入结构

 - 截至 2.6.8.1 未实现

# DVD_AUTH - 认证

 - 使用方法：

   `dvd_authinfo ai;`
   `ioctl(fd, DVD_AUTH, &ai);`

 - 输入：
   `dvd_authinfo` 结构。 参见 `<linux/cdrom.h>`

 - 输出：
   `dvd_authinfo` 结构。

 - 错误返回：

   `ENOTTY`  `ai.type` 未被识别。

# CDROM_SEND_PACKET - 向驱动器发送数据包

 - 使用方法：

   `struct cdrom_generic_command cgc;`
   `ioctl(fd, CDROM_SEND_PACKET, &cgc);`

 - 输入：
   包含要发送的数据包的 `cdrom_generic_command` 结构。

 - 输出：无

   `cdrom_generic_command` 结构包含结果。

 - 错误返回：

   `EIO`  命令失败。
   `EPERM`  操作不允许，要么是因为在只读打开的驱动器上尝试了写入命令，要么是因为该命令需要 `CAP_SYS_RAWIO`
   `EINVAL`  `cgc.data_direction` 未设置

# CDROM_NEXT_WRITABLE - 获取下一个可写入块

 - 使用方法：

   `long next;`
   `ioctl(fd, CDROM_NEXT_WRITABLE, &next);`

 - 输入：无

 - 输出：
   下一个可写入块。

 - 备注：
   如果设备不直接支持此 ioctl，ioctl 将返回 `CDROM_LAST_WRITTEN + 7`。

# CDROM_LAST_WRITTEN - 获取光盘上最后写入的块

 - 使用方法：

   `long last;`
   `ioctl(fd, CDROM_LAST_WRITTEN, &last);`

 - 输入：无

 - 输出：
   光盘上最后写入的块

 - 备注：
   如果设备不直接支持此 ioctl，结果将从光盘的目录表中派生。如果目录表无法读取，此 ioctl 将返回错误。