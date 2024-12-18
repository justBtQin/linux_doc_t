# APEI 错误注入

EINJ 提供了一种硬件错误注入机制。它对于调试和测试 APEI 及 RAS 特性通常非常有用。

你需要首先检查你的 BIOS 是否支持 EINJ。为此，查看类似以下的早期启动消息：

ACPI: EINJ 0x000000007370A000 000150 (v01 INTEL 00000001 INTL 00000001)

这表明 BIOS 正在公开一个 EINJ 表 - 这是进行注入的机制。

或者，在 /sys/firmware/acpi/tables 中查找一个“EINJ”文件，这是同一事物的不同表示形式。

如果上述内容不存在，并不一定意味着不支持 EINJ：在放弃之前，进入 BIOS 设置以查看 BIOS 是否有启用错误注入的选项。查找名为 WHEA 或类似的内容。通常，你需要先启用 ACPI5 支持选项，以便在 BIOS 菜单中看到 APEI、EINJ 等功能得到支持和公开。

要使用 EINJ，请确保在你的内核配置中启用了以下选项：

```
CONFIG_DEBUG_FS
CONFIG_ACPI_APEI
CONFIG_ACPI_APEI_EINJ
```

EINJ 用户界面位于<debugfs 挂载点>/apei/einj。

以下文件属于它：

```
- available_error_type
	此文件显示支持哪些错误类型：
	错误类型值	错误描述
	================	=================
	0x00000001	处理器可纠正
	0x00000002	处理器不可纠正非致命
	0x00000004	处理器不可纠正致命
	0x00000008	内存可纠正
	0x00000010	内存不可纠正非致命
	0x00000020	内存不可纠正致命
	0x00000040	PCI Express 可纠正
	0x00000080	PCI Express 不可纠正致命
	0x00000100	PCI Express 不可纠正非致命
	0x00000200	平台可纠正
	0x00000400	平台不可纠正非致命
	0x00000800	平台不可纠正致命
	
	文件内容的格式如上所示，除了仅显示可用的错误类型。

- error_type
	设置要注入的错误类型的值。可能的错误类型在上面的 available_error_type 文件中定义。

- error_inject
	向此文件写入任何整数以触发错误注入。确保你已经指定了所有必要的错误参数，即此写入应该是注入错误的最后一步。

- flags
	对于内核版本 3.13 及以上存在。用于指定在注入期间固件应使用哪些 param{1..4}。值是 ACPI5.0 规范中 SET_ERROR_TYPE_WITH_ADDRESS 数据结构所指定的位掩码：
		位 0 - 处理器 APIC 字段有效（见下面的 param3）。 	
		位 1 - 内存地址和掩码有效（param1 和 param2）。 	
		位 2 - PCIe（段、总线、设备、函数）有效（见下面的 param4）。
	如果设置为零，则模拟旧行为，其中注入类型仅设置一位，并且 param1 被复用。

- param1
	此文件用于设置第一个错误参数值。其效果取决于 error_type 中指定的错误类型。例如，如果错误类型是与内存相关的类型，则 param1 应该是有效的物理内存地址。[除非“flag”已设置 - 见上文]

- param2
	与上面的 param1 用途相同。例如，如果错误类型是与内存相关的类型，则 param2 应该是物理内存地址掩码。  Linux 需要页面或更窄的粒度，例如 0xfffffffffffff000。

- param3
	当“flags”中的 0x1 位设置时使用，以指定 APIC ID

- param4
	当“flags”中的 0x4 位设置时使用，以指定目标 PCIe 设备

- notrigger
	错误注入机制是一个两步过程。首先注入错误，然后执行一些操作来触发它。将“notrigger”设置为 1 跳过触发阶段，这可能允许用户通过简单地访问作为错误注入目标的 CPU、内存位置或设备，在其他上下文中导致错误。这是否实际有效取决于 BIOS 在触发阶段实际包含的操作。
```

基于 ACPI 4.0 规范的 BIOS 版本在控制错误注入的位置方面有有限的选项。你的 BIOS 可能支持一个扩展（通过 param_extension=1 模块参数或引导命令行 einj.param_extension=1 启用）。这允许通过 apei/einj 中的 param1 和 param2 文件指定内存注入的地址和掩码。

基于 ACPI 5.0 规范的 BIOS 版本对注入的目标有更多的控制。对于与处理器相关的错误（类型 0x1、0x2 和 0x4），你可以将标志设置为 0x3（param3 用于位 0，param1 和 param2 用于位 1），以便在注入的错误签名中添加更多信息。实际传递的数据如下：

```
	memory_address = param1;
	memory_address_range = param2;
	apicid = param3;
	pcie_sbdf = param4;
```

对于内存错误（类型 0x8、0x10 和 0x20），使用 param1 设置地址，并在 param2 中设置掩码（0x0 等效于全 1）。对于 PCI Express 错误（类型 0x40、0x80 和 0x100），使用 param1 指定段、总线、设备和函数：

```
         31     24 23    16 15    11 10      8  7        0
	+-------------------------------------------------+
	| segment |   bus  | device | function | reserved |
	+-------------------------------------------------+
```

无论如何，你明白了，如果有疑问，只需查看 drivers/acpi/apei/einj.c 中的代码。

一个 EINJ 错误注入示例：

```
# cd /sys/kernel/debug/apei/einj
# cat available_error_type		# 查看可以注入哪些错误
0x00000002	处理器不可纠正非致命
0x00000008	内存可纠正
0x00000010	内存不可纠正非致命
# echo 0x12345000 > param1		# 设置注入的内存地址
# echo $((-1 << 12)) > param2		# 掩码 0xfffffffffffff000 - 此页面中的任何位置
# echo 0x8 > error_type			# 选择可纠正内存错误
# echo 1 > error_inject			# 现在注入
```

你应该在 dmesg 中看到类似这样的内容：

```
[22715.830801] EDAC sbridge MC3: HANDLING MCE MEMORY ERROR
[22715.834759] EDAC sbridge MC3: CPU 0: Machine Check Event: 0 Bank 7: 8c00004000010090
[22715.834759] EDAC sbridge MC3: TSC 0
[22715.834759] EDAC sbridge MC3: ADDR 12345000 EDAC sbridge MC3: MISC 144780c86
[22715.834759] EDAC sbridge MC3: PROCESSOR 0:306e7 TIME 1422553404 SOCKET 0 APIC 0
[22716.616173] EDAC MC3: 1 CE memory read error on CPU_SrcID#0_Channel#0_DIMM#0 (channel:0 slot:0 page:0x12345 offset:0x0 grain:32 syndrome:0x0 -  area:DRAM err_code:0001:0090 socket:0 channel_mask:1 rank:0)
```

有关 EINJ 的更多信息，请参考 ACPI 规范版本 4.0，第 17.5 节和 ACPI 5.0，第 18.6 节。