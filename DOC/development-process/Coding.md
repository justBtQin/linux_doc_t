# 4: 使代码正确

虽然对于一个坚实且面向社区的设计过程有很多可说的，但任何内核开发项目的证明都在于最终的代码。是代码将被其他开发人员检查并合并（或不合并）到主线树中。因此，此代码的质量将决定项目的最终成功。

本节将研究编码过程。我们将从查看内核开发人员可能出错的一些方式开始。然后重点将转向正确做事以及可以帮助实现这一目标的工具。

## 4.1: 陷阱

* **编码风格**

内核长期以来一直有一个标准的编码风格，在 Documentation/CodingStyle 中进行了描述。在很长一段时间里，该文件中描述的策略最多被视为建议性的。结果，内核中有大量代码不符合编码风格指南。该代码的存在给内核开发人员带来了两个独立的危害。

首先，认为内核编码标准不重要且不受强制执行。事实上，如果代码不符合标准，将新代码添加到内核中是非常困难的；许多开发人员将要求在审查代码之前对其进行重新格式化。像内核这样庞大的代码库需要一定程度的代码统一性，以使开发人员能够快速理解其任何部分。因此，不再有奇怪格式代码的空间。

偶尔，内核的编码风格会与雇主规定的风格发生冲突。在这种情况下，在代码可以合并之前，内核的风格必须获胜。将代码放入内核意味着在许多方面放弃一定程度的控制权 - 包括对代码格式的控制。

另一个陷阱是假设内核中已经存在的代码迫切需要进行编码风格修复。开发人员可能会开始生成重新格式化补丁，作为熟悉该过程的一种方式，或者作为将其名称放入内核变更日志中的一种方式 - 或者两者兼而有之。但是，纯编码风格修复被开发社区视为噪音；它们往往会受到冷淡的接待。因此，最好避免这种类型的补丁。在出于其他原因处理代码时，自然会修复代码的风格，但不应仅仅为了风格而进行更改。

编码风格文档也不应被视为绝对的法律，永远不能违反。如果有充分的理由违反风格（例如，为了适应 80 列限制而拆分后可读性大大降低的行），那就这样做吧。

* **抽象层**

计算机科学教授教导学生以灵活性和信息隐藏的名义广泛使用抽象层。当然，内核广泛使用抽象；涉及数百万行代码的任何项目都必须这样做才能生存。但经验表明，过度或过早的抽象可能与过早的优化一样有害。抽象应使用到所需的级别，不再进一步。

在简单的层面上，考虑一个函数，其参数始终由所有调用者传递为零。人们可以保留该参数，以防有人最终需要使用它提供的额外灵活性。然而，到那时，很可能实现此额外参数的代码已以某种微妙的方式被破坏，而从未被注意到 - 因为它从未被使用过。或者，当对额外灵活性的需求出现时，它不会以与程序员早期期望相匹配的方式出现。内核开发人员通常会提交补丁以删除未使用的参数；一般来说，不应首先添加它们。

隐藏对硬件访问的抽象层 - 通常是为了允许驱动程序的大部分与多个操作系统一起使用 - 尤其不受欢迎。此类层会掩盖代码并可能导致性能损失；它们不属于 Linux 内核。

另一方面，如果发现自己从另一个内核子系统复制大量代码，是时候问一下是否实际上有意义将其中一些代码提取到单独的库中或在更高层次上实现该功能。在整个内核中复制相同的代码没有价值。

* **#ifdef 和预处理程序的一般使用**

C 预处理程序似乎对一些 C 程序员具有强大的吸引力，他们将其视为在源文件中有效地编码大量灵活性的一种方式。但是，预处理程序不是 C，大量使用它会导致代码更难让他人阅读，也更难让编译器检查正确性。大量使用预处理程序几乎总是需要进行一些清理工作的代码的标志。

使用 #ifdef 进行条件编译确实是一个强大的功能，并且在内核中使用。但是，很少希望看到大量使用 #ifdef 块的代码。一般来说，#ifdef 的使用应尽可能限制在头文件中。有条件编译的代码可以限制在函数中，如果代码不存在，这些函数将简单地变为空。然后编译器将安静地优化掉对空函数的调用。结果是更清晰的代码，更容易理解。

C 预处理程序宏存在许多危险，包括可能多次评估带有副作用的表达式且没有类型安全性。如果您倾向于定义一个宏，请考虑创建一个内联函数代替。结果代码将是相同的，但内联函数更易于阅读，不会多次评估其参数，并允许编译器对参数和返回值进行类型检查。

* **内联函数**

内联函数本身也存在危险。程序员可能会被避免函数调用所固有的感知效率所吸引，并在源文件中填充内联函数。然而，这些函数实际上可能会降低性能。由于它们的代码在每个调用站点都被复制，它们最终会使编译后的内核大小膨胀。这反过来又会给处理器的内存缓存带来压力，从而可能大大降低执行速度。内联函数通常应该非常小且相对较少。毕竟，函数调用的成本并不高；创建大量内联函数是过早优化的经典示例。

一般来说，内核程序员忽视缓存效应是很危险的。在开始数据结构课程中教授的经典时间/空间权衡通常不适用于当代硬件。空间就是时间，因为较大的程序将比更紧凑的程序运行得更慢。

较新的编译器在决定给定函数是否实际应该内联方面发挥着越来越积极的作用。因此，自由放置“内联”关键字可能不仅是过度的；它也可能是无关紧要的。

* **锁定**

2006 年 5 月，“Devicescape”网络堆栈在盛大的宣传下根据 GPL 发布，并可用于包含在主线内核中。这一捐赠是受欢迎的消息；Linux 中对无线网络的支持充其量被认为是不合格的，而 Devicescape 堆栈提供了修复这种情况的希望。然而，直到 2007 年 6 月（2.6.22），此代码才实际进入主线。发生了什么？

此代码显示出许多在公司内部开发的迹象。但特别大的一个问题是，它没有设计为在多处理器系统上工作。在这个网络堆栈（现在称为 mac80211）可以合并之前，需要将一个锁定方案改装到它上面。

曾经，Linux 内核代码可以在不考虑多处理器系统带来的并发问题的情况下进行开发。然而，现在本文档是在一台双核笔记本电脑上编写的。即使在单处理器系统上，为提高响应性而进行的工作也会提高内核中的并发级别。内核代码可以在不考虑锁定的情况下编写的日子早已过去。

任何可能被多个线程同时访问的资源（数据结构、硬件寄存器等）都必须由锁保护。新代码应该在编写时考虑到这一要求；事后改装锁定是一项相当困难的任务。内核开发人员应该花时间充分理解可用的锁定原语，以便为工作选择正确的工具。对并发缺乏关注的代码在进入主线时将面临困难。

* **回归**

最后一个值得提及的危害是：可能会很想进行一个更改（可能带来重大改进），但这会导致现有用户的某些功能出现故障。这种更改称为“回归”，在主线内核中，回归已变得非常不受欢迎。除少数例外情况外，如果回归不能及时修复，导致回归的更改将被撤销。最好首先避免回归。

经常有人认为，如果回归使更多的人受益而不是造成问题，那么回归是合理的。如果一个更改为十个系统带来新功能，而只为一个系统造成问题，为什么不进行更改呢？2007 年 7 月，Linus 对此问题的最佳回答是：

所以我们不会通过引入新问题来修复错误。那样会导致疯狂，而且没有人知道你是否真的取得了任何实际进展。是向前两步，向后一步，还是向前一步，向后两步？

（http://lwn.net/Articles/243460/）。

一种特别不受欢迎的回归类型是对用户空间 ABI 的任何更改。一旦一个接口已导出到用户空间，就必须无限期地支持它。这一事实使得创建用户空间接口特别具有挑战性：由于它们不能以不兼容的方式更改，所以必须在第一次就做好。因此，对于用户空间接口，总是需要大量的思考、清晰的文档和广泛的审查。

## 4.2: 代码检查工具

至少目前，编写无错误的代码仍然是我们中很少有人能够达到的理想。然而，我们可以希望在我们的代码进入主线内核之前尽可能多地捕获和修复这些错误。为此，内核开发人员组合了一系列令人印象深刻的工具，可以以自动化的方式捕获各种模糊的问题。计算机捕获的任何问题都是以后不会困扰用户的问题，因此有理由在可能的情况下使用自动化工具。

第一步只是注意编译器产生的警告。当代版本的 gcc 可以检测（并警告）大量潜在的错误。通常，这些警告指向实际问题。提交审查的代码通常不应产生任何编译器警告。在消除警告时，要小心理解真正的原因，并尝试避免仅消除警告而不解决其原因的“修复”。

请注意，并非所有编译器警告都默认启用。使用“make EXTRA_CFLAGS=-W”构建内核以获得完整的警告集。

内核提供了几个启用调试功能的配置选项；其中大多数在“内核黑客”子菜单中。对于任何用于开发或测试目的的内核，应打开其中的几个选项。特别是，您应该打开：

- ENABLE_WARN_DEPRECATED、ENABLE_MUST_CHECK 和 FRAME_WARN，以获得关于使用已弃用接口或忽略函数的重要返回值等问题的额外警告集。这些警告生成的输出可能很冗长，但不必担心来自内核其他部分的警告。

- DEBUG_OBJECTS 将添加代码来跟踪内核创建的各种对象的生命周期，并在事情按顺序完成时发出警告。如果您正在添加一个创建（并导出）自己的复杂对象的子系统，请考虑添加对对象调试基础结构的支持。

- DEBUG_SLAB 可以发现各种内存分配和使用错误；它应该在大多数开发内核上使用。

- DEBUG_SPINLOCK、DEBUG_ATOMIC_SLEEP 和 DEBUG_MUTEXES 将发现许多常见的锁定错误。

还有很多其他调试选项，其中一些将在下面讨论。其中一些对性能有重大影响，不应一直使用。但是，花一些时间学习可用的选项可能会很快得到回报。

较重的调试工具之一是锁定检查器，或“lockdep”。此工具将跟踪系统中每个锁（自旋锁或互斥锁）的获取和释放、锁相对于彼此的获取顺序、当前中断环境等。然后，它可以确保锁始终以相同的顺序获取，相同的中断假设在所有情况下都适用，等等。换句话说，lockdep 可以发现系统在极少数情况下可能死锁的许多情况。这种问题在部署的系统中可能是痛苦的（对于开发人员和用户而言）；lockdep 允许在事前以自动化的方式发现它们。在提交包含之前，应启用 lockdep 运行任何具有非平凡锁定的代码。

作为一名勤奋的内核程序员，您无疑会检查任何可能失败的操作（例如内存分配）的返回状态。然而，事实是，由此产生的故障恢复路径可能完全未经测试。未经测试的代码往往是有缺陷的代码；如果所有这些错误处理路径都经过几次测试，您对代码的信心会大得多。

内核提供了一个故障注入框架，正是可以做到这一点，特别是在涉及内存分配的情况下。启用故障注入后，可配置百分比的内存分配将被设置为失败；这些失败可以限制在特定范围的代码中。启用故障注入运行允许程序员看到当事情变得糟糕时代码的响应。有关如何使用此功能的更多信息，请参阅 Documentation/fault-injection/fault-injection.txt。

其他类型的错误可以使用“sparse”静态分析工具找到。使用 sparse，程序员可以被警告关于用户空间和内核空间地址之间的混淆、大端和小端数量的混合、在预期一组位标志的地方传递整数值等等。<markdown_document>

Sparse 必须单独安装（如果你的发行版未打包它，可以在 https://sparse.wiki.kernel.org/index.php/Main_Page 找到）；然后可以通过在你的 make 命令中添加“C=1”来在代码上运行它。

“Coccinelle”工具（http://coccinelle.lip6.fr/）能够找到各种潜在的编码问题；它还可以为这些问题提出修复方案。在 scripts/coccinelle 目录下已经打包了相当多的内核“语义补丁”；运行“make coccicheck”将遍历这些语义补丁并报告发现的任何问题。有关更多信息，请参阅 Documentation/coccinelle.txt。

其他类型的可移植性错误最好通过为其他架构编译代码来发现。如果你碰巧没有 S/390 系统或 Blackfin 开发板在手，你仍然可以执行编译步骤。可以在 http://www.kernel.org/pub/tools/crosstool/ 找到大量用于 x86 系统的交叉编译器。花一些时间安装和使用这些编译器将有助于避免以后的尴尬。

## 4.3: 文档

对于内核开发，文档往往是例外而不是规则。即便如此，足够的文档将有助于简化新代码合并到内核中，使其他开发人员的生活更轻松，并对用户有所帮助。在许多情况下，添加文档已成为基本的要求。

任何补丁的第一份文档是其相关的变更日志。日志条目应描述正在解决的问题、解决方案的形式、处理该补丁的人员、对性能的任何相关影响以及理解该补丁可能需要的其他任何内容。确保变更日志说明*为什么*该补丁值得应用；令人惊讶的是，许多开发人员未能提供该信息。

任何添加新用户空间接口（包括新的 sysfs 或 /proc 文件）的代码都应包含该接口的文档，使用户空间开发人员知道他们正在使用什么。有关此文档应如何格式化以及需要提供哪些信息，请参阅 Documentation/ABI/README。

文件 Documentation/kernel-parameters.txt 描述了内核的所有启动时参数。任何添加新参数的补丁都应在此文件中添加适当的条目。

任何新的配置选项必须伴有清晰解释该选项以及用户何时可能希望选择它们的帮助文本。

许多子系统的内部 API 信息通过特殊格式的注释进行记录；这些注释可以通过“kernel-doc”脚本以多种方式提取和格式化。如果你在有 kerneldoc 注释的子系统中工作，你应该维护它们并在适当的时候添加它们，用于外部可用的函数。即使在尚未进行此类记录的区域，添加 kerneldoc 注释也无妨；实际上，这对于初学者内核开发人员来说是一项有用的活动。这些注释的格式以及有关如何创建 kerneldoc 模板的一些信息可以在文件 Documentation/kernel-doc-nano-HOWTO.txt 中找到。

任何阅读大量现有内核代码的人都会注意到，通常情况下，注释最引人注目的是它们的缺失。再次强调，对新代码的期望比过去更高；合并未注释的代码将更加困难。也就是说，并不希望有冗长注释的代码。代码本身应该是可读的，注释应解释更微妙的方面。

某些事情应该始终进行注释。内存屏障的使用应伴有一行解释为什么需要该屏障。数据结构的锁定规则通常需要在某个地方进行解释。主要数据结构通常需要全面的文档。不同代码部分之间的非显而易见的依赖关系应该指出。任何可能诱使代码管理员进行错误“清理”的事情都需要一个注释说明为什么以这种方式完成。等等。

## 4.4: 内部 API 更改

内核提供给用户空间的二进制接口除在最严重的情况下外不能被破坏。相反，内核的内部编程接口高度灵活，可以在需要时进行更改。如果你发现自己不得不绕过内核 API，或者只是因为它不符合你的需求而不使用特定功能，这可能是该 API 需要更改的迹象。作为内核开发人员，你有权进行此类更改。

当然，有一些注意事项。可以进行 API 更改，但需要有充分的理由。因此，任何进行内部 API 更改的补丁都应伴随对更改内容及其必要性的描述。这种更改也应该分解为单独的补丁，而不是埋在较大的补丁中。

另一个注意事项是，更改内部 API 的开发人员通常负责修复内核树中因该更改而损坏的任何代码。对于广泛使用的函数，此任务可能导致数百或数千次更改——其中许多可能与其他开发人员正在进行的工作冲突。不用说，这可能是一项大型工作，因此最好确保理由充分。请注意，Coccinelle 工具可以帮助进行广泛的 API 更改。

在进行不兼容的 API 更改时，应尽可能确保未更新的代码被编译器捕获。这将帮助你确保已找到该接口在树内的所有使用情况。它还将提醒树外代码的开发人员有一个他们需要响应的更改。支持树外代码不是内核开发人员需要担心的事情，但我们也不必使树外开发人员的生活比需要的更困难。

</markdown_document>