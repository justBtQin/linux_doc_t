# 28 GDB 注释

本章描述了 GDB 中的注释。注释旨在将 GDB 与图形用户界面或其他类似程序进行接口，这些程序希望在相对较高的层次上与 GDB 进行交互。

注释机制在很大程度上已被 gdb/mi 所取代（见第 27 章[GDB/MI]，第 591 页）。

## 28.1 什么是注释？

注释以换行符、两个“控制-Z”字符和注释的名称开头。如果此注释没有关联的其他信息，则注释的名称后立即跟一个换行符。如果有其他信息，则注释的名称后跟一个空格、其他信息和一个换行符。其他信息不能包含换行符。

任何不以换行符和两个“控制-Z”字符开头的输出都表示来自 gdb 的文字输出。目前 gdb 不需要输出一个换行符后跟两个“控制-Z”字符，但如果有这样的需求，可以使用“转义”注释来扩展注释，该注释表示这三个字符作为输出。

注释级别，使用 --annotate 命令行选项指定（见第 2.1.2 节[模式选项]，第 13 页），控制 gdb 与提示符、表达式值、源行和其他类型的输出一起打印多少信息。级别 0 用于无注释，级别 1 用于 gdb 作为 gnu Emacs 的子进程运行时，级别 3 是适合控制 gdb 的程序的最大注释，级别 2 注释已过时（见 GDB 的过时注释中的“注释接口的限制”部分）。

```
set annotate level
    gdb 命令“set annotate”将注释级别设置为指定的级别。

show annotate
    显示当前注释级别。
```

本章描述级别 3 注释。

一个使用注释启动 gdb 的简单示例是：
```
$ gdb --annotate=3
GNU gdb 6.0
Copyright 2003 Free Software Foundation, Inc.
GDB is free software, covered by the GNU General Public License,
and you are welcome to change it and/or distribute copies of it
under certain conditions.
Type "show copying" to see the conditions.
There is absolutely no warranty for GDB. Type "show warranty"
for details.
This GDB was configured as "i386-pc-linux-gnu"

^Z^Zpre-prompt
(gdb)
^Z^Zprompt
quit
^Z^Zpost-prompt
$
```

这里`quit`是输入到 gdb 的；其余是 gdb 的输出。以`^Z^Z`开头的三行（其中`^Z`表示`control-z`字符）是注释；其余是 gdb 的输出。

## 28.2 服务器前缀

如果在命令前加上“server ”前缀，那么它不会影响命令历史记录，也不会影响如果在单独的一行上按下 RET 键时 gdb 要重复的命令的概念。

这意味着命令可以通过前端以透明的方式在用户背后运行。

服务器前缀不会影响将值记录到值历史记录中；要打印一个值而不将其记录到值历史记录中，请使用输出命令而不是打印命令。

使用此前缀还会禁用确认请求（请参阅[确认请求]，第 382 页）。

## 28.3 GDB输入注释

当 gdb 提示输入时，它会标注这个事实，以便能够知道何时发送输出、给定命令的输出何时结束等。

不同类型的输入各自具有不同的输入类型。每个输入类型都有三个标注：一个预标注，用于表示正在输出的任何提示符的开始；一个普通标注，用于表示提示符的结束；然后是一个后标注，用于表示可能（或可能不）与输入相关联的任何回显的结束。例如，提示符输入类型具有以下标注：

```
^Z^Zpre-prompt
^Z^Zprompt
^Z^Zpost-prompt
```

输入类型如下：
```
prompt：                 当 gdb 提示输入命令时（主 gdb 提示符）。
commands：               当 gdb 提示输入一组命令时，如在`commands`命令中。对于输入的每个命令，这些标注都会重复。
overload-choice：        当 gdb 希望用户在各种重载函数之间进行选择时。
query：                  当 gdb 希望用户确认一个潜在危险的操作时。
prompt-for-continue：    当 gdb 要求用户按回车键继续时。注意：不要期望此功能能很好地工作；相反，使用`set height 0`来禁用提示。这是因为在存在标注的情况下，行数的计数是有问题的。
```

## 28.4 错误

```
^Z^Zquit
```

此注释出现在 gdb 响应中断之前。

```
^Z^Zerror
```

此注释出现在 gdb 响应错误之前。

退出和错误注释表明 gdb 正在处理的任何注释可能会突然结束。例如，如果一个值历史开始注释之后是一个错误，不能期望收到匹配的值历史结束。然而，也不能期望不收到它；错误注释并不一定意味着 gdb 立即返回到顶层。

退出或错误注释之前可能有

```
^Z^Zerror-begin
```

在该注释和退出或错误注释之间的任何输出都是错误消息。

警告消息尚未被注释。

## 28.5 失效通知

以下注释表明某些状态可能已发生更改。

```
^Z^Zframes-invalid
    帧（例如，来自回溯命令的输出）可能已更改。

^Z^Zbreakpoints-invalid
    断点可能已更改。例如，用户刚刚添加或删除了一个断点。
```

## 28.6 运行程序

当程序由于诸如`step`或`continue`等`gdb`命令而开始执行时，会输出`^Z^Zstarting`。

当程序停止时，会输出`^Z^Zstopped`。

在`stopped`注释之前，各种注释描述了程序是如何停止的。

```
^Z^Zexited exit-status
    程序退出，`exit-status`是退出状态（成功退出为零，否则为非零）。

^Z^Zsignalled
    程序以信号退出。在`^Z^Zsignalled`之后，注释继续：
        intro-text
        ^Z^Zsignal-name
        name
        ^Z^Zsignal-name-end
        middle-text
        ^Z^Zsignal-string
        string
        ^Z^Zsignal-string-end
        end-text
    
    其中`name`是信号的名称，如`SIGILL`或`SIGSEGV`，`string`是信号的解释，如`Illegal Instruction`或`Segmentation fault`。`intro-text`、`middle-text`和`end-text`是为了用户的利益而存在的，没有特定的格式。

^Z^Zsignal
    此注释的语法与`signalled`类似，但`gdb`只是说程序接收到了信号，而不是用它终止。

^Z^Zbreakpoint number
    程序命中了断点编号`number`。

^Z^Zwatchpoint number
    程序命中了观察点编号`number`。
```

## 28.7 显示源代码

以下注释用于替代显示源代码：

```
^Z^Zsource filename:line:character:middle:addr
```

其中，文件名是一个绝对文件名，指示是哪个源文件；行是该文件中的行号（其中 1 是文件中的第一行）；字符是文件中的字符位置（其中 0 是文件中的第一个字符）（对于大多数调试格式，这必然指向一行的开头）；中间如果地址在该行的中间则为“中间”，如果地址在该行的开头则为“开始”；地址是与正在显示的源代码相关联的目标程序中的地址。地址的形式为“0x”后跟一个或多个小写十六进制数字（请注意，这不依赖于语言）。