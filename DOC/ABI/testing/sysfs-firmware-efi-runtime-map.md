**What**: `/sys/firmware/efi/runtime-map/`

**Date**: December 2013

**Contact**: Dave Young <dyoung@redhat.com>

**Description**: 切换 efi 运行时服务到虚拟模式需要将所有具有运行时属性位设置为的 efi 内存范围映射到虚拟地址。efi 运行时服务只能在不重新启动的情况下一次切换到虚拟模式。kexec 内核必须保持与第一个内核相同的物理到虚拟地址映射。映射被导出到 sysfs，以便用户空间工具可以重新组合它们并将它们传递到 kexec 内核。`/sys/firmware/efi/runtime-map/`是内核导出该信息的目录。子目录按内存范围的编号命名：

`/sys/firmware/efi/runtime-map/0`

`/sys/firmware/efi/runtime-map/1`

`/sys/firmware/efi/runtime-map/2`

`/sys/firmware/efi/runtime-map/3`

... 

每个子目录包含五个文件：

`attribute`：内存范围的属性。

`num_pages`：内存范围的页面大小。

`phys_addr`：内存范围的物理地址。

`type`：内存范围的类型。

`virt_addr`：内存范围的虚拟地址。

以上值都是带有“0x”前缀的十六进制数字。

**Users**: Kexec