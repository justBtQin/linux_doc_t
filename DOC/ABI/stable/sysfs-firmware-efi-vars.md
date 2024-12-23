**What**：/sys/firmware/efi/vars

**Date**：2004 年 4 月

**Contact**：Matt Domsch <Matt_Domsch@dell.com>

**Description**：

此目录为与 EFI 变量的交互提供接口。有关 EFI 变量的更多信息，请参阅 UEFI 规范中的“变量服务”（规范版本 2.3 勘误 D 中的第 7.2 节）。简而言之，EFI 变量是有名称的，并通过使用供应商 GUID 分类到不同的命名空间中。它们还具有与之关联的任意二进制值。

efivars 模块枚举这些变量，并为每个找到的变量创建一个单独的目录。每个目录的名称形式为“<key>-<vendor guid>”，并包含以下文件：

**attributes**：一个只读的文本文件，枚举 EFI 变量标志。可能的值包括：

EFI_VARIABLE_NON_VOLATILE

EFI_VARIABLE_BOOTSERVICE_ACCESS

EFI_VARIABLE_RUNTIME_ACCESS

EFI_VARIABLE_HARDWARE_ERROR_RECORD

EFI_VARIABLE_AUTHENTICATED_WRITE_ACCESS

请参阅 EFI 文档以了解这些变量的每个解释。

**data**：一个只读的二进制文件，可以读取以获取 EFI 变量的值。

**guid**：变量的供应商 GUID。此值应始终与变量名称中的 GUID 匹配。

**raw_var**：一个二进制文件，可以读取以获取包含有关变量的所有信息的结构。有关结构定义，请参阅内核源代码中的“struct efi_variable”。此文件也可以写入以更新变量的值。然而，要使其正常工作，传递的“struct efi_variable”的所有字段必须与从文件中读取的结构逐字节匹配，除了值部分。

**注意**：通过此文件读取/写入的 efi_variable 结构包含一个“long”类型，其宽度可能因底层架构而异。

**size**：变量值大小的 ASCII 表示形式。

此外，在顶级目录中还提供了另外两个神奇的二进制文件，用于添加和删除变量：

**new_var**：接受一个“struct efi_variable”并指示 EFI 固件创建一个新变量。

**del_var**：接受一个“struct efi_variable”并指示 EFI 固件删除任何具有匹配供应商 GUID 和变量键名的变量。