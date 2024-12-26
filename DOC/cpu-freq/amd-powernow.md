<markdown_document>

# PowerNow! 和 Cool'n'Quiet 是 AMD 在 AMD 处理器中用于频率管理功能的名称。随着处理器新一代硬件实现的变化，每一代都有不同的 cpu-freq 驱动程序。

注意，驱动程序不会在“错误”的硬件上加载，因此当不确定哪个是正确的驱动程序时，可以依次尝试每个驱动程序，这是安全的。

注意，并非所有处理器都具备更改频率（和电压）的功能。驱动程序将拒绝在没有此功能的处理器上加载。此功能通过 cpuid 指令进行检测。

驱动程序使用 BIOS 提供的表来获取特定平台合适的频率和电压信息。如果 BIOS 不提供这些表，频率转换将不可用。

第六代：powernow-k6

第七代：powernow-k7：Athlon、Duron、Geode。

第八代：powernow-k8：Athlon、Athlon 64、Opteron、Sempron。关于第八代处理器中此功能的文档可在“BIOS 和内核开发者指南”，出版物 26094 中找到，第 9 章，可从 www.amd.com 下载。

BIOS 提供的 powernow-k7 和 powernow-k8 的数据可以来自 PSB 表或 ACPI 对象。仅当内核配置设置了 CONFIG_ACPI_PROCESSOR 时，才支持 ACPI。如果进行了这样的配置，powernow-k8 驱动程序将尝试使用 ACPI，如果失败则回退到 PST。powernow-k7 驱动程序将首先尝试使用 PSB 支持，如果 PSB 支持失败则回退到 ACPI。提供了一个模块参数 acpi_force，用于强制使用 ACPI 支持而不是 PSB 支持。

</markdown_document>