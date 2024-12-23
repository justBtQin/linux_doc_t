**What**: /sys/firmware/dmi/

**Date**: February 2011

**Contact**: Mike Waychison <mikew@google.com>

**Description**:

许多机器的固件（x86 和 ia64）将 DMI / SMBIOS 表导出到操作系统。获取此信息通常对用户空间很有价值，特别是在使用 OEM 扩展的情况下。

内核本身并不依赖这些表中的大部分信息是正确的。同样，它也不能确保导出到用户空间的数据没有错误。

DMI 被构造为一个大型的条目表，其中每个条目都有一个通用的头，指示条目类型和长度，以及一个固件提供的“句柄”，该句柄在所有条目中应该是唯一的。

一些条目是规范要求的，但许多其他条目是可选的。然而，一般来说，用户不应该期望在他们的系统上找到特定的条目类型，除非他们确定他们的固件在做什么。机器到机器的经验会有所不同。

允许有多个相同类型的条目。为了处理这些重复的条目类型，每个条目由操作系统分配一个“实例”，该实例是从条目类型的序号位置派生的。也就是说，如果 DMI 表中有“N”个相同类型“T”的条目（相邻或分散，都没关系），它们将在 sysfs 中表示为条目“T-0”到“T-(N-1)”：

示例条目目录：

/sys/firmware/dmi/entries/17-0

/sys/firmware/dmi/entries/17-1

/sys/firmware/dmi/entries/17-2

/sys/firmware/dmi/entries/17-3

... 

实例编号用于代替固件分配的条目句柄，因为内核本身不能保证导出的句柄是唯一的，并且在实际中可能有固件映像会出错。

sysfs 中的每个 DMI 条目都将通用头值导出为属性：

handle：固件分配给此条目的 16 位“句柄”。其他条目可能会引用此句柄。

length：条目本身中呈现的条目长度。请注意，这不是与条目相关的总字节数。此值表示条目“格式化”部分的长度。此“格式化”区域有时后面跟着由空终止字符串组成的“未格式化”区域，连续两个空字符表示终止。

raw：条目的原始字节。这包括条目“格式化”部分、条目“未格式化”字符串部分以及两个终止空字符。

type：条目的类型。此值与目录名称中的相同。它指示如何解释条目的其余部分。

instance：给定类型的条目的实例序号。此值与父目录名称中的相同。

position：在整个 DMI 条目表中条目的序号位置（从零开始）。

=== 条目特化 ===

一些条目类型在 sysfs 中可能有其他可用信息。并非所有类型都经过特化。

--- 类型 15 - 系统事件日志 ---

此条目允许固件导出系统已发生的事件日志。此信息通常由 nvram 支持，但实现细节由该表抽象。此条目的数据在目录中导出：

/sys/firmware/dmi/entries/15-0/system_event_log

并且具有以下属性（在 SMBIOS / DMI 规范中“系统事件日志（类型 15）”下记录）：

area_length

header_start_offset

data_start_offset

access_method

status

change_token

access_method_address

header_format

per_log_type_descriptor_length

type_descriptors_supported_count

此外，内核还导出二进制属性：

raw_event_log：DMI 条目中描述的事件日志的原始二进制位。