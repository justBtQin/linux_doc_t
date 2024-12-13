# 22 控制 `gdb`

你可以通过使用 `set` 命令来改变 `gdb` 与你交互的方式。对于控制 `gdb` 如何显示数据的命令，请参阅第 10.9 节[打印设置]，第 157 页。其他设置在此处描述。

## 22.1 提示

gdb 通过打印一个称为提示符的字符串来表明它已准备好读取命令。

这个字符串通常是“(gdb)”。你可以使用`set prompt`命令更改提示符字符串。例如，在使用 gdb 调试 gdb 时，更改其中一个 gdb 会话中的提示符是很有用的，这样你就可以始终知道你在与哪个会话交互。

注意：`set prompt`不会在你设置的提示符之后为你添加空格。这允许你设置一个以空格结尾的提示符或一个不以空格结尾的提示符。
```
`set prompt newprompt`
	指示 gdb 从此使用`newprompt`作为其提示符字符串。
	
`show prompt`
	打印一行形式为：“Gdb 的提示符是：your-prompt”
```
带有启用的 Python 脚本的 gdb 版本具有提示符扩展。用于与这些扩展交互的命令是：
```
`set extended-prompt prompt`
	设置一个允许替换的扩展提示符。有关可用于替换的转义序列列表，请参阅第 23.3.4.3 节[gdb.prompt]，第 519 页。每次显示提示符时，作为提示符字符串一部分指定的任何转义序列都将被替换为相应的字符串。
	例如：
		set extended-prompt Current working directory: \w (gdb)
	请注意，当设置扩展提示符时，它将控制提示符钩子`hook`。有关更多信息，请参阅[prompt hook]，第 408 页。
	
`show extended-prompt`
	打印扩展提示符。每次显示提示符时，作为使用`set extended-prompt`设置的提示符字符串一部分指定的任何转义序列都将被替换为相应的字符串。
```
## 22.2 命令编辑

gdb 通过 Readline 接口读取其输入命令。这个 gnu 库为向用户提供命令行接口的程序提供了一致的行为。优点是具有 gnu Emacs 风格或 vi 风格的命令内联编辑、类似 csh 的历史替换，以及在调试会话之间存储和调用命令历史。

你可以使用`set`命令控制 gdb 中的命令行编辑行为。
```
`set editing`
`set editing on`
	启用命令行编辑（默认启用）。

`set editing off`
	禁用命令行编辑。

`show editing`
	显示命令行编辑是否已启用。
```
有关 Readline 接口的更多详细信息，请参阅第 33 章[命令行编辑]，第 715 页。鼓励不熟悉 gnu Emacs 或 vi 的用户阅读该章节。

gdb 将 Readline 应用程序名称设置为“gdb”。这在`.inputrc`中的条件下很有用。

gdb 定义了一个可绑定的 Readline 命令`operate-and-get-next`。默认情况下，它绑定到 C-o。这个命令接受当前行进行执行，并从历史中获取相对于当前行的下一行进行编辑。任何参数都将被忽略。

可以使用`show configuration`命令（见[显示配置]，第 31 页）来发现 gdb 使用的 Readline 版本。

## 22.3 命令历史

gdb 可以在调试会话期间跟踪您输入的命令，以便您可以确定确切发生了什么。使用这些命令来管理 gdb 的命令历史工具。

gdb 使用 GNU 历史库（Readline 包的一部分）来提供历史功能。有关历史库的详细描述，请参阅第 34 章[交互使用历史记录]，第 739 页。

要向 gdb 发出命令而不影响用户看到的某些状态方面，请在其前面加上“server”（请参阅第 28.2 节[服务器前缀]，第 698 页）。这意味着此命令不会影响命令历史，也不会影响 gdb 在单独一行上按下 RET 时要重复的命令的概念。

服务器前缀不会影响将值记录到值历史中；要打印值而不将其记录到值历史中，请使用输出命令而不是打印命令。

以下是与命令历史相关的 gdb 命令的描述。
```
set history filename [fname]
	将 gdb 命令历史文件的名称设置为 fname。这是 gdb 读取初始命令历史列表的文件，也是 gdb 在退出时将此会话的命令历史写入的文件。您可以通过历史扩展或通过下面列出的历史命令编辑字符访问此列表。
	此文件默认为环境变量 GDBHISTFILE 的值，如果未设置此变量，则默认为./.gdb_history（在 MS-DOS 上为./_gdb_history）。
	在处理任何 gdb 初始化文件（请参阅第 2.1.3 节[启动]，第 16 页）以及处理使用命令行选项传递的任何命令（例如，-ex）之后，将读取 GDBHISTFILE 环境变量。
	如果未提供 fname 参数，或者如果 GDBHISTFILE 为空字符串，则 gdb 既不会尝试加载现有历史文件，也不会在退出时尝试保存历史记录。

set history save
set history save on
	将命令历史记录在一个文件中，其名称可以使用 set history filename 命令指定。默认情况下，此选项处于禁用状态。当 gdb 退出时，将记录命令历史。如果将 set history filename 设置为空字符串，则即使 set history save 处于打开状态，也会禁用历史记录保存。

set history save off
	当 gdb 退出时，不要将命令历史记录到由 set history filename 指定的文件中。

set history size size
set history size unlimited
	设置 gdb 在其历史记录列表中保留的命令数量。默认情况下，此值为环境变量 GDBHISTSIZE 的值，如果未设置此变量，则为 256。忽略 GDBHISTSIZE 的非数字值。如果 size 为 unlimited 或 GDBHISTSIZE 为负数或空字符串，则 gdb 在历史记录列表中保留的命令数量不受限制。
	在处理任何 gdb 初始化文件（请参阅第 2.1.3 节[启动]，第 16 页）以及处理使用命令行选项传递的任何命令（例如，-ex）之后，将读取 GDBHISTSIZE 环境变量。

set history remove-duplicates count
set history remove-duplicates unlimited
	控制在命令历史记录列表中删除重复的历史记录条目。如果 count 不为零，gdb 将回溯到最后 count 个历史记录条目，并删除第一个与正在添加到命令历史记录列表的当前条目重复的条目。如果 count 为 unlimited，则此回溯是无界的。
	如果 count 为 0，则禁用重复历史记录条目的删除。
	仅考虑在当前会话期间添加的历史记录条目进行删除。默认情况下，此选项设置为 0。
```
历史扩展为字符“!”赋予特殊含义。有关更多详细信息，请参阅第 34.1.1 节[事件指示符]，第 739 页。

由于“!”在 C 中也是逻辑非运算符，因此默认情况下历史扩展处于关闭状态。如果您决定使用 set history expansion on 命令启用历史扩展，则有时可能需要在“!”（当它在表达式中用作逻辑非时）后面跟一个空格或制表符，以防止其被扩展。readline 历史工具不会尝试对字符串“!=”和“!(”进行替换，即使启用了历史扩展。

控制历史扩展的命令如下：
```
set history expansion on
set history expansion
	启用历史扩展。默认情况下历史扩展处于关闭状态。

set history expansion off
	禁用历史扩展。

show history
show history filename
show history save
show history size
show history expansion
	这些命令显示 gdb 历史参数的状态。单独使用 show history 将显示所有四个状态。

show commands
	显示命令历史中的最后十个命令。

show commands n
	打印以命令编号 n 为中心的十个命令。

show commands +
	打印刚刚打印的命令之后的十个命令。
```
## 22.4 屏幕尺寸

gdb 的某些命令可能会在屏幕上输出大量信息。

为了帮助你阅读所有内容，gdb 在每个输出页面的末尾暂停并要求你输入。

当你想查看更多一页输出时，输入 RET；要丢弃剩余输出，输入 q；要在当前命令的其余部分不进行分页继续，输入 c。

此外，屏幕宽度设置决定何时换行输出。根据正在打印的内容，gdb 尝试在可读的位置断行，而不是简单地让它溢出到下一行。

通常，gdb 从终端驱动程序软件中知道屏幕的大小。例如，在 Unix 系统上，gdb 使用 termcap 数据库以及 TERM 环境变量的值和 stty rows 及 stty cols 设置。如果这不正确，你可以使用 set height 和 set width 命令覆盖它：
```
set height lpp
set height unlimited
show height
set width cpl
set width unlimited
show width
	这些设置命令指定屏幕高度为 lpp 行，屏幕宽度为 cpl 字符。相关的 show 命令显示当前设置。
	如果你指定高度为 unlimited 或零行，无论输出有多长，gdb 在输出期间都不会暂停。这在输出到文件或编辑器缓冲区时很有用。
	同样，你可以指定“set width unlimited”或“set width 0”来防止 gdb 换行输出。

set pagination on
set pagination off
	打开或关闭输出分页；默认是打开的。关闭分页是 set height unlimited 的替代方法。请注意，使用 --batch 选项运行 gdb（见第 2.1.2 节[模式选项]，第 13 页）也会自动禁用分页。

show pagination
	显示当前分页模式。
```
## 22.5 输出样式

gdb 可以在功能强大的终端上设置其输出的样式。在大多数系统上，这是默认启用的，

但在批处理模式下（见第 2.1.2 节[模式选项]，第 13 页）默认是禁用的。

有各种样式设置可用；并且样式也可以完全禁用。
```
`set style enabled 'on|off'`
	启用或禁用所有样式设置。默认值取决于主机，大多数主机默认值为“on”。
	如果设置了`NO_COLOR`环境变量为非空值，那么 gdb将在启动时将其更改为“off”。

`show style enabled`
	显示当前样式设置的状态。

`set style sources 'on|off'`
	启用或禁用源代码样式设置。这会影响源代码的样式，例如`list`命令的输出。默认值为“on”。请注意，只有在一般样式设置启用且gdb 有可用的源代码高亮库时，源代码样式设置才起作用。
	有两种方式可以进行高亮显示。首先，如果 gdb 链接了GNU 源代码高亮库，那么就会使用它。否则，如果 gdb 配置了 Python 脚本支持，并且Python Pygments 包可用，那么就会使用它。

`show style sources`
	显示当前源代码样式设置的状态。

`set style tui-current-position 'on|off'`
	启用或禁用 TUI 的当前位置指示器所突出显示的源代码和汇编代码的样式设置。默认值为“off”。见第 25 章[gdb 文本用户界面]，第 581 页。

`show style tui-current-position`
	显示 TUI 的当前位置指示器所突出显示的源代码和汇编代码是否有样式设置。

`set style disassembler enabled 'on|off'`
	启用或禁用反汇编器样式设置。这会影响反汇编器输出的样式，例如`disassemble`命令的输出。反汇编器样式设置只有在一般样式设置启用（使用`set style enabled on`）且gdb 有可用的源代码高亮库时才起作用。
	gdb 可以用于设置反汇编器输出样式的两个源代码高亮库是：gdb 的内置反汇编器或 Python Pygments 包。
	gdb 的首选是使用内置反汇编器进行样式设置，这通常会提供更好的结果，能够以不同的方式对不同类型的指令操作数进行样式设置。然而，内置反汇编器不能对所有架构进行样式设置。
	对于内置反汇编器无法进行样式设置的架构，gdb 将在可能的情况下回退到使用 Python Pygments 包。为了使用Python Pygments 包，gdb 必须构建时包含 Python 支持，并且 Pygments 包必须安装。
	如果这些选项都不可用，那么即使此设置为“on”，gdb 也将生成未样式化的反汇编器输出。
	要发现当前架构是否支持使用内置反汇编器库进行样式设置，请参阅[维护显示 libopcodes-styling 启用]，第 771 页。

`show style disassembler enabled`
	显示当前反汇编器样式设置的状态。
```
`set style`的子命令控制特定形式的样式设置。这些子命令都遵循相同的模式：每个可样式化的对象都可以设置前景色、背景色和强度。

例如，文件名的样式可以使用`set style filename`命令组进行控制：
```
`set style filename background color`
	设置背景颜色为 color。有效的颜色是“none”（表示终端的默认颜色）、“black”、“red”、“green”、“yellow”、“blue”、“magenta”、“cyan”和“white”。

`set style filename foreground color`
	设置前景颜色为 color。有效的颜色是“none”（表示终端的默认颜色）、“black”、“red”、“green”、“yellow”、“blue”、“magenta”、“cyan”和“white”。

`set style filename intensity value`
	设置强度为 value。有效的强度是“normal”（默认值）、“bold”和“dim”。
```
`show style`命令及其子命令在输出中使用其自身的样式来设置样式名称的样式。因此，使用`show style`来查看样式的完整列表、它们的特征以及每个样式的视觉效果。

可样式化的对象有：
```
`filename` 
	控制文件名和 URL 的样式设置。默认情况下，此样式的前景色为绿色。

`function` 
	控制函数名的样式设置。这些是通过`set style function`命令系列进行管理的。默认情况下，此样式的前景色为黄色。
	此样式也用于在 gdb 使用其内置反汇编器库进行样式设置时的样式化反汇编器输出中的符号名（见[`set style disassembler enabled`]，第 371 页）。

`line-number`
	控制行号的样式设置。默认情况下，此样式的强度为 dim。

`variable` 
	控制变量名的样式设置。这些是通过`set style variable`命令系列进行管理的。默认情况下，此样式的前景色为 cyan。

`address` 
	控制地址的样式设置。这些是通过`set style address`命令系列进行管理的。默认情况下，此样式的前景色为蓝色。
	此样式也用于在 gdb 使用其内置反汇编器库进行样式设置时的样式化反汇编器输出中的地址（见[`set style disassembler enabled`]，第 371 页）。

`version` 
	控制 gdb 版本号文本的样式设置。默认情况下，此样式的前景色为 magenta，强度为 bold。版本号在两个地方显示，`show version`的输出和 gdb 启动时。
	为了控制 gdb 在启动时如何样式化版本号，将`set style version`命令系列添加到早期初始化命令文件中（见第 2.1.4 节[初始化文件]，第 17 页）。

`title` 
	控制标题的样式设置。这些是通过`set style title`命令系列进行管理的。默认情况下，此样式的强度为 bold。命令使用标题样式来提高大量输出的可读性。例如，`apropos`和`help`命令使用标题样式来显示命令名。

`highlight` 
	控制突出显示的样式设置。这些是通过`set style highlight`命令系列进行管理的。默认情况下，此样式的前景色为红色。命令使用突出显示样式来吸引用户对其输出的某些特定部分的注意。例如，`apropos -v REGEXP`命令使用突出显示样式来标记与正则表达式匹配的文档部分。

`metadata` 
	控制 gdb 向其显示的数据添加的数据注释的样式设置。默认情况下，此样式的强度为 dim。元数据注释包括用于抑制重复数组元素显示的“重复 n 次”注释（见第 10.9 节[打印设置]，第 157 页）、用于错误的“<不可用>”和“<错误描述>”注释以及用于回溯中显示堆栈帧信息时优化掉的值的“<优化掉>”注释（见第 8.2 节[回溯]，第 114 页）等。

`tui-border` 
	控制 TUI 边框的样式设置。请注意，与其他样式选项不同，只能通过`set style`控制边框的颜色。这样做是为了兼容原因，因为 TUI 控制设置边框强度的时间早于 gdb 中添加一般样式设置。见第 25.6 节[TUI 配置]，第 587 页。

`tui-active-border` 
	控制活动 TUI 边框的样式设置；即具有焦点的 TUI 窗口。

`disassembler comment` 
	控制反汇编器输出中的注释的样式设置。这些是通过`set style disassembler comment`命令系列进行管理的。此样式仅在 gdb 使用其内置反汇编器库进行样式设置时使用（见[`set style disassembler enabled`]，第 371 页）。默认情况下，此样式的强度为 dim，前景色为白色。

`disassembler immediate` 
	控制反汇编器输出中的数字操作数的样式设置。这些是通过`set style disassembler immediate`命令系列进行管理的。
	此样式不用于表示地址的指令操作数，在这种情况下，使用“disassembler address”样式。此样式仅在 gdb 使用其内置反汇编器库进行样式设置时使用。默认情况下，此样式的前景色为蓝色。

`disassembler address` 
	控制反汇编器输出中的地址操作数的样式设置。这是“address”样式的别名。

`disassembler symbol` 
	控制反汇编器输出中的符号名的样式设置。这是“function”样式的别名。

`disassembler mnemonic` 
	控制反汇编器输出中的指令助记符的样式设置。这些是通过`set style disassembler mnemonic`命令系列进行管理的。
	此样式也用于汇编器指令，例如.byte、.word 等。此样式仅在 gdb 使用其内置反汇编器库进行样式设置时使用。默认情况下，此样式的前景色为绿色。

`disassembler register` 
	控制反汇编器输出中的寄存器操作数的样式设置。这些是通过`set style disassembler register`命令系列进行管理的。
	此样式仅在 gdb 使用其内置反汇编器库进行样式设置时使用。默认情况下，此样式的前景色为红色。
```
## 22.6 数字

你可以通过常规约定在 gdb 中始终以八进制、十进制或十六进制输入数字：八进制数字以“0”开头，十进制数字以“.”结尾，十六进制数字以“0x”开头。既不以“0”或“0x”开头，也不以“.”结尾的数字，默认以十进制输入；同样，当未指定特定格式时，数字的默认显示为十进制。你可以使用以下命令更改输入和输出的默认基数。
```
`set input-radix base`
	设置数字输入的默认基数。基数的支持选项为十进制 8、10 或 16。基数本身必须明确指定，或者使用当前输入基数；例如，以下任何一个：
		set input-radix 012
		set input-radix 10.
		set input-radix 0xa
	将输入基数设置为十进制。另一方面，“set input-radix 10”无论当前基数是什么都不会更改输入基数，因为“10”本身没有任何基数的前导或尾随符号，它将在当前基数中进行解释。因此，如果当前基数是 16，“10”将在十六进制中解释，即 16 进制，这不会更改基数。

`set output-radix base`
	设置数字显示的默认基数。基数的支持选项为十进制 8、10 或 16。基数本身必须明确指定，或者使用当前输入基数。

`show input-radix`
	显示数字输入的当前默认基数。

`show output-radix`
	显示数字显示的当前默认基数。

`set radix [base]`
`show radix`
	这些命令设置和显示数字输入和输出的默认基数。`set radix`将输入和输出的基数设置为相同的基数；如果没有参数，它将基数重置回默认值 10。
```
## 22.7 配置当前 ABI

gdb 可以自动确定应用程序的 ABI（应用程序二进制接口）。然而，有时你需要覆盖它的结论。使用以下命令来管理 gdb 对当前 ABI 的视图。

一个 gdb 配置可以通过远程调试或本地模拟来调试多个操作系统目标的二进制文件。gdb 将自动检测正在使用的操作系统 ABI（操作系统 ABI），但你可以使用`set osabi`命令覆盖它的结论。一个有用的例子是在调试使用替代 C 库（例如用于 gnu/Linux 的 uClibc）的二进制文件时，这些二进制文件没有你的平台的标准 C 库提供的相同标识标记。

当 gdb 调试 AArch64 架构时，它提供了一个“Newlib”操作系统 ABI。这对于在调试使用 newlib C 库的二进制文件时处理`setjmp`和`longjmp`非常有用。可以通过`set osabi Newlib`选择“Newlib”操作系统 ABI。
```
`show osabi`
	显示当前正在使用的操作系统 ABI。

`set osabi` 
	不带参数时，显示注册的可用操作系统 ABI 列表。

`set osabi abi`
	将当前操作系统 ABI 设置为 abi。
```
通常，将类型为`float`的参数传递给函数的方式取决于函数是否有原型。对于有原型（即 ANSI/ISO 风格）的函数，`float`参数按照架构的`float`约定不变地传递。对于无原型（即 K&R 风格）的函数，`float`参数首先提升为`double`类型，然后传递。

不幸的是，某些形式的调试信息不能可靠地指示函数是否有原型。如果 gdb 调用一个未标记为有原型的函数，它将参考`set coerce-float-to-double`。
```
`set coerce-float-to-double`
`set coerce-float-to-double on`
	当传递给无原型函数时，类型为`float`的参数将提升为`double`。这是默认设置。

`set coerce-float-to-double off`
	类型为`float`的参数将直接传递给无原型函数。

`show coerce-float-to-double`
	显示将`float`提升为`double`的当前设置。
```
gdb 需要知道用于你的程序的 C++对象的 ABI。正确的 C++ABI 取决于用于构建你的应用程序的 C++编译器。gdb 仅完全支持具有单个 C++ABI 的程序；如果你的程序包含使用多个 C++ABI 的代码，或者如果 gdb 不能正确识别你的程序的 ABI，你可以告诉 gdb 使用哪个 ABI。

当前支持的 ABI 包括“gnu-v2”，适用于 3.0 之前的 g++版本，“gnu-v3”，适用于 3.0 及更高版本的 g++，以及用于 HP ANSI C++编译器的“hpaCC”。其他 C++编译器也可能使用“gnu-v2”或“gnu-v3”ABI。默认设置是“auto”。
```
`show cp-abi`
	显示当前正在使用的 C++ABI。

`set cp-abi` 
	不带参数时，显示支持的 C++ABI 列表。

`set cp-abi abi`
`set cp-abi auto`
	将当前 C++ABI 设置为 abi，或返回自动检测。
```
## 22.8 自动加载关联文件

gdb 有时会自动读取包含命令和设置的文件，而无需用户明确告知。我们称此功能为自动加载。虽然自动加载对于自动使 gdb 适应项目的需求很有用，但有时可能会产生意外结果或引入安全风险（例如，如果文件来自不受信任的源）。

gdb 可以自动加载各种类型的文件。除了这些文件之外，gdb 还支持用各种扩展语言编写的自动加载代码。请参阅第 23.5 节[自动加载扩展]，第 574 页。

请注意，加载这些关联文件（包括本地.gdbinit 文件）需要相应配置的自动加载安全路径（请参阅第 22.8.3 节[自动加载安全路径]，第 379 页）。

由于这些原因，gdb 包含命令和选项，让您可以控制何时自动加载文件以及应自动加载哪些文件。
```
set auto-load off
	全局禁用所有自动加载文件的加载。您可能希望将此命令与“-iex”选项一起使用（请参阅[选项 -init-eval-command]，第 16 页），例如：
		$ gdb -iex "set auto-load off" untrusted-executable corefile
	请注意，系统初始化文件（请参阅第 C.6 节[系统范围配置]，第 755 页）和您主目录中的初始化文件（请参阅[主目录初始化文件]，第 18 页）仍然会被读取（因为它们来自通常受信任的目录）。
	要防止 gdb 甚至自动加载这些初始化文件，请使用 -nx 选项（请参阅第 2.1.2 节[模式选项]，第 13 页），并加上 set auto-load no。

show auto-load
	显示每个特定的“自动加载”文件的自动加载是否已启用或禁用。
		(gdb) show auto-load
		gdb-scripts: Auto-loading of canned sequences of commands scripts is on.
		libthread-db: Auto-loading of inferior specific libthread_db is on.
		local-gdbinit: Auto-loading of .gdbinit script from current directory
						is on.
		python-scripts: Auto-loading of Python scripts is on.
		safe-path: List of directories from which it is safe to auto-load files
					is $debugdir:$datadir/auto-load.
		scripts-directory: List of directories from which to load auto-loaded scripts
							is $debugdir:$datadir/auto-load.

info auto-load
	打印每个特定的“自动加载”文件是否已自动加载。
		(gdb) info auto-load
		gdb-scripts:
		Loaded Script
		Yes /home/user/gdb/gdb-gdb.gdb
		libthread-db: No auto-loaded libthread-db.
		local-gdbinit: Local .gdbinit file "/home/user/gdb/.gdbinit" has been
						loaded.
		python-scripts:
		Loaded Script
		Yes /home/user/gdb/gdb-gdb.py
```
这些是用于自动加载的 gdb 控制命令：
```
请参阅[set auto-load off]，第 377 页。                 全局禁用自动加载。
请参阅[show auto-load]，第 377 页。                    显示各种文件的设置。
请参阅[info auto-load]，第 377 页。                    显示各种文件的状态。
请参阅[set auto-load gdb-scripts]，第 399 页。         控制 gdb 命令脚本。
请参阅[show auto-load gdb-scripts]，第 399 页。        显示 gdb 命令脚本的设置。
请参阅[info auto-load gdb-scripts]，第 399 页。        显示 gdb 命令脚本的状态。
请参阅[set auto-load python-scripts]，第 516 页。      控制 gdb Python 脚本。
请参阅[show auto-load python-scripts]，第 516 页。     显示 gdb Python 脚本的设置。
请参阅[info auto-load python-scripts]，第 516 页。     显示 gdb Python 脚本的状态。
请参阅[set auto-load guile-scripts]，第 573 页。       控制 gdb Guile 脚本。
请参阅[show auto-load guile-scripts]，第 573 页。      显示 gdb Guile 脚本的设置。
请参阅[info auto-load guile-scripts]，第 573 页。      显示 gdb Guile 脚本的状态。
请参阅[set auto-load scripts-directory]，第 575 页。   控制 gdb 自动加载脚本的位置。
请参阅[show auto-load scripts-directory]，第 576 页。  显示 gdb 自动加载脚本的位置。
请参阅[add-auto-load-scripts-directory]，第 576 页。   添加自动加载脚本位置列表的目录。
请参阅[set auto-load local-gdbinit]，第 378 页。       控制当前目录中的初始化文件。
请参阅[show auto-load local-gdbinit]，第 378 页。      显示当前目录中初始化文件的设置。
请参阅[info auto-load local-gdbinit]，第 378 页。      显示当前目录中初始化文件的状态。
请参阅[set auto-load libthread-db]，第 379 页。        控制线程调试库。
请参阅[show auto-load libthread-db]，第 379 页。       显示线程调试库的设置。
请参阅[info auto-load libthread-db]，第 379 页。       显示线程调试库的状态。
请参阅[set auto-load safe-path]，第 379 页。           控制自动加载信任的目录。
请参阅[show auto-load safe-path]，第 379 页。          显示自动加载信任的目录。
请参阅[add-auto-load-safe-path]，第 379 页。           添加自动加载信任的目录。
```
### 22.8.1 在当前目录中自动加载初始化文件

默认情况下，gdb 从当前工作目录中的初始化文件（如果有）读取并执行命令的预定义序列，参见[启动期间当前目录中的初始化文件]，第 19 页。

请注意，加载此本地.gdbinit 文件还需要相应配置的自动加载安全路径（参见第 22.8.3 节[自动加载安全路径]，第 379 页）。
```
set auto-load local-gdbinit [on|off]
	启用或禁用在当前目录中的初始化文件中找到的命令的预定义序列的自动加载（参见第 23.1 节[序列]，第 391 页）。

show auto-load local-gdbinit
	显示是否启用或禁用从当前目录中的初始化文件自动加载命令的预定义序列。

info auto-load local-gdbinit
	打印当前目录中的初始化文件中的命令的预定义序列是否已自动加载。
```
### 22.8.2 自动加载线程调试库

此功能目前仅在 gnu/Linux 原生主机上存在。

gdb 在某些情况下从特定于被调试程序的位置读取线程调试库（见[设置 libthread-db-search-path]，第 51 页）。

特殊的“libthread-db-search-path”条目“$sdir”在不检查此“设置自动加载 libthread-db”开关的情况下进行处理，因为通常必须信任系统库。

在“libthread-db-search-path”条目的所有其他情况下，gdb 在尝试打开此类线程调试库之前首先检查“设置自动加载 libthread-db”是否已启用。

请注意，加载此调试库还需要相应配置的自动加载安全路径（见第 22.8.3 节[自动加载安全路径]，第 379 页）。
```
set auto-load libthread-db [on|off]
	启用或禁用特定于被调试程序的线程调试库的自动加载。

show auto-load libthread-db
	显示特定于被调试程序的线程调试库的自动加载是否已启用或禁用。

info auto-load libthread-db
	打印所有已加载的特定于被调试程序的线程调试库的列表，并为每个此类库打印使用它的被调试程序的进程 ID 列表。
```
### 22.8.3 自动加载的安全限制

由于低级文件可能来自不可信源（例如由应用程序用户提交），gdb 并不总是自动加载任何文件。gdb 提供了“set auto-load safe-path”设置，用于列出信任的目录，以便在用户未明确请求的情况下加载文件。每个目录也可以是 shell 通配符模式。

如果路径设置不当，您将看到警告，并且文件将不会被加载：
```
$ ./gdb -q ./gdb
Reading symbols from /home/user/gdb/gdb...
warning: File "/home/user/gdb/gdb-gdb.gdb" auto-loading has been
		declined by your ‘auto-load safe-path’ set
		to "$debugdir:$datadir/auto-load".
warning: File "/home/user/gdb/gdb-gdb.py" auto-loading has been
		declined by your ‘auto-load safe-path’ set
		to "$debugdir:$datadir/auto-load".
```
要指示 gdb 继续使用初始化文件，请按如下方式调用 gdb：
```
$ gdb -q -iex "set auto-load safe-path /home/user/gdb" ./gdb
```
受信任目录的列表由以下命令控制：
```
set auto-load safe-path [directories]
	设置信任用于自动加载和执行脚本的目录（及其子目录）列表。您还可以输入特定的受信任文件。每个	目录也可以是 shell 通配符模式；通配符不匹配目录分隔符 - 请参阅系统函数 fnmatch 的 FNM_PATHNAME（请参阅 GNU C 库参考手册中的“通配符匹配”部分）。如果省略目录，“auto-load safe-path”将重置为 gdb 编译期间指定的默认值。
	目录列表使用路径分隔符（GNU 和 Unix 系统上为“:”，MS-Windows 和 MS-DOS 上为“;”）来分隔目录，类似于 PATH 环境变量。

show auto-load safe-path
	显示信任用于自动加载和执行脚本的目录列表。

add-auto-load-safe-path
	将一个条目（或条目列表）添加到信任用于自动加载和执行脚本的目录列表中。多个条目可以由正在使用的主机平台路径分隔符分隔。
```
此变量的默认值为 --with-auto-load-dir 已配置的值（请参阅[with-auto-load-dir]，第 575 页）。$debugdir 和 $datadir 替换应用与[set auto-load scripts-directory]，第 575 页相同。默认的 set auto-load safe-path 值也可以由 gdb 配置选项 --with-auto-load-safe-path 覆盖。

将此变量设置为 / 将禁用此安全保护，相应的 gdb 配置选项为 --without-auto-load-safe-path。此变量应仅设置为系统超级用户可写的系统目录。用户可以在其主目录中的初始化文件中添加其源目录（请参阅[主目录初始化文件]，第 18 页）。另请参阅当前目录中的已弃用初始化文件（请参阅[启动期间的当前目录中的初始化文件]，第 19 页）。

要强制 gdb 加载在前一个示例中拒绝加载的文件，您可以使用以下方法之一：
```
~/.gdbinit：“add-auto-load-safe-path ~/src/gdb”
	将此受信任目录（或文件）指定为列表的附加组件。您还必须指定“show auto-load safe-path”显示的任何现有目录（例如在此示例中的“/usr:/bin”）。

gdb -iex "set auto-load safe-path /usr:/bin:~/src/gdb" …
	像前面的情况一样指定此目录，但仅针对单个 gdb 会话。

gdb -iex "set auto-load safe-path /" …
	为单个 gdb 会话禁用自动加载安全性。这假定您在此 gdb 会话期间调试的所有文件都将来自可信源。

./configure --without-auto-load-safe-path
	在 gdb 编译期间，您可以禁用任何自动加载安全性。这假定您将使用此 gdb 调试的所有文件都来自可信源。
```

另一方面，您也可以明确禁止自动文件加载，这也会抑制任何此类警告消息：

```
gdb -iex "set auto-load no" …
	您可以对单个 gdb 会话使用 gdb 命令行选项。

~/.gdbinit：“set auto-load no”
	全局禁用用户的自动加载（请参阅[主目录初始化文件]，第 18 页）。虽然不太可能，但您也可以使用系统初始化文件代替（请参阅 C.6 节[系统范围的配置]，第 755 页）。
```

此设置适用于用户输入的文件名。如果没有条目匹配，gdb 最后会尝试将所有文件名解析为其规范形式（通常解析符号链接），并再次比较条目。gdb 在开始比较之前已经自己规范化了大多数文件名，因此建议输入目录的规范形式。

### 22.8.4 显示自动加载尝试的文件

为了更清楚地看到可以放置要自动加载的脚本的所有文件位置，或者为了保护自己免受未经信任的脚本的意外执行，gdb 提供了一个打印所有尝试加载的文件的功能。现有和不存在的文件都可能被打印。

例如，安全自动加载文件的目录列表（见第 22.8.3 节[自动加载安全路径]，第 379 页）也适用于规范化的文件名，在设置时可能不是很明显。
```
(gdb) set debug auto-load on
(gdb) file ~/src/t/true
auto-load: Loading canned sequences of commands script "/tmp/true-gdb.gdb"
			for objfile "/tmp/true".
auto-load: Updating directories of "/usr:/opt".
auto-load: Using directory "/usr".
auto-load: Using directory "/opt".
warning: File "/tmp/true-gdb.gdb" auto-loading has been declined
			by your ‘auto-load safe-path’ set to "/usr:/opt".
```

```
set debug auto-load [on|off]
	设置是否打印尝试自动加载的文件名。

show debug auto-load
	显示尝试自动加载的文件名的打印是否打开或关闭。
```

## 22.9 可选警告和消息

默认情况下，gdb 对其内部工作过程保持沉默。如果您在运行缓慢的机器上，您可能希望使用`set verbose`命令。这会使 gdb 在执行冗长的内部操作时告知您，因此您不会认为它已崩溃。

目前，由`set verbose`控制的消息是那些宣布正在读取源文件的符号表的消息；请参阅第 18.1 节[指定文件的命令]，第 291 页中的`symbol-file`。
```
`set verbose on`
	启用 gdb 某些信息消息的输出。

`set verbose off`
	禁用 gdb 某些信息消息的输出。

`show verbose`
	显示`set verbose`是打开还是关闭。
```
默认情况下，如果 gdb 在对象文件的符号表中遇到错误，它会保持沉默；但是如果您正在调试编译器，您可能会发现此信息很有用（请参阅第 18.7 节[读取符号文件的错误]，第 307 页）。
```
`set complaints limit`
	允许 gdb 在对每种类型的不寻常符号变得沉默之前输出关于它们的限制警告。将限制设置为零以抑制所有警告；将其设置为一个较大的数字以防止警告被抑制。

`show complaints`
	显示 gdb 被允许生成的符号警告的数量。
```
默认情况下，gdb 是谨慎的，并且会询问一些有时似乎很愚蠢的问题来确认某些命令。例如，如果您尝试运行已经在运行的程序：
```
(gdb) run
The program being debugged has been started already.
Start it from the beginning? (y or n)
```
如果您愿意毫不退缩地面对自己命令的后果，您可以禁用此“功能”：
```
`set confirm off`
	禁用确认请求。请注意，使用`--batch`选项运行 gdb（请参阅第 2.1.2 节[模式选项]，第 13 页）也会自动禁用确认请求。

`set confirm on`
	启用确认请求（默认）。

`show confirm`
	显示确认请求的状态。
```
如果您需要调试用户定义的命令或源文件，您可能会发现启用命令跟踪很有用。在这种模式下，每个命令在执行时都会被打印出来，前面带有一个或多个“+”符号，数量表示每个命令的调用深度。
```
`set trace-commands on`
	启用命令跟踪。

`set trace-commands off`
	禁用命令跟踪。

`show trace-commands`
	显示命令跟踪的当前状态。
```
## 22.10 内部事件的可选消息

`gdb` 有一些命令，可以启用来自各种 `gdb` 子系统的可选调试消息；通常，这些命令对 `gdb` 维护者或报告错误时感兴趣。本节记录了这些命令。
```
`set exec-done-display`
	打开或关闭异步命令完成的通知。当打开时，`gdb` 将在异步命令完成执行时打印一条消息。默认关闭。

`show exec-done-display`
	显示异步命令完成通知的当前设置。

`set debug aarch64`
	打开或关闭与 ARM AArch64 相关的调试消息的显示。默认关闭。

`show debug aarch64`
	显示与 ARM AArch64 相关的调试消息的当前状态。

`set debug arch`
	打开或关闭 `gdbarch` 调试信息的显示。默认关闭。

`show debug arch`
	显示 `gdbarch` 调试信息的当前状态。

`set debug aix-thread`
	显示关于 AIX 线程模块内部工作的调试消息。

`show debug aix-thread`
	显示 AIX 线程调试信息显示的当前状态。

`set debug amd-dbgapi-lib`
`show debug amd-dbgapi-lib`
	`set debug amd-dbgapi-lib log-level level` 命令可用于启用来自“`amd-dbgapi`”库的诊断消息，其中 `level` 可以是：
		`off`       不启用任何日志记录
		`error`     报告致命错误
		`warning`   报告致命错误和警告
		`info`      报告致命错误、警告和信息消息
		`verbose`   报告所有消息
	`show debug amd-dbgapi-lib log-level` 命令显示当前的 `amd-dbgapi` 库日志级别。

`set debug amd-dbgapi`
`show debug amd-dbgapi`
	`set debug amd-dbgapi`命令可用于在`amd-dbgapi`目标中启用诊断消息。`show debug amd-dbgapi`命令显示当前设置。请参阅 [set debug amd-dbgapi]，第 383 页。

`set debug check-physname`
	检查`physname`计算的结果。在读取 C++ 的 DWARF 调试信息时，`gdb` 尝试计算每个实体的名称。`gdb` 可以通过两种不同的方式进行此计算，具体取决于存在的信息。启用此设置后，`gdb` 将以两种方式计算名称并显示任何差异。

`show debug check-physname`
	显示`physname`检查的当前状态。

`set debug coff-pe-read`
	控制与读取 COFF/PE 导出符号相关的调试消息的显示。默认关闭。

`show debug coff-pe-read`
	显示与读取 COFF/PE 导出符号相关的调试消息的当前状态。

`set debug dwarf-die`
	在读取 DWARF DIE 后转储它们。该值是要打印的嵌套级别数。值为零关闭显示。

`show debug dwarf-die`
	显示 DWARF DIE 调试的当前状态。

`set debug dwarf-line`
	打开或关闭与读取 DWARF 行表相关的调试消息的显示。默认值为 0（关闭）。值为 1 提供基本信息。值大于 1 提供更详细的信息。

`show debug dwarf-line`
	显示 DWARF 行表调试的当前状态。

`set debug dwarf-read`
	打开或关闭与读取 DWARF 调试信息相关的调试消息的显示。默认值为 0（关闭）。值为 1 提供基本信息。值大于 1 提供更详细的信息。

`show debug dwarf-read`
	显示 DWARF 读取器调试的当前状态。

`set debug displaced`
	打开或关闭与位移步支持相关的 `gdb` 调试信息的显示。默认关闭。

`show debug displaced`
	显示与位移步相关的 `gdb` 调试信息的当前状态。

`set debug event`
	打开或关闭 `gdb` 事件调试信息的显示。默认关闭。

`show debug event`
	显示 `gdb` 事件调试信息的当前状态。

`set debug event-loop`
	控制关于事件循环的调试信息的输出。可能的值是“`off`”、“`all`”（显示所有调试信息）和“`all-except-ui`”（显示除与 UI 相关的事件之外的所有调试信息）。

`show debug event-loop`
	显示关于事件循环的调试信息的当前状态。

`set debug expression`
	打开或关闭关于 `gdb` 表达式解析的调试信息的显示。默认关闭。

`show debug expression`
	显示关于 `gdb` 表达式解析的调试信息的当前状态。

`set debug fbsd-lwp`
	打开或关闭来自 FreeBSD LWP 调试支持的调试消息。

`show debug fbsd-lwp`
	显示 FreeBSD LWP 调试消息的当前状态。

`set debug fbsd-nat`
	打开或关闭来自 FreeBSD 本机目标的调试消息。

`show debug fbsd-nat`
	显示 FreeBSD 本机目标调试消息的当前状态。

`set debug fortran-array-slicing`
	打开或关闭 `gdb` Fortran 数组切片调试信息的显示。默认关闭。

`show debug fortran-array-slicing`
	显示 `gdb` Fortran 数组切片调试信息的当前状态。

`set debug frame`
	打开或关闭 `gdb` 帧调试信息的显示。默认关闭。

`show debug frame`
	显示 `gdb` 帧调试信息的当前状态。

`set debug gnu-nat`
	打开或关闭来自 gnu/Hurd 调试支持的调试消息。

`show debug gnu-nat`
	显示 gnu/Hurd 调试消息的当前状态。

`set debug infrun`
	打开或关闭关于运行下级进程的 `gdb` 调试信息的显示。默认关闭。`infrun.c` 包含用于实现诸如单步执行下级进程等操作的 GDB 运行时状态机。

`show debug infrun`
	显示 `gdb` 下级调试的当前状态。

`set debug infcall`
	打开或关闭与 `gdb` 进行的下级函数调用相关的调试信息的显示。

`show debug infcall`
	显示 `gdb` 下级函数调用调试的当前状态。

`set debug jit`
	打开或关闭来自 JIT 调试支持的调试消息。

`show debug jit`
	显示 `gdb` JIT 调试的当前状态。

`set debug linux-nat [on|off]`
	打开或关闭来自 Linux 本机目标调试支持的调试消息。

`show debug linux-nat`
	显示 Linux 本机目标调试消息的当前状态。

`set debug linux-namespaces`
	打开或关闭来自 Linux 命名空间调试支持的调试消息。

`show debug linux-namespaces`
	显示 Linux 命名空间调试消息的当前状态。

`set debug mach-o`
	控制与 Mach-O 符号处理相关的调试消息的显示。默认关闭。

`show debug mach-o`
	显示与读取 COFF/PE 导出符号相关的调试消息的当前状态。

`set debug notification`
	打开或关闭关于远程异步通知的调试消息。默认关闭。

`show debug notification`
	显示远程异步通知调试消息的当前状态。

`set debug observer`
	打开或关闭 `gdb` 观察者调试的显示。这包括诸如可观察事件的通知等信息。

`show debug observer`
	显示观察者调试的当前状态。

`set debug overload`
	打开或关闭 `gdb` C++ 重载调试信息的显示。这包括诸如函数排名等信息。默认关闭。

`show debug overload`
	显示显示 `gdb` C++ 重载调试信息的当前状态。

`set debug parser`
	打开或关闭表达式解析器调试输出的显示。在内部，这会设置表达式解析器中的 `yydebug` 变量。有关详细信息，请参阅 Bison 中的“跟踪您的解析器”部分。默认关闭。

`show debug parser`
	显示表达式解析器调试的当前状态。

`set debug remote`
	打开或关闭关于跨串行线往返发送到远程机器的所有数据包的报告的显示。信息打印在 `gdb` 标准输出流上。默认关闭。

`show debug remote`
	显示远程数据包的显示状态。

`set debug remote-packet-max-chars`
	设置当 `set debug remote` 打开时每个远程数据包要显示的最大字符数。这对于防止 `gdb` 显示冗长的远程数据包并污染控制台很有用。
	默认值为 512，这意味着 `gdb` 将在 512 字节后截断每个远程数据包。
	将此选项设置为无限制将禁用截断并输出远程数据包的完整长度。

`show debug remote-packet-max-chars`
	显示用于远程数据包调试的输出字节数。

`set debug separate-debug-file`
	打开或关闭关于单独调试文件搜索的调试输出的显示。

`show debug separate-debug-file`
	显示单独调试文件搜索调试输出的状态。

`set debug serial`
	打开或关闭 `gdb` 串行调试信息的显示。默认关闭。

`show debug serial`
	显示显示 `gdb` 串行调试信息的当前状态。

`set debug solib`
	打开或关闭与共享库相关的调试消息的显示。默认关闭。

`show debug solib`
	显示 solib 调试消息的当前状态。

`set debug symbol-lookup`
	打开或关闭与符号查找相关的调试消息的显示。默认值为 0（关闭）。值为 1 提供基本信息。值大于 1 提供更详细的信息。

`show debug symbol-lookup`
	显示符号查找调试消息的当前状态。

`set debug symfile`
	打开或关闭与符号文件函数相关的调试消息的显示。默认关闭。请参阅第 18.1 节 [文件]，第 291 页。

`show debug symfile`
	显示符号文件调试消息的当前状态。

`set debug symtab-create`
	打开或关闭与符号表创建相关的调试消息的显示。默认值为 0（关闭）。值为 1 提供基本信息。值大于 1 提供更详细的信息。

`show debug symtab-create`
	显示符号表创建调试的当前状态。

`set debug target`
	打开或关闭 `gdb` 目标调试信息的显示。此信息包括在 `GDB` 的目标级别发生的事情。默认值为 0。将其设置为 1 以跟踪事件，并设置为 2 以跟踪大型内存传输的值。

`show debug target`
	显示显示 `gdb` 目标调试信息的当前状态。

`set debug timestamp`
	打开或关闭带有 `gdb` 调试信息的时间戳的显示。启用后，将在每个调试消息之前显示秒和微秒。

`show debug timestamp`
	显示带有 `gdb` 调试信息的时间戳的当前状态。

`set debug varobj`
	打开或关闭 `gdb` 变量对象调试信息的显示。默认关闭。

`show debug varobj`
	显示显示 `gdb` 变量对象调试信息的当前状态。

`set debug xml`
	打开或关闭内置 XML 解析器的调试消息。

`show debug xml`
	显示 XML 调试消息的当前状态。

`set debug breakpoints`
	打开或关闭关于断点插入和删除的 `gdb` 调试信息的显示。默认关闭。

`show debug breakpoints`
	显示关于断点插入和删除的 `gdb` 调试信息的当前状态。
```
## 22.11 其他杂项设置
```
set interactive-mode
	如果开启，强制 gdb 假设 GDB 是在终端中启动的。实际上，这意味着 gdb 应该等待用户回答在命令提示符下输入的命令生成的查询。如果关闭，强制 gdb 在相反的模式下操作，并使用所有查询的默认答案。如果是自动（默认值），gdb 尝试确定其标准输入是否是终端，如果是，则以交互模式工作，否则以非交互模式工作。
	在绝大多数情况下，调试器应该能够正确猜测应该使用哪种模式。但在某些特定情况下，此设置可能很有用，例如在 cygwin 窗口中运行 MinGW gdb。

show interactive-mode
	显示调试器是否在交互模式下运行。

set suppress-cli-notifications
	如果开启，gdb 打印的命令行接口（CLI）通知将被抑制。如果关闭，通知将照常打印。默认值为关闭。当您更改所选上下文或调试的程序停止时，会发生 CLI 通知，如下所述。
	用户选择的上下文更改：
		当您更改所选上下文（即当前 inferior、线程和/或框架）时，gdb 会打印有关新上下文的信息。例如，默认行为如下：
			(gdb) inferior 1
			[Switching to inferior 1 [process 634] (/tmp/test)]
			[Switching to thread 1 (process 634)]
			#0 main () at test.c:3
			3             return 0;
			(gdb)
		当通知被抑制时，不会打印新上下文：
			(gdb) set suppress-cli-notifications on
			(gdb) inferior 1
			(gdb)

	正在调试的程序停止：
		当您正在调试的程序停止（例如，因为命中断点、完成源代码单步执行、中断等）时，gdb 会打印有关停止事件的信息。例如，下面是命中断点：
			(gdb) break test.c:3
			Breakpoint 2 at 0x555555555155: file test.c, line 3.
			(gdb) continue
			Continuing.
			Breakpoint 2, main () at test.c:3
			3 return 0;
			(gdb)
		当通知被抑制时，输出变为：
			(gdb) break test.c:3
			Breakpoint 2 at 0x555555555155: file test.c, line 3.
			(gdb) set suppress-cli-notifications on
			(gdb) continue
			Continuing.
			(gdb)
		抑制 CLI 通知在脚本中可能很有用，以从命令列表中获得减少的输出。

show suppress-cli-notifications
	显示是否抑制打印 CLI 通知。
```