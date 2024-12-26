此目录包含 NetWinder 浮点模拟器的 0.92 版本测试发布。

大部分代码由我（Scott Bambrough）编写。它是用 C 语言编写的，在需要的地方有少量的内联汇编例程。它编写得很快，目标是作为第一个目标实现编译器发出的所有浮点指令的工作版本。我已尽力做到尽可能优化，但仍有很大的改进空间。

我已尝试使模拟器尽可能可移植。其中一个问题是内核符号上的前导下划线。Elf 内核没有前导下划线，a.out 编译的内核有。在可能重要的地方，我已尝试使用`C_SYMBOL_NAME`宏。

我做出的另一个选择是文件结构。我已尝试将所有操作系统特定的代码包含在一个模块（`fpmodule.*`）中。所有其他文件包含模拟器特定的代码。这应该允许其他人相对容易地将模拟器移植到 NetBSD 等系统。

浮点运算基于 John Hauser 的 SoftFloat 版本 2。SoftFloat 是符合 IEC/IEEE 二进制浮点算术标准的浮点软件实现。支持多达四种格式：单精度、双精度、扩展双精度和四精度。实现了标准要求的所有操作，除了十进制的转换。我们仅使用单精度、双精度和扩展双精度格式。SoftFloat 到 ARM 的移植由 Phil Blundell 完成，基于 Neil Carson 为 NetBSD/arm32 进行的早期 SoftFloat 版本 1 的移植。

`README.FPE`文件包含模拟器到目前为止已实现内容的描述。`TODO`文件包含关于仍需完成的事项以及模拟器的其他想法的信息。

错误报告、评论和建议应发送至我<scottb@netwinder.org>。关于“安装你的模拟器后此程序无法正常工作”的一般报告对于确定仍存在错误很有用；但在试图隔离问题时几乎毫无用处。请报告它们，但不要期望有快速的行动。仍然存在错误。问题仍然在于隔离哪个指令包含错误。说明特定问题的小程序是天赐之物。

# 法律声明

-------------

NetWinder 浮点模拟器是免费软件。Rebel.com 编写的所有内容均根据 GNU GPL 提供。有关复制条件，请参阅`COPYING`文件。上述内容不包括 SoftFloat 代码。John Hauser 对 SoftFloat 的法律声明如下。

-------------------------------------------------------------------------------

# SoftFloat 法律声明

SoftFloat 由 John R. Hauser 编写。这项工作的部分实现得益于位于加利福尼亚州伯克利市中心街 1947 号 600 套房的国际计算机科学研究所。部分资金由美国国家科学基金会在 MIP-9311980 资助下提供。此代码的原始版本是与加利福尼亚大学伯克利分校合作构建定点向量处理器项目的一部分，由 Nelson Morgan 和 John Wawrzynek 教授监督。

本软件按原样免费分发。尽管已尽合理努力避免，但本软件可能包含有时会导致错误行为的故障。本软件的使用仅限于能够并将对其使用引起的任何和所有损失、成本或其他问题承担全部责任的个人和组织。

-------------------------------------------------------------------------------