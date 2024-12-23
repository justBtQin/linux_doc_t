**1. INTRODUCTION**

现代文件系统具有数据和元数据的校验和功能，以防止数据损坏。然而，对损坏的检测是在读取时进行的，这可能是在数据写入后的数月之后。在那一点上，应用程序尝试写入的原始数据很可能已经丢失。

解决方案是确保磁盘实际上存储了应用程序所期望的内容。SCSI 家族协议（SBC 数据完整性字段、SCC 保护提案）以及 SATA/T13（外部路径保护）的最新添加试图通过添加对将完整性元数据附加到 I/O 的支持来解决这个问题。完整性元数据（或 SCSI 术语中的保护信息）包括每个扇区的校验和以及一个递增计数器，以确保各个扇区以正确的顺序写入。对于某些保护方案，还可以确保 I/O 被写入磁盘上的正确位置。

当前的存储控制器和设备实现了各种保护措施，例如校验和和清理。但这些技术在它们自己的孤立域中工作，或者最多在 I/O 路径中的相邻节点之间工作。关于 DIF 和其他完整性扩展的有趣之处在于，保护格式定义良好，I/O 路径中的每个节点都可以验证 I/O 的完整性，并在检测到损坏时拒绝它。这不仅允许防止损坏，还可以隔离故障点。

**2. THE DATA INTEGRITY EXTENSIONS**

如所写，协议扩展仅保护控制器和存储设备之间的路径。然而，许多控制器实际上允许操作系统与完整性元数据（IMD）进行交互。我们一直在与几家 FC/SAS HBA 供应商合作，以实现保护信息在其控制器之间的传输。

SCSI 数据完整性字段通过在每个扇区后附加 8 字节的保护信息来工作。数据+完整性元数据存储在磁盘上的 520 字节扇区中。在控制器和目标之间传输时，数据+IMD 是交错的。T13 提案类似。

由于操作系统处理 520（和 4104）字节扇区非常不方便，我们与几家 HBA 供应商联系，并鼓励他们允许分离数据和完整性元数据的分散-聚集列表。

控制器将在写入时交错缓冲区，并在读取时拆分它们。这意味着 Linux 可以在不更改页缓存的情况下将数据缓冲区 DMA 到主机内存和从主机内存 DMA 出来。

此外，SCSI 和 SATA 规范要求的 16 位 CRC 校验和在软件中计算有些繁重。基准测试发现，计算此校验和对许多工作负载的系统性能有显著影响。一些控制器允许在与操作系统接口时使用更轻量级的校验和。例如，Emulex 支持 TCP/IP 校验和。从操作系统接收的 IP 校验和在写入时转换为 16 位 CRC，反之亦然。这允许 Linux 或应用程序以非常低的成本（与软件 RAID5 相当）生成完整性元数据。

在检测位错误方面，IP 校验和比 CRC 弱。然而，真正的优势在于数据缓冲区和完整性元数据的分离。这两个不同的缓冲区必须匹配才能完成 I/O。

数据和完整性元数据缓冲区的分离以及校验和的选择被称为数据完整性扩展。由于这些扩展超出了协议机构（T10、T13）的范围，Oracle 及其合作伙伴正在尝试在存储网络行业协会内对其进行标准化。

**3. KERNEL CHANGES**

Linux 中的数据完整性框架允许将保护信息固定到 I/O 并发送到/从支持它的控制器接收。

SCSI 和 SATA 中完整性扩展的优点是，它们使我们能够保护从应用程序到存储设备的整个路径。然而，与此同时，这也是最大的缺点。这意味着保护信息必须采用磁盘能够理解的格式。

通常，Linux/POSIX 应用程序对它们正在访问的存储设备的细节不敏感。虚拟文件系统开关和块层使硬件扇区大小和传输协议等事情对应用程序完全透明。

然而，在准备发送到磁盘的保护信息时，需要这种细节级别。因此，端到端保护方案的概念完全违反了分层原则。对于应用程序来说，知道它是否正在访问 SCSI 或 SATA 磁盘是完全不合理的。

Linux 中实现的数据完整性支持试图将此从应用程序中隐藏起来。就应用程序（在某种程度上也包括内核）而言，完整性元数据是附加到 I/O 的不透明信息。

当前的实现允许块层自动为任何 I/O 生成保护信息。最终的意图是将完整性元数据计算移动到用户空间以处理用户数据。在内核中生成的元数据和其他 I/O 仍将使用自动生成接口。

一些存储设备允许每个硬件扇区用 16 位值标记。此标记空间的所有者是块设备的所有者。即在大多数情况下是文件系统。文件系统可以根据需要使用此额外空间标记扇区。由于标记空间有限，块接口允许通过交错标记更大的块。这样，典型的 4KB 文件系统块可以附加 8*16 位的信息。

这也意味着诸如 fsck 和 mkfs 之类的应用程序需要从用户空间访问以操纵标记。正在为此工作的一个直通接口。

**4. BLOCK LAYER IMPLEMENTATION DETAILS**

**4.1 BIO**

当启用`CONFIG_BLK_DEV_INTEGRITY`时，数据完整性补丁会向`struct bio`添加一个新字段。`bio_integrity(bio)`返回一个指向`struct bip`的指针，其中包含生物完整性有效负载。本质上，`bip`是一个精简的`struct bio`，它包含一个`bio_vec`，其中包含完整性元数据和所需的管理信息（`bvec 池`、向量计数等）。

内核子系统可以通过调用`bio_integrity_alloc(bio)`在`bio`上启用数据完整性保护。这将分配并将`bip`附加到`bio`。

随后，可以使用`bio_integrity_add_page()`附加包含完整性元数据的单个页面。

`bio_free()`将自动释放`bip`。

**4.2 BLOCK DEVICE**

由于保护数据的格式与物理磁盘相关，每个块设备都使用`block integrity profile`（`struct blk_integrity`）进行了扩展。此可选配置文件使用`blk_integrity_register()`向块层注册。

该配置文件包含用于生成和验证保护数据的回调函数，以及获取和设置应用程序标签的函数。该配置文件还包含一些常量，以帮助完成、合并和拆分完整性元数据。

分层块设备需要选择一个适合所有子设备的配置文件。`blk_integrity_compare()`可以帮助实现这一点。目前支持 DM 和 MD 线性、RAID0 和 RAID1。由于应用程序标签，RAID4/5/6 需要额外的工作。**5.0 BLOCK LAYER INTEGRITY API**

**5.1 NORMAL FILESYSTEM**

普通文件系统不知道底层块设备能够发送/接收完整性元数据。对于写操作，在`submit_bio()`时块层将自动生成 IMD。读请求将在完成时验证 I/O 完整性。

可以使用以下标志来切换 IMD 生成和验证：

`/sys/block/<bdev>/integrity/write_generate`

和

`/sys/block/<bdev>/integrity/read_verify`

**5.2 INTEGRITY-AWARE FILESYSTEM**

具有完整性感知的文件系统可以准备带有附加 IMD 的 I/O。如果块设备支持，它还可以使用应用程序标签空间。

`int bio_integrity_prep(bio);`

要为写生成 IMD 并为读设置缓冲区，文件系统必须调用`bio_integrity_prep(bio)`。在调用此函数之前，必须设置`bio`数据方向和起始扇区，并且`bio`应该添加了所有数据页。由调用者确保在 I/O 进行期间`bio`不会改变。`bio_integrity_prep()`仅应在`bio_integrity_enabled()`返回 1 时调用。

**5.3 PASSING EXISTING INTEGRITY METADATA**

生成自己的完整性元数据或能够从用户空间传输 IMD 的文件系统可以使用以下调用：

`struct bip * bio_integrity_alloc(bio, gfp_mask, nr_pages);`

分配`bio`完整性有效负载并将其挂接到`bio`上。`nr_pages`表示需要在完整性`bio_vec`列表中存储多少页的保护数据（类似于`bio_alloc()`）。完整性有效负载将在`bio_free()`时释放。

`int bio_integrity_add_page(bio, page, len, offset);`

将包含完整性元数据的页附加到现有`bio`。`bio`必须有一个现有的`bip`，即`bio_integrity_alloc()`必须已被调用。对于写操作，页中的完整性元数据必须是目标设备理解的格式，但有一个显著的例外，即扇区编号将在请求遍历 I/O 堆栈时重新映射。这意味着使用此调用添加的页在 I/O 期间将被修改！完整性元数据中的第一个引用标签必须具有`bip->bip_sector`的值。只要`bip`的`bio_vec`数组（`nr_pages`）中有空间，就可以使用`bio_integrity_add_page()`添加页。在完成读操作后，附加的页将包含从存储设备接收的完整性元数据。由接收者处理它们并在完成时验证数据完整性。

**5.4 REGISTERING A BLOCK DEVICE AS CAPABLE OF EXCHANGING INTEGRITY METADATA**

要在块设备上启用完整性交换，必须将`gendisk`注册为能够：

`int blk_integrity_register(gendisk, blk_integrity);`

`blk_integrity`结构是一个模板，应包含以下内容：

```
`static struct blk_integrity my_profile = {
	.name                   = "STANDARDSBODY-TYPE-VARIANT-CSUM",
	.generate_fn            = my_generate_fn,
	.verify_fn              = my_verify_fn,
	.tuple_size             = sizeof(struct my_tuple_size),
	.tag_size               = <tag bytes per hw sector>,
};`
```

`name`是一个文本字符串，在`sysfs`中可见。这是用户空间 API 的一部分，因此要仔细选择并永远不要更改它。格式为标准机构-类型-变体。例如`T10-DIF-TYPE1-IP`或`T13-EPP-0-CRC`。`generate_fn`生成适当的完整性元数据（用于写）。`verify_fn`验证数据缓冲区是否与完整性元数据匹配。`tuple_size`必须设置为匹配每个扇区的完整性元数据大小。即对于 DIF 和 EPP 为 8。`tag_size`必须设置为标识每个硬件扇区可用的标签空间字节数。对于 DIF，根据控制模式页 ATO 位的值，它可以是 2 或 0。

**2007-12-24 Martin K. Petersen <martin.petersen@oracle.com>**