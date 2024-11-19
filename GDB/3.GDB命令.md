# 3 gdb 命令

您可以将 gdb 命令缩写为命令名称的前几个字母，如果该缩写无歧义；并且您可以通过仅键入回车键来重复某些 gdb 命令。您还可以使用 TAB 键让 gdb 填写命令中的剩余单词（或者如果有多种可能，向您展示可用的选项）。

## 3.1 命令语法

gdb 命令是单行输入。其长度没有限制。它以命令名称开头，后面跟着参数，其含义取决于命令名称。例如，`step` 命令接受一个参数，即要执行的步数，如 `step 5` 。您也可以使用没有参数的 `step` 命令。有些命令不允许任何参数。

gdb 命令名称如果缩写无歧义，可以进行截断。其他可能的命令缩写在各个命令的文档中列出。在某些情况下，甚至有歧义的缩写也是允许的；例如，`s` 被特别定义为等同于 `step` ，即使有其他命令的名称以 `s` 开头。您可以通过将缩写用作 `help` 命令的参数来测试缩写。

向 gdb 输入一个空行（仅键入回车键）意味着重复上一个命令。某些命令（例如，`run` ）不会以这种方式重复；这些是意外重复可能会导致麻烦并且您不太可能想要重复的命令。用户定义的命令可以禁用此功能；请参阅第 23.1.1 节[定义]，第 391 页。

当您使用回车键重复 `list` 和 `x` 命令时，它们会构造新的参数，而不是完全按照输入的重复。这便于轻松扫描源代码或内存。

gdb 还可以以另一种方式使用回车键：以类似于常见的实用程序 `more` 的方式划分冗长的输出（请参阅第 22.4 节[屏幕大小]，第 370 页）。由于在这种情况下很容易多按一次回车键，gdb 在生成此类显示的任何命令之后禁用命令重复。

从 `#` 到行尾的任何文本都是注释；它没有任何作用。这在命令文件中主要有用（请参阅第 23.1.3 节[命令文件]，第 395 页）。

`Ctrl - o` 绑定对于重复复杂的命令序列很有用。此命令接受当前行，就像回车键一样，然后从历史记录中获取相对于当前行的下一行进行编辑。

## 3.2 命令设置

许多命令根据特定于命令的变量或设置更改其行为。可以使用 `set` 子命令更改这些设置。例如，`print` 命令（请参阅第 10 章[检查数据]，第 141 页）根据可使用 `set print elements NUMBER - OF - ELEMENTS` 和 `set print array - indexes` 等命令更改的设置以不同方式打印数组。

您可以在 gdb 启动时加载的 `gdbinit` 文件中根据自己的偏好更改这些设置。请参阅第 2.1.3 节[启动]，第 16 页。

在调试会话期间也可以交互地更改这些设置。例如，要更改要打印的数组元素的限制，您可以执行以下操作：

```
(gdb) set print elements 10
(gdb) print some_array
$1 = {0, 10, 20, 30, 40, 50, 60, 70, 80, 90...}
```

上面的 `set print elements 10` 命令将打印的元素数量从默认的 200 更改为 10 。如果您仅打算将此限制 10 用于打印 `some_array` ，则必须使用 `set print elements 200` 将限制恢复为 200 。

某些命令允许使用命令选项覆盖设置。例如，`print` 命令支持许多选项，允许覆盖由 `set print` 子命令设置的相关全局打印设置。请参阅[`print` 选项]，第 141 页。上面的示例可以重写为：

```
(gdb) print - elements 10 -- some_array
$1 = {0, 10, 20, 30, 40, 50, 60, 70, 80, 90...}
```

或者，您可以使用 `with` 命令在命令调用期间临时更改设置。

```
with setting [value] [-- command]
w setting [value] [-- command]
```

在命令执行期间将 `setting` 临时设置为 `value` 。

`setting` 是可以使用 `set` 子命令更改的任何设置。`value` 是在运行命令时分配给 `setting` 的值。

如果未提供命令，则重复上次执行的命令。

如果提供了命令，则必须在其前面加上双破折号（`--`）分隔符。这是因为某些设置接受自由格式的参数，例如表达式或文件名。

例如，命令

```
(gdb) with print array on -- print some_array
```

等同于以下 3 个命令：

```
(gdb) set print array on
(gdb) print some_array
(gdb) set print array off
```

当您要在运行用户定义的命令或在 Python 或 Guile 中定义的命令时覆盖设置时，`with` 命令特别有用。

请参阅第 23 章[扩展 GDB]，第 391 页。

```
(gdb) with print pretty on -- my_complex_command
```

要为同一命令更改多个设置，可以嵌套 `with` 命令。例如，`with language ada -- with print elements 10` 临时将语言更改为 Ada 并设置打印数组和字符串的元素限制为 10 。

## 3.3 命令补全

gdb 可以为您填写命令中单词的剩余部分，如果只有一种可能性；它还可以随时向您展示命令中下一个单词的有效可能性。这适用于 gdb 命令、gdb 子命令、命令选项和程序中的符号名称。

每当您希望 gdb 填写单词的剩余部分时，按 TAB 键。如果只有一种可能性，gdb 会填写单词，并等待您完成命令（或按回车键输入）。例如，如果您输入

```
(gdb) info breTAB
```

gdb 会填写 `breakpoints` 的剩余部分，因为这是唯一以 `bre` 开头的 `info` 子命令：

```
(gdb) info breakpoints
```

您可以在此处按回车键来运行 `info breakpoints` 命令，或者退格并输入其他内容，如果 `breakpoints` 不是您期望的命令。（如果您一开始就确定想要 `info breakpoints` ，您不妨在 `info bre` 之后立即按回车键，以利用命令缩写而不是命令补全）。

如果按 TAB 键时下一个单词有多种可能性，gdb 会响铃。您可以提供更多字符并再次尝试，或者只需再次按 TAB 键；gdb 会显示该单词的所有可能补全。例如，您可能想要在名称以 `make_` 开头的子例程上设置断点，但当您输入 `b make_TAB` 时，gdb 只会响铃。再次按 TAB 键会显示您程序中所有以这些字符开头的函数名称，例如：

```
(gdb) b make_TAB
gdb 响铃；再次按 TAB 键，查看：
make_a_section_from_file make_environ
make_abs_section make_function_type
make_blockvector make_pointer_type
make_cleanup make_reference_type
make_command make_symbol_completion_list
(gdb) b make_
```

显示可用可能性后，gdb 会复制您的部分输入（在此示例中为 `b make_` ），以便您完成命令。

如果您尝试完成的命令期望后跟关键字或数字，则可用补全中会显示 `NUMBER` ，例如：

```
(gdb) print - elements TABTAB
NUMBER unlimited
(gdb) print - elements
```

这里，该选项期望一个数字（例如，100），而不是文字 `NUMBER` 。此类元语法参数始终以大写形式显示。

如果您一开始就只想查看替代项列表，可以按 `M -?` 而不是按两次 TAB 键。`M -?` 表示 `META?` 。您可以通过在键盘上按住指定为 `META` 移位的键（如果有）同时键入 `?` ，或者按 `ESC` 后跟 `?` 来键入。

如果可能的补全数量很大，gdb 会打印它收集到的尽可能多的列表，以及一条表明列表可能被截断的消息。

```
(gdb) b mTABTAB
main
<... 其余可能的补全...>
*** 列表可能被截断，达到最大补全数量。 ***
(gdb) b m
```

此行为可以使用以下命令进行控制：

```
set max - completions limit
set max - completions unlimited
```

设置最大补全候选数量。gdb 一旦收集到这么多候选就会停止寻找更多补全。当对函数名称等进行补全时，这很有用，因为收集所有可能的候选可能很耗时。默认值为 200 。值为 0 会禁用标签补全。请注意，设置无限制或非常大的限制可能会使补全变慢。

```
show max - completions
```

显示 gdb 在补全期间将收集和显示的最大候选数量。

有时您需要的字符串，虽然逻辑上是一个“单词”，但可能包含括号或 gdb 通常从其单词概念中排除的其他字符。为了在这种情况下允许单词补全工作，您可以在 gdb 命令中将单词括在 `'`（单引号）中。

在输入涉及带有模板参数的 C++ 符号名称的表达式时，可能需要这样做。这是因为在完成表达式时，GDB 将 `<` 字符视为单词分隔符，假设它是小于比较运算符（请参阅第 15.4.1.1 节[C 和 C++ 运算符]，第 231 页）。

例如，当您想要使用 `print` 或 `call` 命令交互式调用 C++ 模板函数时，您可能需要区分是指为 `int` 特化的版本 `name < int > ()` ，还是为 `float` 特化的版本 `name < float > ()` 。要在这种情况下使用单词补全功能，请在函数名称的开头输入一个单引号 `'` 。这会提醒 gdb 在您按 TAB 或 `M -?` 请求单词补全时可能需要考虑比平常更多的信息：

```
(gdb) p 'func < M -?
func < int > () func < float > ()
(gdb) p 'func <
```

但是在设置断点时（请参阅第 9.2 节[位置规范]，第 126 页），通常不需要在函数名称之前输入引号，因为 gdb 理解您要在函数上设置断点：

```
(gdb) b func < M -?
func < int > () func < float > ()
(gdb) b func <
```

即使在输入 C++ 重载函数（同一函数的多个定义，通过参数类型区分）的名称时也是如此。例如，当您想要设置断点时，您不需要区分是指接受 `int` 参数的 `name(int)` 版本，还是接受 `float` 参数的 `name(float)` 版本。

```
(gdb) b bubble(M -?
bubble(int) bubble(double)
(gdb) b bubble(douM -?
bubble(double)
```

有关需要引号的其他场景的描述，请参阅[引号名称]，第 263 页。有关重载函数的更多信息，请参阅第 15.4.1.3 节[C++ 表达式]，第 234 页。您可以使用命令 `set overload - resolution off` 禁用重载解析；请参阅第 15.4.1.7 节[gdb 对 C++ 的功能]，第 235 页。

当在表达式中完成查找结构中的字段时，gdb 还会尝试

```
(gdb) p gdb_stdout.M -?
magic to_fputs to_rewind
to_data to_isatty to_write
to_delete to_put to_write_async_safe
to_flush to_read
```

这是因为 `gdb_stdout` 是在 gdb 源中定义为以下类型 `struct ui_file` 的变量：

```
struct ui_file
{
    int *magic;
    ui_file_flush_ftype *to_flush;
    ui_file_write_ftype *to_write;
    ui_file_write_async_safe_ftype *to_write_async_safe;
    ui_file_fputs_ftype *to_fputs;
    ui_file_read_ftype *to_read;
    ui_file_delete_ftype *to_delete;
    ui_file_isatty_ftype *to_isatty;
    ui_file_rewind_ftype *to_rewind;
    ui_file_put_ftype *to_put;
    void *to_data;
}
```
## 3.4 文件名作为命令参数

当将文件名（或目录名）作为参数传递给命令时，如果文件名参数不包含任何空格、双引号或单引号，那么对于所有命令，文件名都可以写成一个简单的字符串，例如：
```
(gdb) file /path/to/some/file
```
如果文件名包含空格、双引号或单引号，那么 gdb 对于这些文件名的格式化有两种方法；使用哪种格式取决于正在使用的命令。

大多数 gdb 命令不需要或不支持引号和转义。这些命令将命令名称后面的任何文本（不是命令选项，请参阅第 3.5 节[命令选项]，第 28 页）视为文件名，即使文件名包含空格或引号字符。在下面的示例中，用户正在将`/path/that contains/two spaces/`添加到自动加载安全路径（请参阅[添加自动加载安全路径]，第 379 页）：
```
(gdb) add-auto-load-safe-path /path/that contains/two spaces/
```
少数命令要求包含空格或引号字符的文件名要么用引号括起来，要么用反斜杠转义特殊字符。支持这种风格的命令在手册中有标记，任何未标记为接受其文件名参数的引号和转义的命令，都不接受这种文件名参数样式。

例如，要使用`file`命令（请参阅第 18.1 节[指定文件的命令]，第 291 页）加载文件`/path/with spaces/to/a file`，可以用反斜杠转义空格字符：
```
(gdb) file /path/with\ spaces/to/a\ file
```
或者整个文件名可以用单引号或双引号括起来，在这种情况下不需要反斜杠，例如：
```
(gdb) symbol-file "/path/with spaces/to/a file"
(gdb) exec-file '/path/with spaces/to/a file'
```
在引号括起来的文件名中包含引号字符，可以用反斜杠转义，例如，在双引号括起来的文件名中，双引号字符应该用反斜杠转义，但单引号字符不应该转义。在单引号括起来的字符串中，单引号字符需要转义，但双引号字符不需要。

一个文字反斜杠字符也可以通过用反斜杠转义来包含。

## 3.5 命令选项

某些命令接受以前导破折号开头的选项。例如，`print -pretty` 。

与命令名称类似，如果缩写是明确的，您可以将 gdb 选项缩写为选项名称的前几个字母，并且您还可以使用 TAB 键让 gdb 填写选项中的剩余单词（或者如果有多个可能性，向您展示可用的替代项）。

有些命令将原始输入作为参数。例如，`print` 命令处理 gdb 支持的任何语言中的任意表达式。对于此类命令，由于原始输入可能以前导破折号开头，这可能会与选项或其任何缩写混淆，例如 `print -p` （`print -pretty` 的缩写还是打印负数 `p` ？），如果您指定了任何命令选项，则必须使用双破折号（`--`）分隔符来指示选项的结束。

某些选项被描述为接受可以是 `on` 或 `off` 的参数。这些被称为布尔选项。与布尔设置命令类似 - `on` 和 `off` 是典型的值，但 `1` 、`yes` 和 `enable` 中的任何一个也可以用作“真”值，`0` 、`no` 和 `disable` 中的任何一个也可以用作“假”值。您也可以省略“真”值，因为默认情况下是隐含的。

例如，以下是等效的：
```
(gdb) print -object on -pretty off -element unlimited -- *myptr
(gdb) p -o -p 0 -e u -- *myptr
```
您可以通过在命令名称后完成 `-` 来发现某些命令接受的选项集。例如：
```
(gdb) print -TABTAB
-address -max-depth -object -static-members
-array -memory-tag-violations -pretty -symbol
-array-indexes -nibbles -raw-values -union
-elements -null-stop -repeats -vtbl
```
在某些情况下，补全将指导您选项期望的参数类型。例如：
```
(gdb) print -elements TABTAB
NUMBER unlimited
```
这里，该选项期望一个数字（例如，100），而不是文字 `NUMBER` 。此类元语法参数始终以大写形式显示。

（有关使用 `print` 命令的更多信息，请参阅第 10 章[检查数据]，第 141 页。）

## 3.6 获取帮助

您总是可以使用 `help` 命令向 gdb 本身询问有关其命令的信息。

`help`
`h` 您可以使用不带参数的 `help`（缩写为 `h`）来显示命名的命令类的简短列表：
```
(gdb) help
List of classes of commands:
aliases -- User-defined aliases of other commands
breakpoints -- Making program stop at certain points
data -- Examining data
files -- Specifying and examining files
internals -- Maintenance commands
obscure -- Obscure features
running -- Running the program
stack -- Examining the stack
status -- Status inquiries
support -- Support facilities
tracepoints -- Tracing of program execution without
stopping the program
user-defined -- User-defined commands
Type "help" followed by a class name for a list of
commands in that class.
Type "help" followed by command name for full
documentation.
Command name abbreviations are allowed if unambiguous.
(gdb)
```
使用其中一个通用的帮助类作为参数，您可以获取该类中的单个命令列表。如果一个命令有别名，别名将在命令名称后面用逗号分隔给出。如果别名有默认参数，则在第一行之后给出别名的完整定义。

例如，这里是 `status` 类的帮助显示：
```
(gdb) help status
Status inquiries.
List of commands:
info, inf, i -- Generic command for showing things
about the program being debugged
info address, iamain -- Describe where symbol SYM is stored.
alias iamain = info address main
info all-registers -- List of all registers and their contents,
for selected stack frame.
...
show, info set -- Generic command for showing things
about the debugger
Type "help" followed by command name for full
documentation.
Command name abbreviations are allowed if unambiguous.
(gdb)
```
使用命令名称作为 `help` 的参数，gdb 会显示关于如何使用该命令的简短段落。如果该命令有一个或多个别名，gdb 将显示第一行，其中命令名称和所有别名用逗号分隔。这第一行之后将是具有默认参数的所有别名的完整定义。当询问别名的帮助时，将显示被别名的命令的文档。

用户定义的别名可以使用 `document` 命令（请参阅第 23.1.1 节[定义]，第 391 页）进行可选的文档记录。然后，gdb 会将此别名视为与被别名的命令不同：此别名不会在被别名的命令的帮助输出中列出，并且询问此别名的帮助将显示为该别名提供的文档，而不是被别名的命令的文档。

`apropos [-v] regexp`
`apropos` 命令在所有的 gdb 命令和别名及其文档中搜索在 `args` 中指定的正则表达式。它打印出所有找到的匹配项。可选标志 `-v` ，表示“详细”，表示输出匹配命令的完整文档并突出显示文档中与 `regexp` 匹配的部分。例如：
```
apropos alias
```
结果为：
```
alias -- Define a new command that is an alias of an existing command
aliases -- User-defined aliases of other commands
```
而
```
apropos -v cut.*thread apply
```
结果为以下输出，如果启用了样式，`cut for 'thread apply'` 将被突出显示：
```
taas -- Apply a command to all threads (ignoring errors
and empty output).
Usage: taas COMMAND
shortcut for 'thread apply all -s COMMAND'
tfaas -- Apply a command to all frames of all threads
(ignoring errors and empty output).
Usage: tfaas COMMAND
shortcut for 'thread apply all -s frame apply all -s COMMAND'
```
`complete args`
`complete` 命令列出命令开头的所有可能的补全。使用 `args` 指定您想要完成的命令的开头。例如：
```
complete i
```
结果为：
```
if
ignore
info
inspect
```
这是为 gnu Emacs 设计的。

除了 `help` 之外，您还可以使用 gdb 命令 `info` 和 `show` 来查询您的程序的状态，或者 gdb 本身的状态。每个命令支持许多查询主题；本手册在适当的上下文中介绍了它们中的每一个。`Command, Variable, and Function Index` 中的 `info` 和 `show` 下的列表指向所有子命令。请参阅[命令和变量索引]，第 937 页。

`info` 此命令（缩写为 `i`）用于描述您的程序的状态。例如，您可以使用 `info args` 显示传递给函数的参数，使用 `info registers` 列出当前正在使用的寄存器，或者使用 `info breakpoints` 列出您设置的断点。您可以使用 `help info` 获取 `info` 子命令的完整列表。

`set` 您可以使用 `set` 将表达式的结果分配给环境变量。例如，您可以使用 `set prompt $` 将 gdb 提示符设置为 `$` 。

`show` 与 `info` 相反，`show` 用于描述 gdb 本身的状态。您可以通过使用相关的命令 `set` 更改大多数您可以显示的内容；例如，您可以使用 `set radix` 控制用于显示的数字系统，或者只是使用 `show radix` 查询当前正在使用的数字系统。

要显示所有可设置的参数及其当前值，您可以使用不带参数的 `show` ；您也可以使用 `info set` 。这两个命令产生相同的显示。

以下是几个不同的 `show` 子命令，它们都缺乏相应的 `set` 命令：
`show version` 显示正在运行的 gdb 的版本。您应该在 gdb 错误报告中包含此信息。如果您的站点使用多个版本的 gdb，您可能需要确定您正在运行的 gdb 版本；随着 gdb 的发展，新的命令被引入，旧的命令可能会消失。此外，许多系统供应商提供 gdb 的变体版本，gnu/Linux 发行版中也有 gdb 的变体版本。版本号与您启动 gdb 时宣布的相同。

`show copying`
`info copying` 显示关于 gdb 复制许可的信息。

`show warranty`
`info warranty` 显示 gnu 的“无保修”声明，或者如果您的 gdb 版本带有保修，则显示保修。

`show configuration` 显示 gdb 在构建时的配置方式的详细信息。这显示了传递给 `configure` 脚本的可选参数以及 `configure` 自动检测到的配置参数。在报告 gdb 错误（请参阅第 32 章[GDB 错误]，第 711 页）时，在您的报告中包含此信息非常重要。

此命令还显示 gdb 正在使用的 Readline 库的版本。版本号后面将出现“(system)”，表示 gdb 正在使用共享的系统 Readline 库，或者“(internal)”，表示 gdb 正在使用静态链接的 Readline 库版本。
