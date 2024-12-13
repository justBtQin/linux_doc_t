# 23 扩展 gdb

gdb 提供了几种扩展机制。gdb 还提供了在读取调试文件时自动加载扩展的能力。这允许用户为正在调试的程序自动定制 gdb。

为了便于使用扩展语言，gdb 能够评估文件的内容。在这样做时，gdb 可以通过查看文件名扩展名来识别正在使用的扩展语言。具有无法识别的文件名扩展名的文件始终被视为 gdb 命令文件。请参阅第 23.1.3 节[命令文件]，第 395 页。

您可以使用以下设置控制 gdb 如何评估这些文件：
```
`set script-extension off`
    所有脚本始终被评估为 gdb 命令文件。

`set script-extension soft`
    调试器根据文件名扩展名确定脚本语言。
    如果此脚本语言受支持，gdb 使用该语言评估脚本。否则，它将文件评估为 gdb 命令文件。

`set script-extension strict`
    调试器根据文件名扩展名确定脚本语言，并使用该语言评估脚本。如果语言不受支持，则评估失败。

`show script-extension`
    显示 `script-extension` 选项的当前值。
```
## 23.1 命令的罐装序列

除了断点命令（见第 5.1.7 节[断点命令列表]，第 77 页），`gdb`提供了两种将命令序列存储为一个单元以供执行的方式：用户定义的命令和命令文件。

### 23.1.1 用户定义命令

用户定义命令是一系列`gdb`命令，你为其分配一个新名称作为命令。这通过`define`命令完成。

用户命令可以接受任意数量的以空格分隔的参数。在用户命令中，通过`$arg0...$argN`访问参数。参数是文本替换，因此它们可以引用变量、使用复杂表达式，甚至执行下级函数调用。然而，请注意，这种文本替换意味着处理某些参数很困难。例如，用户无法传递包含空格的参数；并且虽然可以使用像`"$arg1"`这样的表达式将参数字符串化，但如果参数包含引号，这将失败。对于更复杂和健壮的命令，我们建议用 Python 编写它们；请参阅第 23.3.2.21 节[Python 中的`CLI`命令]，第 463 页。

一个简单的例子：

```
define adder
  print $arg0 + $arg1 + $arg2
end
```

要执行该命令，请使用：

```
adder 1 2 3
```

这定义了命令`adder`，它打印其三个参数的和。

此外，`$argc`可用于确定已传递了多少个参数。
```
define adder
    if $argc == 2
        print $arg0 + $arg1
    end
    if $argc == 3
        print $arg0 + $arg1 + $arg2
    end
end
```
与`eval`命令（见[`eval`]，第 398 页）结合使用，可以更轻松地处理可变数量的参数：
```
define adder
    set $i = 0
    set $sum = 0
    while $i < $argc
        eval "set $sum = $sum + $arg%d", $i
        set $i = $i + 1
    end
    print $sum
end
```
```
`define commandname`
    定义一个名为`commandname`的命令。如果已经有一个同名的命令，你将被要求确认是否要重新定义它。参数`commandname`可以是由字母、数字、破折号、点和下划线组成的裸命令名。它也可以以任何预定义或用户定义的前缀命令开头。例如，`'define target my-target'`创建一个用户定义的`'target my-target'`命令。
    命令的定义由其他`gdb`命令行组成，这些命令行在`define`命令之后给出。这些命令的结束以包含`end`的一行标记。

`document commandname`
    为用户定义的命令`commandname`编写文档，以便可以通过`help`访问它。命令`commandname`必须已经定义。此命令读取文档行，就像`define`读取命令定义的行一样，以`end`结束。`document`命令完成后，对命令`commandname`的`help`显示你编写的文档。
    你可以再次使用`document`命令来更改命令的文档。使用`define`重新定义命令不会更改文档。
    也可以为用户定义的别名编写文档。然后，别名文档将由`help`和`apropos`命令使用，而不是别名命令的文档。在定义一个由一组嵌套的`with`命令组成的别名时（见第 23.2.1 节[命令别名默认参数]，第 400 页），为用户定义的别名编写文档特别有用。

`define-prefix commandname`
    将命令`commandname`定义或标记为用户定义的前缀命令。一旦标记，`commandname`可以用作`define`命令的前缀命令。请注意，`define-prefix`可以与尚未定义的命令名一起使用。在这种情况下，`commandname`被定义为一个空的用户定义命令。如果你重新定义一个被标记为用户定义前缀命令的命令，重新定义命令的子命令将被保留（并且`gdb`会向用户指示这一点）。
    示例：
        (gdb) define-prefix abc
        (gdb) define-prefix abc def
        (gdb) define abc def
        Type commands for definition of "abc def".
        End with a line saying just "end".
        >echo command initial def\n
        >end
        (gdb) define abc def ghi
        Type commands for definition of "abc def ghi".
        End with a line saying just "end".
        >echo command ghi\n
        >end
        (gdb) define abc def
        Keeping subcommands of prefix command "def".
        Redefine command "def"? (y or n) y
        Type commands for definition of "abc def".
        End with a line saying just "end".
        >echo command def\n
        >end
        (gdb) abc def ghi
        command ghi
        (gdb) abc def
        command def
        (gdb)

`dont-repeat`
    在用户定义的命令内部使用，这告诉`gdb`，当用户按下`RET`时，不应重复此命令（见第 3.1 节[命令语法]，第 23 页）。

`help user-defined`
    列出所有用户定义的命令和在`COMMAND USER`类中定义的所有 Python 命令。包括文档的第一行或文档字符串（如果有）。

`show user`
`show user commandname`
    显示用于定义`commandname`的`gdb`命令（但不包括其文档）。如果未提供命令名，则显示所有用户定义命令的定义。这不适用于用户定义的 Python 命令。

`show max-user-call-depth`
`set max-user-call-depth`
    `max-user-call-depth`的值控制在`gdb`怀疑无限递归并中止命令之前，用户定义命令中允许的递归级别数。这不适用于用户定义的 Python 命令。
```
除了上述命令外，用户定义命令经常使用控制流命令，在第 23.1.3 节[命令文件]，第 395 页中描述。

当执行用户定义命令时，不会打印定义的命令。任何命令中的错误都会停止用户定义命令的执行。

如果以交互方式使用，在用户定义命令内部使用时，会要求确认的命令将无需询问即可继续。许多通常会打印消息以说明其操作的`gdb`命令在用户定义命令中使用时会省略这些消息。

### 23.1.2 用户定义的命令钩子

你可以定义钩子，它是一种特殊的用户定义命令。每当你运行命令“foo”时，如果存在用户定义的命令“hook-foo”，它将在该命令之前执行（无参数）。

也可以定义一个在你执行的命令之后运行的钩子。每当你运行命令“foo”时，如果存在用户定义的命令“hookpost-foo”，它将在该命令之后执行（无参数）。对于同一个命令，执行后钩子可以与执行前钩子同时存在。

钩子调用它所挂钩的命令是有效的。如果发生这种情况，钩子不会重新执行，从而避免无限递归。

此外，存在一个伪命令“stop”。定义（“hook-stop”）使得相关命令在你的程序中每次执行停止时都执行：在断点命令运行之前、显示打印之前或堆栈帧打印之前。

例如，要在单步执行时忽略`SIGALRM`信号，但在正常执行期间正常处理它们，你可以定义：

```
define hook-stop
handle SIGALRM nopass
end

define hook-run
handle SIGALRM pass
end

define hook-continue
handle SIGALRM pass
end
```

作为另一个例子，要在`echo`命令的开始和结束处挂钩，并在消息的开始和结束处添加额外的文本，你可以定义：

```
define hook-echo
echo <<<---
end

define hookpost-echo
echo --->>>\n
end

(gdb) echo Hello World
<<<---Hello World--->>>
(gdb)
```

你可以为 gdb 中的任何单字命令定义一个钩子，但不能为命令别名定义；你应该为基本命令名定义一个钩子，例如`backtrace`而不是`bt`。你可以通过在命令的最后一个单词前添加`hook-`或`hookpost-`来挂钩一个多字命令，例如“`define target hook-remote`”来为“`target remote`”添加一个钩子。

如果在钩子执行期间发生错误，gdb 命令的执行将停止，gdb 将发出一个提示符（在你实际输入的命令有机会运行之前）。

如果你尝试定义一个与任何已知命令不匹配的钩子，你将从`define`命令中收到一个警告。

### 23.1.3 命令文件

gdb 的命令文件是由 gdb 命令组成的文本文件。也可以包含注释（以`#`开头的行）。命令文件中的空行不执行任何操作；它不像在终端中那样意味着重复上一个命令。

你可以使用`source`命令请求执行命令文件。请注意，`source`命令也用于评估不是命令文件的脚本。可以使用`script-extension`设置来配置确切的行为。请参阅第 23 章[扩展 GDB]，第 391 页。
```
source [-s] [-v] filename
    执行命令文件`filename`。
```
命令文件中的行通常按顺序执行，除非执行顺序被下面描述的流控制命令之一更改。执行命令时不会打印命令。任何命令中的错误都会终止命令文件的执行，并将控制权返回给控制台。

gdb 首先在当前目录中搜索`filename`。如果在那里未找到文件，并且`filename`未指定目录，那么 gdb 还会在源搜索路径（使用`directory`命令指定）上查找该文件；但不会搜索`$cdir`，因为编译目录与脚本无关。

如果指定了`-s`，那么即使`filename`指定了目录，gdb 也会在搜索路径上搜索`filename`。通过将`filename`附加到搜索路径的每个元素来进行搜索。例如，如果`filename`是`mylib/myscript`并且搜索路径包含`/home/user`，那么 gdb 将查找脚本`/home/user/mylib/myscript`。如果`filename`是绝对路径，也会进行搜索。例如，如果`filename`是`/tmp/myscript`并且搜索路径包含`/home/user`，那么 gdb 将查找脚本`/home/user/tmp/myscript`。

对于类似 DOS 的系统，如果`filename`包含驱动器规范，在连接之前将其删除。例如，如果`filename`是`d:myscript`并且搜索路径包含`c:/tmp`，那么 gdb 将查找脚本`c:/tmp/myscript`。

如果给出了`-v`（详细模式），那么 gdb 在执行时会显示每个命令。该选项必须在`filename`之前给出，并且在其他任何地方都被解释为`filename`的一部分。

如果在交互模式下使用会请求确认的命令，在命令文件中使用时不会请求确认。许多通常会打印消息以说明其操作的 gdb 命令在从命令文件调用时会省略这些消息。

gdb 也接受来自标准输入的命令输入。在这种模式下，正常输出转到标准输出，错误输出转到标准错误。标准输入中提供的命令文件中的错误不会终止命令文件的执行——执行会继续执行下一个命令。
```
gdb < cmds > log 2>&1
```
（上述语法将根据使用的 shell 而有所不同。）此示例将从文件`cmds`执行命令。所有输出和错误都将定向到`log`。

由于存储在命令文件中的命令往往比交互输入的命令更通用，它们经常需要处理复杂的情况，例如变量和符号的不同或意外值、正在调试的程序的构建方式的变化等。gdb 提供了一组流控制命令来处理这些复杂性。

使用这些命令，你可以编写循环遍历数据结构、有条件地执行命令等的复杂脚本。
```
`if`
`else` 
    此命令允许在脚本中包含有条件执行的命令。`if`命令接受一个参数，即要评估的表达式。后面是一系列仅在表达式为真（其值不为零）时才执行的命令。然后可以可选地有一个`else`行，后面是仅在表达式为假时才执行的一系列命令。列表的结束由包含`end`的一行标记。

`while` 
    此命令允许编写循环。其语法类似于`if`：该命令接受一个参数，即要评估的表达式，并且必须后跟要执行的命令，每行一个，以`end`结束。这些命令称为循环体。只要表达式评估为真，`while`循环体中的命令就会重复执行。

`loop_break` 
    此命令退出包含它的`while`循环。脚本在该`while`的`end`行之后继续执行。

`loop_continue` 
    此命令跳过包含它的`while`循环体中其余命令的执行。执行分支到`while`循环的开头，在那里它评估控制表达式。

`end` 
    终止`if`、`else`或`while`流控制命令的命令块。
```
### 23.1.4 控制输出的命令

在执行命令文件或用户定义的命令期间，正常的 gdb 输出被抑制；唯一出现的输出是命令定义中明确打印的内容。本节介绍三个对于生成所需输出非常有用的命令。
```
`echo text` 
    打印文本。文本中可以包含使用 C 转义序列的非打印字符，例如 `\n` 来打印换行。除非指定，否则不会打印换行。除了标准的 C 转义序列外，反斜杠后跟一个空格表示一个空格。这对于显示开头或结尾有空格的字符串很有用，因为否则所有参数的前导和尾随空格都会被修剪。要打印 `'` 和 `foo = `，可以使用命令 `echo \ and foo = \ `。
    可以像在 C 中一样，在文本末尾使用反斜杠来将命令延续到后续行。例如：
        echo This is some text\n\
        which is continued\n\
        onto several lines.\n
        
    产生的输出与以下内容相同：
        echo This is some text\n
        echo which is continued\n
        echo onto several lines.\n

`output expression` 
    打印表达式的值，仅此而已：没有换行，没有 `$nn = `。该值也不会被输入到值历史记录中。有关表达式的更多信息，请参阅第 10.1 节[表达式]，第 145 页。

`output/fmt expression` 
    以格式 `fmt` 打印表达式的值。可以使用与 `print` 相同的格式。有关更多信息，请参阅第 10.5 节[输出格式]，第 150 页。

`printf template, expressions...` 
    在字符串模板的控制下打印一个或多个表达式的值。要打印多个值，使表达式成为用逗号分隔的单个表达式列表，这些表达式可以是数字或指针。它们的值按照模板指定的方式打印，就像 C 程序通过执行以下代码一样：
        printf (template, expressions...);
    与 C `printf` 一样，模板中的普通字符按原样打印，而由 `%` 字符引入的转换规范会导致后续表达式被求值，它们的值根据转换规范中编码的类型和样式信息进行转换和格式化，然后打印。
    例如，你可以这样以十六进制打印两个值：
        printf "foo, bar-foo = 0x%x, 0x%x\n", foo, bar-foo
    `printf` 支持所有标准的 C 转换规范，包括在 `%` 字符和转换字母之间的标志和修饰符，但有以下例外：
        • 不支持参数排序修饰符，如 `2$`。
        • 不支持用于指定精度或宽度的修饰符 `*`。
        • 不支持 `’` 标志（用于根据 `LC_NUMERIC` 分隔数字成组）。
        • 不支持类型修饰符 `hh`、`j`、`t` 和 `z`。
        • 不支持转换字母 `n`（如 `%n`）。
        • 不支持转换字母 `a` 和 `A`。
    请注意，只有在用于构建 gdb 的底层 C 实现支持 `long long int` 类型时，才支持 `ll` 类型修饰符，只有在存在 `long double` 类型时，才支持 `L` 类型修饰符。
    与 C 一样，`printf` 支持简单的反斜杠转义序列，如 `\n`、`\t`、`\\`、`\"`、`\a` 和 `\f`，它们由反斜杠后跟一个字符组成。不支持八进制和十六进制转义序列。
    此外，`printf` 支持使用以下长度修饰符与浮点说明符一起对 DFP（十进制浮点数）类型进行转换规范。字母：
        • `H` 用于打印 `Decimal32` 类型。
        • `D` 用于打印 `Decimal64` 类型。
        • `DD` 用于打印 `Decimal128` 类型。
    
    如果用于构建 gdb 的底层 C 实现支持 DFP 类型的这三个长度修饰符，那么 gdb 也将可用其他修饰符，如宽度和精度。
    如果没有这样的 C 支持，将不会有其他修饰符可用，并且该值将以标准方式打印。
    以下是使用上述转换字母打印 DFP 类型的示例：
        printf "D32: %Hf - D64: %Df - D128: %DDf\n",1.2345df,1.2E10dd,1.2E10dl
    此外，`printf` 支持特殊的 `%V` 输出格式。此格式打印表达式的字符串表示形式，就像 gdb 使用标准 `print` 命令生成的那样（见第 10 章[检查数据]，第 141 页）：

        (gdb) print array
        $1 = {0, 1, 2, 3, 4, 5}
        (gdb) printf "Array is: %V\n", array
        Array is: {0, 1, 2, 3, 4, 5}

    可以通过将打印选项放在 `%V` 之后的 `[...]` 中来包含 `%V` 格式的打印选项，如下所示：
        (gdb) printf "Array is: %V[-array-indexes on]\n", array
        Array is: {[0] = 0, [1] = 1, [2] = 2, [3] = 3, [4] = 4, [5] = 5}

    如果需要在 `%V` 之后直接打印一个文字 `[`，则只需包含一个空的打印选项列表：
        (gdb) printf "Array is: %V[][Hello]\n", array
        Array is: {0, 1, 2, 3, 4, 5}[Hello]

`eval template, expressions...` 
    在字符串模板的控制下将一个或多个表达式的值转换为命令行，并调用它。
```
### 23.1.5 控制自动加载本地 gdb 脚本

当读取一个新的目标文件时（例如，由于`file`命令，或者因为被调试程序加载了一个共享库），gdb 将查找命令文件`objfile-gdb.gdb`。请参阅第 23.5 节[自动加载扩展]，第 574 页。

可以启用或禁用自动加载，并且可以打印自动加载脚本的列表。
```
`set auto-load gdb-scripts [on|off]`
    启用或禁用命令脚本的预定义序列的自动加载。

`show auto-load gdb-scripts`
    显示是否启用了命令脚本的预定义序列的自动加载。

`info auto-load gdb-scripts [regexp]`
    打印 gdb 自动加载的所有命令脚本的预定义序列的列表。
```
如果提供了`regexp`，则仅打印名称匹配的命令脚本的预定义序列。

## 23.2 命令别名

别名允许你为现有命令定义替代拼写。例如，如果在 Python 中定义了一个新的`gdb`命令（见第 23.3 节[Python]，第 401 页），它有一个很长的名字，那么有一个涉及较少输入的缩写版本会很方便。

`gdb`本身使用别名。例如，`s`是`step`命令的别名，尽管它在其他方面是`set`和`show`等命令的模糊缩写。

别名还用于提供多字命令的缩短或更常见的版本。例如，`gdb`提供了`set inferior-tty`命令的`tty`别名。

你可以使用`alias`命令定义一个新的别名。
```
alias [-a] [--] alias = command [default-args]
```
`alias`指定新别名的名称。`alias`的每个单词必须由字母、数字、破折号和下划线组成。

`command`指定正在被别名的现有命令的名称。

`command`也可以是现有别名的名称。在这种情况下，`command`不能是具有默认参数的别名。

`-a`选项指定新别名是该命令的缩写。缩写不用于命令补全。

`--`选项指定选项的结束，当别名以破折号开头时很有用。

你可以为别名指定默认参数。这些默认参数将自动添加到在命令行上显式输入的别名参数之前。

例如，以下定义了一个别名`btfullall`，用于显示所有局部变量和所有帧参数：
```
(gdb) alias btfullall = backtrace -full -frame-arguments all
```
有关默认参数的更多信息，请参阅第 23.2.1 节[默认参数]，第 400 页。

这是一个简单的示例，展示如何创建一个命令的缩写，以便减少输入量。假设你厌倦了输入`disas`，这是`disassemble`命令当前最短的无歧义缩写，并且你想要一个更短的版本，名为`di`。以下将实现此目的。
```
(gdb) alias -a di = disas
```
请注意，别名与用户定义的命令不同。对于用户定义的命令，你还需要使用`document`命令为其编写文档。别名会自动获取现有命令的文档。

这是一个示例，其中我们在`set print elements`命令中将`elms`作为`elements`的缩写。这是为了表明你可以对命令的任何部分进行缩写。
```
(gdb) alias -a set print elms = set print elements
(gdb) alias -a show print elms = show print elements
(gdb) set p elms 200
(gdb) show p elms
```
要打印的字符串字符或数组元素的限制为 200。

请注意，如果你正在定义一个`set`命令的别名，并且你想要为相应的`show`命令定义一个别名，那么你需要分别定义后者。

在命令和别名中允许使用无歧义的缩写命令，就像通常一样。
```
(gdb) alias -a set pr elms = set p ele
```
最后，这是一个示例，展示了为更复杂的命令创建一个单字别名。这创建了`set print elements`命令的别名`spe`。
```
(gdb) alias spe = set print elements
(gdb) spe 20
```
### 23.2.1 默认参数

你可以告诉 gdb，在使用用户定义的别名时，总是在用户显式提供的参数列表前面添加一些默认参数。

如果对于一个命令你反复使用相同的参数或选项，你可以为这个命令定义一个别名，并告诉 gdb 在使用该别名时自动在你显式输入的参数列表前面添加这些参数或选项。

例如，如果你经常使用`thread apply all`命令，并指定按升序处理线程，并且在遇到错误时继续，你可以通过以下方式告诉 gdb 自动添加`-ascending`和`-c`选项：
```
(gdb) alias thread apply asc-all = thread apply all -ascending -c
```
一旦你使用默认参数定义了这个别名，任何时候你输入`thread apply asc-all`以及一些参数，gdb 将执行`thread apply all -ascending -c 一些参数`。

为了减少输入，你也可以定义一个单字别名：
```
(gdb) alias t_a_c = thread apply all -ascending -c
```
像往常一样，对于别名和默认参数可以使用明确的缩写。

一个命令的不同别名不会共享它们的默认参数。例如，你定义一个新的别名`bt_ALL`，用于显示所有可能的信息，以及另一个别名`bt_SMALL`，用于显示非常有限的信息，使用：
```
(gdb) alias bt_ALL = backtrace -entry-values both -frame-arg all \
    -past-main -past-entry -full
(gdb) alias bt_SMALL = backtrace -entry-values no -frame-arg none \
    -past-main off -past-entry off
```
（关于使用别名命令的更多信息，请参阅第 23.2 节[别名]，第 399 页。）

默认参数不仅限于命令的参数和选项，还可以指定嵌套命令，如果命令接受这样的嵌套命令作为参数。例如，下面定义了`faalocalsoftype`，它列出具有某种类型局部变量的帧，以及匹配的局部变量：
```
(gdb) alias faalocalsoftype = frame apply all info locals -q -t
(gdb) faalocalsoftype int
#1 0x55554f5e in sleeper_or_burner (v=0xdf50) at sleepers.c:86
i = 0
ret = 21845
```
这对于定义一组嵌套的`with`命令的别名以具有特定的临时设置组合也非常有用。例如，下面定义了别名`pp10`，用于美化打印一个表达式参数，如果表达式是字符串或数组，则最多打印 10 个元素：
```
(gdb) alias pp10 = with print pretty -- with print elements 10 -- print
```
这将别名`pp10`定义为一系列 3 个命令。第一部分`with print pretty --`临时激活设置`set print pretty`，然后启动跟随分隔符`--`的命令。跟随第一部分的命令也是一个`with`命令，它临时将设置`set print elements`更改为 10，然后启动跟随第二个分隔符`--`的命令。第三部分`print`是`pp10`别名将启动的命令，使用设置的临时值和用户显式给出的参数。有关`with`命令用法的更多信息，请参阅第 3.2 节[命令设置]，第 23 页。

默认情况下，请求别名的帮助将显示别名命令的文档。当别名是一组嵌套命令时，别名的帮助将显示第一个命令的文档。对于像`pp10`这样的别名，使用`document`命令提供特定的文档非常有用（请参阅第 23.1.1 节[定义]，第 391 页）。

## 23.3 使用 Python 扩展 gdb

你可以使用 Python 编程语言来扩展 gdb（http://www.python.org/）。仅当 gdb 使用 --with-python 进行配置时，此功能才可用。

gdb 使用的 Python 脚本应安装在 data-directory/python 中，其中 data-directory 是在 gdb 启动时确定的数据目录（参见第 18.8 节[数据文件]，第 308 页）。此目录称为 python 目录，会自动添加到 Python 搜索路径中，以允许 Python 解释器定位在此位置安装的所有脚本。

此外，在 Python 中编写并位于 data-directory/python/gdb/command 或 data-directory/python/gdb/function 目录中的 gdb 命令和便利函数在 gdb 启动时会自动导入。

### 23.3.1 Python 命令

gdb 提供了两个用于访问 Python 解释器的命令和一个相关设置：
```
`python-interactive [command]`
`pi [command]`
    如果没有参数，`python-interactive` 命令可用于启动一个交互式 Python 提示符。要返回 gdb，请输入 EOF 字符（例如，在空提示符上按 Ctrl-D）。
    或者，可以将单行 Python 命令作为参数给出并进行评估。如果命令是一个表达式，结果将被打印；否则，不会打印任何内容。例如：
        (gdb) python-interactive 2 + 3
        5

`python [command]`
`py [command]`
    `python` 命令可用于评估 Python 代码。
    如果给定参数，`python` 命令将把参数作为 Python 命令进行评估。例如：
        (gdb) python print 23
        23
    如果您没有向 `python` 提供参数，它将充当多行命令，类似于 `define`。在这种情况下，Python 脚本由 `python` 命令之后的后续命令行组成。此命令列表使用包含 `end` 的行终止。例如：
        (gdb) python
        >print 23
        >end
        23

`set python print-stack`
    默认情况下，当 Python 脚本中发生错误时，gdb 将仅打印 Python 异常的消息部分。这可以使用 `set python print-stack` 进行控制：如果为 `full`，则启用完整的 Python 堆栈打印；如果为 `none`，则禁用 Python 堆栈和消息打印；如果为 `message`（默认值），则仅打印错误的消息部分。

`set python ignore-environment [on|off]`
    默认情况下，此选项为 `off`，并且当 gdb 初始化其内部 Python 解释器时，Python 解释器将检查环境中的变量，这些变量将影响其行为，例如 `PYTHONHOME` 和 `PYTHONPATH2` 。
    如果在 Python 初始化之前将此选项设置为 `on`，则 Python 将忽略所有此类环境变量。由于 Python 在 gdb 启动过程的早期进行初始化，因此此选项必须放置在早期初始化文件中（请参阅第 2.1.4 节[初始化文件]，第 17 页）才能达到预期效果。
    此选项等同于向真实的 Python 可执行文件传递 `-E`。

`set python dont-write-bytecode [auto|on|off]`
    当此选项为 `off` 时，一旦 gdb 初始化了 Python 解释器，解释器将对其导入的任何 Python 模块进行字节编译，并将字节码写入磁盘上的 `.pyc` 文件。
    如果在 Python 初始化之前将此选项设置为 `on`，则 Python 将不再将字节码写入磁盘。由于 Python 在 gdb 启动过程的早期进行初始化，因此此选项必须放置在早期初始化文件中（请参阅第 2.1.4 节[初始化文件]，第 17 页）才能达到预期效果。
    默认情况下，此选项设置为 `auto`。在这种模式下，如果 `python ignore-environment` 设置为 `off`，则将检查环境变量 `PYTHONDONTWRITEBYTECODE` 以确定是否应写出字节码。`PYTHONDONTWRITEBYTECODE` 被视为关闭/禁用，当设置为空字符串或环境变量不存在时。所有其他设置，包括那些似乎没有意义的设置，都表示它是打开/启用的。
    此选项等同于向真实的 Python 可执行文件传递 `-B`。
```
也可以从 gdb 解释器执行 Python 脚本：
```
`source script-name`
    脚本名称必须以 `.py` 结尾，并且 gdb 必须使用 `script-extension` 设置配置为基于文件名扩展名识别脚本语言。请参阅第 23 章[扩展 GDB]，第 391 页。
```
以下命令旨在帮助调试 gdb 本身：
```
`set debug py-breakpoint on|off`
`show debug py-breakpoint`
    当为 `on` 时，gdb 打印与 Python 断点 API 相关的调试消息。默认情况下为 `off`。

`set debug py-unwind on|off`
`show debug py-unwind`
    当为 `on` 时，gdb 打印与 Python 解卷器 API 相关的调试消息。默认情况下为 `off`。
```
### 23.3.2 Python API

你可以通过执行命令`python help (gdb)`来获取`gdb`的 Python API 的快速在线帮助。

具有两个或更多可选参数的函数和方法允许使用关键字语法来指定它们。这允许在跳过其他参数的同时传递一些可选参数。例如：`gdb.some_function ('foo', bar = 1, baz = 2)`。

#### 23.3.2.1 基本 Python

在启动时，`gdb`会覆盖 Python 的`sys.stdout`和`sys.stderr`，使用`gdb`的输出分页流进行打印。输出到这些流之一的 Python 程序的输出可能会被用户中断（见第 22.4 节[屏幕大小]，第 370 页）。在这种情况下，会抛出一个 Python 的`KeyboardInterrupt`异常。

在编写要在`gdb`中运行的 Python 代码时，必须注意一些事项。特别值得注意的两点是：

- `gdb`为`SIGCHLD`和`SIGINT`安装了处理程序。Python 代码不得覆盖这些处理程序，甚至不得使用`sigaction`更改选项。如果你的程序更改了这些信号的处理方式，`gdb`很可能会停止正常工作。请注意，不幸的是，GUI 工具包通常会安装一个`SIGCHLD`处理程序。在创建新的 Python 线程时，你可以使用`gdb.block_signals`或`gdb.Thread`来正确处理此问题；见第 23.3.2.2 节[`gdb`中的线程]，第 409 页。

- `gdb`会小心地将其内部文件描述符标记为在执行时关闭。然而，并非在所有平台上都能以线程安全的方式做到这一点。你的 Python 程序应该意识到这一点，并且在启动子进程之前，应该创建带有`close-on-exec`标志设置的新文件描述符，并安排关闭不需要的文件描述符。

`gdb`引入了一个新的 Python 模块，名为`gdb`。`gdb`添加的所有方法和类都放在这个模块中。`gdb`会自动导入`gdb`模块，以便在`python`命令评估的所有脚本中使用。

`gdb`模块的某些类型具有文本表示形式（可通过`repr`或`str`函数访问）。这些仅用于调试目的，预计它们会随时间而变化。
```
[变量] `gdb.PYTHONDIR`
    一个包含 Python 目录的字符串（见第 23.3 节[Python]，第 401 页）。

[函数] `gdb.execute (command [, from tty [, to string]])`
    将`command`（一个字符串）作为`gdb`命令行命令进行求值。如果在`command`运行期间发生`GDB`异常，它将按照第 23.3.2.3 节[异常处理]，第 410 页中所述进行转换。
    `from tty`标志指定`gdb`是否应将此命令视为由用户交互调用。它必须是一个布尔值。如果省略，默认为`False`。
    默认情况下，`command`生成的任何输出都将发送到`gdb`的标准输出（如果打开了日志记录，则也发送到日志输出）。如果`to string`参数为`True`，则`gdb.execute`将收集输出并将其作为字符串返回。默认值为`False`，在这种情况下，返回值为`None`。如果`to string`为`True`，`gdb`虚拟终端将暂时设置为无限宽度和高度，并且其分页将被禁用；见第 22.4 节[屏幕大小]，第 370 页。

[函数] `gdb.breakpoints ()`
    返回一个包含`gdb`所有断点的序列。有关更多信息，请见第 23.3.2.33 节[Python 中的断点]，第 491 页。在`gdb`版本 7.11 及更早版本中，如果没有断点，此函数返回`None`。此特性随后被修复，现在在这种情况下`gdb.breakpoints`返回一个空序列。

[函数] `gdb.rbreak (regex [, minsyms [, throttle, [, symtabs ]])`
    返回一个包含新设置的与`regex`模式定义的函数名称匹配的`gdb.Breakpoint`对象集合的 Python 列表。如果`minsyms`关键字为`True`，则所有系统函数（那些在下级中未明确定义的函数）也将包含在匹配中。`throttle`关键字接受一个整数，该整数定义`regex`模式匹配的函数的最大匹配数。如果匹配数超过`throttle`的整数值，将引发`RuntimeError`，并且不会创建任何断点。如果未定义`throttle`，则对要创建的最大匹配数和断点没有施加限制。`symtabs`关键字接受一个 Python 可迭代对象，该对象生成`gdb.Symtab`对象的集合，并将搜索限制为仅包含在`gdb.Symtab`对象中的那些函数。

[函数] `gdb.parameter (parameter)`
    返回给定名称（字符串）的`gdb`参数的值；如果参数具有多部分名称，则参数名称字符串可以包含空格。例如，`‘print object’`是一个有效的参数名称。
    如果指定的参数不存在，此函数将引发`gdb.error`（见第 23.3.2.3 节[异常处理]，第 410 页）。否则，将参数的值转换为适当类型的 Python 值并返回。

[函数] `gdb.set_parameter (name, value)`
    将`gdb`参数`name`设置为`value`。与`gdb.parameter`一样，如果参数具有多部分名称，则参数名称字符串可以包含空格。

[函数] `gdb.with_parameter (name, value)`
    创建一个 Python 上下文管理器（用于与 Python 的`with`语句一起使用），该管理器将`gdb`参数`name`临时设置为`value`。在退出上下文时，将恢复先前的值。
    此函数在其实现中使用`gdb.parameter`，因此它可以引发与该函数相同的异常。
    例如，有时在使用特定的`gdb`语言评估一些 Python 代码时很有用：
        with gdb.with_parameter(’language’, ’pascal’):
            ... language-specific operations

[函数] `gdb.history (number)`
    从`gdb`的值历史记录中返回一个值（见第 10.11 节[值历史记录]，第 170 页）。`number`参数指示要返回的历史元素。如果`number`为负数，则`gdb`将取其绝对值，并从最后一个元素（即最近的元素）开始向后计数以找到要返回的值。如果`number`为零，则`gdb`将返回最近的元素。如果`number`指定的元素在值历史记录中不存在，将引发`gdb.error`异常。
    如果没有引发异常，返回值始终是`gdb.Value`的一个实例（见第 23.3.2.4 节[来自下级的值]，第 411 页）。

[函数] `gdb.add_history (value)`
    接受`value`（`gdb.Value`的一个实例，见第 23.3.2.4 节[来自下级的值]，第 411 页），并将该对象表示的值附加到`gdb`的值历史记录中（见第 10.11 节[值历史记录]，第 170 页），并返回一个整数，其历史编号。
    如果`value`不是`gdb.Value`，将使用`gdb.Value`构造函数进行转换。如果`value`无法转换为`gdb.Value`，将引发`TypeError`。
    当在 Python 中实现的命令将单个`gdb.Value`作为其结果打印时，将该值放入历史记录中将允许用户通过 CLI 历史记录工具方便地访问这些值。

[函数] `gdb.history_count ()`
    返回一个整数，指示`gdb`的值历史记录中的值的数量（见第 10.11 节[值历史记录]，第 170 页）。

[函数] `gdb.convenience_variable (name)`
    返回名为`name`的便利变量的值（见第 10.12 节[便利变量]，第 171 页）。`name`必须是一个字符串。该名称不应包含在表达式中用于标记便利变量的`‘$’`。如果便利变量不存在，则返回`None`。

[函数] `gdb.set_convenience_variable (name, value)`
    设置名为`name`的便利变量的值（见第 10.12 节[便利变量]，第 171 页）。`name`必须是一个字符串。该名称不应包含在表达式中用于标记便利变量的`‘$’`。如果`value`为`None`，则删除便利变量。否则，如果`value`不是`gdb.Value`（见第 23.3.2.4 节[来自下级的值]，第 411 页），将使用`gdb.Value`构造函数进行转换。

[函数] `gdb.parse_and_eval (expression [, global context])`
    将`expression`（必须是一个字符串）解析为当前语言中的表达式，对其求值，并将结果作为`gdb.Value`返回。
    `global context`（如果提供）是一个布尔值，指示解析是否应在全局上下文中进行。默认值为`False`，意味着应使用当前帧或当前静态上下文。
    在实现新命令时（见第 23.3.2.21 节[`gdb`中的 CLI 命令]，第 463 页，见第 23.3.2.22 节[`gdb/MI`命令在 Python 中]，第 467 页），此函数很有用，因为它提供了一种将命令的参数解析为表达式的方法。它也仅用于计算值很有用。

[函数] `gdb.find_pc_line (pc)`
    返回与`pc`值对应的`gdb.Symtab_and_line`对象。见第 23.3.2.31 节[Python 中的符号表]，第 489 页。如果将无效的`pc`值作为参数传递，则返回的`gdb.Symtab_and_line`对象的`symtab`和`line`属性将分别为`None`和 0。这与`gdb.current_progspace().find_pc_line(pc)`相同，是为了历史兼容性而包含的。

[函数] `gdb.write (string [, stream])`
    将一个字符串打印到`gdb`的分页输出流。可选的`stream`确定要打印到的流。默认流是`gdb`的标准输出流。可能的`stream`值是：
    - `gdb.STDOUT`：`gdb`的标准输出流。
    - `gdb.STDERR`：`gdb`的标准错误流。
    - `gdb.STDLOG`：`gdb`的日志流（见第 2.4 节[日志输出]，第 21 页）。
    写入`sys.stdout`或`sys.stderr`将自动调用此函数，并将输出自动定向到相关流。

[函数] `gdb.flush ([, stream])`
    刷新`gdb`分页流的缓冲区，以便立即显示内容。当`gdb`在缓冲区中遇到换行符时，它将自动刷新流的内容。可选的`stream`确定要刷新的流。默认流是`gdb`的标准输出流。可能的`stream`值是：
    - `gdb.STDOUT`：`gdb`的标准输出流。
    - `gdb.STDERR`：`gdb`的标准错误流。
    - `gdb.STDLOG`：`gdb`的日志流（见第 2.4 节[日志输出]，第 21 页）。
    刷新`sys.stdout`或`sys.stderr`将自动为相关流调用此函数。

[函数] `gdb.target_charset ()`
    返回当前目标字符集的名称（见第 10.21 节[字符集]，第 187 页）。这与`gdb.parameter('target-charset')`的不同之处在于，永远不会返回`‘auto’`。

[函数] `gdb.target_wide_charset ()`
    返回当前目标宽字符集的名称（见第 10.21 节[字符集]，第 187 页）。这与`gdb.parameter('target-wide-charset')`的不同之处在于，永远不会返回`‘auto’`。

[函数] `gdb.host_charset ()`
    返回一个字符串，即当前主机字符集的名称（见第 10.21 节[字符集]，第 187 页）。这与`gdb.parameter('host-charset')`的不同之处在于，永远不会返回`‘auto’`。

[函数] `gdb.solib_name (address)`
    将包含给定地址的共享库的名称作为字符串返回，或者返回`None`。这与`gdb.current_progspace().solib_name(address)`相同，是为了历史兼容性而包含的。

[函数] `gdb.decode_line ([expression])`
    返回由`expression`指定的行的位置，或者如果未提供参数则返回当前行的位置。此函数返回一个包含两个元素的 Python 元组。第一个元素包含一个字符串，其中包含`expression`的任何未解析部分（如果`expression`已完全解析，则为`None`）。第二个元素包含`None`或另一个包含与表示为`gdb.Symtab_and_line`对象的表达式匹配的所有位置的元组（见第 23.3.2.31 节[Python 中的符号表]，第 489 页）。如果提供了`expression`，它将以`gdb`的内置`break`或`edit`命令的方式进行解码（见第 9.2 节[位置规范]，第 126 页）。

[函数] `gdb.prompt_hook (current prompt)`
    如果`prompt hook`是可调用的，`gdb`将在`gdb`显示提示符之前调用分配给此操作的方法。
    `parameter current_prompt`包含当前`gdb`提示符。此方法必须返回一个 Python 字符串，或`None`。如果返回一个字符串，`gdb`提示符将设置为该字符串。如果返回`None`，`gdb`将继续使用当前提示符。
    某些提示符不能在`gdb`中替换。例如，`readline`用于命令输入的辅助提示符以及与注释相关的提示符被禁止更改。

[函数] `gdb.architecture_names ()`
    返回一个包含`gdb`当前构建的所有架构名称的列表。  每个架构名称都是一个字符串。此列表中返回的名称与从`gdb.Architecture.name`（见[Architecture.name]，第 497 页）返回的名称相同。

[函数]`gdb.connections`
    返回一个`gdb.TargetConnection`对象的列表，每个当前活动的连接对应一个对象（见第 23.3.2.38 节[Python 中的连接]，第 499 页）。在返回的列表中，连接对象没有特定的顺序。

[函数]`gdb.format_address (address [, progspace, architecture])`
    以“addr <symbol+offset>”的格式返回一个字符串，其中`addr`是以十六进制格式化的地址，`symbol`是其地址最接近`address`且在内存中低于`address`的符号，`offset`是以十进制表示的从`symbol`到`address`的偏移量。如果未找到合适的符号，则<symbol+offset>部分不包含在返回的字符串中，而是返回的字符串仅包含以十六进制格式化的地址。可以使用`set print max-symbolic-offset`（见第 10.9 节[打印设置]，第 157 页）控制`gdb`向后查找合适符号的距离。
    此外，当设置`print symbol-filename on`（见第 10.9 节[打印设置]，第 157 页）时，返回的字符串可以包含文件名和行号信息，在这种情况下，返回字符串的格式为“addr <symbol+offset> at filename:line-number”。
    `progspace`是在其中查找`symbol`的`gdb.Progspace`，`architecture`在格式化`addr`时使用，例如为了确定地址的字节大小。如果既不传递`progspace`也不传递`architecture`，则默认情况下`gdb`将使用当前选定的下级的程序空间和架构，因此，以下两个调用是等效的：
        gdb.format_address(address)
        gdb.format_address(address, 
                            gdb.selected_inferior().progspace, 
                            gdb.selected_inferior().architecture())
    
    仅传递`progspace`或`architecture`之一是无效的，必须同时提供两者，或者都不提供（并使用默认值）。
    此方法使用与核心`gdb`在`disassemble`等命令中用于格式化地址、符号和偏移信息相同的机制。
    以下是可能的字符串格式的一些示例：
        0x00001042
        0x00001042 <symbol+16>
        0x00001042 <symbol+16 at file.c:123>

[函数]`gdb.current_language ()`
    以字符串形式返回当前语言的名称。与`gdb.parameter('language')`不同，此函数永远不会返回“auto”。如果有`gdb.Frame`对象可用（见第 23.3.2.28 节[Python 中的帧]，第 479 页），在某些情况下，`language`方法可能更可取，因为它不受用户语言设置的影响。
```
#### 23.3.2.2 GDB 中的线程

gdb 不是线程安全的。如果你的 Python 程序使用多个线程，你必须小心，仅在 gdb 线程中调用特定于 gdb 的函数。gdb 提供了一些函数来帮助处理此问题。
```
[函数] gdb.block_signals()
    如前所述（见第 23.3.2.1 节[基本 Python]，第 403 页），某些信号必须传递到 gdb 主线程。block_signals 函数返回一个上下文管理器，在进入时将阻塞这些信号。这可用于在启动新线程时确保在那里阻塞信号，如下所示：
        with gdb.block_signals():
            start_new_thread()

[类] gdb.Thread
    这是 Python 的 threading.Thread 类的子类。它重写了 start 方法以调用 block_signals，使其成为创建在 gdb 中能良好工作的线程的易于使用的替换方案。

[函数] gdb.interrupt()
    这会导致 gdb 做出反应，就好像用户在终端输入了控制-C 字符一样。也就是说，如果被调试程序正在运行，它将被中断；如果正在执行 gdb 命令，它将停止；如果正在运行 Python 命令，将引发 KeyboardInterrupt。与 gdb 中的大多数 Python API 不同，interrupt 是线程安全的。

[函数] gdb.post_event(event)
    将 event（一个不接受参数的可调用对象）放入 gdb 的内部事件队列中。这个可调用对象将在稍后的某个时间点，在 gdb 的事件处理期间被调用。使用 post_event 发布的事件将按照它们被发布的顺序运行；然而，无法知道相对于 gdb 内部的其他事件它们将在何时被处理。
    与 gdb 中的大多数 Python API 不同，post_event 是线程安全的。例如：
        (gdb) python
        >import threading
        >
        >class Writer():
        > def __init__(self, message):
        > self.message = message;
        > def __call__(self):
        > gdb.write(self.message)
        >
        >class MyThread1 (threading.Thread):
        > def run (self):
        > gdb.post_event(Writer("Hello "))
        >
        >class MyThread2 (threading.Thread):
        > def run (self):
        > gdb.post_event(Writer("World\n"))
        >
        >MyThread1().start()
        >MyThread2().start()
        >end
        (gdb) Hello World
```
#### 23.3.2.3 异常处理

在执行 Python 命令时，Python 代码中未捕获的异常会被转换为对 gdb 错误报告机制的调用。如果调用 Python 的命令未处理该错误，gdb 将终止它并打印错误消息。具体将打印的内容取决于`set python print-stack`（参见第 23.3.1 节[Python 命令]，第 402 页）。例如：
```
(gdb) python print foo
Traceback (most recent call last):
  File "<string>", line 1, in <module>
NameError: name ’foo’ is not defined
```
在 Python 代码调用的 gdb 命令中发生的 gdb 错误会转换为 Python 异常。Python 异常的类型取决于错误。
```
gdb.error
    这是 gdb 生成的大多数异常的基类。它派生自`RuntimeError`，以与早期版本的 gdb 兼容。
    如果 gdb 中发生的错误不适合某些更具体的类别，那么生成的异常将具有此类型。

gdb.MemoryError
    这是 gdb.error 的子类，当操作尝试访问 inferior 中的无效内存时抛出。

KeyboardInterrupt
    用户中断（通过 C-c 或在分页提示处输入 q）被转换为 Python 的`KeyboardInterrupt`异常。
```
在所有情况下，你的异常处理程序将看到 gdb 错误消息作为其值，并且在最接近 gdb 错误发生的 Python 语句处的 Python 调用堆栈回溯作为回溯。

当通过`gdb.Command`在 Python 中实现 gdb 命令，或通过`gdb.Function`实现函数时，能够抛出一个不会导致打印回溯的异常是很有用的。例如，用户可能错误地调用了该命令。gdb 提供了一个特殊的异常类可用于此目的。
```
gdb.GdbError
    从命令或函数中抛出时，此异常将导致命令或函数失败，但不会显示 Python 堆栈。gdb 本身不会抛出此异常，而是在从用户 Python 代码中抛出时识别它。例如：
        (gdb) python
        >class HelloWorld (gdb.Command):
        > """Greet the whole world."""
        > def __init__ (self):
        > super (HelloWorld, self).__init__ ("hello-world", gdb.COMMAND_USER)
        > def invoke (self, args, from_tty):
        > argv = gdb.string_to_argv (args)
        > if len (argv) != 0:
        > raise gdb.GdbError ("hello-world takes no arguments")
        > print ("Hello, World!")
        >HelloWorld ()
        >end
        (gdb) hello-world 42
        hello-world takes no arguments
```
#### 23.3.2.4 来自 inferior 的值

gdb 在`gdb.Value`类型的对象中提供从 inferior 程序获取的值。

gdb 使用此对象进行 inferior 值的内部记账，并在必要时获取值。

简单标量的 inferior 值可以直接在对值的数据类型有效的 Python 表达式中使用。以下是整数或浮点数`some_val`的示例：
```
bar = some_val + 2
```
这样，`bar`也将是一个`gdb.Value`对象，其值的类型与`some_val`相同。对于表示结构体或类对象的`gdb.Value`对象，也可以执行有效的 Python 操作。在这种情况下，重载的运算符（如果存在）用于执行操作。例如，如果`val1`和`val2`是表示重载`+`运算符的类的实例的`gdb.Value`对象，则可以在 Python 脚本中如下使用`+`运算符：
```
val3 = val1 + val2
```
操作的结果`val3`也是一个对应于重载`+`运算符返回值的`gdb.Value`对象。通常，对于以下操作调用重载运算符：`+`（二进制加法）、`-`（二进制减法）、`*`（乘法）、`/`、`%`、`<<`、`>>`、`|`、`&`、`^`。

结构体或某些类的实例的 inferior 值可以使用 Python 字典语法访问。例如，如果`some_val`是一个持有结构体的`gdb.Value`实例，则可以使用以下方式访问其`foo`元素：
```
bar = some_val['foo']
```
同样，`bar`也将是一个`gdb.Value`对象。也可以通过使用`gdb.Field`对象作为下标来访问结构体元素（有关`gdb.Field`对象的更多信息，请参阅第 23.3.2.5 节[Python 中的类型]，第 418 页）。例如，如果`foo_field`是对应于上述结构体的`foo`元素的`gdb.Field`对象，则也可以如下访问`bar`：
```
bar = some_val[foo_field]
```
如果`gdb.Value`具有数组或指针类型，则可以使用整数索引来访问元素。
```
result = some_array[23]
```
表示函数的`gdb.Value`可以通过 inferior 函数调用执行。提供给调用的任何参数必须与函数的原型匹配，并且必须按照该原型指定的顺序提供。

例如，`some_val`是一个表示接受两个整数作为参数的函数的`gdb.Value`实例。要执行此函数，请如下调用它：
```
result = some_val(10, 20)
```
从函数调用返回的任何值都将存储为`gdb.Value`。

提供了以下属性：
```
[变量] `Value.address`
    如果此对象是可寻址的，则此只读属性包含一个表示地址的`gdb.Value`对象。否则，此属性为`None`。

[变量] `Value.is_optimized_out`
    如果编译器优化掉了此值，则此只读布尔属性为`True`，因此无法从 inferior 中获取此值。

[变量] `Value.type`
    此`gdb.Value`的类型。此属性的值是一个`gdb.Type`对象（请参阅第 23.3.2.5 节[Python 中的类型]，第 418 页）。

[变量] `Value.dynamic_type`
    此`gdb.Value`的动态类型。此使用对象的虚表和 C++运行时类型信息（RTTI）来确定值的动态类型。如果此值是类类型，则如果有，它将返回嵌入该值的类。如果此值是指向类类型的指针或引用，则它将计算引用对象的动态类型，并分别返回指向该类型的指针或引用。
    在所有其他情况下，它将返回值的静态类型。
    请注意，此功能仅在调试包含针对所讨论对象的 RTTI 的 C++程序时才有效。否则，它将仅返回值的静态类型，如`ptype foo`（请参阅第 16 章[符号]，第 263 页）。

[变量] `Value.is_lazy`
    如果此`gdb.Value`尚未从 inferior 中获取，则此只读布尔属性的值为`True`。为了提高效率，gdb 直到必要时才获取值。
    例如：
        myval = gdb.parse_and_eval('somevar')
    此时不会获取`somevar`的值。它将在需要该值时或调用`fetch_lazy`方法时获取。

[变量]`Value.bytes`
    此属性的值是一个字节对象，包含以小端序组成此`Value`的完整值的字节。如果此值的完整内容不可用，则访问此属性将引发异常。
    此属性也可以赋值。新值应该是一个缓冲区对象（例如，一个字节对象），新缓冲区的长度必须与该`Value`类型的长度完全匹配。新缓冲区中的字节值应以小端序排列。
    与`Value.assign`（请参阅[Value.assign]，第 414 页）一样，如果此值无法赋值，则将引发异常。
```
提供了以下方法：
```
[函数] `Value.__init__(val)`
    通过此对象初始化器，可以将许多 Python 值直接转换为`gdb.Value`。具体而言：
    Python boolean      Python 布尔值将转换为当前语言的布尔类型。
    Python integer      Python 整数将转换为当前体系结构的 C `long`类型。
    Python long         Python 长整数将转换为当前体系结构的 C `long long`类型。
    Python float        Python 浮点数将转换为当前体系结构的 C `double`类型。
    Python string       Python 字符串将使用当前目标编码转换为当前目标语言中的目标字符串。如果某个字符无法在当前目标编码中表示，则将引发异常。
    gdb.Value           如果`val`是一个`gdb.Value`，则会制作该值的副本。
    gdb.LazyString      如果`val`是一个`gdb.LazyString`（请参阅第 23.3.2.35 节[Python 中的延迟字符串]，第 497 页），则将调用延迟字符串的`value`方法，并使用其结果。

[函数] `Value.__init__(val, type)`
    此`gdb.Value`构造函数的第二种形式返回一个类型为`type`的`gdb.Value`，其中值内容取自由`val`指定的 Python 缓冲区对象。
    Python 缓冲区对象中的字节数必须大于或等于`type`的大小。
    如果`type`为`None`，则此版本的`__init__`的行为就像根本没有传递`type`一样。

[函数]`Value.assign(rhs)`
    将`rhs`分配给此值，并返回`None`。如果此值无法分配，或者由于某种原因分配无效（例如类型检查失败），则将引发异常。

[函数] `Value.cast(type)`
    返回一个新的`gdb.Value`实例，该实例是将此实例强制转换为`type`描述的类型的结果，`type`必须是一个`gdb.Type`对象。如果由于某种原因无法执行强制转换，此方法将引发异常。

[函数] `Value.dereference()`
    对于指针数据类型，此方法返回一个新的`gdb.Value`对象，其内容是指针所指向的对象。例如，如果`foo`是 C 中指向`int`的指针，在 C 程序中声明为
        int *foo;
    则可以使用相应的`gdb.Value`如下访问`foo`所指向的内容：
        bar = foo.dereference()
    结果`bar`将是一个持有`foo`所指向值的`gdb.Value`对象。
    存在类似的函数`Value.referenced_value`，它也返回与指针值所指向的值对应的`gdb.Value`对象（以及引用值所引用的值）。然而，`Value.dereference`的行为与`Value.referenced_value`的不同之处在于，`Value.dereference`的行为与在给定值上应用 C 一元运算符`*`相同。例如，考虑在 C++程序中声明的对指针`ptrref`的引用：
        typedef int *intptr;
        ...
        int val = 10;
        intptr ptr = &val;
        intptr &ptrref = ptr;
    虽然`ptrref`是一个引用值，但可以对与其对应的`gdb.Value`对象应用`Value.dereference`方法，并获得与`val`对应的`gdb.Value`，该值相同。但是，如果应用`Value.referenced_value`方法，则结果将是与`ptr`对应的`gdb.Value`对象。
        py_ptrref = gdb.parse_and_eval ("ptrref")
        py_val = py_ptrref.dereference ()
        py_ptr = py_ptrref.referenced_value ()
    `gdb.Value`对象`py_val`与`val`对应的对象相同，`py_ptr`与`ptr`对应的对象相同。通常，只要可以对相应的 C 值应用 C 一元运算符`*`，就可以应用`Value.dereference`。对于允许同时应用`Value.dereference`和`Value.referenced_value`的情况，获得的结果不一定相同（如我们在上述示例中所见）。但是，当应用于 C/C++程序中对应于指针（类型代码为`TYPE_CODE_PTR`的`gdb.Value`对象）的值时，结果是相同的。

[函数] `Value.referenced_value()`
    对于指针或引用数据类型，此方法返回一个新的`gdb.Value`对象，该对象对应于指针/引用值所引用的值。对于指针数据类型，`Value.dereference`和`Value.referenced_value`产生相同的结果。
    这些方法之间的区别在于，`Value.dereference`无法获取引用值所引用的值。例如，考虑在 C++程序中声明的对`int`的引用：
        int val = 10;
        int &ref = val;
    然后，对与`ref`对应的`gdb.Value`对象应用`Value.dereference`将导致错误，而应用`Value.referenced_value`将导致与`val`对应的`gdb.Value`对象。
        py_ref = gdb.parse_and_eval ("ref")
        er_ref = py_ref.dereference () # Results in error
        py_val = py_ref.referenced_value () # Returns the referenced value
    `gdb.Value`对象`py_val`与`val`对应的对象相同。

[函数] `Value.reference_value()`
    返回一个`gdb.Value`对象，该对象是对由该实例封装的值的引用。

[函数] `Value.const_value()`
    返回一个`gdb.Value`对象，该对象是由该实例封装的值的常量版本。

[函数] `Value.dynamic_cast(type)`
    类似于`Value.cast`，但行为类似于使用 C++ `dynamic_cast`运算符。有关详细信息，请咨询 C++参考。

[函数] `Value.reinterpret_cast(type)`
    类似于`Value.cast`，但行为类似于使用 C++ `reinterpret_cast`运算符。有关详细信息，请咨询 C++参考。

[函数] `Value.format_string(...)`
    将`gdb.Value`转换为字符串，类似于`print`命令的作用。不带参数调用时，这等效于在`gdb.Value`上调用`str`函数。
    同一值的表示可能在不同版本的 gdb 中有所变化，因此，例如，不应解析此方法返回的字符串。
    所有参数都是关键字参数。如果未指定参数，则使用当前全局默认设置。
    `raw`               如果不应使用漂亮打印机（请参阅第 10.10 节[漂亮打印]，第 168 页）来格式化值，则为`True`。如果启用了与`gdb.Value`表示的类型匹配的漂亮打印机，则为`False`。
    `pretty_arrays`     如果应将数组进行漂亮打印以更方便阅读，则为`True`，否则为`False`（请参阅第 10.9 节[打印设置]，第 157 页中的`set print array`）。
    `pretty_structs`    如果应将结构体进行漂亮打印以更方便阅读，则为`True`，否则为`False`（请参阅第 10.9 节[打印设置]，第 157 页中的`set print pretty`）。
    `array_indexes`     如果数组的字符串表示应包括数组索引，则为`True`，否则为`False`（请参阅第 10.9 节[打印设置]，第 157 页中的`set print array-indexes`）。
    `symbols`           如果指针的字符串表示应包括相应的符号名称（如果存在），则为`True`，否则为`False`（请参阅第 10.9 节[打印设置]，第 157 页中的`set print symbol`）。
    `unions`            如果包含在其他结构体或联合中的联合应展开，则为`True`，否则为`False`（请参阅第 10.9 节[打印设置]，第 157 页中的`set print union`）。
    `address`           如果指针的字符串表示应包括地址，则为`True`，否则为`False`（请参阅第 10.9 节[打印设置]，第 157 页中的`set print address`）。
    `nibbles`           如果二进制值应以四位一组（称为“半字节”）显示，则为`True`。如果不应显示（见第 10.9 节[打印设置]，第 157 页），则为`False`。
    `deref_refs`        如果 C++引用应解析为它们所引用的值，则为`True`；如果不应解析（默认值），则为`False`。请注意，与`print`命令不同，在使用`format_string`方法或`str`函数时，引用不会自动展开。没有全局打印设置可更改默认行为。
    `actual_objects`    如果指向对象的指针的表示应使用虚函数表来标识对象的实际（派生）类型，而不是声明类型，则为`True`；如果应使用声明类型，则为`False`。（见第 10.9 节[打印设置]，第 157 页中的`set print object`）。
    `static_members`    如果静态成员应包含在 C++对象的字符串表示中，则为`True`；如果不应包含，则为`False`。（见第 10.9 节[打印设置]，第 157 页中的`set print static-members`）。
    `max_characters`    要打印的字符串字符数，0 表示遵循`max_elements`，`UINT_MAX`表示打印不限数量的字符。（见第 10.9 节[打印设置]，第 157 页中的`set print characters`）。
    `max_elements`      要打印的数组元素数，或 0 表示打印不限数量的元素。（见第 10.9 节[打印设置]，第 157 页中的`set print elements`）。
    `max_depth`         嵌套结构和联合的打印最大深度，或-1 表示打印不限数量的元素。（见第 10.9 节[打印设置]，第 157 页中的`set print max-depth`）。
    `repeat_threshold`  设置抑制重复数组元素显示的阈值。（见第 10.9 节[打印设置]，第 157 页）。
    `format`            包含单个字符的字符串，表示用于返回字符串的格式。例如，`'x'`等同于使用`gdb`命令`print`并带有`/x`选项，将值格式化为十六进制数。
    `styling`           如果`gdb`应将样式应用于返回的字符串，则为`True`。应用样式时，返回的字符串可能包含 ANSI 终端转义序列。仅在打开样式时才会包含转义序列，见第 22.5 节[输出样式]，第 371 页。此外，`gdb`仅对某些值内容应用样式，因此并非每个输出字符串都将包含转义序列。当为`False`（默认值）时，不应用输出样式。
    `summary`           仅应打印摘要时为`True`。在这种模式下，标量值将完整打印，但诸如结构或联合之类的聚合将被省略。此模式由`set print frame-arguments scalars`使用（见第 10.9 节[打印设置]，第 157 页）。

[Function] Value.to_array ()
    如果此值是类似数组的（见[Type.is array like]，第 420 页），则此方法将其转换为数组并返回。如果此值已经是数组，则直接返回。否则，将抛出异常。

[Function] Value.string ([encoding[, errors[, length]]])
    如果此`gdb.Value`表示一个字符串，则此方法将内容转换为 Python 字符串。否则，此方法将抛出异常。
    根据当前语言的规则解释值为字符串。如果提供了可选的`length`参数，字符串将转换为该长度，并将包含字符串可能包含的任何嵌入零。否则，对于字符串以零终止的语言，将转换整个字符串。例如，在类似 C 的语言中，如果值是指向字符或`wchar_t`、`char16_t`或`char32_t`类型的整数的指针或数组，则该值是一个字符串。
    如果提供了可选的`encoding`参数，它必须是一个命名`gdb.Value`中字符串编码的字符串，例如"ascii"、"iso-8859-6"或"utf-8"。它接受与 Python 的`string.decode`方法的相应参数相同的编码，并且将使用 Python 编解码器机制来转换字符串。如果未提供`encoding`或`encoding`为空字符串，则将使用目标字符集（见第 10.21 节[字符集]，第 187 页），或者如果当前语言能够提供一个特定于语言的编码，则将使用该编码。
    可选的`errors`参数与 Python 的`string.decode`方法的相应参数相同。
    如果提供了可选的`length`参数，将获取字符串并将其转换为给定的长度。

[Function]Value.lazy_string ([encoding [, length]])
    此方法尝试将此`gdb.Value`转换为`gdb.LazyString`（见第 23.3.2.35 节[Python 中的懒字符串]，第 497 页）。可以转换数组或指针类型的值；对于其他类型，此方法将抛出异常。
    如果提供了可选的`encoding`参数，它必须是一个命名`gdb.LazyString`编码的字符串。一些示例是：'ascii'、'iso-8859-6'或'utf-8'。如果`encoding`参数是`gdb`识别的编码，`gdb`将引发错误。
    当打印懒字符串时，将在打印期间使用`gdb`编码机制来转换字符串。如果未提供可选的`encoding`参数或为空字符串，`gdb`将自动选择最适合字符串类型的编码。有关`gdb`中编码的更多信息，请见第 10.21 节[字符集]，第 187 页。
    如果提供了可选的`length`参数，将获取字符串并将其编码为指定的字符长度。如果未提供`length`参数，将获取字符串并进行编码，直到找到适当宽度的空字符。

[Function] Value.fetch_lazy ()
    如果`gdb.Value`对象当前是懒值（`gdb.Value.is_lazy`为`True`），则从下级获取值。在此过程中发生的任何错误都将产生 Python 异常。
    如果`gdb.Value`对象不是懒值，此方法将不起作用。
    此方法不返回值。
```

#### 23.3.2.5 Types In Python

`gdb` 使用类 `gdb.Type` 来表示来自下级的类型。

`gdb` 模块中提供了以下与类型相关的函数：

```
[Function] gdb.lookup_type (name [, block])
    此函数通过名称查找类型，名称必须是字符串。
    如果提供了 `block`，则在该作用域中查找名称。否则，将在全局范围内搜索。
    通常，此函数将返回 `gdb.Type` 的实例。如果找不到指定的类型，它将抛出异常。
```
    
无需通过名称查找即可找到整数类型。有关整数类型，请参阅第 23.3.2.36 节[Python 中的体系结构]，第 497 页的 `integer_type` 方法。

如果类型是结构或类类型，或枚举类型，则可以使用 Python 字典语法访问该类型的字段。例如，如果 `some_type` 是一个 `gdb.Type` 实例，持有一个结构类型，则可以使用以下方式访问其 `foo` 字段：

```
bar = some_type['foo']
```

`bar` 将是一个 `gdb.Field` 对象；有关 `gdb.Field` 类的描述，请参阅下面关于 `Type.fields` 方法的描述。

一个 `Type` 的实例具有以下属性：

```
[Variable] `Type.alignof`
    此类型的对齐方式，以字节为单位。类型对齐来自调试信息；如果未指定，则 `gdb` 将使用相关的 ABI 来尝试确定对齐方式。在某些情况下，甚至这也是不可能的，将返回零。

[Variable] `Type.code`
    此类型的类型代码。类型代码将是下面定义的 `TYPE_CODE_` 常量之一。

[Variable] `Type.dynamic`
    一个布尔值，表示此类型是否是动态的。在某些情况下，例如 Rust 枚举类型或 Ada 变体记录，值的具体类型可能取决于其内容。也就是说，变量的声明类型或 `gdb.lookup_type` 返回的类型可能是动态的；而变量值的类型将是该动态类型的具体实例。
    例如，考虑以下代码：
        int n;
        int array[n];
    在这里，至少在概念上（你的编译器是否实际这样做是另一个问题），检查 `gdb.lookup_symbol("array",...)`.type 可能会返回一个报告大小为 `None` 的 `gdb.Type`。这是动态类型。
    然而，检查 `gdb.parse_and_eval("array")`.type 将返回一个具体类型，其长度是已知的。

[Variable] `Type.name`
    此类型的名称。如果此类型没有名称，则返回 `None`。

[Variable] `Type.sizeof`
    此类型的大小，以目标字符单位为单位。通常，目标的字符类型将是一个 8 位字节。然而，在一些不寻常的平台上，此类型可能具有不同的大小。
    动态类型可能没有固定的大小；在这种情况下，此属性的值将为 `None`。

[Variable] `Type.tag`
    此类型的标记名称。标记名称是 C 和 C++ 中 `struct`、`union` 或 `enum` 之后的名称；并非所有语言都有此概念。如果此类型没有标记名称，则返回 `None`。

[Variable] `Type.objfile`
    定义此类型的 `gdb.Objfile`，或者如果没有关联的 `objfile`，则为 `None`。

[Variable] `Type.is_scalar`
    如果类型是标量类型，则此属性为 `True`，否则此属性为 `False`。
    非标量类型的示例包括结构、联合和类。

[Variable]`Type.is_signed`
    对于标量类型（`Type.is_scalar` 为 `True` 的类型），如果类型是有符号的，则此属性为 `True`，否则此属性为 `False`。
    对于非标量类型（`Type.is_scalar` 为 `False` 的类型）尝试读取此属性将引发 `ValueError`。

[Variable] `Type.is_array_like`
    一个布尔值，表示此类型是否是类似数组的。
    某些语言具有内部表示为结构的类似数组的对象。例如，对于 Rust 切片类型或 Ada 无约束数组，这是正确的。
    `gdb` 可能知道这些类型。此确定是基于类型的起源语言。

[Variable] `Type.is_string_like`
    一个布尔值，表示此类型是否是类似字符串的。与 `Type.is_array_like` 一样，这是基于类型的起源语言确定的。
```

提供了以下方法：

```
[Function] `Type.fields ()`
    返回此类型的字段。行为取决于类型代码：
        - 对于结构和联合类型，此方法返回字段。
        - 枚举类型每个枚举常量有一个字段。
        - 函数和方法类型每个参数有一个字段。C++ 类的基类型也表示为字段。
        - 数组类型有一个字段表示数组的范围。
        - 如果类型不适合这些类别之一，则引发 `TypeError`。
    每个字段都是一个 `gdb.Field` 对象，具有一些预定义的属性：
        - `bitpos`：         此属性对于枚举或静态（如 C++）字段不可用。值是从包含类型的开头开始计数的位位置。请注意，在动态类型中，字段的位置可能不是恒定的。在这种情况下，值将为 `None`。此外，动态类型可能具有在相应的具体类型中不出现的字段。
        - `enumval`：        此属性仅适用于枚举字段，其值是枚举成员的整数表示。
        - `name`：           字段的名称，对于匿名字段为 `None`。
        - `artificial`：     如果字段是人工的，通常意味着它是由编译器提供而不是用户提供的，则此为 `True`。此属性始终提供，并且如果字段不是人工的，则为 `False`。
        - `is_base_class`：  如果字段表示 C++ 结构的基类，则此为 `True`。此属性始终提供，并且如果字段不是作为 `fields` 参数的类型的基类，或者如果该类型不是 C++ 类，则为 `False`。
        - `bitsize`：        如果字段是打包的或位域，则此将具有非零值，即字段的大小（以位为单位）。否则，此将为零；在这种情况下，字段的大小由其类型给出。
        - `type`：           字段的类型。通常是 `Type` 的实例，但在某些情况下可能为 `None`。
        - `parent_type`：    包含此字段的类型。这是一个 `gdb.Type` 实例。

[Function] `Type.array (n1 [, n2])`
    返回一个新的 `gdb.Type` 对象，该对象表示此类型的数组。如果提供一个参数，它是数组的包含上限；在这种情况下，下限为零。如果提供两个参数，第一个参数是数组的下限，第二个参数是数组的上限。数组的长度不得为负数，但边界可以。

[Function] `Type.vector (n1 [, n2])`
    返回一个新的 `gdb.Type` 对象，该对象表示此类型的向量。如果提供一个参数，它是向量的包含上限；在这种情况下，下限为零。如果提供两个参数，第一个参数是向量的下限，第二个参数是向量的上限。向量的长度不得为负数，但边界可以。
    数组和向量的区别在于，数组的行为类似于 C：在表达式中使用时，它们会衰减为指向第一个元素的指针，而向量被视为一流值。

[Function] `Type.const ()`
    返回一个新的 `gdb.Type` 对象，该对象表示此类型的 `const` 限定变体。

[Function] `Type.volatile ()`
    返回一个新的 `gdb.Type` 对象，该对象表示此类型的 `volatile` 限定变体。

[Function] `Type.unqualified ()`
    返回一个新的 `gdb.Type` 对象，该对象表示此类型的未限定变体。也就是说，结果既不是 `const` 也不是 `volatile`。

[Function] `Type.range ()`
    返回一个 Python `Tuple` 对象，其中包含两个元素：参数类型的下限和该类型的上限。如果类型没有范围，`gdb` 将引发 `gdb.error` 异常（请参阅第 23.3.2.3 节[异常处理]，第 410 页）。

[Function] `Type.reference ()`
    返回一个新的 `gdb.Type` 对象，该对象表示对此类型的引用。

[Function] `Type.pointer ()`
    返回一个新的 `gdb.Type` 对象，该对象表示指向此类型的指针。

[Function] `Type.strip_typedefs ()`
    返回一个新的 `gdb.Type`，表示在删除所有 `typedef` 层之后的真实类型。

[Function] `Type.target ()`
    返回一个新的 `gdb.Type` 对象，该对象表示此类型的目标类型。
    对于指针类型，目标类型是指向对象的类型。对于数组类型（意味着类似 C 的数组），目标类型是数组元素的类型。对于函数或方法类型，目标类型是返回值的类型。对于复杂类型，目标类型是元素的类型。对于 `typedef`，目标类型是别名类型。
    如果类型没有目标，此方法将引发异常。

[Function] `Type.template_argument (n [, block])`
    如果此 `gdb.Type` 是模板的实例化，则此将返回一个新的 `gdb.Value` 或 `gdb.Type`，表示第 `n` 个模板参数的值（从 0 开始索引）。
    如果此 `gdb.Type` 不是模板类型，或者类型的模板参数少于 `n`，则将引发异常。通常，只有 C++ 代码才会有模板类型。
    如果提供了 `block`，则在该作用域中查找名称。否则，将在全局范围内搜索。

[Function] `Type.optimized_out ()`
    返回此类型的 `gdb.Value` 实例，其值已被优化掉。这允许框架装饰器指示参数或局部变量的值未知。
```

每种类型都有一个代码，用于指示此类型属于哪个类别。可用的类型类别由 `gdb` 模块中定义的常量表示：

```
- `gdb.TYPE_CODE_PTR`：                  类型是指针。
- `gdb.TYPE_CODE_ARRAY`：                类型是数组。
- `gdb.TYPE_CODE_STRUCT`：               类型是结构。
- `gdb.TYPE_CODE_UNION`：                类型是联合。
- `gdb.TYPE_CODE_ENUM`：                 类型是枚举。
- `gdb.TYPE_CODE_FLAGS`：                位标志类型，用于诸如状态寄存器之类的东西。
- `gdb.TYPE_CODE_FUNC`：                 类型是函数。
- `gdb.TYPE_CODE_INT`：                  类型是整数类型。
- `gdb.TYPE_CODE_FLT`：                  浮点类型。
- `gdb.TYPE_CODE_VOID`：                 特殊类型 `void`。
- `gdb.TYPE_CODE_SET`：                  Pascal 集类型。
- `gdb.TYPE_CODE_RANGE`：                范围类型，即具有边界的整数类型。
- `gdb.TYPE_CODE_STRING`：               字符串类型。请注意，这仅用于某些具有语言定义的字符串类型的语言；C 字符串不是以这种方式表示的。
- `gdb.TYPE_CODE_BITSTRING`：            位字符串。已弃用。
- `gdb.TYPE_CODE_ERROR`：                未知或错误的类型。
- `gdb.TYPE_CODE_METHOD`：               方法类型，如在 C++ 中找到的。
- `gdb.TYPE_CODE_METHODPTR`：            指向成员函数的指针。
- `gdb.TYPE_CODE_MEMBERPTR`：            指向成员的指针。
- `gdb.TYPE_CODE_REF`：                  引用类型。
- `gdb.TYPE_CODE_RVALUE_REF`：           C++11 右值引用类型。
- `gdb.TYPE_CODE_CHAR`：                 字符类型。
- `gdb.TYPE_CODE_BOOL`：                 布尔类型。
- `gdb.TYPE_CODE_COMPLEX`：              复数浮点数类型。
- `gdb.TYPE_CODE_TYPEDEF`：              指向其他类型的 `typedef`。
- `gdb.TYPE_CODE_NAMESPACE`：            C++ 命名空间。
- `gdb.TYPE_CODE_DECFLOAT`：             十进制浮点数类型。
- `gdb.TYPE_CODE_INTERNAL_FUNCTION`：    `gdb` 内部的函数。这是用于表示便利函数的类型。
- `gdb.TYPE_CODE_XMETHOD`：              `gdb` 内部的方法。这是用于表示 xmethods 的类型（请参阅第 23.3.2.16 节[编写 Xmethod]，第 446 页）。
- `gdb.TYPE_CODE_FIXED_POINT`：          定点数。
- `gdb.TYPE_CODE_NAMESPACE`：            Fortran 命名列表。
```
在 `gdb.types` Python 模块中提供了对类型的进一步支持（请参阅第 23.3.4.2 节[gdb.types]，第 517 页）。

#### 23.3.2.6 美化打印器 API

一个美化打印器（pretty-printer）只是一个持有一个值并实现特定接口的对象，该接口在此处定义。提供了一个示例输出（参见第 10.10 节[美化打印]，第 168 页）。

由于 gdb 没有记录美化打印器的可扩展性，默认情况下，gdb 将假定仅基本的美化打印器方法可用。基本方法如下所示并被标记为这样。

为了允许可扩展性，gdb 提供了`gdb.ValuePrinter`基类。这个类不提供任何属性或行为，而是作为一个可以被 gdb 识别的标签。对于这样的打印器，gdb 保留所有以小写字母开头的属性。也就是说，将来，gdb 可能会向美化打印器协议添加一个新方法或属性，并且基于`gdb.ValuePrinter`的打印器应该优雅地处理这个问题。一种简单的方法是对实现中局部的任何属性使用前导下划线（或两个，遵循 Python 的名称修饰方案）。

```
[函数] `pretty_printer.children (self)`
    gdb 将在一个美化打印器上调用这个方法来计算美化打印器值的子节点。
    这个方法必须返回一个符合 Python 迭代器协议的对象。迭代器返回的每个项必须是一个包含两个元素的元组。第一个元素是子节点的“名称”；第二个元素是子节点的值。该值可以是任何可以转换为 gdb 值的 Python 对象。
    这是一个基本方法，是可选的。如果不存在，gdb 将表现得好像该值没有子节点。
    为了提高效率，`children`方法应该延迟计算其结果。这将允许 gdb 读取尽可能少的元素，例如当各种打印设置（参见第 10.9 节[打印设置]，第 157 页）或`-var-list-children`（参见第 27.17 节[GDB/MI 变量对象]，第 642 页）限制要显示的元素数量时。
    基于“set print max-depth”的值（参见第 10.9 节[打印设置]，第 157 页），子节点可能会被隐藏不显示。

[函数]`pretty_printer.display_hint (self)`
    CLI 可以调用这个方法并使用其结果来更改值的格式。
    该结果也将作为正在打印的变量的“displayhint”属性提供给 MI 消费者。
    这是一个基本方法，是可选的。如果存在，这个方法必须返回一个字符串或特殊值`None`。
    一些显示提示是由 gdb 预定义的：
        ‘array’ 表示正在打印的对象是“类似数组的”。CLI 使用此来尊重诸如“set print elements”和“set print array”等参数。
        ‘map’ 表示正在打印的对象是“类似映射的”，并且这个值的子节点可以假定在键和值之间交替。
        ‘string’ 表示正在打印的对象是“类似字符串的”。如果打印器的`to_string`方法返回某种 Python 字符串，那么 gdb 将调用其内部特定于语言的字符串打印函数来格式化字符串。对于 CLI ，这意味着添加引号，可能转义一些字符，尊重“set print elements”等。
    特殊值`None`使 gdb 应用默认的显示规则。

[函数] `pretty_printer.to_string (self)`
    gdb 将调用这个方法来显示传递给对象构造函数的值的字符串表示形式。
    这是一个基本方法，是可选的。
    从 CLI 打印时，如果`to_string`方法存在，那么 gdb 将在子节点返回的值之前添加其结果。具体如何进行这种格式化取决于显示提示，并且随着更多提示的添加可能会发生变化。此外，根据打印设置（参见第 10.9 节[打印设置]，第 157 页），CLI 可能仅在堆栈跟踪中打印`to_string`的结果，省略子节点的结果。
    如果这个方法返回一个字符串，它将被逐字打印。
    否则，如果这个方法返回一个`gdb.Value`的实例，那么 gdb 将打印这个值。这可能会导致调用另一个美化打印器。
    如果该方法返回一个可以转换为`gdb.Value`的 Python 值，那么 gdb 将执行转换并打印结果值。同样，这可能会导致调用另一个美化打印器。Python 标量（整数、浮点数和布尔值）和字符串可以转换为`gdb.Value`；其他类型则不行。
    最后，如果这个方法返回`None`，那么在这个方法中不会执行进一步的操作，也不会打印任何内容。
    如果结果不是这些类型之一，将引发异常。

[函数] `pretty_printer.num_children ()`
    这不是一个基本方法，所以 gdb 只会在从`gdb.ValuePrinter`派生的对象上调用它。
    如果可用，这个方法应该返回子节点的数量。如果数量不容易计算，可以返回`None`。

[函数] `pretty_printer.child (n)`
    这不是一个基本方法，所以 gdb 只会在从`gdb.ValuePrinter`派生的对象上调用它。
    如果可用，这个方法应该返回由`n`指示的子项（即包含这个子项的名称和值的元组）。索引从 0 开始。
```

gdb 提供了一个可以用于查找 gdb.Value 的默认美化打印器的函数：

```
[函数] `gdb.default_visualizer (value)`
    这个函数将一个`gdb.Value`对象作为参数。如果存在这个值的美化打印器，那么它将被返回。如果不存在这样的打印器，那么它将返回`None`。
```

通常，一个美化打印器可以通过调用`Value.format_string`（参见第 23.3.2.4 节[来自下级的值]，第 411 页）来尊重用户的打印设置（包括临时应用的设置，如“/x”）。然而，这些设置也可以直接查询：

```
[函数] `gdb.print_options ()`
    返回一个字典，其键是可以传递给`Value.format_string`的有效关键字，其值是用户的设置。在打印或其他操作期间，这些值将反映任何临时有效的标志。
        (gdb) python print (gdb.print_options ()[’max_elements’])
        200
```

#### 23.3.2.7 选择漂亮打印机

gdb 提供了几种注册漂亮打印机的方式：全局注册、每个程序空间注册以及每个对象文件注册。在选择如何注册漂亮打印机时，一个好的规则是在尽可能小的范围内进行注册：即优先在特定的对象文件中注册，然后在程序空间中注册，仅在万不得已时才在全局范围内注册。
```
[变量] gdb.pretty_printers
    Python 列表 gdb.pretty_printers 包含一个通过添加注册为漂亮打印机的函数或可调用对象的数组。此列表中的打印机称为全局打印机，在调试所有 inferior 时它们都可用。
```
每个 gdb.Progspace 都包含一个 pretty_printers 属性。每个 gdb.Objfile 也包含一个 pretty_printers 属性。

这些列表上的每个函数都传递一个单个的 gdb.Value 参数，并应返回一个符合上述接口定义（见第 23.3.2.6 节[漂亮打印 API]，第 424 页）的漂亮打印机对象。如果一个函数无法为该值创建漂亮打印机，它应返回 None。

gdb 首先检查当前程序空间中每个 gdb.Objfile 的 pretty_printers 属性，并在该 gdb.Objfile 的列表中迭代调用每个启用的查找例程，直到收到一个漂亮打印机对象。如果在对象文件列表中未找到漂亮打印机，gdb 然后搜索当前程序空间的漂亮打印机列表，调用每个启用的函数，直到返回一个对象。在这些列表用尽之后，它尝试全局的 gdb.pretty_printers 列表，再次调用每个启用的函数，直到返回一个对象。

对象文件的搜索顺序未指定。对于给定的列表，函数总是从列表的头部开始调用，并按顺序迭代直到列表结束或返回一个打印机对象。

由于各种原因，一个漂亮打印机可能无法工作。例如，底层数据结构可能已更改，而漂亮打印机已过时。

损坏的漂亮打印机的后果非常严重，以至于 gdb 提供了支持来启用和禁用单个打印机。例如，如果“打印帧参数”处于打开状态，并且任何参数都使用损坏的打印机打印，则回溯可能会变得非常难以辨认。

通过向注册的函数或可调用对象附加一个 enabled 属性来启用和禁用漂亮打印机。如果存在此属性且其值为 False，则打印机被禁用，否则打印机被启用。

#### 23.3.2.8 编写一个美化打印器

一个美化打印器由两部分组成：一个用于检测类型是否受支持的查找函数，以及打印器本身。

以下是一个示例，展示如何编写一个`std::string`打印器。有关此类必须提供的 API 的详细信息，请参阅第 23.3.2.6 节[美化打印 API]，第 424 页。请注意，此示例使用`gdb.ValuePrinter`基类，并小心地为其局部状态使用前导下划线。

```python
class StdStringPrinter(gdb.ValuePrinter):

    "Print a std::string"

    def __init__(self, val):
        self.__val = val

    def to_string(self):
        return self.__val['_M_dataplus']['_M_p']

    def display_hint(self):
        return 'string'

```

以下是一个用于上述打印器示例的查找函数的示例：

```python
def str_lookup_function(val):
    lookup_tag = val.type.tag
    if lookup_tag is None:
        return None
    regex = re.compile("^std::basic_string<char,.*>$")
    if regex.match(lookup_tag):
        return StdStringPrinter(val)
    return None
```

示例查找函数提取值的类型，并尝试将其与它可以美化打印的类型匹配。如果它是打印器可以美化打印的类型，它将返回一个打印器对象。如果不是，则返回`None`。

我们建议将您的核心美化打印器放入一个 Python 包中。如果您的美化打印器用于与库一起使用，我们进一步建议将版本号嵌入到包名称中。这种做法将使`gdb`能够同时加载您的美化打印器的多个版本，因为它们将具有不同的名称。

您应该编写自动加载的代码（请参阅第 23.3.3 节[Python 自动加载]，第 516 页），以便可以多次评估而不改变其含义。一个理想的自动加载文件将仅包含对您的打印器模块的导入，然后是对具有当前`objfile`的注册美化打印器的调用。

总体而言，这种方法可以很好地扩展到多个 inferior，每个 inferior 可能使用不同的库版本。在 Python 包名称中嵌入版本号将确保`gdb`能够同时加载两组打印器。然后，因为美化打印器的搜索是通过`objfile`完成的，并且因为您的自动加载代码负责将库的打印器注册到特定的`objfile`，所以`gdb`将为每个 inferior 使用的库的特定版本找到正确的打印器。

继续`std::string`示例（请参阅第 23.3.2.6 节[美化打印 API]，第 424 页），此代码可能出现在`gdb.libstdcxx.v6`中：

```python
def register_printers(objfile):
    objfile.pretty_printers.append(str_lookup_function)
```

然后，自动加载文件的相应内容将是：

```python
import gdb.libstdcxx.v6
gdb.libstdcxx.v6.register_printers(gdb.current_objfile())
```

前面的示例说明了一个基本的美化打印器。有一些可以改进的地方。打印器没有名称，这使得在已安装的打印器列表中很难识别。查找函数有一个名称，但查找函数可以有任意的，甚至相同的名称。

其次，打印器仅处理一种类型，而库通常有几种类型。可以为库中的每个所需类型安装一个查找函数，但也可以让一个单个查找函数识别多种类型。后者是处理此问题的常规方法。如果一个美化打印器可以处理多种数据类型，那么它的子打印器就是用于各个数据类型的打印器。

`gdb.printing`模块提供了一种正式的方法来解决这些问题（请参阅第 23.3.4.1 节[gdb.printing]，第 517 页）。以下是另一个处理多种类型的示例。

这些是我们要美化打印的类型：

```python
struct foo { int a, b; };
struct bar { struct foo x, y; };
```

以下是打印器：

```python
class fooPrinter(gdb.ValuePrinter):
    """Print a foo object."""
    
    def __init__(self, val):
        self.__val = val

    def to_string(self):
        return ("a=<" + str(self.__val["a"]) +
                "> b=<" + str(self.__val["b"]) + ">")

class barPrinter(gdb.ValuePrinter):
    """Print a bar object."""

    def __init__(self, val):
        self.__val = val

    def to_string(self):
        return ("x=<" + str(self.__val["x"]) +
                "> y=<" + str(self.__val["y"]) + ">")

```

此示例不需要查找函数，这由`gdb.printing`模块处理。而是提供了一个函数来构建处理查找的对象。

```python
import gdb.printing

def build_pretty_printer():
    pp = gdb.printing.RegexpCollectionPrettyPrinter(
        "my_library")
    pp.add_printer('foo', '^foo$', fooPrinter)
    pp.add_printer('bar', '^bar$', barPrinter)
    return pp
```

以下是自动加载支持：

```python
import gdb.printing
import my_library
gdb.printing.register_pretty_printer(
    gdb.current_objfile(),
    my_library.build_pretty_printer())
```

最后，当此打印器加载到`gdb`中时，这是`info pretty-printer`的相应输出：

```
(gdb) info pretty-printer
my_library.so:
  my_library
    foo
    bar
```

#### 23.3.2.9 类型打印 API

gdb 提供了一种让 Python 代码定制类型显示的方式。这主要用于用规范的类型定义名称替换类型。

一个类型打印机只是一个符合特定协议的 Python 对象。提供了一个实现该协议的简单基类；请参阅第 23.3.4.2 节[gdb.types]，第 517 页。类型打印机必须至少提供：

```
[类型打印机的实例变量] enabled
    一个布尔值，如果打印机已启用，则为 True，否则为 False。这通过启用类型打印机和禁用类型打印机命令进行操作。

[类型打印机的实例变量] name
    类型打印机的名称。必须是一个字符串。这由启用类型打印机和禁用类型打印机命令使用。

[类型打印机上的方法] instantiate(self)
    这在开始类型打印时由 gdb 调用。仅在类型打印机启用时才调用。此方法必须返回一个提供识别方法的新对象，如下所述。
```

当显示一个类型时，例如通过 ptype 命令，gdb 将计算一个类型识别器列表。这通过首先遍历每个对象文件的类型打印机（见第 23.3.2.27 节[Python 中的对象文件]，第 476 页），然后遍历每个程序空间的类型打印机（见第 23.3.2.26 节[Python 中的程序空间]，第 474 页），最后遍历全局类型打印机来完成。

gdb 将调用每个启用的类型打印机的 instantiate 方法。如果此方法返回 None，则结果将被忽略；否则，它将被添加到识别器列表中。然后，当 gdb 要显示一个类型名称时，它将遍历识别器列表。对于每个识别器，它将调用识别函数，如果函数返回非 None 值，则停止。识别函数定义为：

```
[类型识别器上的方法] recognize(self, type)
    如果未识别类型，则返回 None。否则，返回一个要作为类型名称打印的字符串。类型参数将是 gdb.Type 的一个实例（见第 23.3.2.5 节[Python 中的类型]，第 418 页）。
```

gdb 使用这种两遍方法，以便类型打印机可以有效地缓存信息而不会持有太久。例如，在类型打印机中查找类型信息并在识别器的生命周期内持有它可能很方便；如果只进行一次遍历，那么类型打印机将不得不使用事件系统，以避免在下级更改时持有可能过时的信息。

#### 23.3.2.10 过滤帧

帧过滤器是 Python 对象，当`gdb`打印回溯（见第 8.2 节[回溯]，第 114 页）时，它们会操作一个或多个帧的可见性。

只有打印回溯的命令，或者在`gdb/mi`命令的情况下（见第 27 章[GDB/MI]，第 591 页），那些返回帧集合的命令才会受到影响。

与帧过滤器一起使用的命令有：`backtrace`（见[回溯命令]，第 114 页）、`-stack-list-frames`（见[`-stack-list-frames`命令]，第 639 页）、`-stack-list-variables`（见[`-stack-list-variables`命令]，第 641 页）、`-stack-list-arguments`（见[`-stack-list-arguments`命令]，第 637 页）和`-stack-list-locals`（见[`-stack-list-locals`命令]，第 641 页）。

帧过滤器的工作方式是将一个迭代器作为参数，对该迭代器的内容应用操作，并返回另一个迭代器（或者，在过滤器不执行任何操作的情况下，可能返回提供给它的相同迭代器）。通常，帧过滤器利用 Python 的`itertools`模块等工具来处理和从源迭代器创建新的迭代器。无论过滤器选择如何应用操作，它都不能更改底层的`gdb`帧，也不能尝试在`gdb`中更改调用栈。这保留了`gdb`中的数据完整性。帧过滤器是按优先级执行的，应该注意，一些帧过滤器可能已经在之前执行，而一些帧过滤器将在之后执行。

在设计帧过滤器时，一个重要的考虑因素，值得深思的是，帧过滤器应该尽可能避免展开调用栈。有些栈可以运行得非常深，在某些情况下甚至可以达到数万个。在帧过滤器执行时搜索每个帧在该步骤可能太昂贵了。帧过滤器不知道它必须迭代多少个帧，并且它可能必须迭代所有的帧。这最终会重复工作，因为`gdb`在打印帧时会执行此迭代。如果过滤器可以将展开帧推迟到帧装饰器执行之后，即在最后一个过滤器执行之后，它应该这样做。有关装饰器的更多信息，请参见第 23.3.2.11 节[帧装饰器 API]，第 432 页。此外，在后面的章节中还有帧装饰器和过滤器的示例。

有关更多信息，请参见第 23.3.2.12 节[编写帧过滤器]，第 435 页。

Python 字典`gdb.frame_filters`包含组成帧过滤器的键/对象对。此字典中的帧过滤器称为全局帧过滤器，在调试所有 inferior 时都可用。这些帧过滤器必须直接向字典注册。除了全局字典之外，还有其他字典通过自动加载（见第 23.3.3 节[Python 自动加载]，第 516 页）加载不同的 inferior。可以找到帧过滤器字典的另外两个区域是：`gdb.Progspace`，它包含一个`frame_filters`字典属性，以及每个`gdb.Objfile`对象，它也包含一个`frame_filters`字典属性。

当从`gdb`执行与帧过滤器兼容的命令时，`gdb`会合并当前加载的全局、`gdb.Progspace`和所有`gdb.Objfile`字典。所有的`gdb.Objfile`字典都将合并，因为可能正在使用多个帧，因此也可能正在使用多个对象文件。然后，`gdb`会修剪其`enabled`属性为`False`的任何帧过滤器。此修剪后的列表将根据每个过滤器中的`priority`属性进行排序。

一旦字典合并、修剪和排序，`gdb`将创建一个迭代器，该迭代器将调用栈中的每个帧包装在一个`FrameDecorator`对象中，并按顺序调用每个过滤器。前一个过滤器的输出将始终是下一个过滤器的输入，依此类推。

帧过滤器有一个必须实现的强制接口，在此定义：
```
[函数]`FrameFilter.filter(iterator)`
    当`gdb`到达该过滤器在优先级列表中的顺序时，它将在帧过滤器上调用此方法。
    例如，如果有四个帧过滤器：
        Name        Priority
        Filter1     5
        Filter2     10
        Filter3     100
        Filter4     1
    帧过滤器的调用顺序为：
        Filter3 -> Filter2 -> Filter1 -> Filter4
    请注意，Filter3 的输出将传递给 Filter2 的输入，依此类推。
    此过滤器方法将传递一个 Python 迭代器。此迭代器包含一系列包装每个`gdb.Frame`的帧装饰器，或者一个包装另一个帧装饰器的帧装饰器。在帧过滤器序列中执行的第一个过滤器将接收一个完全由默认`FrameDecorator`对象组成的迭代器。然而，在每个帧过滤器执行之后，前一个帧过滤器可能已经用它们自己的帧装饰器包装了一些或所有的帧装饰器。由于帧装饰器也必须符合强制接口，因此可以假设这些装饰器以统一的方式行事（见第 23.3.2.11 节[帧装饰器 API]，第 432 页）。
    此方法必须返回一个符合 Python 迭代器协议的对象。迭代器中的每个项必须是一个符合帧装饰器接口的对象。
    如果帧过滤器不希望对该迭代器执行任何操作，它应该保持该迭代器不变。
    此方法不是可选的。如果它不存在，`gdb`将引发并打印错误。

[变量]`FrameFilter.name`
    `name`属性必须是 Python 字符串，其中包含`gdb`显示的过滤器名称（见第 8.6 节[帧过滤器管理]，第 122 页）。此属性可以包含字母或数字的任何组合。应注意确保其唯一性。此属性是强制的。

[变量]`FrameFilter.enabled`
    `enabled`属性必须是 Python 布尔值。此属性向`gdb`指示帧过滤器是否已启用，在执行帧过滤器时应考虑此属性。如果`enabled`为`True`，则在执行本章前面详细介绍的任何回溯命令时，将执行帧过滤器。如果`enabled`为`False`，则不会执行帧过滤器。此属性是强制的。

[变量]`FrameFilter.priority`
    `priority`属性必须是 Python 整数。此属性控制与其他帧过滤器的执行顺序。除了必须是有效的整数之外，对优先级范围没有任何限制。优先级属性越高，帧过滤器相对于其他帧过滤器将越早执行。尽管优先级可以为负，但建议假设零是可以分配给帧过滤器的最低优先级。具有相同优先级的帧过滤器将在该优先级槽中以未排序的顺序执行。此属性是强制的。100 是一个很好的默认优先级。
```
#### 23.3.2.11 装饰框架

框架装饰器是框架过滤器的姊妹对象（见第 23.3.2.10 节[框架过滤器 API]，第 430 页）。框架装饰器由框架过滤器应用，只能与框架过滤器结合使用。

框架装饰器的目的是在执行框架过滤器的命令中自定义每个`gdb.Frame`的打印内容。这个概念称为装饰框架。

框架装饰器使用每个 API 调用中包含的 Python 代码来装饰`gdb.Frame`。这将`gdb.Frame`中包含的实际数据与框架装饰器生成的装饰数据分开。这种抽象对于维护每个`gdb.Frame`中包含的数据的完整性是必要的。

框架装饰器有一个强制接口，如下定义。

`gdb`已经包含一个名为`FrameDecorator`的框架装饰器。它包含大量用于装饰`gdb.Frame`内容的样板代码。建议其他框架装饰器继承并扩展这个对象，并且只重写需要的方法。

`FrameDecorator`在 Python 模块`gdb.FrameDecorator`中定义，因此你的代码可以像这样导入它：

```from gdb.FrameDecorator import FrameDecorator```
```
[函数]`FrameDecorator.elided`（`self`）
    `elided`方法在一个层次结构系统中将框架分组在一起。例如，在一个解释器中，多个低级框架构成解释语言中的单个调用。在这个例子中，框架过滤器将省略低级框架，并向用户呈现一个表示解释语言中调用的单个高级框架。
    `elided`函数必须返回一个可迭代对象，并且这个可迭代对象必须包含被省略的框架，这些框架包装在一个合适的框架装饰器中。如果没有框架被省略，这个函数可以返回一个空的可迭代对象或`None`。在 CLI 回溯中，被省略的框架相对于普通框架是缩进的，或者在`gdb/mi`的情况下，被放置在省略框架的`children`字段中。
    框架过滤器的任务也是从源迭代器中过滤掉被省略的框架。这将避免打印框架两次。

[函数]`FrameDecorator.function`（`self`）
    这个方法返回要打印的框架中的函数名称。
    这个方法必须返回一个描述函数的 Python 字符串，或者`None`。
    如果这个函数返回`None`，`gdb`将不会为这个字段打印任何数据。

[函数]`FrameDecorator.address`（`self`）
    这个方法返回要打印的框架的地址。
    这个方法必须返回一个足够大的 Python 数字整数类型来描述框架的地址，或者`None`。
    如果这个函数返回`None`，`gdb`将不会为这个字段打印任何数据。

[函数]`FrameDecorator.filename`（`self`）
    这个方法返回与这个框架相关联的文件名和路径。
    这个方法必须返回一个包含文件名和支持框架的对象文件路径的 Python 字符串，或者`None`。
    如果这个函数返回`None`，`gdb`将不会为这个字段打印任何数据。

[函数]`FrameDecorator.line`（`self`）：
    这个方法返回与这个框架所寻址的函数内的当前位置相关联的行号。
    这个方法必须返回一个 Python 整数类型，或者`None`。
    如果这个函数返回`None`，`gdb`将不会为这个字段打印任何数据。

[函数]`FrameDecorator.frame_args`（`self`）
    这个方法必须返回一个可迭代对象，或者`None`。返回一个空的可迭代对象或`None`意味着这个框架的框架参数将不会被打印。这个可迭代对象必须包含实现以下两个方法的对象。
    这个对象必须实现一个`symbol`方法，该方法接受一个`self`参数，并且必须返回一个`gdb.Symbol`（见第 23.3.2.30 节[Python 中的符号]，第 484 页），或者一个 Python 字符串。这个对象还必须实现一个`value`方法，该方法接受一个`self`参数，并且必须返回一个`gdb.Value`（见第 23.3.2.4 节[从 inferior 获取值]，第 411 页），一个 Python 值，或者`None`。如果`value`方法返回`None`，并且`argument`方法返回一个`gdb.Symbol`，`gdb`将自动查找并打印`gdb.Symbol`的值。
    一个简短的示例：
        class SymValueWrapper():
            def __init__(self, symbol, value):
                self.sym = symbol
                self.val = value

            def value(self):
                return self.val

            def symbol(self):
                return self.sym

        class SomeFrameDecorator():
           ...
           ...

            def frame_args(self):
                args = []
                try:
                    block = self.inferior_frame.block()
                except:
                    return None
                # 遍历块中的所有符号。只添加作为参数的符号。
                for sym in block:
                    if not sym.is_argument:
                        continue
                    args.append(SymValueWrapper(sym, None))
                # 添加一个示例合成参数。
                args.append(SymValueWrapper("foo", 42))
                return args


[函数]`FrameDecorator.frame_locals`（`self`）
    这个方法必须返回一个可迭代对象或`None`。返回一个空的可迭代对象或`None`意味着这个框架的框架局部变量将不会被打印。
    对象接口、读取框架局部变量的各种策略的描述以及示例与`frame_args`函数中描述的非常相似（见[框架过滤器框架参数函数]，第 433 页）。下面是一个修改后的示例：
        class SomeFrameDecorator():
           ...
           ...
            def frame_locals(self):
                vars = []
                try:
                    block = self.inferior_frame.block()
                except:
                    return None
                # 遍历块中的所有符号。添加所有除了参数的符号。
                for sym in block:
                    if sym.is_argument:
                        continue
                    vars.append(SymValueWrapper(sym, None))
                # 添加一个合成局部变量的示例。
                vars.append(SymValueWrapper("bar", 99))
                return vars

[函数]`FrameDecorator.inferior_frame`（`self`）
    这个方法必须返回这个框架装饰器正在装饰的底层`gdb.Frame`。`gdb`需要底层框架的内部框架信息来确定在打印框架时如何打印某些值。
```
#### 23.3.2.12 编写框架过滤器

一个框架过滤器必须实现三个基本元素：它必须正确实现文档化的接口（见第 23.3.2.10 节[框架过滤器 API]，第 430 页），它必须向`gdb`注册自身，最后，它必须决定是否处理`gdb`提供的数据。在所有情况下，无论它是否处理迭代器，每个框架过滤器都必须返回一个迭代器。一个简单的框架过滤器遵循以下示例中的模式。
```
import gdb
class FrameFilter():
    def __init__(self):
        # Frame filter attribute creation.
        #
        # ’name’ is the name of the filter that GDB will display.
        #
        # ’priority’ is the priority of the filter relative to other
        # filters.
        #
        # ’enabled’ is a boolean that indicates whether this filter is
        # enabled and should be executed.
        
        self.name = "Foo"
        self.priority = 100
        self.enabled = True
        
        # Register this frame filter with the global frame_filters
        # dictionary.
        gdb.frame_filters[self.name] = self
        
    def filter(self, frame_iter):
        # Just return the iterator.
        return frame_iter
```
上述示例中的框架过滤器实现了所有框架过滤器的三个要求。它实现了 API，自我注册，并对迭代器做出决定（在这种情况下，它只是返回未修改的迭代器）。

第一步是属性创建和赋值，如注释所示，过滤器分配了以下属性：`name`、`priority`以及是否应使用`enabled`属性启用过滤器。

第二步是将框架过滤器注册到框架过滤器感兴趣的一个或多个字典中。如注释所示，此过滤器仅将自身注册到全局字典`gdb.frame_filters`中。如前所述，`gdb.frame_filters`是在`gdb`启动时在`gdb`模块中初始化的字典。过滤器注册到哪个字典是一个重要的考虑因素。一般来说，如果一个过滤器特定于一组代码，它应该注册到`objfile`或`progspace`字典中，因为它们特定于当前在`gdb`中加载的程序。全局字典始终存在于`gdb`中，并且永远不会卸载。注册到全局字典的任何过滤器将一直存在，直到`gdb`退出。为了避免可能冲突的过滤器，通常最好将框架过滤器注册到与当前正在讨论的过滤器的使用更紧密对齐的字典中。

有关自动加载 Python 脚本的更多信息，请参阅第 23.3.3 节[Python 自动加载]，第 516 页。

`gdb`对框架过滤器注册采取放手的方法，因此框架过滤器有责任确保注册已发生，并且适当地处理任何异常。特别是，您可能希望处理与 Python 字典键唯一性相关的异常。字典键必须与框架过滤器的`name`属性相同，这是强制性的。当用户管理框架过滤器（见第 8.6 节[框架过滤器管理]，第 122 页）时，`gdb`将显示的名称是`name`属性中包含的名称。

此示例的最后一步是实现`filter`方法。如示例注释所示，我们定义了`filter`方法，并注意到该方法必须接受一个迭代器，并且还必须返回一个迭代器。在这个简单的示例中，框架过滤器不是很有用，因为它只是返回未修改的迭代器。然而，对于已设置`enabled`属性但决定不对任何框架进行操作的框架过滤器，这是一个有效的操作。

在下一个示例中，框架过滤器对所有框架进行操作，并使用框架装饰器对框架执行一些工作。有关框架装饰器接口的更多信息，请参阅第 23.3.2.11 节[框架装饰器 API]，第 432 页。

此示例适用于内联框架。它通过标记为“[内联]”来突出显示内联的框架。通过将框架装饰器应用于所有使用 Python`itertools``imap`方法的框架，该示例将操作推迟到框架装饰器。仅在`gdb`打印回溯时才处理框架装饰器。

这引入了一个新的决策主题：是在过滤步骤执行决策操作，还是在打印步骤执行决策操作。在这个示例的方法中，它在过滤步骤除了将框架装饰器映射到每个框架之外，不执行任何过滤决策。这允许在打印每个框架时执行实际的决策。这是一个重要的考虑因素，在设计框架过滤器时值得深思。框架过滤器应避免的一个问题是尽可能避免展开堆栈。

一些堆栈可以非常深，在某些情况下可以达到数万个。在过滤步骤提前搜索每个框架以确定它是否内联可能太昂贵。框架过滤器不知道它必须迭代多少个框架，并且它必须遍历所有框架。这最终会重复工作，因为`gdb`在打印框架时会执行此迭代。

在这个示例中，决策可以推迟到打印步骤。当每个框架被打印时，框架装饰器可以在`gdb`迭代时依次检查每个框架。从性能角度来看，这是做出的最适当的决策，因为它避免了重复`gdb`无论如何都会进行的工作。此外，如果有许多框架过滤器在过滤期间展开堆栈，它可能会大大延迟回溯的打印，这将导致大量内存使用和不良的用户体验。
```
class InlineFilter():
    def __init__(self):
        self.name = "InlinedFrameFilter"
        self.priority = 100
        self.enabled = True
        gdb.frame_filters[self.name] = self
        
    def filter(self, frame_iter):
        frame_iter = itertools.imap(InlinedFrameDecorator,
                                    frame_iter)
        return frame_iter
```
这个框架过滤器与前面的示例有些相似，不同之处在于`filter`方法将一个名为`InlinedFrameDecorator`的框架装饰器对象应用于迭代器中的每个元素。`imap` Python 方法很轻量级。它不会主动迭代迭代器，而是创建一个新的迭代器，该迭代器包装现有的迭代器。

以下是此示例的框架装饰器。
```
class InlinedFrameDecorator(FrameDecorator):
    def __init__(self, fobj):
        super(InlinedFrameDecorator, self).__init__(fobj)
        
    def function(self):
        frame = self.inferior_frame()
        name = str(frame.name())
        
        if frame.type() == gdb.INLINE_FRAME:
            name = name + " [inlined]"
            
        return name
```
这个框架装饰器仅定义并覆盖了`function`方法。它让`gdb`附带的提供的`FrameDecorator`执行与打印此框架相关的其他工作。

这两个对象的组合从回溯中创建以下输出：
```
#0 0x004004e0 in bar () at inline.c:11
#1 0x00400566 in max [inlined] (b=6, a=12) at inline.c:21
#2 0x00400566 in main () at inline.c:31
```
所以在这个示例的情况下，无论框架是否可能内联，都会将框架装饰器应用于所有框架。当`gdb`迭代由框架过滤器生成的迭代器时，`gdb`执行每个框架装饰器，然后在函数回调中决定要打印什么。使用这样的策略是将框架内容的决策推迟到打印时间的一种方法。

**省略框架**

上述示例可能不适合表示内联框架，可能更倾向于分层方法。如果我们希望分层表示框架，省略的框架装饰器接口可能更可取。

此示例通过`elided`方法解决该问题。此示例相当长，但非常简单。编写一个全面涵盖查找和打印内联框架的所有方法的完整示例超出了本节的范围。然而，此示例说明了作者可能使用的方法。

此示例包括三个部分。
```
class InlineFrameFilter():
    def __init__(self):
        self.name = "InlinedFrameFilter"
        self.priority = 100
        self.enabled = True
        gdb.frame_filters[self.name] = self
        
    def filter(self, frame_iter):
        return ElidingInlineIterator(frame_iter)
```
这个框架过滤器与其他示例非常相似。唯一的区别是，此框架过滤器使用一个名为`ElidingInlineIterator`的自定义迭代器包装提供给它的迭代器（`frame_iter`）。这再次将操作推迟到`gdb`打印回溯时，因为在打印之前不会遍历迭代器。

此示例的迭代器如下。在示例的这一部分中，对回溯的内容做出决策。
```
class ElidingInlineIterator:
    def __init__(self, ii):
    self.input_iterator = ii
    
    def __iter__(self):
    return self
    
    def next(self):
        frame = next(self.input_iterator)
        if frame.inferior_frame().type() != gdb.INLINE_FRAME:
            return frame
        try:
            eliding_frame = next(self.input_iterator)
        except StopIteration:
            return frame
        return ElidingFrameDecorator(eliding_frame, [frame])
```
这个迭代器实现了 Python 迭代器协议。当调用`next`函数（当`gdb`打印每个框架时）时，迭代器检查此框架装饰器`frame`是否包装了一个内联框架。如果不是，它将返回未修改的现有框架装饰器。如果它包装了一个内联框架，它假定内联框架包含在接下来的最旧框架`eliding_frame`中，它获取该框架。然后，它创建并返回一个框架装饰器`ElidingFrameDecorator`，其中包含省略的框架和省略的框架。
```
class ElidingInlineDecorator(FrameDecorator):
    def __init__(self, frame, elided_frames):
        super(ElidingInlineDecorator, self).__init__(frame)
        self.frame = frame
        self.elided_frames = elided_frames
        
    def elided(self):
        return iter(self.elided_frames)
```
这个框架装饰器重写了一个函数，并在`elided`方法中返回内联框架。与之前一样，它让`FrameDecorator`完成打印此框架所涉及的其余工作。这产生以下输出。
```
#0 0x004004e0 in bar () at inline.c:11
#2 0x00400529 in main () at inline.c:25
    #1 0x00400529 in max (b=6, a=12) at inline.c:15
```
在该输出中，内联到`main`中的`max`以分层方式打印。另一种方法是将`function`方法和`elided`方法结合起来，既在内联框架中打印一个标记，又显示层次关系。

#### 23.3.2.13 在 Python 中展开帧

在 gdb 术语中，“展开”是从当前帧找到前一帧（即调用者的帧）的过程。展开器有三种方法。第一种方法检查它是否可以处理给定的帧（“嗅探”它）。对于它可以嗅探的帧，展开器提供两种额外的方法：它可以返回帧的 ID，并且可以从前一帧获取寄存器。正在运行的 gdb 维护一个展开器列表，并依次调用每个展开器的嗅探器，直到找到识别当前帧的展开器。有一个 API 用于注册展开器。

gdb 附带的展开器处理标准帧。然而，混合语言应用程序（例如，运行 Java 虚拟机的应用程序）有时会使用 gdb 展开器无法处理的帧布局。您可以编写 Python 代码来处理此类自定义帧。

您在 Python 中实现一个帧展开器作为一个类，该类具有两个属性`name`和`enabled`，具有明显的含义，以及一个`__call__`方法，该方法检查给定的帧并返回一个描述它的对象（`gdb.UnwindInfo`类的实例）。如果展开器无法识别一个帧，它应该返回`None`。gdb 中用于在 Python 中编写展开器的代码使用此对象在 gdb 核心请求时返回帧的 ID 和前一帧寄存器。

展开器应该尽可能少地工作。一些原本无害的操作可能会导致问题（甚至崩溃，因为此代码尚未充分加固）。例如，从展开器中进行低级调用是不可取的，因为低级调用将重置 gdb 的堆栈展开过程，可能导致重新进入展开。

**展开器输入**

传递给展开器的对象（`gdb.PendingFrame`实例）提供了一种读取帧寄存器的方法：
```
[函数]`PendingFrame.read_register`(寄存器)
    此方法以`gdb.Value`对象的形式返回帧中寄存器的内容。有关寄存器可接受值的描述，请参阅[`Frame.read register`]，第 482 页。如果寄存器未为当前体系结构命名，此方法将引发异常。
    请注意，对于有效的寄存器名称，此方法将始终返回`gdb.Value`。这并不意味着该值将是有效的。例如，您可能请求一个先前的展开器无法展开的寄存器 - 该值将不可用。相反，从此方法返回的`gdb.Value`将是惰性的；也就是说，其基础位将在首次使用之前不会被获取。因此，尝试使用此类值将在使用点导致异常。
    返回的`gdb.Value`的类型取决于寄存器和体系结构。寄存器通常具有标量类型，如`long long`；但也可能有许多其他类型，如指针、指向函数的指针、浮点数或向量类型。
    它还提供了一个工厂方法来创建要返回给 gdb 的`gdb.UnwindInfo`实例：

[函数]`PendingFrame.create_unwind_info`(帧 ID)
    返回一个由给定帧 ID 标识的新`gdb.UnwindInfo`实例。帧 ID 由 gdb 内部用于标识当前线程堆栈中的帧。
    帧 ID 的属性决定在 gdb 中创建何种类型的帧：
        `sp`，`pc`               通过给定的堆栈地址和 PC 标识帧。堆栈地址必须选择为在帧的整个生命周期内是恒定的，因此典型的选择是函数开始时的堆栈指针值 - 在 DWARF 标准中，这将是“调用帧地址”。
                                到目前为止，这是最常见的情况。其他情况是为了完整性而记录的，但仅在特定情况下有用。
        `sp`，`pc`，`special` 通过堆栈地址、PC 和“特殊”地址标识帧。特殊地址用于可以具有不更改堆栈但仍然不同的帧的体系结构，例如 IA-64，它有第二个用于寄存器的堆栈。`sp`和`special`在帧的整个生命周期内都必须是恒定的。
        `sp`                    仅通过堆栈地址标识帧。任何其他具有匹配`sp`的堆栈帧都将被视为与此帧匹配。在 gdb 内部，这称为“通配帧”。您永远不需要这个。
    每个属性值应该是`gdb.Value`的实例或整数。
    gdb.unwinder 模块中提供了一个辅助类，可用于表示帧 ID（请参阅[`gdb.unwinder.FrameId`]，第 442 页）。

[函数]`PendingFrame.architecture()`
    返回此`gdb.PendingFrame`的`gdb.Architecture`（请参阅第 23.3.2.36 节[Python 中的体系结构]，第 497 页）。这表示正在展开的特定帧的体系结构。

[函数]`PendingFrame.level()`
    返回一个整数，此帧的堆栈帧级别。请参阅第 8.1 节[堆栈帧]，第 113 页。

[函数]`PendingFrame.name()`
    返回此挂起帧的函数名称，如果无法获取则返回`None`。

[函数]`PendingFrame.is_valid()`
    如果`gdb.PendingFrame`对象有效，则返回`true`，否则返回`false`。当创建挂起帧的展开器的调用返回时，挂起帧对象将变为无效。
    除了此方法之外，所有`gdb.PendingFrame`方法在调用该方法时如果挂起帧对象无效，将引发异常。

[函数]`PendingFrame.pc()`
    返回挂起帧的恢复地址。

[函数]`PendingFrame.block()`
    返回挂起帧的代码块（请参阅第 23.3.2.29 节[Python 中的块]，第 482 页）。如果该帧没有块 - 例如，如果没有针对所讨论代码的调试信息 - 则会引发`RuntimeError`异常。

[函数]`PendingFrame.function()`
    返回与此挂起帧对应的函数的符号。请参阅第 23.3.2.30 节[Python 中的符号]，第 484 页。

[函数]`PendingFrame.find_sal()`
    返回挂起帧的符号表和行对象（请参阅第 23.3.2.31 节[Python 中的符号表]，第 489 页）。

[函数]`PendingFrame.language()`
    返回此帧的语言，作为字符串，或`None`。
```

**展开器输出：UnwindInfo**

使用上面描述的`PendingFrame.create_unwind_info`方法创建一个`gdb.UnwindInfo`实例。使用以下方法指定在该帧中已保存的调用者寄存器：
```
[函数]`gdb.UnwindInfo.add_saved_register`(寄存器，值)
    寄存器标识寄存器，有关可接受值的描述，请参阅[`Frame.read register`]，第 482 页。值是寄存器值（`gdb.Value`对象）。
```

**gdb.unwinder模块**

gdb 带有一个`gdb.unwinder`模块，其中包含以下类：
```
[类]`gdb.unwinder.Unwinder`
    `Unwinder`类是一个基类，用户创建的展开器可以从中派生，尽管展开器不需要从这个类派生，只要任何用户创建的展开器具有所需的`name`和`enabled`属性即可。
        [函数]`gdb.unwinder.Unwinder.__init__(name)`
            `name`是一个字符串，用于在某些 gdb 命令中引用此展开器（请参阅[管理注册的展开器]，第 443 页）。
        [变量]`gdb.unwinder.name`
            一个只读属性，是一个字符串，此展开器的名称。
        [变量]`gdb.unwinder.enabled`
            一个可修改的属性，包含一个布尔值；当为`True`时，展开器已启用，并将由 gdb 使用。当为`False`时，展开器已被禁用，将不会被使用。

[类]`gdb.unwinder.FrameId`
    这是一个适合在调用`gdb.PendingFrame.create_unwind_info`时用作帧 ID 的类。不需要使用此类，任何具有所需属性（请参阅[`gdb.PendingFrame.create unwind info`]，第 440 页）的类都将被接受，但在大多数情况下，此类将足够。
    `gdb.unwinder.FrameId`具有以下方法：
        [函数]`gdb.unwinder.FrameId.__init__(sp, pc, special = None)`
            `sp`和`pc`参数是必需的，应该是`gdb.Value`对象或整数。
            `special`参数是可选的；如果指定，它应该是`gdb.Value`对象或整数。
    `gdb.unwinder.FrameId`具有以下只读属性：
        [变量]`gdb.unwinder.sp`
            传递给构造函数的`sp`值。
        [变量]`gdb.unwinder.pc`
            传递给构造函数的`pc`值。
        [变量]`gdb.unwinder.special`
            传递给构造函数的特殊值，如果未传递此类值，则为`None`。
```
**注册展开器**

对象文件和程序空间可以注册展开器。此外，您可以全局注册展开器。

`gdb.unwinders`模块提供了一个函数来注册展开器：

```
[函数]`gdb.unwinder.register_unwinder`(轨迹，展开器，替换=False)
    `locus`指定将展开器前置到哪个展开器列表中。它可以是对象文件（请参阅第 23.3.2.27 节[Python 中的对象文件]，第 476 页）、程序空间（请参阅第 23.3.2.26 节[Python 中的程序空间]，第 474 页）或`None`，在这种情况下，展开器将全局注册。新添加的展开器将在来自同一轨迹的任何其他展开器之前被调用。同一轨迹中的两个展开器不能具有相同的名称。如果尝试添加具有已存在名称的展开器，除非`replace`为`True`，否则将引发异常，在这种情况下，旧的展开器将被删除，新的展开器将在其位置注册。
    gdb 首先以任意顺序调用所有对象文件中的展开器，然后是当前程序空间中的展开器，然后是全局注册的展开器，最后是 gdb 内置的展开器。
```

**展开器框架代码**

以下是如何构造用户创建的展开器的示例：

```
from gdb.unwinder import Unwinder, FrameId

class MyUnwinder(Unwinder):
    def __init__(self):
        super().__init___("MyUnwinder_Name")
        
    def __call__(self, pending_frame):
        if not <we recognize frame>:
            return None
            
        # Create a FrameID. Usually the frame is identified by a
        # stack pointer and the function address.
        sp = ... compute a stack address ...
        pc = ... compute function address ...
        unwind_info = pending_frame.create_unwind_info(FrameId(sp, pc))
        
        # Find the values of the registers in the caller’s frame and
        # save them in the result:
        unwind_info.add_saved_register(<register-number>, <register-value>)
        ....
        
        # Return the result:
        return unwind_info

gdb.unwinder.register_unwinder(<locus>, MyUnwinder(), <replace>)
```

**管理注册的展开器**

gdb 定义了 3 个命令来管理注册的展开器。这些是：

```
`info unwinder [ 轨迹 [ 名称正则表达式 ] ]`
    列出所有注册的展开器。参数`locus`和`name-regexp`都是可选的，可用于过滤要列出的展开器。
    `locus`参数应该是`global`、`progspace`或对象文件的名称。仅列出为指定轨迹注册的展开器。
    `name-regexp`是一个用于匹配展开器名称的正则表达式。当尝试匹配包含字符串的展开器名称时，请将`name-regexp`用引号括起来。

`disable unwinder [ 轨迹 [ 名称正则表达式 ] ]`
    `locus`和`name-regexp`的解释与`info unwinder`上面的相同，但不是列出匹配的展开器，而是禁用所有匹配的展开器。每个匹配的展开器的`enabled`字段被设置为`False`。

`enable unwinder [ 轨迹 [ 名称正则表达式 ] ]`
    `locus`和`name-regexp`的解释与`info unwinder`上面的相同，但不是列出匹配的展开器，而是启用所有匹配的展开器。每个匹配的展开器的`enabled`字段被设置为`True`。
```

#### 23.3.2.14 Xmethods In Python

Xmethods 是 C++类现有方法的额外方法或替换方法。

此功能在以下情况下很有用：C++源代码中定义的方法可能会被编译器内联或优化掉，从而在 gdb 中不可用。对于这种情况，可以定义一个 xmethod 来替换 C++源代码中定义的方法。然后，gdb 将调用 xmethod 而不是 C++方法来计算表达式。在使用核心文件进行调试时，也可以使用 xmethods。此外，在调试实时程序时，调用 xmethod 不需要运行 inferior（这可能会潜在地扰乱其状态）。因此，即使 C++方法可用，如果定义了其替换 xmethod，最好使用它。

Python 中的 xmethods 功能可通过 xmethod 匹配器和 xmethod 工作器的概念获得。要实现一个 xmethod，必须为其实现一个匹配器和一个相应的工作器（可以实现多个工作器，每个工作器对应于方法的不同重载实例）。内部地，gdb 调用匹配器的 match 方法来匹配类类型和方法名称。匹配时，match 方法返回一个匹配的工作器对象列表。每个工作器对象通常对应于 xmethod 的一个重载实例。它们实现一个 get_arg_types 方法，该方法返回与 xmethod 需要的参数对应的类型序列。gdb 使用此类型序列执行重载解析并选择一个获胜的 xmethod 工作器。

还会从 gdb 在 C++源代码中找到的方法中选择一个获胜者。接下来，将获胜的 xmethod 工作器和获胜的 C++方法进行比较，以选择一个总体获胜者。如果 xmethod 工作器和 C++方法之间出现平局，则选择 xmethod 工作器作为获胜者。也就是说，如果找到一个获胜的 xmethod 工作器与获胜的 C++方法等效，则将 xmethod 工作器视为 C++方法的替换。gdb 使用总体获胜者来调用该方法。如果获胜的 xmethod 工作器是总体获胜者，则通过工作器对象的`__call__`方法调用相应的 xmethod。

如果要将 xmethod 实现为现有 C++方法的替换，则必须实现一个等效的 xmethod，该 xmethod 具有与 C++方法完全相同的名称，并接受与 C++方法完全相同类型的参数。如果用户即使有该方法的替换 xmethod 可用，仍想调用 C++方法，则可以禁用 xmethod。

有关在 Python 中实现 xmethods 的 API，请参阅第 23.3.2.15 [Xmethod API]节，第 444 页。有关在 Python 中实现 xmethods 的内容，请参阅第 23.3.2.16 [编写 Xmethod]节，第 446 页。

#### 23.3.2.15 Xmethod API

`gdb Python API` 提供了类、接口和函数，用于实现、注册和操作 `xmethods`。请参阅第 23.3.2.14 节 [Python 中的 `Xmethods`]，第 443 页。

一个 `xmethod` 匹配器应该是从 `gdb.xmethod` 模块中定义的 `XMethodMatcher` 派生的类的实例，或者是具有类似接口和属性的对象。`XMethodMatcher` 的实例具有以下属性：

```
[变量] `name`
    匹配器的名称。

[变量] `enabled`
    一个布尔值，表示匹配器是启用还是禁用。

`methods`
    由匹配器管理的命名方法的列表。列表中的每个对象都是 `gdb.xmethod` 模块中定义的 `XMethod` 类的实例，或者是具有以下属性的任何对象：
        `name` 匹配器管理的每个 `xmethod` 的名称，应该是唯一的。
        `enabled` 一个布尔值，表示 `xmethod` 是启用还是禁用。
        
    `XMethod` 类是一个方便类，具有与上述相同的属性，以及以下构造函数：
        [函数] `XMethod.__init__(self, name)`
            构造一个具有名称 `name` 的启用的 `xmethod`。
```

`XMethodMatcher` 类具有以下方法：

```
[函数] `XMethodMatcher.__init__(self, name)`
    构造一个具有名称 `name` 的启用的 `xmethod` 匹配器。`methods` 属性初始化为 `None`。

[函数] `XMethodMatcher.match(self, class type, method name)`
    派生类应该重写此方法。它应该返回与类类型和方法名称匹配的 `xmethod` 工作对象（或 `xmethod` 工作对象的序列）。`class type` 是一个 `gdb.Type` 对象，`method name` 是一个字符串值。如果匹配器在其 `methods` 属性中管理命名方法，那么只应该返回那些在 `methods` 列表中对应的条目已启用的工作对象。
```
    
一个 `xmethod` 工作者应该是从 `gdb.xmethod` 模块中定义的 `XMethodWorker` 派生的类的实例，或者支持以下接口：

```
[函数] `XMethodWorker.get_arg_types(self)`
    此方法返回与 `xmethod` 接受的参数对应的 `gdb.Type` 对象的序列。如果 `xmethod` 不接受任何参数，则可以返回一个空序列或 `None`。如果 `xmethod` 接受单个参数，则可以返回一个对应的 `gdb.Type` 对象。

[函数] `XMethodWorker.get_result_type(self, *args)`
    此方法返回一个表示调用此 `xmethod` 的结果类型的 `gdb.Type` 对象。`args` 参数是将传递给此工作者的 `__call__` 方法的相同参数元组。

[函数] `XMethodWorker.__call__(self, *args)`
    这是执行 `xmethod` 工作的方法。`args` 参数是 `xmethod` 的参数元组。此元组中的每个元素都是一个 `gdb.Value` 对象。第一个元素始终是 `this` 指针值。
```

为了让 `gdb` 查找 `xmethods`，应该使用在 `gdb.xmethod` 模块中定义的以下函数注册 `xmethod` 匹配器：

```
[函数] `register_xmethod_matcher(locus, matcher, replace=False)`
    将匹配器注册到 `locus`，如果 `replace` 为 `True`，则替换与 `matcher` 具有相同名称的现有匹配器。`locus` 可以是一个 `gdb.Objfile` 对象（请参阅第 23.3.2.27 节 [Python 中的 `Objfiles`]，第 476 页），或者是一个 `gdb.Progspace` 对象（请参阅第 23.3.2.26 节 [Python 中的 `Progspaces`]，第 474 页），或者为 `None`。如果为 `None`，则匹配器将全局注册。
```

#### 23.3.2.16 编写 X 方法

在 Python 中实现 x 方法需要实现 x 方法匹配器和 x 方法工作者（见第 23.3.2.14 节[Python 中的 X 方法]，第 443 页）。考虑以下 C++类：

```cpp
class MyClass
{
    public:
        MyClass (int a) : a_(a) { }
        
        int geta (void) { return a_; }
        int operator+ (int b);
    private:
        int a_;
};

int
MyClass::operator+ (int b)
{
    return a_ + b;
}
```

让我们为类`MyClass`定义两个 x 方法，一个替换`geta`方法，另一个添加一个接受`MyClass`参数的重载`operator+`（上面的 C++代码已经有一个接受`int`参数的重载`operator+`）。x 方法匹配器可以定义如下：

```python
class MyClass_geta(gdb.xmethod.XMethod):
    def __init__(self):
        gdb.xmethod.XMethod.__init__(self, ’geta’)

    def get_worker(self, method_name):
        if method_name == ’geta’:
            return MyClassWorker_geta()

class MyClass_sum(gdb.xmethod.XMethod):
    def __init__(self):
        gdb.xmethod.XMethod.__init__(self, ’sum’)

    def get_worker(self, method_name):
        if method_name == ’operator+’:
            return MyClassWorker_plus()

class MyClassMatcher(gdb.xmethod.XMethodMatcher):
    def __init__(self):
        gdb.xmethod.XMethodMatcher.__init__(self, ’MyClassMatcher’)
        # List of methods ’managed’ by this matcher
        self.methods = [MyClass_geta(), MyClass_sum()]

    def match(self, class_type, method_name):
        if class_type.tag!= ’MyClass’:
            return None
        workers = []
        for method in self.methods:
            if method.enabled:
                worker = method.get_worker(method_name)
                if worker:
                    workers.append(worker)

        return workers
```

注意，`MyClassMatcher`的`match`方法为`geta`方法返回一个类型为`MyClassWorker_geta`的工作者对象，为`operator+`方法返回一个类型为`MyClassWorker_plus`的工作者对象。这是通过从`gdb.xmethod.XMethod`派生的辅助类间接完成的。不需要在匹配器中使用`methods`属性，因为它是可选的。然而，如果一个匹配器管理多个 x 方法，在匹配器的`methods`属性中列出 x 方法是一个好习惯。这样，就可以通过`enable/disable`命令方便地启用和禁用单个 x 方法。

还要注意，只有当匹配器的`methods`属性中的相应条目被启用时，才会返回一个工作者对象。

上述匹配器设置返回的工作者类的实现如下：

```python
class MyClassWorker_geta(gdb.xmethod.XMethodWorker):
    def get_arg_types(self):
        return None

    def get_result_type(self, obj):
        return gdb.lookup_type(’int’)

    def __call__(self, obj):
        return obj[’a_’]

class MyClassWorker_plus(gdb.xmethod.XMethodWorker):
    def get_arg_types(self):
        return gdb.lookup_type(’MyClass’)

    def get_result_type(self, obj):
        return gdb.lookup_type(’int’)

    def __call__(self, obj, other):
        return obj[’a_’] + other[’a_’]
```

为了让 gdb 实际查找 x 方法，必须向其注册。上面定义的匹配器全局注册到 gdb 如下：

```python
gdb.xmethod.register_xmethod_matcher(None, MyClassMatcher())
```

如果在 C++代码中如下初始化类型为`MyClass`的对象`obj`：

```cpp
MyClass obj(5);
```

那么，在将定义 x 方法匹配器和工作者的 Python 脚本加载到 gdb 后，调用`geta`方法或在`obj`上使用`operator+`将调用上面定义的 x 方法：

```
(gdb) p obj.geta()
$1 = 5

(gdb) p obj + obj
$2 = 10
```

考虑另一个带有 C++模板类的示例：

```cpp
template <class T>
class MyTemplate
{
public:
    MyTemplate () : dsize_(10), data_ (new T [10]) { }
    ~MyTemplate () { delete [] data_; }

    int footprint (void)
    {
        return sizeof (T) * dsize_ + sizeof (MyTemplate<T>);
    }

private:
    int dsize_;
    T *data_;
};
```

让我们为上述类实现一个 x 方法，作为`footprint`方法的替代品。x 方法工作者和 x 方法匹配器的完整代码列表如下：

```python
class MyTemplateWorker_footprint(gdb.xmethod.XMethodWorker):
    def __init__(self, class_type):
        self.class_type = class_type

    def get_arg_types(self):
        return None

    def get_result_type(self):
        return gdb.lookup_type(’int’)

    def __call__(self, obj):
        return (self.class_type.sizeof +
                obj[’dsize_’] *
                self.class_type.template_argument(0).sizeof)

class MyTemplateMatcher_footprint(gdb.xmethod.XMethodMatcher):
    def __init__(self):
        gdb.xmethod.XMethodMatcher.__init__(self, ’MyTemplateMatcher’)

    def match(self, class_type, method_name):
        if (re.match(’MyTemplate<[ \t\n]*[_a-zA-Z][ _a-zA-Z0-9]*>’,
                     class_type.tag) and
            method_name == ’footprint’):
            return MyTemplateWorker_footprint(class_type)
```

注意，在这个示例中，我们没有使用匹配器的`methods`属性，因为匹配器只管理一个 x 方法。用户可以通过启用/禁用匹配器本身来启用/禁用这个 x 方法。

#### 23.3.2.17 Inferiors In Python

在 gdb 下运行的程序被称为“从属程序”（见第 4.9 节 [从属程序连接和程序]，第 42 页）。Python 脚本可以通过`gdb.Inferior`类的对象访问和操作由 gdb 控制的从属程序的信息。

`gdb`模块中提供了以下与从属程序相关的函数：

```
[Function] gdb.inferiors()
    返回一个包含所有从属程序对象的元组。

[Function] gdb.selected_inferior()
    返回一个表示当前从属程序的对象。
```

一个`gdb.Inferior`对象具有以下属性：

```
[Variable] Inferior.num
    由 gdb 分配的从属程序的 ID。你可以使用此 ID 在从属程序特定的情况下设置 Python 断点，例如（见 [断点.inferior 属性]，第 494 页）。

[Variable] Inferior.connection
    此从属程序的`gdb.TargetConnection`（见第 23.3.2.38 节 [Python 中的连接]，第 499 页），如果此从属程序没有连接，则为`None`。

[Variable] Inferior.connection_num
    由 gdb 分配的从属程序连接的 ID，如果从属程序未连接到目标，则为`None`。见第 4.9 节 [从属程序连接和程序]，第 42 页。在`gdb.Inferior.connection`不为`None`的情况下，这等效于`gdb.Inferior.connection.num`。

[Variable] Inferior.pid
    由底层操作系统分配的从属程序的进程 ID。

[Variable] Inferior.was_attached
    一个布尔值，表示从属程序是使用“attach”创建的，还是由 gdb 自身启动的。

[Variable] Inferior.main_name
    一个字符串，包含此从属程序“主”函数的名称（如果可以确定）。如果主函数的名称未知，则为`None`。

[Variable] Inferior.progspace
    从属程序的程序空间。见第 23.3.2.26 节 [Python 中的程序空间]，第 474 页。

[Variable] Inferior.arguments
    从属程序的命令行参数（如果已知）。这对应于`set args`和`show args`命令。见第 4.3 节 [参数]，第 38 页。
    访问时，该值是一个包含所有参数的字符串。内容按照传递给 shell 时的方式进行引用。如果没有参数，则值为`None`。
    可以将一个字符串或字符串序列分配给此属性。当分配一个字符串时，假定它具有 shell 所需的任何引用；当分配一个序列时，引用由 gdb 应用。
```

一个`gdb.Inferior`对象具有以下方法：

```
[Function] Inferior.is_valid()
    如果`gdb.Inferior`对象有效，则返回`True`，否则返回`False`。如果从属程序在 gdb 中不再存在，`gdb.Inferior`对象将变为无效。在调用该方法时，如果对象无效，所有其他`gdb.Inferior`方法将抛出异常。

[Function] Inferior.threads()
    此方法返回一个包含在调用时有效的所有线程的元组。如果没有有效的线程，该方法将返回一个空元组。

[Function] Inferior.architecture()
    返回此从属程序的`gdb.Architecture`（见第 23.3.2.36 节 [Python 中的体系结构]，第 497 页）。这表示从属程序的整体体系结构。
    某些平台在单个地址空间中可以有多个体系结构，因此这可能与特定帧的体系结构不匹配（见第 23.3.2.28 节 [Python 中的帧]，第 479 页）。

[Function] Inferior.read_memory(address, length)
    从从属程序中读取从`address`开始的`length`个可寻址内存单元。返回一个`memoryview`对象，其行为类似于数组或字符串。它可以被修改并传递给`Inferior.write_memory`函数。

[Function] Inferior.write_memory(address, buffer [, length])
    将`buffer`的内容写入从属程序中从`address`开始的位置。`buffer`参数必须是支持缓冲区协议的 Python 对象，即字符串、数组或`Inferior.read_memory`返回的对象。如果提供了`length`，则确定要写入的`buffer`中的可寻址内存单元的数量。

[Function] Inferior.search_memory(address, length, pattern)
    使用在`pattern`中提供的搜索模式，在从`address`开始的从属程序内存区域中进行搜索。`pattern`参数必须是支持缓冲区协议的 Python 对象，即字符串、数组或`gdb.read_memory`返回的对象。返回一个 Python Long 类型的对象，包含找到模式的地址，如果找不到模式，则为`None`。

[Function] Inferior.thread_from_handle(handle)
    返回与`handle`对应的线程对象，`handle`是线程库特定的数据结构，例如 pthreads 库实现中的`pthread_t`。
    函数`Inferior.thread_from_thread_handle`提供相同的功能，但不推荐使用`Inferior.thread_from_thread_handle`。
```

可以通过使用以下方法从 Python 更改传递给从属程序的环境。这些方法仅在从属程序启动时生效 - 它们不会影响已经在执行的从属程序。

```
[Function] Inferior.clear_env()
    清除将传递给此从属程序的当前环境变量。

[Function] Inferior.set_env(name, value)
    将环境变量`name`设置为指定的值。两个参数都必须是字符串。

[Function] Inferior.unset_env(name)
    取消设置环境变量`name`。`name`必须是字符串。
```

可以以通常的 Python 方式向`gdb.Inferior`对象添加任意属性。例如，如果需要对从属程序进行一些额外的记录保存，这很有用。

在为新属性选择名称时，避免以小写字母开头；gdb 添加的未来属性将以小写字母开头。

此外，避免以两个下划线字符开头的属性名称，因为这些可能与 Python 内置属性名称冲突。

在这个虚构的示例中，我们记录了从属程序最后停止的时间：

```gdb
(gdb) python
import datetime

def thread_stopped(event):
    if event.inferior_thread is not None:
        thread = event.inferior_thread
    else:
        thread = gdb.selected_thread()
    inferior = thread.inferior
    inferior._last_stop_time = datetime.datetime.today()

gdb.events.stop.connect(thread_stopped)
(gdb) file /tmp/hello
Reading symbols from /tmp/hello...
(gdb) start
Temporary breakpoint 1 at 0x401198: file /tmp/hello.c, line 18.
Starting program: /tmp/hello

Temporary breakpoint 1, main () at /tmp/hello.c:18
18 printf ("Hello World\n");
(gdb) python print(gdb.selected_inferior()._last_stop_time)
2024-01-04 14:48:41.347036

```
#### 23.3.2.18 Python 中的事件

gdb 提供了一个通用的事件机制，以便 Python 代码能够收到各种状态变化的通知，特别是下级进程中的变化。

事件只是一个描述某些状态变化的对象。对象的类型及其属性将根据变化的细节而有所不同。下面描述了所有现有的事件。

为了接收事件通知，您必须使用事件注册表注册事件处理程序。事件注册表是 `gdb.events` 模块中的一个对象，用于分发特定的事件。注册表提供了注册和注销事件处理程序的方法：

```
`[Function] EventRegistry.connect (object)`
    将给定的可调用对象添加到注册表中。当与该注册表对应的事件发生时，将调用此对象。

`EventRegistry.disconnect (object)`
    从注册表中删除给定的对象。一旦删除，该对象将不再接收事件通知。
```

以下是一个示例：

```python
def exit_handler(event):
    print("event type: exit")
    if hasattr(event, 'exit_code'):
        print("exit code: %d" % (event.exit_code))
    else:
        print("exit code not available")
gdb.events.exited.connect(exit_handler)
```

在上述示例中，我们将我们的处理程序 `exit_handler` 连接到注册表 `events.exited` 。一旦连接，当下级进程退出时，将调用 `exit_handler` 。此示例中的参数 `event` 是 `gdb.ExitedEvent` 类型。正如您在示例中看到的，`ExitedEvent` 对象具有一个指示下级进程退出代码的属性。

当 gdb 在非停止模式下运行时，某些事件可能是特定于线程的。在 Python 中表示时，这些事件都扩展了 `gdb.ThreadEvent` 。此事件是一个基类，永远不会直接发出；相反，由这个或其他模块发出的事件可能会扩展此事件。这些事件的示例包括 `gdb.BreakpointEvent` 和 `gdb.ContinueEvent` 。`gdb.ThreadEvent` 具有以下属性：

```
`[Variable] ThreadEvent.inferior_thread`
    在非停止模式下，此属性将设置为参与发出事件的特定线程。否则，它将设置为 `None` 。
```

以下是可用的事件注册表及其发出的事件的详细信息列表：

```
`events.cont`
    发出 `gdb.ContinueEvent`，它扩展了 `gdb.ThreadEvent` 。此事件表示下级进程在停止后继续。有关继承的属性，请参考上面的 `gdb.ThreadEvent` 。

`events.exited`
    发出 `events.ExitedEvent`，表示下级进程已退出。
    `events.ExitedEvent` 具有两个属性：
        
        `[Variable] ExitedEvent.exit_code`
            一个整数，表示下级进程返回的退出代码（如果可用）。（如果例如 gdb 从下级进程分离，则退出代码可能不可用。）如果退出代码不可用，则该属性不存在。
        `[Variable] ExitedEvent.inferior`
            对触发退出事件的下级进程的引用。

`events.stop`
    发出 `gdb.StopEvent`，它扩展了 `gdb.ThreadEvent` 。
    表示下级进程已停止。此注册表发出的所有事件都扩展了 `gdb.StopEvent` 。作为 `gdb.ThreadEvent` 的子类，在 gdb 以非停止模式运行时，`gdb.StopEvent` 将指示停止的线程。有关更多详细信息，请参考上面的 `gdb.ThreadEvent` 。
    `gdb.StopEvent` 具有以下附加属性：
        
        `[Variable] StopEvent.details`
            一个包含与停止相关的任何详细信息的字典。确切的键和值取决于停止的类型，但与相应的 MI 输出相同（请参阅第 27.7.3 节[GDB/MI 异步记录]，第 600 页）。
            使用字典（而不是直接向事件对象添加属性），以便可以不变地使用 MI 键。
            当 `StopEvent` 是由 `finish` 命令导致时，如果可用，它还将包含函数的返回值。这将是 `details` 字典中的一个名为 `'return-value'` 的条目。此条目的值将是一个 `gdb.Value` 对象。
        
        发出 `gdb.SignalEvent`，它扩展了 `gdb.StopEvent` 。
        此事件表示下级进程或其一个线程已收到信号。
        `gdb.SignalEvent` 具有以下属性：
        
        `[Variable] SignalEvent.stop_signal`
            一个字符串，表示下级进程收到的信号。通过在 gdb 命令提示符中运行 `info signals` 命令可以获取可能的信号值列表。
        
        还发出 `gdb.BreakpointEvent`，它扩展了 `gdb.StopEvent` 。
        `gdb.BreakpointEvent` 事件表示一个或多个断点已命中，并具有以下属性：

        `[Variable] BreakpointEvent.breakpoints`
            一个包含对所有命中的断点（类型为 `gdb.Breakpoint` ）的引用的序列。有关 `gdb.Breakpoint` 对象的详细信息，请参阅第 23.3.2.33 节[Python 中的断点]，第 491 页。

        `[Variable] BreakpointEvent.breakpoint`
            对第一个命中的断点的引用。为了向后兼容，维护了此属性，但现在已弃用，建议使用 `gdb.BreakpointEvent.breakpoints` 属性。

events.new_objfile
    发出 `gdb.NewObjFileEvent`，表示 gdb 已加载新的对象文件。`gdb.NewObjFileEvent` 有一个属性：
    `[Variable] NewObjFileEvent.new_objfile`
        对已加载的对象文件（`gdb.Objfile`）的引用。有关 `gdb.Objfile` 对象的详细信息，请参阅第 23.3.2.27 节[Python 中的对象文件]，第 476 页。

events.free_objfile
    发出 `gdb.FreeObjFileEvent`，表示即将从 gdb 中删除一个对象文件。发生这种情况的一个原因是当下级进程调用 `dlclose` 时。`gdb.FreeObjFileEvent` 有一个属性：
    `[Variable] FreeObjFileEvent.objfile`
        对将要卸载的对象文件（`gdb.Objfile`）的引用。有关 `gdb.Objfile` 对象的详细信息，请参阅第 23.3.2.27 节[Python 中的对象文件]，第 476 页。

events.clear_objfiles
    发出 `gdb.ClearObjFilesEvent`，表示程序空间的对象文件列表已重置。`gdb.ClearObjFilesEvent` 有一个属性：
    `[Variable] ClearObjFilesEvent.progspace`
        对其对象文件列表已被清除的程序空间（`gdb.Progspace`）的引用。请参阅第 23.3.2.26 节[Python 中的程序空间]，第 474 页。

events.inferior_call
    在 gdb 调用下级进程中的函数之前和之后发出事件。在下级调用之前，发出 `gdb.InferiorCallPreEvent` 类型的事件，在下级调用之后，发出 `gdb.InferiorCallPostEvent` 类型的事件。
    `gdb.InferiorCallPreEvent`
        表示即将在下级调用一个函数。
        `[Variable] InferiorCallPreEvent.ptid`
            将运行调用的线程。
        `[Variable] InferiorCallPreEvent.address`
            要调用的函数的位置。
    `gdb.InferiorCallPostEvent`
        表示下级中的一个函数刚刚被调用。
        `[Variable] InferiorCallPostEvent.ptid`
            运行调用的线程。
        `[Variable] InferiorCallPostEvent.address`
            被调用的函数的位置。

events.memory_changed
    发出 `gdb.MemoryChangedEvent`，表示下级的内存已被 gdb 用户修改，例如通过诸如 `set *addr = value` 的命令。该事件具有以下属性：
    `[Variable] MemoryChangedEvent.address`
        更改区域的起始地址。
    `[Variable] MemoryChangedEvent.length`
        更改区域的字节长度。

events.register_changed
    发出 `gdb.RegisterChangedEvent`，表示下级中的一个寄存器已被 gdb 用户修改。
    `[Variable] RegisterChangedEvent.frame`
        表示修改寄存器的帧的 `gdb.Frame` 对象。
    `[Variable] RegisterChangedEvent.regnum`
        表示哪个寄存器被修改。

events.breakpoint_created
    当创建新断点时发出。传递的参数是新的 `gdb.Breakpoint` 对象。

events.breakpoint_modified
    当断点以某种方式被修改时发出。传递的参数是新的 `gdb.Breakpoint` 对象。

events.breakpoint_deleted
    当断点被删除时发出。传递的参数是 `gdb.Breakpoint` 对象。当发出此事件时，`gdb.Breakpoint` 对象已经处于无效状态；即，`is_valid` 方法将返回 `False` 。

events.before_prompt
    此事件不携带有效负载。每次 gdb 向用户显示提示符时都会发出。

events.new_inferior
    当创建新的下级进程时发出。请注意，下级进程不一定正在运行；实际上，它甚至可能没有关联的可执行文件。该事件是 `gdb.NewInferiorEvent` 类型。它有一个属性：
    `[Variable] NewInferiorEvent.inferior`
        新的下级进程，一个 `gdb.Inferior` 对象。

events.inferior_deleted
    当下级进程被删除时发出。请注意，这与进程退出不同；当通过例如 `remove-inferiors` 命令删除下级进程本身时会收到通知。
    该事件是 `gdb.InferiorDeletedEvent` 类型。它有一个属性：
    `[Variable] InferiorDeletedEvent.inferior`
        正在被删除的下级进程，一个 `gdb.Inferior` 对象。

events.new_thread
    当 gdb 注意到新线程时发出。该事件是 `gdb.NewThreadEvent` 类型，它扩展了 `gdb.ThreadEvent` 。它有一个属性：
    `NewThreadEvent.inferior_thread`
        新线程。

events.thread_exited
    当 gdb 注意到线程已退出时发出。该事件是 `gdb.ThreadExitedEvent` 类型，它扩展了 `gdb.ThreadEvent` 。它有一个属性：
    `[Variable] ThreadExitedEvent.inferior_thread`
        退出的线程。

events.gdb_exiting
    当 gdb 退出时发出。如果 gdb 由于内部错误或意外信号退出，则不会发出此事件。该事件是 `gdb.GdbExitingEvent` 类型，它有一个属性：
    `[Variable] GdbExitingEvent.exit_code`
        一个整数，gdb 将返回的退出代码的值。

events.connection_removed
    当 gdb 删除连接时发出（请参阅第 23.3.2.38 节[Python 中的连接]，第 499 页）。该事件是 `gdb.ConnectionEvent` 类型。它有一个只读属性：
    `[Variable] ConnectionEvent.connection`
        正在被删除的 `gdb.TargetConnection` 。

events.executable_changed
    发出 `gdb.ExecutableChangedEvent`，表示 `gdb.Progspace.executable_filename` 已更改。
    当 `gdb.Progspace.executable_filename` 的值更改为不同的文件，或者由 `gdb.Progspace.executable_filename` 命名的可执行文件在磁盘上已更改，并且 gdb 已重新加载它时，会发出此事件。
    `[Variable] ExecutableChangedEvent.progspace`
        当前可执行文件已更改的 `gdb.Progspace` 。
        更新的可执行文件的文件名将在 `gdb.Progspace.executable_filename` 中可见（请参阅第 23.3.2.26 节[Python 中的程序空间]，第 474 页）。
    `[Variable] ExecutableChangedEvent.reload`
        如果 `gdb.Progspace.executable_filename` 的值没有更改，但它所命名的文件在磁盘上已更改，并且 gdb 重新加载了它，则此属性将为 `True` 。
        当此属性为 `False` 时，`gdb.Progspace.executable_filename` 中的值已更改为命名不同的文件。
    请记住，gdb 分别跟踪可执行文件和符号文件，它们分别显示为 `gdb.Progspace.executable_filename` 和 `gdb.Progspace.filename` 。使用 `file` 命令时，gdb 会更新这两个字段，但首先更新可执行文件，因此当发出此事件时，可执行文件名将已更改，但符号文件名可能仍保持其先前的值。

events.new_progspace
    当 gdb 添加新的程序空间时发出（请参阅第 23.3.2.26 节[Python 中的程序空间]，第 474 页）。该事件是 `gdb.NewProgspaceEvent` 类型，并且有一个只读属性：
    `[Variable] NewProgspaceEvent.progspace`
        添加到 gdb 的 `gdb.Progspace` 。
    对于第一个程序空间（分配给第一个下级进程），不会发出 `NewProgspaceEvent` 。这个第一个程序空间是在加载任何 Python 脚本之前在 gdb 内部创建的。

events.free_progspace
    当 gdb 删除程序空间时发出（请参阅第 23.3.2.26 节[Python 中的程序空间]，第 474 页），例如作为 `remove-inferiors` 命令的结果（请参阅[remove-inferiors]，第 45 页）。该事件是 `gdb.FreeProgspaceEvent` 类型，并且有一个只读属性：
    `[Variable] FreeProgspaceEvent.progspace`
        即将从 gdb 中删除的 `gdb.Progspace` 。

events.tui_enabled
    当 TUI 启用或禁用时发出。该事件是 `gdb.TuiEnabledEvent` 类型，它有一个只读属性：
    `[Variable] TuiStatusEvent.enabled`
        如果 TUI 刚刚启用，此为 `True` ；否则为 `False` 。
```

#### 23.3.2.19 线程在Python中

Python 脚本可以通过`gdb.InferiorThread`类的对象访问关于由`gdb`控制的低级线程的信息，并对其进行操作。

在`gdb`模块中提供了以下与线程相关的函数：

```
[Function] gdb.selected_thread()
    此函数返回所选线程的线程对象。如果没有选定的线程，此函数将返回`None`。
```

要获取低级线程的线程列表，请使用`Inferior.threads()`方法。请参阅第 23.3.2.17 节[Python 中的 Inferiors]，第 449 页。

一个`gdb.InferiorThread`对象具有以下属性：

```
[Variable] InferiorThread.name
    线程的名称。如果用户使用线程名称指定了一个名称，那么此属性将返回该名称。否则，如果有操作系统提供的名称可用，那么它将被返回。否则，此属性将返回`None`。
    此属性可以被赋值。新值必须是一个字符串对象，用于设置新名称，或者为`None`，用于删除任何用户指定的线程名称。

[Variable] InferiorThread.num
    由`GDB`分配的线程的低级编号。

[Variable] InferiorThread.global_num
    由`GDB`分配的线程的全局 ID。您可以使用此属性来使 Python 断点特定于线程，例如（请参阅[“The Breakpoint.thread attribute”]，第 494 页）。

[Variable] InferiorThread.ptid
    由操作系统分配的线程 ID。此属性是一个包含三个整数的元组。第一个是进程 ID（PID）；第二个是轻量级进程 ID（LWPID），第三个是线程 ID（TID）。LWPID 或 TID 可能为 0，这表示操作系统不使用该标识符。

[Variable] InferiorThread.ptid_string
    此只读属性包含一个表示`InferiorThread.ptid`的字符串。这是`gdb`在`info threads`输出的“Target Id”列中使用的字符串（请参阅[“info threads”]，第 48 页）。

[Variable] InferiorThread.inferior
    此线程所属的低级对象。此属性表示为一个`gdb.Inferior`对象。此属性不可写。

[Variable] InferiorThread.details
    一个包含目标特定线程状态信息的字符串。此字符串的格式因目标而异。如果此线程没有其他状态信息，那么此属性将包含`None`。
    例如，在 gnu/Linux 系统上，一个正在退出的线程将返回字符串“Exiting”。对于远程目标，如果目标支持，将通过“qThreadExtraInfo”远程数据包获取详细信息字符串（请参阅[“qThreadExtraInfo”]，第 811 页）。
    `gdb`在`info threads`输出的“Target Id”列中显示详细信息字符串（请参阅[“info threads”]，第 48 页）。
    一个`gdb.InferiorThread`对象具有以下方法：

[Function] InferiorThread.is_valid()
    如果`gdb.InferiorThread`对象有效，则返回`True`，否则返回`False`。当线程退出或线程所属的低级对象被删除时，`gdb.InferiorThread`对象将变为无效。如果在调用方法时它无效，所有其他`gdb.InferiorThread`方法都将抛出异常。

[Function] InferiorThread.switch()
    此函数将`gdb`的当前选定线程更改为由该对象表示的线程。

[Function] InferiorThread.is_stopped()
    返回一个布尔值，表示线程是否已停止。

[Function] InferiorThread.is_running()
    返回一个布尔值，表示线程是否正在运行。

[Function] InferiorThread.is_exited()
    返回一个布尔值，表示线程是否已退出。

[Function] InferiorThread.handle()
    返回线程对象的句柄，以 Python 字节对象表示。可以通过`gdb.Value(bufobj, type)`构造句柄的`gdb.Value`表示，其中`bufobj`是句柄的 Python 字节表示，`type`是句柄类型的`gdb.Type`。
```

可以以通常的 Python 方式向`gdb.InferiorThread`对象添加任意属性。例如，如果需要对线程进行一些额外的记录保存，这将非常有用。

有关为新属性选择合适名称的指导，请参阅[选择属性名称]，第 451 页。

在这个虚构的示例中，我们记录了线程上次停止的时间：

```gdb
(gdb) python
import datetime

def thread_stopped(event):
    if event.inferior_thread is not None:
        thread = event.inferior_thread
    else:
        thread = gdb.selected_thread()
    thread._last_stop_time = datetime.datetime.today()

gdb.events.stop.connect(thread_stopped)
(gdb) file /tmp/hello
Reading symbols from /tmp/hello...
(gdb) start
Temporary breakpoint 1 at 0x401198: file /tmp/hello.c, line 18.
Starting program: /tmp/hello

Temporary breakpoint 1, main () at /tmp/hello.c:18
18 printf ("Hello World\n");
(gdb) python print(gdb.selected_thread()._last_stop_time)
2024-01-04 14:48:41.347036
```

#### 23.3.2.20 Python 中的录制

在`gdb`模块中，提供了以下与录制相关的函数（见第 7 章[处理记录和回放]，第 103 页）：

```
[函数]`gdb.start_recording([方法], [格式])`
    使用给定的方法和格式开始录制。如果未指定格式，则使用录制方法的默认格式。如果未指定方法，则使用默认方法。成功时返回一个`gdb.Record`对象。失败时抛出异常。
    以下字符串可以作为方法传递：
        "full"
        "btrace"：格式的可能值："pt"、"bts"或留空为默认格式。

[函数]`gdb.current_recording()`
    访问当前正在运行的录制。成功时返回一个`gdb.Record`对象。如果当前没有活动的录制，则返回`None`。

[函数]`gdb.stop_recording()`
    停止当前的录制。如果当前没有活动的录制，则抛出异常。此调用后，所有的记录对象都将无效。
```

一个`gdb.Record`对象具有以下属性：

```
[变量]`Record.method`
    一个字符串，包含当前的录制方法，例如"full"或"btrace"。

[变量]`Record.format`
    一个字符串，包含当前的录制格式，例如"bt"、"pts"或`None`。

[变量]`Record.begin`
    一个方法特定的指令对象，表示此记录中的第一条指令。

[变量]`Record.end`
    一个方法特定的指令对象，表示当前的指令，但实际上不是记录的一部分。

[变量]`Record.replay_position`
    表示当前回放位置的指令。如果没有活动的回放，则为`None`。

[变量]`Record.instruction_history`
    包含所有记录的指令的列表。

[变量]`Record.function_call_history`
    包含所有记录的函数调用段的列表。
```

一个`gdb.Record`对象具有以下方法：

```
[函数]`Record.goto(instruction)`
    将回放位置移动到给定的指令。

[函数]`Record.clear()`
    清除当前录制的跟踪数据。这将强制对后续命令重新解码跟踪。
```
    
记录方法特定的指令对象继承自的通用`gdb.Instruction`类，具有以下属性：

```
[变量]`Instruction.pc`
    一个整数，表示此指令的地址。

[变量]`Instruction.data`
    一个`memoryview`对象，包含原始指令数据。

[变量]`Instruction.decoded`
    一个人类可读的字符串，包含反汇编的指令。

[变量]`Instruction.size`
    指令的字节大小。
```

此外，`gdb.RecordInstruction`具有以下属性：

```
[变量]`RecordInstruction.number`
    一个整数，用于标识此指令。`number`与在记录的指令历史中看到的数字相对应（见第 7 章[处理记录和回放]，第 103 页）。

[变量]`RecordInstruction.sal`
    一个`gdb.Symtab_and_line`对象，表示与此指令相关的符号表和行。如果没有可用的调试信息，则可能为`None`。

[变量]`RecordInstruction.is_speculative`
    一个布尔值，表示该指令是否是推测执行的。
```
    
如果在录制或解码记录期间发生错误，此错误将由指令列表中的`gdb.RecordGap`对象表示。它具有以下属性：

```
[变量]`RecordGap.number`
    一个整数，用于标识此间隙。`number`与在记录的指令历史中看到的数字相对应（见第 7 章[处理记录和回放]，第 103 页）。

[变量]`RecordGap.error_code`
    错误原因的数字表示。该值特定于当前的录制方法。

[变量]`RecordGap.error_string`
    一个人类可读的字符串，包含间隙的原因。
```
    
一些`gdb`功能将辅助信息写入执行历史。此信息由指令列表中的`gdb.RecordAuxiliary`对象表示。它具有以下属性：

```
[变量]`RecordAuxiliary.number`
    一个整数，用于标识此辅助信息。`number`与在记录的指令历史中看到的数字相对应（见第 7 章[处理记录和回放]，第 103 页）。

[变量]`RecordAuxiliary.data`
    辅助数据的字符串表示。
```

一个`gdb.RecordFunctionSegment`对象具有以下属性：

```
[变量]`RecordFunctionSegment.number`
    一个整数，用于标识此函数段。`number`与在记录的函数调用历史中看到的数字相对应（见第 7 章[处理记录和回放]，第 103 页）。

[变量]`RecordFunctionSegment.symbol`
    一个`gdb.Symbol`对象，表示相关的符号。如果没有可用的调试信息，则可能为`None`。

[变量]`RecordFunctionSegment.level`
    一个整数，表示函数调用的栈级别。如果函数调用是一个间隙，则可能为`None`。

[变量]`RecordFunctionSegment.instructions`
    一个包含与此函数调用相关的`gdb.RecordInstruction`或`gdb.RecordGap`对象的列表。

[变量]`RecordFunctionSegment.up`
    一个`gdb.RecordFunctionSegment`对象，表示调用者的函数段。如果未记录调用，则这将是控制返回的函数段。如果既未记录调用也未记录返回，则为`None`。

[变量]`RecordFunctionSegment.prev`
    一个`gdb.RecordFunctionSegment`对象，表示此函数调用的前一个段。可能为`None`。

[变量]`RecordFunctionSegment.next`
    一个`gdb.RecordFunctionSegment`对象，表示此函数调用的下一个段。可能为`None`。
```

以下示例演示了使用这些对象和函数来创建一个函数，该函数将把记录倒回到在不同文件中执行函数的最后一次。这通常用于在库中跟踪用户提供的回调函数的执行，这些回调函数在回溯中通常不可见。

```python
def bringback():
    rec = gdb.current_recording()
    if not rec:
        return

    insn = rec.instruction_history
    if len(insn) == 0:
        return

    try:
        position = insn.index(rec.replay_position)
    except:
        position = -1
    try:
        filename = insn[position].sal.symtab.fullname()
    except:
        filename = None
    for i in reversed(insn[:position]):
        try:
            current = i.sal.symtab.fullname()
        except:
            current = None
        if filename == current:
            continue
        rec.goto(i)
        return
```

另一个可能的应用是编写一个函数，该函数计算给定行范围内的代码执行次数。此行范围可以包含函数的部分或跨越多个函数，并且不限于连续的。

```python
def countrange(filename, linerange):
    count = 0
    def filter_only(file_name):
        for call in gdb.current_recording().function_call_history:
            try:
                if file_name in call.symbol.symtab.fullname():
                    yield call
            except:
                pass
    for c in filter_only(filename):
        for i in c.instructions:
            try:
                if i.sal.line in linerange:
                    count += 1
                    break;
            except:
                pass
    return count
```

#### 23.3.2.21 Python中的CLI命令

你可以在 Python 中实现新的 gdb CLI 命令。CLI 命令是使用`gdb.Command`类的实例实现的，最常见的是使用子类。

```
[函数] `Command.__init__ (name, command class [, completer class [, prefix]])
    `Command`的对象初始化器将新命令注册到 gdb 中。这个初始化器通常在子类自己的`__init__`方法中被调用。
    `name`是命令的名称。如果`name`由多个单词组成，那么初始的单词将作为前缀命令被查找。在这种情况下，如果其中一个前缀命令不存在，将引发异常。
    不支持多行命令。
    `command class`应该是下面定义的`'COMMAND_'`常量之一。这个参数告诉 gdb 在帮助系统中如何对新命令进行分类。
    `completer class`是一个可选参数。如果提供，它应该是下面定义的`'COMPLETE_'`常量之一。这个参数告诉 gdb 如何对这个命令进行补全。如果未提供，gdb 将尝试使用对象的`complete`方法（见下文）进行补全；如果未找到这样的方法，在尝试补全时将发生错误。
    `prefix`是一个可选参数。如果为`True`，那么新命令是一个前缀命令；可以注册这个命令的子命令。
    新命令的帮助文本取自命令类的 Python 文档字符串（如果有）。如果未提供文档字符串，将使用默认值“此命令未记录。”。

[函数] `Command.dont_repeat()`
    默认情况下，当用户在命令提示符处输入空白行时，gdb 命令将被重复执行。命令可以通过在其`invoke`方法的某个点调用`dont_repeat`方法来抑制这种行为（通常在发生异常的情况下尽早进行此操作）。这类似于用户命令`dont-repeat`，请参见第 23.1.1 [定义]，第 391 页。

[函数] `Command.invoke`(参数, 从 tty)
    当调用此命令时，gdb 将调用此方法。
    `argument`是一个字符串。它是命令的参数，在去除前导和尾随空格后。
    `from tty`是一个布尔参数。当为`true`时，意味着命令是由用户在终端输入的；当为`false`时，意味着命令来自其他地方。
    如果此方法引发异常，它将被转换为 gdb 错误调用。否则，返回值将被忽略。
    要将`argument`分解为类似`argv`的字符串，请使用`gdb.string_to_argv`。此函数的行为与 gdb 的内部参数词法分析器`buildargv`完全相同。建议使用此函数以保持一致性。参数以空格分隔，并且可以被引号括起来。示例：
        `print gdb.string_to_argv ("1 2\ \"3 ’4 \"5’ \"6 ’7\"")`
        [’1’, ’2 "3’, ’4 "5’, "6 ’7"]

[函数] `Command.complete`(文本, 单词)
    当用户尝试对这个命令进行补全时，gdb 将调用此方法。所有形式的补全都由这个方法处理，即 TAB 和 M-? 键绑定（参见第 3.3 [补全]，第 24 页），以及`complete`命令（参见第 3.6 [帮助]，第 29 页）。
    `text`和`word`都是字符串；`text`包含到光标位置的完整命令行，而`word`包含命令行的最后一个单词；这是使用单词分割启发式算法计算的。
    `complete`方法可以返回几个值：
    • 如果返回值是一个序列，序列的内容将被用作补全结果。由`complete`确保内容实际上确实完成了单词。允许零长度序列，这意味着没有可用的补全。仅使用序列的字符串元素；序列中的其他元素将被忽略。
    • 如果返回值是下面定义的`'COMPLETE_'`常量之一，那么将调用相应的 gdb 内部补全函数，并使用其结果。
    • 所有其他结果都被视为没有可用的补全。
```
    
当注册一个新命令时，它必须被声明为某些通用命令类的成员。这用于在在线帮助系统中对顶级命令进行分类；注意，前缀命令不会在其自己的类别下列出，而是在其顶级命令的类别下列出。可用的分类由`gdb`模块中定义的常量表示：

```
`gdb.COMMAND_NONE`
    该命令不属于任何特定类别。此类别中的命令不会在任何帮助类别中显示。

`gdb.COMMAND_RUNNING`
    该命令与运行下级程序有关。例如，`start`、`step`和`continue`在这个类别中。在 gdb 提示符下输入`help running`以查看此类别中的命令列表。

`gdb.COMMAND_DATA`
    该命令与数据或变量有关。例如，`call`、`find`和`print`在这个类别中。在 gdb 提示符下输入`help data`以查看此类别中的命令列表。

`gdb.COMMAND_STACK`
    该命令与堆栈的操作有关。例如，`backtrace`、`frame`和`return`在这个类别中。在 gdb 提示符下输入`help stack`以查看此类别中的命令列表。

`gdb.COMMAND_FILES`
    这个类用于与文件相关的命令。例如，`file`、`list`和`section`在这个类别中。在 gdb 提示符下输入`help files`以查看此类别中的命令列表。

`gdb.COMMAND_SUPPORT`
    这应该用于“支持设施”，通常意味着在与 gdb 交互时对用户有用的东西，但与下级程序的状态无关。例如，`help`、`make`和`shell`在这个类别中。在 gdb 提示符下输入`help support`以查看此类别中的命令列表。

`gdb.COMMAND_STATUS`
    该命令是一个与“info”相关的命令，即与 gdb 本身的状态有关。例如，`info`、`macro`和`show`在这个类别中。在 gdb 提示符下输入`help status`以查看此类别中的命令列表。

`gdb.COMMAND_BREAKPOINTS`
    该命令与断点有关。例如，`break`、`clear`和`delete`在这个类别中。在 gdb 提示符下输入`help breakpoints`以查看此类别中的命令列表。

`gdb.COMMAND_TRACEPOINTS`
    该命令与跟踪点有关。例如，`trace`、`actions`和`tfind`在这个类别中。在 gdb 提示符下输入`help tracepoints`以查看此类别中的命令列表。

`gdb.COMMAND_TUI`
    该命令与文本用户界面有关（参见第 25 章 [TUI]，第 581 页）。在 gdb 提示符下输入`help tui`以查看此类别中的命令列表。

`gdb.COMMAND_USER`
    该命令是用户的通用命令，通常不属于其他类别之一。在 gdb 提示符下输入`help user-defined`以查看此类别中的命令列表，以及 gdb 宏的列表（参见第 23.1 [序列]，第 391 页）。

`gdb.COMMAND_OBSCURE`
    该命令仅在不寻常的情况下使用，或者对用户一般不感兴趣。例如，`checkpoint`、`fork`和`stop`在这个类别中。在 gdb 提示符下输入`help obscure`以查看此类别中的命令列表。

`gdb.COMMAND_MAINTENANCE`
    该命令仅对 gdb 维护人员有用。`maintenance`和`flushregs`命令在这个类别中。在 gdb 提示符下输入`help internals`以查看此类别中的命令列表。
```

一个新命令可以使用预定义的补全函数，要么通过在初始化时通过参数指定它，要么从`complete`方法中返回它。这些预定义的补全常量都在`gdb`模块中定义：

```
`gdb.COMPLETE_NONE`
    这个常量意味着不应进行补全。

`gdb.COMPLETE_FILENAME`
    这个常量意味着应该执行文件名补全。

`gdb.COMPLETE_LOCATION`
    这个常量意味着应该进行位置补全。参见第 9.2 [位置规范]，第 126 页。

`gdb.COMPLETE_COMMAND`
    这个常量意味着补全应该检查 gdb 命令名称。

`gdb.COMPLETE_SYMBOL`
    这个常量意味着补全应该使用符号名称作为源。

`gdb.COMPLETE_EXPRESSION`
    这个常量意味着应该在表达式上进行补全。通常这意味着在符号名称上进行补全，但某些语言解析器也支持在字段名称上进行补全。
```
    
以下代码片段展示了如何在 Python 中实现一个简单的 CLI 命令：

```python
class HelloWorld (gdb.Command):
    """Greet the whole world."""
    def __init__ (self):
        super (HelloWorld, self).__init__ ("hello-world", gdb.COMMAND_USER)
    def invoke (self, arg, from_tty):
        print ("Hello, World!")
HelloWorld ()
```

最后一行实例化类，并且是触发将命令注册到 gdb 所必需的。根据 Python 代码如何被读入 gdb，你可能需要显式导入`gdb`模块。

#### 23.3.2.22 GDB/MI命令在Python中的实现

可以在 Python 中添加 gdb/mi（参见第 27 章[GDB/MI]，第 591 页）命令。gdb/mi 命令是使用`gdb.MICommand`类的实例实现的，最常见的是使用子类。

```
[函数] `MICommand.__init__(name)`
    `MICommand`的对象初始化器将新命令注册到 gdb 中。这个初始化器通常从子类自己的`__init__`方法中调用。
    `name`是命令的名称。它必须是 gdb/mi 命令的有效名称，特别是必须以连字符（-）开头。不允许重用内置 gdb/mi 的名称，否则将引发`RuntimeError`。允许使用之前在 Python 中定义的 gdb/mi 命令的名称，之前的命令将被新命令替换。

[函数] `MICommand.invoke(arguments)`
    当调用新的 MI 命令时，gdb 将调用此方法。
    `arguments`是字符串列表。注意，`--thread`和`--frame`参数由 gdb 自己处理，因此它们不会出现在`arguments`中。
    如果此方法引发异常，它将被转换为 gdb/mi 的`^error`响应。仅应使用`gdb.GdbError`异常（或其子类）向用户报告错误，任何其他异常类型都被视为`invoke`方法的失败，并且根据设置的 Python `print-stack`设置（参见[设置 Python print-stack]，第 402 页），异常将被打印到错误流中。
    如果此方法返回`None`，则 gdb/mi 命令将返回一个`^done`响应，没有其他值。
    否则，返回值必须是一个字典，该字典将转换为 gdb/mi 的结果记录（参见第 27.4.2 节[GDB/MI 输出语法]，第 595 页）。此字典的键必须是字符串，并在结果记录中用作变量名，这些字符串必须符合以下详细的命名规则。此字典的值将按如下方式递归处理：
    - 如果值是 Python 序列或迭代器，它将转换为 gdb/mi 列表，并递归转换元素。
    - 如果值是 Python 字典，它将转换为 gdb/mi 元组。该字典中的键必须是字符串，符合以下详细的变量命名规则。值将递归转换。
    - 否则，首先使用`str()`将值转换为 Python 字符串，然后将其转换为 gdb/mi 常量。
    gdb/mi 输出中用于变量名的字符串必须遵循以下规则：字符串必须至少有一个字符长，第一个字符必须在集合`[a-zA-Z]`中，而后续每个字符必须在集合`[-_a-zA-Z0-9]`中。
```
    
`MICommand`实例具有以下属性：

```
[变量] `MICommand.name`
    一个字符串，此 gdb/mi 命令的名称，如传递给`__init__`方法的那样。此属性是只读的。

[变量] `MICommand.installed`
    一个布尔值，表示此命令是否已安装好，可供用户从命令行调用。命令在实例化时自动安装，之后此属性将为`True`。
    如果稍后创建了具有相同名称的新命令，则原始命令将变为未安装状态，此属性将为`False`。
    此属性是读写的，将此属性设置为`False`将卸载命令，将其从可用命令集中删除。将此属性设置为`True`将安装该命令以供使用。如果已经有一个具有此名称的 Python 命令安装，则当前安装的命令将被卸载，此命令将取而代之安装。
```

以下代码片段展示了如何在 Python 中实现一些简单的 MI 命令：

```python
class MIEcho(gdb.MICommand):
    """Echo arguments passed to the command."""
    def __init__(self, name, mode):
        self._mode = mode
        super(MIEcho, self).__init__(name)
    def invoke(self, argv):
        if self._mode == 'dict':
            return {'dict': {'argv': argv}}
        elif self._mode == 'list':
            return {'list': argv}
        else:
            return {'string': ", ".join(argv)}

MIEcho("-echo-dict", "dict")
MIEcho("-echo-list", "list")
MIEcho("-echo-string", "string")
```

最后三行将类实例化三次，创建三个新的 gdb/mi 命令`-echo-dict`、`-echo-list`和`-echo-string`。每次实例化`gdb.MICommand`的子类时，新命令将自动注册到 gdb 中。

根据 Python 代码如何读入 gdb，您可能需要显式导入`gdb`模块。

以下示例展示了添加了上述命令的 gdb 会话：

```
(gdb)
-echo-dict abc def ghi
^done,dict={"argv":["abc","def","ghi"]}
(gdb)
-echo-list abc def ghi
^done,list=["abc","def","ghi"]
(gdb)
-echo-string abc def ghi
^done,string="abc, def, ghi"
(gdb)
```

相反，也可以从 Python 中执行 gdb/mi 命令，结果是一个 Python 对象，而不是特殊格式化的字符串。这是通过`gdb.execute_mi`函数完成的。

```
[函数] `gdb.execute_mi(command [, arg]...)`
    调用 gdb/mi 命令。`command`是命令的名称，一个字符串。参数`arg`被传递给命令。每个参数也必须是一个字符串。
    此函数返回一个 Python 字典，其内容反映相应的 GDB/MI 命令的输出。有关这些命令的详细信息，请参考文档。列表表示为 Python 列表，元组表示为 Python 字典。
    如果命令失败，它将引发 Python 异常。
```

以下是使用上述示例中的命令的工作方式：

```
(gdb) python print(gdb.execute_mi("-echo-dict", "abc", "def", "ghi"))
{"dict": {"argv": ["abc", "def", "ghi"]}}
(gdb) python print(gdb.execute_mi("-echo-list", "abc", "def", "ghi"))
{"list": ["abc", "def", "ghi"]}
(gdb) python print(gdb.execute_mi("-echo-string", "abc", "def", "ghi"))
{"string": "abc, def, ghi"}
```

#### 23.3.2.23 gdb/mi 通知在 Python 中的使用

从 Python 中发出 gdb/mi 通知是可行的。可以使用`gdb.notify_mi`函数来实现。

```
[函数] `gdb.notify_mi(name [, data])`
    发出一个 gdb/mi 异步通知。`name`是通知的名称，由字母数字字符和连字符（-）组成。`data`是要随通知一起发出的任何其他数据，以 Python 字典的形式传递。此参数是可选的。字典将被转换为 gdb/mi 结果记录（参见第 27.4.2 节[GDB/MI 输出语法]，第 595 页），方式与 Python MI 命令的结果相同（参见第 23.3.2.22 节[Python 中的 GDB/MI 命令]，第 467 页）。
    如果`data`为`None`，则不发出其他值。
```

虽然允许使用现有的通知名称（参见第 27.7.3 节[GDB/MI 异步记录]，第 600 页）与`gdb.notify_mi`一起使用，但鼓励用户在用户定义的通知前加上连字符（-），以避免可能的冲突。gdb 永远不会引入以连字符开头的通知。
    
以下是在每次与远程 GDB 服务器的连接关闭时发出`=-connection-removed`通知的示例（参见第 23.3.2.38 节[Python 中的连接]，第 499 页）：

```python
def notify_connection_removed(event):
    data = {"id": event.connection.num, "type": event.connection.type}
    gdb.notify_mi("-connection-removed", data)
gdb.events.connection_removed.connect(notify_connection_removed)
```

然后，每次连接关闭时，MI 通道上都会有一个通知：

```
=-connection-removed,id="1",type="remote"
```

#### 23.3.2.24 在 Python 中的参数

你可以使用 Python 实现新的 gdb 参数。一个新参数被实现为`gdb.Parameter`类的一个实例。

参数通过`set`和`show`命令暴露给用户。请参阅第 3.6 节[帮助]，第 29 页。

gdb 中已经存在许多可以设置的参数。两个例子是：`set follow fork`和`set charset`。设置这些参数会影响 gdb 中的某些行为。同样，你可以定义可用于影响自定义 Python 脚本和命令行为的参数。

```
[函数]`Parameter.__init__(name, command class, parameter class [, enum sequence])`
    `Parameter`的对象初始化器向 gdb 注册新参数。这个初始化器通常从子类自己的`__init__`方法中调用。
    `name`是新参数的名称。如果`name`由多个单词组成，那么最初的单词将被视为前缀参数。这可以通过`set print`参数集来说明。如果`name`是`print foo`，那么`print`将被搜索为前缀参数。在这种情况下，参数随后可以在 gdb 中被访问为`set print foo`。
    如果`name`由多个单词组成，并且找不到前缀参数组，将引发异常。
    `command class`应该是`‘COMMAND_’`常量之一（请参阅第 23.3.2.21 节[Python 中的 CLI 命令]，第 463 页）。这个参数告诉 gdb 在帮助系统中如何对新参数进行分类。
    `parameter class`应该是下面定义的`‘PARAM_’`常量之一。这个参数告诉 gdb 新参数的类型；此信息用于输入验证和完成。
    如果`parameter class`是`PARAM_ENUM`，那么`enum sequence`必须是一个字符串序列。这些字符串表示参数的可能值。
    如果`parameter class`不是`PARAM_ENUM`，那么存在第四个参数将导致引发异常。
    新参数的帮助文本包括来自参数类的 Python 文档字符串（如果有）。如果没有文档字符串，则使用默认值。文档字符串包含在`parameters help set`和`help show`命令的输出中，编写时应考虑到这一点。

[变量]`Parameter.set_doc`
    如果这个属性存在，并且是一个字符串，那么它的值将用作此参数的`set`命令的帮助文本的第一部分。帮助文本的第二部分取自参数类的文档字符串（如果有）。
    `set_doc`的值应该给出关于`set`操作的简短摘要，此文本仅在用户为该参数运行`help set`命令时显示。类文档应用于给出关于参数作用的更完整描述，此文本在`help set`和`help show`命令中都显示。
    在调用`Parameter.__init__`时会检查`set_doc`的值；后续更改无效。

[变量]`Parameter.show_doc`
    如果这个属性存在，并且是一个字符串，那么它的值将用作此参数的`show`命令的帮助文本的第一部分。帮助文本的第二部分取自参数类的文档字符串（如果有）。
    `show_doc`的值应该给出关于`show`操作的简短摘要，此文本仅在用户为该参数运行`help show`命令时显示。类文档应用于给出关于参数作用的更完整描述，此文本在`help set`和`help show`命令中都显示。
    在调用`Parameter.__init__`时会检查`show_doc`的值；后续更改无效。

[变量]`Parameter.value`
    `value`属性保存参数的基础值。可以像读取和分配任何其他属性一样读取和分配它。gdb 在赋值时会进行验证。
```

在任何`Parameter`类中可以实现两个方法。这些是：

```
[函数]`Parameter.get_set_string(self)`
    如果存在这个方法，当参数的值通过`set API`（例如`set foo off`）被更改时，gdb 将调用它。`value`属性已经被填充了新值，可以在输出中使用。这个方法必须返回一个字符串。如果返回的字符串不为空，gdb 将将其呈现给用户。
        class ExampleParam (gdb.Parameter):
            def __init__(self, name):
                super(ExampleParam, self).__init__(name,
                                                    gdb.COMMAND_DATA,
                                                    gdb.PARAM_BOOLEAN)
                self.value = True
                self.saved_value = True
            def validate(self):
                return False
            def get_set_string(self):
                if not self.validate():
                    self.value = self.saved_value
                    raise gdb.GdbError('Failed to validate')
                self.saved_value = self.value
                return ""

[函数]`Parameter.get_show_string(self, svalue)`
    当调用参数的`show API`（例如`show foo`）时，gdb 将调用这个方法。参数`svalue`接收当前值的字符串表示。这个方法必须返回一个字符串。
```
    
当定义一个新参数时，必须指定其类型。可用的类型由`gdb`模块中定义的常量表示：

```
`gdb.PARAM_BOOLEAN`
    值是一个普通的布尔值。Python 布尔值`True`和`False`是唯一有效的值。

`gdb.PARAM_AUTO_BOOLEAN`
    值有三种可能的状态：`true`、`false`和`‘auto’`。在 Python 中，`true`和`false`使用布尔常量表示，`‘auto’`使用`None`表示。

`gdb.PARAM_UINTEGER`
    值是一个无符号整数。`None`的值应解释为“无限制”（字面量`‘unlimited’`也可用于设置该值），并且值 0 是保留的，不应使用。

`gdb.PARAM_INTEGER`
    值是一个有符号整数。`None`的值应解释为“无限制”（字面量`‘unlimited’`也可用于设置该值），并且值 0 是保留的，不应使用。

`gdb.PARAM_STRING`
    值是一个字符串。当用户修改字符串时，任何转义序列，如`‘\t’`、`‘\f’`和八进制转义，将被转换为相应的字符并编码为当前主机字符集。

`gdb.PARAM_STRING_NOESCAPE`
    值是一个字符串。当用户修改字符串时，转义序列将不被翻译而直接传递。

`gdb.PARAM_OPTIONAL_FILENAME`
    值是一个文件名（一个字符串）或`None`。

`gdb.PARAM_FILENAME`
    值是一个文件名。这与`PARAM_STRING_NOESCAPE`类似，但用于完成的是文件名。

`gdb.PARAM_ZINTEGER`
    值是一个有符号整数。这与`PARAM_INTEGER`类似，除了允许 0 且不支持`None`的值。

`gdb.PARAM_ZUINTEGER`
    值是一个无符号整数。这与`PARAM_UINTEGER`类似，除了允许 0 且不支持`None`的值。

`gdb.PARAM_ZUINTEGER_UNLIMITED`
    值是一个有符号整数。这与`PARAM_INTEGER`类似，包括`None`的值应解释为“无限制”（字面量`‘unlimited’`也可用于设置该值），除了允许 0 且值不能为负数，除非对于“无限制”的设置返回特殊值-1。

`gdb.PARAM_ENUM`
    值是一个字符串，必须是在创建参数时提供的字符串常量集合之一。
```
    
#### 23.3.2.25 编写新的便利函数

你可以在 Python 中实现新的便利函数（见第 10.12 节[便利变量]，第 171 页）。便利函数是`gdb.Function`类的子类的实例。

```
[函数] `Function.__init__(name)`
    `Function`的初始化器将新函数注册到`gdb`中。参数`name`是函数的名称，一个字符串。该函数将对用户可见，作为内部函数类型的便利变量，其名称与给定的名称相同。
    新函数的文档取自新类的文档字符串。

[函数] `Function.invoke(*args)`
    当计算便利函数时，其参数将转换为`gdb.Value`的实例，然后调用函数的`invoke`方法。请注意，`gdb`不会预先确定便利函数的参数数量。相反，所有可用的参数都按照标准的 Python 调用约定传递给`invoke`。特别是，便利函数可以为参数具有默认值而不会产生不良影响。
    此方法的返回值在包围表达式中用作其值。如果返回普通的 Python 值，它将按照通常的规则转换为`gdb.Value`。
```

以下代码片段展示了如何在 Python 中实现一个简单的便利函数：

```python
class Greet(gdb.Function):
    """Return string to greet someone.
    Takes a name as argument."""
    def __init__(self):
        super(Greet, self).__init__("greet")
    def invoke(self, name):
        return "Hello, %s!" % name.string()
Greet()
```

最后一行实例化类，并且对于触发函数在`gdb`中的注册是必要的。根据 Python 代码如何读入`gdb`，你可能需要显式地导入`gdb`模块。

现在你可以在表达式中使用该函数：

```
(gdb) print $greet("Bob")
$1 = "Hello, Bob!"
```

#### 23.3.2.26 Python 中的程序空间

程序空间（或 progspace）表示地址空间的符号视图。它由程序的所有 objfiles 组成。请参阅第 23.3.2.27 节[Python 中的 Objfiles]，第 476 页。有关程序空间的更多详细信息，请参阅第 4.9 节[下级连接和程序]，第 42 页。

gdb 模块中提供了以下与 progspace 相关的函数：

```
[函数] gdb.current_progspace()
    此函数返回当前选定下级的程序空间。请参阅第 4.9 节[下级连接和程序]，第 42 页。这与 gdb.selected_inferior().progspace（请参阅第 23.3.2.17 节[Python 中的下级]，第 449 页）相同，是为了保持历史兼容性而包含的。

[函数] gdb.progspaces()
    返回 gdb 当前已知的所有 progspaces 的序列。
```

每个 progspace 由 gdb.Progspace 类的一个实例表示。

```
[变量] Progspace.filename
    程序空间的主符号文件的文件名（作为字符串）（已从其中加载调试符号），例如 symbol-file 或 file 命令的参数。
    如果当前未加载主符号表，则此属性将为 None。

[变量] Progspace.symbol_file
    表示主符号文件（已从其中加载调试符号）的 gdb.Objfile。这是通过 symbol-file 或 file 命令设置的符号文件。
    当 Progspace.filename 不为 None 时，这将是表示 Progspace.filename 的 gdb.Objfile。
    如果当前未加载主符号表，则此属性将为 None。
    如果 progspace 无效，即当 Progspace.is_valid()返回 False 时，尝试访问此属性将引发 RuntimeError 异常。

[变量] Progspace.executable_filename
    此程序空间正在使用的可执行文件的文件名（作为字符串）。可执行文件是 gdb 在使用本机目标时启动下级时将调用的文件。此属性中的文件名由 exec-file 和 file 命令更新。
    如果此 Progspace 中当前未设置可执行文件，则此属性包含 None。
    如果 progspace 无效，即当 Progspace.is_valid()返回 False 时，尝试访问此属性将引发 RuntimeError 异常。

[变量] Progspace.pretty_printers
    pretty_printers 属性是一个函数列表。它用于查找漂亮打印机。每个函数都会传递一个 Value；如果函数返回 None，则继续搜索。否则，返回值应该是一个用于格式化值的对象。有关更多信息，请参阅第 23.3.2.6 节[漂亮打印 API]，第 424 页。

[变量] Progspace.type_printers
    type_printers 属性是一个类型打印机对象列表。有关更多信息，请参阅第 23.3.2.9 节[类型打印 API]，第 429 页。

[变量] Progspace.frame_filters
    frame_filters 属性是一个帧过滤器对象字典。有关更多信息，请参阅第 23.3.2.10 节[帧过滤器 API]，第 430 页。

[变量] Progspace.missing_file_handlers
    missing_file_handlers 属性是一个元组列表。每个元组都包含此程序空间的一个丢失文件处理程序对象。有关更多信息，请参阅第 23.3.2.41 节[Python 中的丢失调试信息]，第 511 页，以及第 23.3.2.42 节[Python 中的丢失 Objfiles]，第 513 页。
```
    
程序空间具有以下方法：

```
[函数] Progspace.block_for_pc(pc)
    返回包含给定 pc 值的最内层 gdb.Block。如果找不到指定 pc 值的块，该函数将返回 None。

[函数] Progspace.find_pc_line(pc)
    返回与 pc 值对应的 gdb.Symtab_and_line 对象。请参阅第 23.3.2.31 节[Python 中的符号表]，第 489 页。如果将无效的 pc 值作为参数传递，则返回的 gdb.Symtab_and_line 对象的 symtab 和 line 属性将分别为 None 和 0。

[函数] Progspace.is_valid()
    如果 gdb.Progspace 对象有效，则返回 True；如果无效，则返回 False。如果程序空间文件不再被任何下级引用，gdb.Progspace 对象可能会变为无效。如果在调用该方法时它无效，所有其他 gdb.Progspace 方法都将引发异常。

[函数] Progspace.objfiles()
    返回此程序空间引用的所有 objfiles 的序列。请参阅第 23.3.2.27 节[Python 中的 Objfiles]，第 476 页。

[函数] Progspace.solib_name(address)
    返回包含给定地址的共享库的名称（作为字符串），或 None。

[函数] Progspace.objfile_for_address(address)
    返回包含给定地址的 gdb.Objfile，如果没有 objfile 覆盖它，则返回 None。
```

可以以通常的 Python 方式向 gdb.Progspace 对象添加任意属性。例如，如果需要对程序空间进行一些额外的记录保存，这很有用。

有关为新属性选择合适名称的指导，请参阅[选择属性名称]，第 451 页。

在这个虚构的示例中，我们希望在加载具有特定符号的 objfile 时执行一些处理，但我们只希望执行一次，因为这很昂贵。为了实现这一点，我们将结果记录在程序空间中，因为我们无法预测何时将加载所需的 objfile。

```python
(gdb) python
def clear_objfiles_handler(event):
    event.progspace.expensive_computation = None
def expensive(symbol):
    """一个模拟例程，对符号执行“昂贵”的计算。"""
    print("Computing the answer to the ultimate question...")
    return 42
def new_objfile_handler(event):
    objfile = event.new_objfile
    progspace = objfile.progspace
    if not hasattr(progspace, ’expensive_computation’) or \
       progspace.expensive_computation is None:
        # 为了使示例简单，我们使用“main”作为符号。
        # 注意：目前没有办法将查找限制在一个 objfile 中。
        symbol = gdb.lookup_global_symbol(’main’)
        if symbol is not None:
            progspace.expensive_computation = expensive(symbol)
gdb.events.clear_objfiles.connect(clear_objfiles_handler)
gdb.events.new_objfile.connect(new_objfile_handler)
end
(gdb) file /tmp/hello
Reading symbols from /tmp/hello...
Computing the answer to the ultimate question...
(gdb) python print(gdb.current_progspace().expensive_computation)
(gdb) run
Starting program: /tmp/hello
Hello.
[Inferior 1 (process 4242) exited normally]
```

#### 23.3.2.27 Objfiles In Python

gdb 从各种包含符号的文件中为被调试程序加载符号（见 18.1 节[文件]，第 291 页）。这些文件包括主可执行文件、被调试程序使用的任何共享库以及任何单独的调试信息文件（见 18.3 节[单独的调试文件]，第 300 页）。gdb 将这些包含符号的文件称为 objfiles。

在 gdb 模块中提供了以下与 objfile 相关的函数：

```
[函数] `gdb.current_objfile()`
    当自动加载 Python 脚本（见 23.3.3 节[Python 自动加载]，第 516 页）时，gdb 将“当前 objfile”设置为相应的 objfile。此函数返回当前的 objfile。如果没有当前的 objfile，此函数返回 None。

[函数] `gdb.objfiles()`
    返回当前程序空间引用的 objfiles 序列。
    见 23.3.2.27 节[Python 中的 Objfiles]，第 476 页，以及 23.3.2.26 节[Python 中的程序空间]，第 474 页。这与 `gdb.selected_inferior().progspace.objfiles()` 相同，是为了保持历史兼容性而包含的。

[函数] `gdb.lookup_objfile(name [, by build id])`
    在当前程序空间的 objfiles 列表中查找名为 name 的文件或构建 ID（见 23.3.2.26 节[Python 中的程序空间]，第 474 页）。如果未找到 objfile，则抛出 Python ValueError 异常。
    如果 name 是相对文件名称，则它将与具有相同尾部组件的任何源文件名称匹配。例如，如果 name 是“gcc/expr.c”，则它将与 /build/trunk/gcc/expr.c 的源文件名称匹配，但不与 /build/trunk/libcpp/expr.c 或 /build/trunk/gcc/x-expr.c 匹配。
    如果提供了 by build id 且为 True，则 name 是 objfile 的构建 ID。否则，name 是文件名。此功能仅在某些操作系统上受支持，特别是那些使用 ELF 格式的二进制文件和 gnu Binutils 的操作系统。有关此功能的更多详细信息，请参阅 The GNU Linker 中“命令行选项”部分中的 --build-id 命令行选项的描述。
```
    
每个 objfile 由 gdb.Objfile 类的一个实例表示。

``
[变量] `Objfile.filename`
    objfile 的文件名，作为字符串，已解析符号链接。
    如果 objfile 不再有效，则值为 None。见下面描述的 gdb.Objfile.is_valid 方法。

[变量] `Objfile.username`
    用户指定的 objfile 的文件名，作为字符串。
    如果 objfile 不再有效，则值为 None。见下面描述的 gdb.Objfile.is_valid 方法。

[变量] `Objfile.is_file`
    objfile 通常来自普通文件，但在某些情况下，它可能是从内存内容构造的。此属性对于基于文件的 objfiles 为 True，对于其他类型为 False。

[变量] `Objfile.owner`
    对于单独的调试信息 objfiles，这是提供调试信息的相应 gdb.Objfile 对象。否则为 None。单独的调试信息 objfiles 通过 gdb.Objfile.add_separate_debug_file 方法添加，如下所述。

[变量] `Objfile.build_id`
    objfile 的构建 ID，作为字符串。如果 objfile 没有构建 ID，则值为 None。
    此功能仅在某些操作系统上受支持，特别是那些使用 ELF 格式的二进制文件和 gnu Binutils 的操作系统。有关此功能的更多详细信息，请参阅 The GNU Linker 中“命令行选项”部分中的 --build-id 命令行选项的描述。

[变量] `Objfile.progspace`
    objfile 所在的程序空间，作为 gdb.Progspace 对象。见 23.3.2.26 节[Python 中的程序空间]，第 474 页。

[变量] `Objfile.pretty_printers`
    `pretty_printers` 属性是一个函数列表。用于查找漂亮打印机。将一个值依次传递给每个函数；如果函数返回 None，则继续搜索。否则，返回值应该是一个用于格式化值的对象。有关更多信息，请见 23.3.2.6 节[漂亮打印 API]，第 424 页。

[变量] `Objfile.type_printers`
    `type_printers` 属性是一个类型打印机对象列表。有关更多信息，请见 23.3.2.9 节[类型打印 API]，第 429 页。

[变量] `Objfile.frame_filters`
    `frame_filters` 属性是一个帧过滤器对象字典。有关更多信息，请见 23.3.2.10 节[帧过滤器 API]，第 430 页。
```
    
可以以通常的 Python 方式向 gdb.Objfile 对象添加任意属性。例如，如果需要对与 objfile 相关的一些额外记录进行维护，这将很有用。

见[选择属性名称]，第 451 页，以获取关于为新属性选择合适名称的指导。

在这个虚构的示例中，我们记录了 gdb 加载 objfile 的时间。

```gdb
(gdb) python
import datetime
def new_objfile_handler(event):
    # 设置新 objfile 的 time_loaded 属性。
    event.new_objfile.time_loaded = datetime.datetime.today()
gdb.events.new_objfile.connect(new_objfile_handler)
end
(gdb) file./hello
Reading symbols from./hello...
(gdb) python print(gdb.objfiles()[0].time_loaded)
2014-10-09 11:41:36.770345
```

一个 gdb.Objfile 对象具有以下方法：

```
[函数] `Objfile.is_valid()`
    如果 gdb.Objfile 对象有效，则返回 True，否则返回 False。如果 gdb.Objfile 对象引用的对象文件不再在 gdb 中加载，该对象就会变为无效。如果在调用方法时它无效，所有其他 gdb.Objfile 方法都将抛出异常。

[函数] `Objfile.add_separate_debug_file(file)`
    将 file 添加到 gdb 将为 objfile 搜索调试信息的文件列表中。当调试信息已从程序中删除并存储在单独的文件中时，这很有用。gdb 内置支持查找单独的调试信息文件（见 18.3 节[单独的调试文件]，第 300 页），但如果文件不在 gdb 搜索的标准位置之一，则可以使用此函数从其他位置添加调试信息文件。

[函数] `Objfile.lookup_global_symbol(name [, domain])`
    在这个 objfile 中搜索名为 name 的全局符号。可选地，可以使用 domain 参数限制搜索范围。domain 参数必须是 gdb 模块中定义的域常量，并在 23.3.2.30 节[Python 中的符号]，第 484 页中进行了描述。此函数类似于 gdb.lookup_global_symbol，不同之处在于搜索仅限于此 objfile。
    结果是一个 gdb.Symbol 对象或 None，如果未找到符号。

[函数] `Objfile.lookup_static_symbol(name [, domain])`
    与 `Objfile.lookup_global_symbol` 类似，但在这个 objfile 中搜索名为 name 且具有静态链接的全局符号。
```

#### 23.3.2.28 从 Python 访问下级栈帧

当被调试的程序停止时，gdb 能够分析其调用栈（见第 8.1 节[栈帧]，第 113 页）。gdb.Frame 类表示栈中的一个帧。

gdb.Frame 对象仅在其对应的帧存在于下级的栈中时才有效。

如果尝试使用无效的帧对象，gdb 将抛出 gdb.error 异常（见第 23.3.2.3 节[异常处理]，第 410 页）。

可以使用 == 运算符比较两个 gdb.Frame 对象是否相等，例如：

```
(gdb) python print gdb.newest_frame() == gdb.selected_frame()
True
```

gdb 模块中提供了以下与帧相关的函数：

```
[函数] gdb.selected_frame()
    返回所选的帧对象。（见第 8.3 节[选择帧]，第 117 页）。

[函数] gdb.newest_frame()
    返回所选线程的最新帧对象。

[函数] gdb.frame_stop_reason_string(reason)
    返回一个字符串，解释 gdb 停止展开帧的原因，如给定的原因代码（一个整数，见本节下面的 unwind_stop_reason 方法）所表示。

[函数] gdb.invalidate_cached_frames
    gdb 内部保留了已展开帧的缓存。此函数使此缓存无效。
    通常不应由普通 Python 代码调用此函数。为了完整性而记录在此。
```

一个 gdb.Frame 对象具有以下方法：

```
[函数] Frame.is_valid()
    如果 gdb.Frame 对象有效，则返回 True，否则返回 False。如果帧所引用的帧在下级中不再存在，帧对象可能会变为无效。如果在调用方法时它无效，所有 gdb.Frame 方法都将抛出异常。

[函数] Frame.name()
    返回帧的函数名，如果无法获取则返回 None。

[函数] Frame.architecture()
    返回与帧的体系结构对应的 gdb.Architecture 对象。见第 23.3.2.36 节[Python 中的体系结构]，第 497 页。

[函数] Frame.type()
    返回帧的类型。值可以是以下之一：
        gdb.NORMAL_FRAME
            普通栈帧。
        gdb.DUMMY_FRAME
            gdb 在执行下级函数调用时创建的假栈帧。
        gdb.INLINE_FRAME
            表示内联函数的帧。该函数被内联到一个比此帧更旧的 gdb.NORMAL_FRAME 中。
        gdb.TAILCALL_FRAME
            表示尾调用的帧。见第 11.2 节[尾调用帧]，第 196 页。
        gdb.SIGTRAMP_FRAME
            信号跳板帧。这是操作系统在调用信号处理程序时创建的帧。
        gdb.ARCH_FRAME
            表示跨体系结构调用的假栈帧。
        gdb.SENTINEL_FRAME
            这类似于 gdb.NORMAL_FRAME，但仅用于最新的帧。

[函数] Frame.unwind_stop_reason()
    返回一个整数，表示无法找到更多帧朝向最外层帧的原因。使用 gdb.frame_stop_reason_string 将此函数返回的值转换为字符串。值可以是以下之一：
        gdb.FRAME_UNWIND_NO_REASON
            没有特别原因（应该可以访问更旧的帧）。
        gdb.FRAME_UNWIND_NULL_ID
            前一帧的分析器返回无效结果。gdb 不再使用此值，仅为了向后兼容而保留。
        gdb.FRAME_UNWIND_OUTERMOST
            此帧是最外层的。
        gdb.FRAME_UNWIND_UNAVAILABLE
            无法进一步展开，因为这需要知道尚未收集的寄存器或内存的值。
        gdb.FRAME_UNWIND_INNER_ID
            此帧 ID 看起来应该属于下一个帧，但我们是从前一个帧获得的。通常，这是展开器失败的迹象。也可能表示栈损坏。
        gdb.FRAME_UNWIND_SAME_ID
            此帧与前一帧具有相同的 ID。这意味着进一步展开几乎肯定会给我们另一个具有完全相同 ID 的帧，因此中断链。通常，这是展开器失败的迹象。也可能表示栈损坏。
        gdb.FRAME_UNWIND_NO_SAVED_PC
            帧展开器未找到任何保存的 PC，但我们需要一个来进一步展开。
        gdb.FRAME_UNWIND_MEMORY_ERROR
            帧展开器在尝试访问内存时导致错误。
        gdb.FRAME_UNWIND_FIRST_ERROR
            任何大于或等于此值的停止原因都表示某种错误。此特殊值有助于编写测试展开错误的代码，即使其他值的列表在未来的 gdb 版本中被修改，也能正确工作。使用它，你可以编写：
                reason = gdb.selected_frame().unwind_stop_reason()
                reason_str = gdb.frame_stop_reason_string(reason)
                if reason >= gdb.FRAME_UNWIND_FIRST_ERROR:
                    print("An error occurred: %s" % reason_str)

[函数] Frame.pc()
    返回帧的恢复地址。

[函数] Frame.block()
    返回帧的代码块。见第 23.3.2.29 节[Python 中的块]，第 482 页。如果帧没有块 - 例如，如果没有关于所讨论代码的调试信息 - 那么这将抛出异常。

[函数] Frame.function()
    返回与该帧对应的函数的符号。见第 23.3.2.30 节[Python 中的符号]，第 484 页。

[函数] Frame.older()
    返回调用此帧的帧。如果这是最旧的帧，则返回 None。

[函数] Frame.newer()
    返回此帧调用的帧。如果这是最新的帧，则返回 None。

[函数] Frame.find_sal()
    返回帧的符号表和行对象。见第 23.3.2.31 节[Python 中的符号表]，第 489 页。

[函数] Frame.read_register(register)
    返回此帧中寄存器的值。返回一个 Gdb.Value 对象。如果寄存器不存在，则抛出异常。寄存器参数必须是以下之一：
    1. 一个字符串，是有效的寄存器名称（例如，'sp' 或 'rax'）。
    2. 一个 gdb.RegisterDescriptor 对象（见第 23.3.2.37 节[Python 中的寄存器]，第 498 页）。
    3. 一个 gdb 内部的、特定于平台的数字。由于历史原因支持使用这些数字，但不建议这样做，因为未来对 gdb 的更改可能会改变数字与它们表示的寄存器之间的映射，从而破坏使用特定于平台的数字的任何 Python 代码。这些数字通常在 gdb 源代码树中的相应 platform-tdep.h 文件中找到。
    使用字符串访问寄存器比其他两种方法稍慢，因为 gdb 必须查找名称和内部寄存器编号之间的映射。如果性能至关重要，请考虑查找并缓存一个 gdb.RegisterDescriptor 对象。

[函数] Frame.read_var(variable[, block])
    返回此帧中变量的值。如果提供了可选参数 block，则从该块中搜索变量；否则从帧的当前块开始（由帧的当前程序计数器确定）。变量参数必须是一个字符串或一个 gdb.Symbol 对象；block 必须是一个 gdb.Block 对象。

[函数] Frame.select()
    将此帧设置为所选帧。见第 8 章[检查栈]，第 113 页。

[函数] Frame.static_link()
    在某些语言（例如 Ada，但也包括 GNU C 扩展）中，嵌套函数可以访问外部作用域中的变量。这是通过“静态链接”完成的，这是从嵌套帧到适当外部帧的引用。
    如果存在此帧的静态链接帧，则此方法返回该帧。如果没有静态链接，则此方法返回 None。

[函数] Frame.level()
    返回一个整数，此帧的栈帧级别。见第 8.1 节[栈帧]，第 113 页。

[函数] Frame.language()
    返回一个字符串，此帧的源语言。
```

#### 23.3.2.29 从 Python 访问块

在 gdb 中，符号存储在块中。一个块大致对应于源代码中的一个作用域。块是分层组织的，在 Python 中以`gdb.Block`的形式单独表示。块依赖于可用的调试信息。

一个框架有一个块。有关框架的更深入讨论，请参阅第 23.3.2.28 节[Python 中的框架]，第 479 页。

最外层的块称为全局块。全局块通常包含公共全局变量和函数。

嵌套在全局块内部的块是静态块。静态块通常包含文件作用域的变量和函数。

gdb 提供了一种获取块的超块的方法，但目前无法检查块的子块，也无法遍历符号表中的所有块（请参阅第 23.3.2.31 节[Python 中的符号表]，第 489 页）。

以下是一个简短的示例，应该有助于解释块：

```python
/* 这在全局块中。*/
int global;

/* 这在静态块中。*/
static int file_scope;

/* 'function' 在全局块中，'argument' 在 'function' 内部的一个块中。*/
int function (int argument)
{
    /* 'local' 在 'function' 内部的一个块中。它可能在也可能不在与 'argument' 相同的块中。*/
    int local;
    
    {
        /* 'inner' 在一个其超块是包含 'local' 的块中的块中。*/
        int inner;
        /* 如果此调用被编译器展开，您可能会在此处看到一个嵌套块，其函数为 'inline_function'，其超块是包含 'inner' 的块。*/
        inline_function ();
    }
}
```

一个`gdb.Block`是可迭代的。迭代器返回块中的本地符号（请参阅第 23.3.2.30 节[Python 中的符号]，第 484 页）。Python 程序不应假设特定的块对象将始终包含给定的符号，因为 gdb 功能和基础结构的更改可能导致符号在符号表中跨块移动。您还可以使用 Python 的字典语法来访问此块中的变量，例如：

```
symbol = some_block[’variable’] # symbol is of type gdb.Symbol
```

以下是 gdb 模块中可用的与块相关的函数：

```
[函数] gdb.block_for_pc (pc)
    返回包含给定 pc 值的最内层`gdb.Block`。如果找不到指定 pc 值的块，该函数将返回 None。这与 gdb.current_progspace().block_for_pc(pc)相同，是为了历史兼容性而包含的。
```

一个`gdb.Block`对象具有以下方法：

```
[函数] Block.is_valid ()
    如果`gdb.Block`对象有效，则返回 True，否则返回 False。如果它引用的块在下级中不再存在，块对象可能会变为无效。如果在调用该方法时它无效，所有其他`gdb.Block`方法都将抛出异常。在对块的符号进行迭代时也会检查块的有效性。
```

一个`gdb.Block`对象具有以下属性：

```
[变量] Block.start
    块的起始地址。此属性不可写。

[变量] Block.end
    块中出现的最后一个地址之后的地址。此属性不可写。

[变量] Block.function
    表示为`gdb.Symbol`的块的名称。如果块未命名，则此属性为 None。此属性不可写。
    对于普通函数块，超块是静态块。但是，您应该注意，函数块的超块可能不是静态块 - 例如，对于内联函数会发生这种情况。

[变量] Block.superblock
    包含此块的块。如果此父块不存在，则此属性为 None。此属性不可写。

[变量] Block.global_block
    与此块关联的全局块。此属性不可写。

[变量] Block.static_block
    与此块关联的静态块。此属性不可写。

[变量] Block.is_global
    如果`gdb.Block`对象是全局块，则为 True，否则为 False。此属性不可写。

[变量] Block.is_static
    如果`gdb.Block`对象是静态块，则为 True，否则为 False。此属性不可写。
```

#### 23.3.2.30 Python 中 Symbols 的表示

gdb 将每个变量、函数和类型表示为符号表中的一个条目。请参阅

第 16 章 [检查符号表]，第 263 页。类似地，Python 使用`gdb.Symbol`对象在 gdb 中表示这些符号。

在`gdb`模块中提供了以下与符号相关的函数：

```
[函数] `gdb.lookup_symbol(name [, block [, domain]])`
    此函数通过名称搜索符号。搜索范围可以限制为可选的`domain`和`block`参数中定义的参数。
    `name`是符号的名称。它必须是一个字符串。可选的`block`参数将搜索限制在该块中可见的符号。`block`参数必须是一个`gdb.Block`对象。如果省略，则使用当前帧的块。可选的`domain`参数将搜索限制在域类型中。`domain`参数必须是在`gdb`模块中定义并在本章后面描述的域常量。
    结果是一个包含两个元素的元组。第一个元素是`gdb.Symbol`对象或如果未找到符号则为`None`。如果找到符号，第二个元素如果符号是方法对象的字段（例如 C++中的`this`）则为`True`，否则为`False`。如果未找到符号，第二个元素为`False`。

[函数] `gdb.lookup_global_symbol(name [, domain])`
    此函数通过名称搜索全局符号。搜索范围可以通过`domain`参数进行限制。
    `name`是符号的名称。它必须是一个字符串。可选的`domain`参数将搜索限制在域类型中。`domain`参数必须是在`gdb`模块中定义并在本章后面描述的域常量。
    结果是一个`gdb.Symbol`对象或如果未找到符号则为`None`。

[函数] `gdb.lookup_static_symbol(name [, domain])`
    此函数通过名称搜索具有静态链接的全局符号。搜索范围可以通过`domain`参数进行限制。
    `name`是符号的名称。它必须是一个字符串。可选的`domain`参数将搜索限制在域类型中。`domain`参数必须是在`gdb`模块中定义并在本章后面描述的域常量。
    结果是一个`gdb.Symbol`对象或如果未找到符号则为`None`。
    请注意，此函数不会找到函数作用域的静态变量。要查找此类变量，请遍历函数的`gdb.Block`的变量，并检查`block.addr_class`是否为`gdb.SYMBOL_LOC_STATIC`。
    可能有多个具有相同名称的具有静态链接的全局符号。此函数将仅返回它找到的第一个匹配符号。找到哪个符号取决于 gdb 当前停止的位置，因为 gdb 将首先在当前目标文件中搜索匹配的符号，然后搜索所有其他目标文件。如果应用程序尚未运行，则 gdb 将按照它们在调试信息中出现的顺序搜索所有目标文件。

[函数] `gdb.lookup_static_symbols(name [, domain])`
    与`gdb.lookup_static_symbol`类似，此函数通过名称搜索具有静态链接的全局符号，并可选地通过`domain`参数进行限制。但是，此函数返回找到的所有匹配符号的列表，而不仅仅是第一个。
    `name`是符号的名称。它必须是一个字符串。可选的`domain`参数将搜索限制在域类型中。`domain`参数必须是在`gdb`模块中定义并在本章后面描述的域常量。
    结果是一个`gdb.Symbol`对象列表，如果未找到匹配符号，则可能为空。
    请注意，此函数不会找到函数作用域的静态变量。要查找此类变量，请遍历函数的`gdb.Block`的变量，并检查`block.addr_class`是否为`gdb.SYMBOL_LOC_STATIC`。
```

`gdb.Symbol`对象具有以下属性：

```
[变量]  `Symbol.type`
    符号的类型或如果未记录类型则为`None`。此属性表示为一个`gdb.Type`对象。请参阅第 23.3.2.5 节 [Python 中的类型]，第 418 页。此属性不可写。

[变量] `Symbol.symtab`
    符号出现的符号表。此属性表示为一个`gdb.Symtab`对象。请参阅第 23.3.2.31 节 [Python 中的符号表]，第 489 页。此属性不可写。

[变量] `Symbol.line`
    符号在源代码中定义的行号。这是一个整数。

[变量] `Symbol.name`
    符号的名称，作为一个字符串。此属性不可写。

[变量] `Symbol.linkage_name`
    链接器使用的符号名称（即可能被修饰）。此属性不可写。

[变量] `Symbol.print_name`
    适合输出的符号名称。这是`name`或`linkage_name`，具体取决于用户是否要求 gdb 显示去修饰或修饰的名称。

[变量] `Symbol.addr_class`
    符号的地址类。这对如何找到符号的值进行分类。每个地址类都是在`gdb`模块中定义并在本章后面描述的常量。

[变量] `Symbol.needs_frame`
    如果评估此符号的值需要一个帧（请参阅第 23.3.2.28 节 [Python 中的帧]，第 479 页），则为`True`，否则为`False`。通常，局部变量将需要一个帧，但其他符号则不需要。

[变量] `Symbol.is_argument`
    如果符号是函数的参数，则为`True`。

[变量] `Symbol.is_constant`
    如果符号是常量，则为`True`。

[变量] `Symbol.is_function`
    如果符号是函数或方法，则为`True`。

[变量] `Symbol.is_variable`
    如果符号是变量，而不是像函数或类型这样的东西，则为`True`。请注意，对于参数，此也返回`False`。
```

`gdb.Symbol`对象具有以下方法：

```
[函数]  `Symbol.is_valid()`
    如果`gdb.Symbol`对象有效，则返回`True`，否则返回`False`。如果`gdb.Symbol`对象所引用的符号在 gdb 中不再存在，则该对象可能变为无效。在调用方法时，如果该对象无效，所有其他`gdb.Symbol`方法都将抛出异常。

[函数] `Symbol.value([frame])`
    计算符号的值，作为一个`gdb.Value`。对于函数，这计算函数的地址，并转换为适当的类型。如果符号需要一个帧才能计算其值，则必须提供`frame`。如果未提供`frame`或`frame`无效，则此方法将抛出异常。
```
    
在`gdb.Symbol`中的可用域类别在`gdb`模块中表示为常量：

```
`gdb.SYMBOL_UNDEF_DOMAIN`
    当尚未发现域或以下域都不适用时使用。这通常表示符号信息或 gdb 对符号的处理中存在错误。

`gdb.SYMBOL_VAR_DOMAIN`
    此域包含变量。

`gdb.SYMBOL_FUNCTION_DOMAIN`
    此域包含函数。

`gdb.SYMBOL_TYPE_DOMAIN`
    此域包含类型。在类似 C 的语言中，使用标签（出现在`struct`、`union`或`enum`关键字之后的名称）的类型不会出现在此处；在其他语言中，所有类型都在此域中。

`gdb.SYMBOL_STRUCT_DOMAIN`
    此域包含`struct`、`union`和`enum`标签名称。此域仅用于类似 C 的语言。例如，在以下代码中：
        struct type_one { int x; };
        typedef struct type_one type_two;
    这里`type_one`将在`SYMBOL_STRUCT_DOMAIN`中，但`type_two`将在`SYMBOL_TYPE_DOMAIN`中。

`gdb.SYMBOL_LABEL_DOMAIN`
    此域包含标签（用于`gotos`）的名称。

`gdb.SYMBOL_MODULE_DOMAIN`
    此域包含 Fortran 模块类型的名称。

`gdb.SYMBOL_COMMON_BLOCK_DOMAIN`
    此域包含 Fortran 公共块的名称。
```

在搜索符号时，可以将所需的域常量按字面意思传递给查找函数。例如：

```
`symbol = gdb.lookup_symbol("name", domain=gdb.SYMBOL_VAR_DOMAIN)`
```

对于更复杂的搜索，有一组相应的常量，每个常量都以前面的常量之一命名，但用`SEARCH`前缀替换`SYMBOL`前缀；例如，`SEARCH_LABEL_DOMAIN`。这些可以或在一起以形成搜索常量，例如：

```
`symbol = gdb.lookup_symbol("name",
domain=gdb.SEARCH_VAR_DOMAIN | gdb.SEARCH_TYPE_DOMAIN)`
```

在`gdb.Symbol`中的可用地址类类别在`gdb`模块中表示为常量：

```
`gdb.SYMBOL_LOC_UNDEF`
    如果此地址类返回，则表示符号信息或 gdb 对符号的处理中存在错误。

`gdb.SYMBOL_LOC_CONST`
    值是常量整数。

`gdb.SYMBOL_LOC_STATIC`
    值位于固定地址。

`gdb.SYMBOL_LOC_REGISTER`
    值在寄存器中。

`gdb.SYMBOL_LOC_ARG`
    值是一个参数。此值位于帧的参数列表中符号内部存储的偏移量处。

`gdb.SYMBOL_LOC_REF_ARG`
    值的地址存储在帧的参数列表中。与`LOC_ARG`类似，只是值的地址存储在偏移量处，而不是值本身。

`gdb.SYMBOL_LOC_REGPARM_ADDR`
    值是一个指定的寄存器。与`LOC_REGISTER`类似，只是寄存器保存参数的地址而不是参数本身。

`gdb.SYMBOL_LOC_LOCAL`
    值是一个局部变量。

`gdb.SYMBOL_LOC_TYPEDEF`
    值未使用。`SYMBOL_STRUCT_DOMAIN`域中的符号都具有此类别。

`gdb.SYMBOL_LOC_LABEL`
    值是一个标签。

`gdb.SYMBOL_LOC_BLOCK`
    值是一个块。

`gdb.SYMBOL_LOC_CONST_BYTES`
    值是一个字节序列。

`gdb.SYMBOL_LOC_UNRESOLVED`
    值位于固定地址，但在引用变量时必须从最小符号表中确定变量的地址。

`gdb.SYMBOL_LOC_OPTIMIZED_OUT`
    该值实际上在程序中不存在。

`gdb.SYMBOL_LOC_COMPUTED`
    值的地址是一个计算位置。

`gdb.SYMBOL_LOC_COMMON_BLOCK`
    值的地址是一个符号。此仅用于 Fortran 公共块。
```

#### 23.3.2.31 在 Python 中的符号表表示

通过两个对象将由 inferior 上的 gdb 维护的符号表数据暴露给 Python：gdb.Symtab_and_line 和 gdb.Symtab。一个帧的符号表和行数据从 gdb.Frame 对象的 find_sal 方法返回。请参阅第 23.3.2.28 节[Python 中的帧]，第 479 页。

有关 gdb 的符号表管理的更多信息，请参阅第 16 章[检查符号表]，第 263 页，以获取更多信息。

一个 gdb.Symtab_and_line 对象具有以下属性：

```
[变量] Symtab_and_line.symtab
    此帧的符号表对象（gdb.Symtab）。此属性不可写。

[变量] Symtab_and_line.pc
    指示当前源行的代码占用的地址范围的开始。此属性不可写。

[变量] Symtab_and_line.last
    指示当前源行的代码占用的地址范围的结束。此属性不可写。

[变量] Symtab_and_line.line
    指示此对象的当前行号。此属性不可写。
```

一个 gdb.Symtab_and_line 对象具有以下方法：

```
[函数] Symtab_and_line.is_valid()
    如果 gdb.Symtab_and_line 对象有效，则返回 True，否则返回 False。如果它引用的符号表和行对象在 gdb 中不再存在，gdb.Symtab_and_line 对象可能会变为无效。如果在调用方法时它无效，所有其他 gdb.Symtab_and_line 方法将引发异常。
```

一个 gdb.Symtab 对象具有以下属性：

```
[变量] Symtab.filename
    符号表的源文件名。此属性不可写。

[变量] Symtab.objfile
    符号表的支持对象文件。请参阅第 23.3.2.27 节[Python 中的对象文件]，第 476 页。此属性不可写。

[变量] Symtab.producer
    编译符号表中的代码的程序的名称和可能的版本号。此字符串的内容由编译器决定。如果没有提供生产者信息，则返回 None。此属性不可写。
```

一个 gdb.Symtab 对象具有以下方法：

```
[函数] Symtab.is_valid()
    如果 gdb.Symtab 对象有效，则返回 True，否则返回 False。如果它引用的符号表在 gdb 中不再存在，gdb.Symtab 对象可能会变为无效。如果在调用方法时它无效，所有其他 gdb.Symtab 方法将引发异常。

[函数] Symtab.fullname()
    返回符号表的源绝对文件名。

[函数] Symtab.global_block()
    返回底层符号表的全局块。请参阅第 23.3.2.29 节[Python 中的块]，第 482 页。

[函数] Symtab.static_block()
    返回底层符号表的静态块。请参阅第 23.3.2.29 节[Python 中的块]，第 482 页。

[函数] Symtab.linetable()
    返回与符号表关联的行表。请参阅第 23.3.2.32 节[Python 中的行表]，第 490 页。
```

#### 23.3.2.32 使用 Python 操作行表

Python 代码可以从在 gdb 中加载的符号表中请求和检查行表信息。行表是源行到其在内存中的可执行位置的映射。要获取特定符号表的行表信息，请使用`linetable`函数（参见第 23.3.2.31 节[Python 中的符号表]，第 489 页）。

一个`gdb.LineTable`是可迭代的。迭代器返回`LineTableEntry`对象，这些对象对应于每个行表条目的源行和地址。`LineTableEntry`对象具有以下属性：

```
[变量] `LineTableEntry.line`
    此行表条目的源行号。此编号对应于实际的源代码行。此属性不可写。

[变量] `LineTableEntry.pc`
    与行表条目相关联的地址，该源行的可执行代码驻留在内存中。此属性不可写。
```

由于单个源行可能有多个地址，您可能会收到多个具有匹配行属性但具有不同`pc`属性的`LineTableEntry`对象。

迭代器按`pc`升序排序。以下是一个小示例，说明如何遍历行表。

```
symtab = gdb.selected_frame().find_sal().symtab
linetable = symtab.linetable()
for line in linetable:
    print ("Line: "+str(line.line)+" Address: "+hex(line.pc))
```

这将具有以下输出：

```
Line: 33 Address: 0x4005c8L
Line: 37 Address: 0x4005caL
Line: 39 Address: 0x4005d2L
Line: 40 Address: 0x4005f8L
Line: 42 Address: 0x4005ffL
Line: 44 Address: 0x400608L
Line: 42 Address: 0x40060cL
Line: 45 Address: 0x400615L
```

除了能够遍历`LineTable`之外，它还具有以下直接访问方法：

```
[函数] `LineTable.line (line)`
    对于给定的行（指定源代码行），返回行表中任何条目的`LineTableEntry`对象的 Python 元组。如果该源代码行没有条目，则返回 Python`None`。

[函数] `LineTable.has_line (line)`
    返回一个 Python 布尔值，指示该行表中是否有该源行的条目。如果找到条目，则返回`True`，否则返回`False`。

[函数] `LineTable.source_lines ()`
    返回符号表中的源行号的 Python 列表。仅返回具有可执行代码位置的行。列表的内容将只是表示为 Python 长整型值的源行条目。
```

#### 23.3.2.33 使用 Python 操作断点

Python 代码可以通过`gdb.Breakpoint`类来操作断点。

可以使用`gdb.Breakpoint`构造函数的两种形式之一来创建断点。第一种形式接受一个字符串，类似于传递给`break`（见第 5.1.1 节[设置断点]，第 58 页）和`watch`（见第 5.1.2 节[设置观察点]，第 66 页）命令的字符串，可以用于创建断点和观察点。第二种形式接受类似于第 9.2.2 节[显式位置]，第 129 页的单独的 Python 参数，并且只能用于创建断点。

```
[函数] `Breakpoint.__init__(spec [, type ][, wp class ][, internal ][, temporary ][, qualified ])`
    根据`spec`创建一个新的断点，`spec`是一个命名断点位置的字符串，或者是一个定义观察点的表达式。字符串应该以`break`命令（见第 5.1.1 节[设置断点]，第 58 页）或观察点的`watch`命令（见第 5.1.2 节[设置观察点]，第 66 页）所识别的格式描述一个位置。
    可选的`type`参数指定要创建的断点类型，如下定义。
    可选的`wp class`参数定义要创建的观察点的类，如果`type`是`gdb.BP_WATCHPOINT`。如果省略`wp class`，它默认值为`gdb.WP_WRITE`。
    可选的`internal`参数允许断点对用户不可见。断点在创建时不会被报告，也不会在`info breakpoints`的输出中列出（但会在`maint info breakpoints`命令中列出）。
    可选的`temporary`参数使断点成为临时断点。临时断点在被命中后将被删除。在断点被命中后对 Python 断点的任何进一步访问都将导致运行时错误（因为该断点现在已被自动删除）。
    可选的`qualified`参数是一个布尔值，允许将`spec`中传递的函数解释为完全限定名称。它等同于`break`的`-qualified`标志（见第 9.2.1 节[行规范位置]，第 127 页，和第 9.2.2 节[显式位置]，第 129 页）。

[函数] `Breakpoint.__init__([ source ][, function ][, label ][, line ], ][ internal ][, temporary ][, qualified ])`
    这种创建新断点的第二种形式使用关键字指定显式位置（见第 9.2.2 节[显式位置]，第 129 页）。新的断点将在指定的源文件`source`中，在指定的函数、标签和行处创建。
    `internal`、`temporary`和`qualified`的用法与前面解释的相同。

    可用的类型由`gdb`模块中定义的常量表示：
        `gdb.BP_BREAKPOINT`
            普通代码断点。
        `gdb.BP_HARDWARE_BREAKPOINT`
            硬件辅助代码断点。
        `gdb.BP_WATCHPOINT`
            观察点断点。
        `gdb.BP_HARDWARE_WATCHPOINT`
            硬件辅助观察点。
        `gdb.BP_READ_WATCHPOINT`
            硬件辅助读观察点。
        `gdb.BP_ACCESS_WATCHPOINT`
            硬件辅助访问观察点。
        `gdb.BP_CATCHPOINT`
            捕获点。目前，在创建`gdb.Breakpoint`对象时不能使用此类型，但会在从`gdb.BreakpointEvents`报告的`gdb.Breakpoint`对象中存在（见第 23.3.2.18 节[Python 中的事件]，第 451 页）。
    可用的观察点类型由`gdb`模块中定义的常量表示：
        `gdb.WP_READ`
            只读观察点。
        `gdb.WP_WRITE`
            只写观察点。
        `gdb.WP_ACCESS`
            读/写观察点。

[函数] `Breakpoint.stop(self)`
    `gdb.Breakpoint`类可以被子类化，特别是你可以选择实现`stop`方法。如果在`gdb.Breakpoint`的子类中定义了此方法，当下级到达实例化该子类的断点的任何位置时，将调用此方法。如果该方法返回`True`，下级将在断点的位置停止，否则下级将继续。
    如果在同一位置有多个带有`stop`方法的断点，每个断点都将被调用，而不管前一个的返回状态如何。这确保了所有的`stop`方法都有机会在该位置执行。在这种情况下，如果其中一个方法返回`True`，而其他方法返回`False`，下级仍将停止。
    你不应该改变下级的执行状态（即，单步执行、下一步等），改变当前帧上下文（即，更改当前活动帧），或改变、添加或删除任何断点。一般来说，此时你不应该改变`gdb`或下级中的任何数据。
    示例`stop`实现：
        class MyBreakpoint(gdb.Breakpoint):
            def stop(self):
                inf_val = gdb.parse_and_eval("foo")
                if inf_val == 3:
                    return True
                return False

[函数]`Breakpoint.is_valid()`
    如果此`Breakpoint`对象有效，则返回`True`，否则返回`False`。如果用户删除了断点，`Breakpoint`对象可能会变得无效。在这种情况下，对象仍然存在，但底层断点不存在。在观察点范围的情况下，即使下级的执行离开该观察点的范围，观察点仍然有效。

[函数]`Breakpoint.delete()`
    永久删除`gdb`断点。这也使 Python`Breakpoint`对象无效。对该对象的属性或方法的任何进一步访问都将引发错误。

[变量]`Breakpoint.enabled`
    如果断点已启用，则此属性为`True`，否则为`False`。此属性是可写的。你可以使用它来启用或禁用断点。

[变量]`Breakpoint.silent`
    如果断点是静默的，则此属性为`True`，否则为`False`。此属性是可写的。
    请注意，如果断点有命令并且第一个命令是静默的，断点也可以是静默的。此属性不会报告此情况。

[变量]`Breakpoint.pending`
    如果断点处于挂起状态，则此属性为`True`，否则为`False`。见第 5.1.1 节[设置断点]，第 58 页。此属性是只读的。

[变量]`Breakpoint.thread`
    如果断点是线程特定的（见第 5.5.4 节[线程特定断点]，第 96 页），此属性保存线程的全局 ID。如果断点不是线程特定的，此属性为`None`。此属性是可写的。
    在任何时候，只能将`Breakpoint.thread`或`Breakpoint.inferior`中的一个设置为有效的 ID，即，断点可以是线程特定的，也可以是下级特定的，但不能两者都是。

[变量]`Breakpoint.inferior`
    如果断点是下级特定的（见第 4.9.1 节[下级特定断点]，第 46 页），此属性保存下级的 ID。如果断点不是下级特定的，此属性为`None`。
    此属性可以为类型为`gdb.BP_BREAKPOINT`和`gdb.BP_HARDWARE_BREAKPOINT`的断点设置。

[变量]`Breakpoint.task`
    如果断点是 Ada 任务特定的，此属性保存 Ada 任务 ID。如果断点不是任务特定的（或底层语言不是 Ada），此属性为`None`。此属性是可写的。

[变量]`Breakpoint.ignore_count`
    此属性保存断点的忽略计数，一个整数。此属性是可写的。

[变量]`Breakpoint.number`
    此属性保存断点的编号——用户用于操作断点的标识符。此属性不可写。

[变量]`Breakpoint.type`
    此属性保存断点的类型——用于确定实际断点类型或用例的标识符。此属性不可写。

[变量]`Breakpoint.visible`
    此属性指示断点在设置时或运行`'info breakpoints'`命令时对用户是否可见。此属性不可写。

[变量]`Breakpoint.temporary`
    此属性指示断点是否作为临时断点创建。临时断点在被命中后将自动删除。在断点被命中后（因为它已被自动删除），对该属性以及除`is_valid`函数之外的所有其他属性和函数的访问都将导致错误。此属性不可写。

[变量]`Breakpoint.hit_count`
    此属性保存断点的命中计数，一个整数。此属性是可写的，但目前只能将其设置为零。

[变量]`Breakpoint.location`
    此属性保存断点的位置，由用户指定。它是一个字符串。如果断点没有位置（即，它是一个观察点），该属性的值为`None`。此属性不可写。

[变量]`Breakpoint.locations`
    获取为此断点插入的当前断点位置列表，元素类型为`gdb.BreakpointLocation`（如下所述）。此功能与`info breakpoint`命令（见第 5.1.1 节[设置断点]，第 58 页）匹配，因为它仅检索当前位置列表，因此返回的列表本身在后台不会更新。此属性不可写。

[变量]`Breakpoint.expression`
    此属性保存断点表达式，由用户指定。它是一个字符串。如果断点没有表达式（断点不是观察点），该属性的值为`None`。此属性不可写。

[变量]`Breakpoint.condition`
    此属性保存断点的条件，由用户指定。它是一个字符串。如果没有条件，此属性的值为`None`。此属性是可写的。

[变量]`Breakpoint.commands`
    此属性保存附加到断点的命令。如果有命令，此属性的值是一个包含所有命令的字符串，以换行符分隔。如果没有命令，此属性为`None`。此属性是可写的。
```

**断点位置**

断点位置是设置断点的实际位置之一，在 Python API 中由`gdb.BreakpointLocation`类型表示。此类型永远不会由用户直接实例化，而是从`Breakpoint.locations`中检索，`Breakpoint.locations`返回当前设置的断点位置列表。如果加载了新的符号文件或关闭了动态加载的库，断点位置可能会变得无效。访问已失效的断点位置的属性将引发`RuntimeError`异常。再次访问`Breakpoint.locations`属性以检索新的有效断点位置列表。

```
[变量]`BreakpointLocation.source`
    此属性返回设置此位置的源文件路径和行号。该属性的类型是字符串和长整数的元组。如果断点位置没有源位置，它返回`None`，对于观察点和捕获点就是这种情况。如果位置已失效，将引发`RuntimeError`异常。此属性不可写。

[变量]`BreakpointLocation.address`
    此属性返回设置此位置的地址。此属性的类型是长整数。如果位置已失效，将引发`RuntimeError`异常。此属性不可写。

[变量]`BreakpointLocation.enabled`
    此属性保存此位置是否已启用的值。此属性是可写的（布尔值）。如果位置已失效，将引发`RuntimeError`异常。

[变量]`BreakpointLocation.owner`
    此属性保存对`gdb.Breakpoint`所有者对象的引用，从该对象中检索此`gdb.BreakpointLocation`。如果位置已失效，将引发`RuntimeError`异常。此属性不可写。

[变量]`BreakpointLocation.function`
    此属性获取设置此位置的函数的名称。如果找不到函数，此属性返回`None`。如果位置已失效，将引发`RuntimeError`异常。此属性不可写。

[变量]`BreakpointLocation.fullname`
    此属性获取设置此位置的完整名称。如果找不到完整名称，此属性返回`None`。如果位置已失效，将引发`RuntimeError`异常。此属性不可写。

[变量] `BreakpointLocation.thread_groups`
    此属性获取设置它的线程组。它返回线程组 ID 的列表。如果位置已失效，这将引发`RuntimeError`异常。此属性不可写。
```

#### 23.3.2.34 完成断点

完成断点是基于`finish`命令在帧的返回地址设置的临时断点。`gdb.FinishBreakpoint`扩展了`gdb.Breakpoint`。当执行超出断点范围（即`Breakpoint.stop`或`FinishBreakpoint.out_of_scope`触发）时，底层断点将被禁用和删除。完成断点是线程特定的，必须在选择正确的线程时创建。

```
[函数] `FinishBreakpoint.__init__`([帧][, 内部])
    在`gdb.Frame`对象`frame`的返回地址处创建一个完成断点。
    如果未提供`frame`，则默认为最新的帧。可选的`internal`参数允许断点对用户不可见。有关此参数的更多详细信息，请参阅第 23.3.2.33 节[Python 中的断点]，第 491 页。

[函数] `FinishBreakpoint.out_of_scope`(self)
    在某些情况下（例如`longjmp`、C++异常、`gdb return`命令等），函数可能无法正确终止，因此永远不会命中完成断点。当`gdb`注意到这种情况时，`out_of_scope`回调将被触发。
    您可能希望子类化`gdb.FinishBreakpoint`并重写此方法：
        class MyFinishBreakpoint (gdb.FinishBreakpoint)
            def stop (self):
                print ("normal finish")
                return True
            def out_of_scope ():
                print ("abnormal finish")

[变量] `FinishBreakpoint.return_value`
    当`gdb`在完成断点处停止并且用于构建`gdb.FinishBreakpoint`对象的帧具有调试符号时，此属性将包含一个与函数返回值对应的`gdb.Value`对象。如果函数返回类型为`void`或返回值不可计算，则该值将为`None`。此属性不可写。
```

#### 23.3.2.35 Python 中惰性字符串的表示

惰性字符串是一种其内容在需要之前不会被检索或编码的字符串。

在 gdb 中，`gdb.LazyString`被表示为一个指向内存区域的地址、用于编码该内存区域的编码以及用于界定表示字符串的内存区域的长度。`gdb.LazyString`与包装在`gdb.Value`中的字符串之间的区别在于，`gdb.LazyString`仅在打印时才会被检索和编码，而包装字符串的`gdb.Value`在创建时立即被检索和编码。

可以使用`gdb.Value.lazy_string`方法创建`gdb.LazyString`（见第 23.3.2.4 节[来自 inferior 的值]，第 411 页）。

`gdb.LazyString`对象具有以下函数：

```
[函数] `LazyString.value()`
    将`gdb.LazyString`转换为`gdb.Value`。此值将指向内存中的字符串，但会丢失 gdb 应用于`gdb.LazyString`的所有延迟检索、编码和处理。

[变量] `LazyString.address`
    此属性保存字符串的地址。此属性不可写。

[变量] `LazyString.length`
    此属性保存字符串的字符长度。如果长度为 -1，则将获取并编码字符串，直到遇到适当宽度的第一个空字符。此属性不可写。

[变量] `LazyString.encoding`
    此属性保存在 gdb 打印字符串时将应用于字符串的编码。如果未设置编码或包含空字符串，则在打印字符串时 gdb 将选择最合适的编码。此属性不可写。

[变量] `LazyString.type`
    此属性保存由惰性字符串的类型表示的类型。对于惰性字符串，这是一个指针或数组类型。要将其解析为惰性字符串的字符类型，请使用类型的`target`方法。见第 23.3.2.5 节[Python 中的类型]，第 418 页。此属性不可写。
```

#### 23.3.2.36 架构的 Python 表示

`gdb`在其许多不同的计算中使用特定于架构的参数和工件。架构由`gdb.Architecture`类的实例表示。

`gdb.Architecture`类具有以下方法：

```
[函数] `Architecture.name()`
    返回架构的名称（字符串值）。

[函数] `Architecture.disassemble(start pc [, end pc [, count]])`
    从内存地址`start pc`开始返回反汇编指令的列表。
    可选参数`end pc`和`count`确定返回列表中的指令数量。如果同时指定了可选参数`end pc`和`count`，则返回最多`count`条反汇编指令的列表，其起始地址在从`start pc`到`end pc`的封闭内存地址区间内。如果未指定`end pc`但指定了`count`，则返回从地址`start pc`开始的`count`条指令。如果未指定`count`但指定了`end pc`，则返回所有起始地址在从`start pc`到`end pc`的封闭内存地址区间内的指令。如果既未指定`end pc`也未指定`count`，则返回`start pc`处的一条指令。对于所有这些情况，返回列表的每个元素都是一个具有以下字符串键的 Python 字典：
        `addr` 与此键对应的值是一个 Python 长整数，捕获指令的内存地址。
        `asm` 与此键对应的值是一个字符串值，用汇编语言助记符表示指令。使用的汇编语言风格与当前`CLI`变量`disassembly-flavor`指定的相同。请参阅第 9.6 节[机器代码]，第 135 页。
        `length` 与此键对应的值是指令的长度（整数值），以字节为单位。

[函数] `Architecture.integer_type(size [, signed])`
    此函数根据其大小查找整数类型，以及可选的是否为有符号。
    `size`是所需整数类型的大小，以位为单位。目前仅支持某些大小：0、8、16、24、32、64 和 128。
    如果未指定`signed`，则默认为`True`。如果`signed`为`False`，则返回的类型将为无符号。
    如果找不到指示的类型，此函数将引发`ValueError`异常。

[函数] `Architecture.registers([reggroup])`
    返回`reggroup`中所有寄存器的`gdb.RegisterDescriptorIterator`（请参阅第 23.3.2.37 节[Python 中的寄存器]，第 498 页），`reggroup`是一个寄存器组的名称字符串。如果省略`reggroup`或为空字符串，则假定为寄存器组“all”。

[函数] `Architecture.register_groups()`
    返回`gdb.Architecture`可用的所有寄存器组的`gdb.RegisterGroupsIterator`（请参阅第 23.3.2.37 节[Python 中的寄存器]，第 498 页）。
```

#### 23.3.2.37 Python 中的寄存器

Python 代码可以从`gdb.Architecture`请求有关可用寄存器集的信息（见[Architecture.registers]，第 498 页）。寄存器信息作为`gdb.RegisterDescriptorIterator`返回，这是一个迭代器，它依次返回`gdb.RegisterDescriptor`对象。

一个`gdb.RegisterDescriptor`不提供寄存器的值（见[Frame.read_register]，第 482 页，用于读取寄存器的值），而是一种发现特定体系结构可用寄存器的方式。

一个`gdb.RegisterDescriptor`具有以下只读属性：

```
[变量] `RegisterDescriptor.name`
    此寄存器的名称。
```

也可以使用以下`gdb.RegisterDescriptorIterator`函数根据名称查找寄存器描述符：

```
[函数] `RegisterDescriptorIterator.find(name)`
    将`name`作为参数，必须是一个字符串，并返回具有该名称的寄存器的`gdb.RegisterDescriptor`，如果没有具有该名称的寄存器，则返回`None`。
```

Python 代码还可以从`gdb.Architecture`请求有关给定体系结构上可用的寄存器组集的信息（见[Architecture.register_groups]，第 498 页）。

每个寄存器可以是零个或多个寄存器组的成员。一些寄存器组在`gdb`内部用于控制诸如在调用正在调试的程序时必须保存哪些寄存器等事情（见第 17.5 节[调用程序函数]，第 281 页）。其他寄存器组的存在是为了允许用户在`info registers`等命令中轻松查看相关的寄存器集（见[info registers reggroup]，第 179 页）。

寄存器组信息作为`gdb.RegisterGroupsIterator`返回，这是一个迭代器，它依次返回`gdb.RegisterGroup`对象。

一个`gdb.RegisterGroup`对象具有以下只读属性：

```
[变量] `RegisterGroup.name`
    一个字符串，是此寄存器组的名称。
```

#### 23.3.2.38 Python 中的连接

gdb 允许你在单个会话中运行和调试多个程序。每个被调试的程序都有一个连接，该连接描述了 gdb 如何控制被调试的程序。不同连接类型的示例有“本地”和“远程”。请参阅第 4.9 节[下属连接和程序]，第 42 页。

gdb 中的连接表示为 gdb.TargetConnection 的实例，或者是其一个子类。要获取所有连接的列表，请使用 gdb.connections（请参阅[gdb.connections]，第 408 页）。

要获取单个 gdb.Inferior 的连接，请读取其 gdb.Inferior.connection 属性（请参阅[gdb.Inferior.connection]，第 449 页）。

目前，gdb.TargetConnection 只有一个子类，即 gdb.RemoteTargetConnection，然而，在未来的 gdb 版本中可能会添加其他子类。因此，你应该避免编写如下代码：

```python
conn = gdb.selected_inferior().connection
if type(conn) is gdb.RemoteTargetConnection:
    print("This is a remote target connection")
```

因为当添加更多连接类型时，此代码可能会失败。相反，你应该编写：

```python
conn = gdb.selected_inferior().connection
if isinstance(conn, gdb.RemoteTargetConnection):
    print("This is a remote target connection")
```

gdb.TargetConnection 具有以下方法：

```
[函数] TargetConnection.is_valid()
    如果 gdb.TargetConnection 对象有效，则返回 True，否则返回 False。如果连接在 gdb 中不再存在，gdb.TargetConnection 将变为无效，这可能在没有下属使用该连接时发生，但可能会延迟到用户替换当前目标时。
    如果连接无效，读取任何 gdb.TargetConnection 属性都将引发异常。
```

gdb.TargetConnection 具有以下只读属性：

```
[变量] TargetConnection.num
    gdb 分配的一个整数，用于唯一标识此连接。这与 info connections 命令输出的“Num”列中显示的值相同（请参阅第 4.9 节[info connections]，第 42 页）。

[变量] TargetConnection.type
    一个描述此连接类型的字符串。此字符串将是可以传递给 target 命令的有效名称之一（请参阅第 19.2 节[target 命令]，第 311 页）。

[变量] TargetConnection.description
    一个给出此目标类型简短描述的字符串。这与 info connection 命令输出的“Description”列中显示的字符串相同（请参阅第 4.9 节[info connections]，第 42 页）。

[变量] TargetConnection.details
    一个可选的字符串，提供有关此连接的其他信息。如果此连接没有其他详细信息，则此属性可以为 None。
    可能具有其他详细信息的连接类型的一个示例是“远程”连接，在这种情况下，详细信息字符串可以包含用于连接到远程目标的“hostname:port”。
```

gdb.RemoteTargetConnection 类是 gdb.TargetConnection 的子类，用于表示“远程”和“扩展远程”连接。除了从 gdb.TargetConnection 基类可用的属性和方法之外，gdb.RemoteTargetConnection 具有以下方法：

```
[函数] RemoteTargetConnection.send_packet(packet)
    此方法将 packet 发送到远程目标并返回响应。packet 应该是一个字节对象或一个 Unicode 字符串。
    如果 packet 是一个 Unicode 字符串，则使用 ascii 编解码器将字符串编码为字节对象。如果字符串无法编码，则会引发 UnicodeError。
    如果 packet 不是字节对象或 Unicode 字符串，则会引发 TypeError。如果 packet 为空，则会引发 ValueError。
    响应以字节对象形式返回。如果知道响应可以表示为字符串，则可以从缓冲区中解码。例如，如果知道响应是一个 ascii 字符串：
    remote_connection.send_packet("some_packet").decode("ascii")
    前缀、后缀和校验和（远程串行协议所需）将自动添加到传出数据包中，并在返回回复内容之前从传入数据包中删除。
    这等效于维护数据包命令（请参阅[maint packet]，第 761 页）。
```

#### 23.3.2.39 实现新的 TUI 窗口

新的 TUI（见第 25 章[TUI]，第 581 页）窗口可以在 Python 中实现。

```
[函数]gdb.register_window_type(name, factory)
    由于 TUI 窗口是根据用户选择的布局创建和销毁的，因此通过向 gdb 注册一个工厂函数来实现新的窗口类型。
    name 是新窗口的名称。尝试替换内置窗口之一是错误的，但可以替换其他窗口类型。名称应与正则表达式 [a-zA-Z][-_.a-zA-Z0-9]* 匹配，尝试创建具有无效名称的窗口是错误的。
    function 是一个工厂函数，用于创建 TUI 窗口。它被调用时带有一个类型为 gdb.TuiWindow 的单个参数，如下所述。它应该返回一个实现 TUI 窗口协议的对象，也在下面进行了描述。
```

如前所述，当调用工厂函数时，它会传递一个类型为 gdb.TuiWindow 的对象。这个对象有以下方法和属性：

```
[函数]TuiWindow.is_valid()
    当此窗口有效时，此方法返回 True。当用户更改 TUI 布局时，在新布局中不再可见的窗口将被销毁。此时，gdb.TuiWindow 将不再有效，除 is_valid 之外的方法（和属性）将引发异常。
    当使用`tui disable`（见第 25.5 节[tui disable]，第 584 页）禁用 TUI 时，窗口将被隐藏而不是销毁，但 is_valid 仍将返回 False，其他方法（和属性）仍将引发异常。

[变量]TuiWindow.width
    此属性保存窗口的宽度。它是不可写的。

[变量]TuiWindow.height
    此属性保存窗口的高度。它是不可写的。

[变量]TuiWindow.title
    此属性保存窗口的标题，一个字符串。通常在窗口上方显示。此属性可以修改。

[函数]TuiWindow.erase()
    删除窗口的所有内容。

[函数]TuiWindow.write(string [, full window])
    将字符串写入窗口。string 可以包含 ANSI 终端转义样式序列；gdb 将根据终端的情况适当地进行翻译。 
    如果 full window 参数为 True，则 string 包含窗口的全部内容。这类似于在写入之前调用 erase，但可以避免闪烁。
```

您提供的工厂函数应该返回一个符合 TUI 窗口协议的对象。这些是可以在此对象上调用的方法，在下面称为“窗口对象”。下面记录的方法是可选的；如果对象未实现这些方法之一，gdb 将不会尝试调用它。将来可能会向窗口协议中添加其他新方法。gdb 保证它们将以小写字母开头，因此您可以使用大写字母或下划线开始实现方法，以避免将来的任何冲突。

```
[函数]Window.close()
    当 TUI 窗口关闭时，gdb.TuiWindow 对象将进入无效状态。此时，gdb 将在窗口对象上调用 close 方法。
    此方法调用后，gdb 将丢弃它在该窗口对象上持有的任何引用，并且不再在此对象上调用方法。

[函数]Window.render()
    在某些情况下，TUI 窗口的大小可能会改变。例如，如果用户调整终端大小或更改布局，就会发生这种情况。发生这种情况时，gdb 将在窗口对象上调用 render 方法。
    如果您的窗口旨在响应 inferior 的更改而更新，您可能还希望注册事件监听器并将输出发送到 gdb.TuiWindow。

[函数]Window.hscroll(num)
    这是一个水平滚动窗口的请求。num 是要滚动的数量，负数表示向右滚动。在 TUI 模型中，移动的是视口，而不是内容。正参数应导致视口向右移动，因此内容应向左移动。

[函数]Window.vscroll(num)
    这是一个垂直滚动窗口的请求。num 是要滚动的数量，负数表示向后滚动。在 TUI 模型中，移动的是视口，而不是内容。正参数应导致视口向下移动，因此内容应向上移动。

[函数]Window.click(x, y, button)
    在这个窗口中点击鼠标时会调用此函数。x 和 y 是窗口内的鼠标坐标（基于 0，从左上角开始），button 指定使用了哪个鼠标按钮，其值可以是 1（左键）、2（中键）或 3（右键）。
    当通过关闭`tui mouse-events`设置（见[设置 tui mouse-events]，第 588 页）禁用 TUI 鼠标事件时，click 将不会被调用。
```

#### 23.3.2.40指令反汇编（在 Python 中）

`gdb` 的内置反汇编器可以使用 Python API 进行扩展甚至替换。与反汇编相关的功能包含在`gdb.disassembler`模块中：

```
[类] `gdb.disassembler.DisassembleInfo`
    反汇编由此类的实例驱动。每次`gdb`需要反汇编一条指令时，都会创建此类的一个实例并将其传递给注册的反汇编器。反汇编器负责反汇编一条指令并返回结果。
    此类的实例通常在`gdb`内部创建，但是也可以创建此类实例的副本，更多细节请参见`__init__`的描述。
    此类具有以下属性和方法：
        [变量] `DisassembleInfo.address`
            一个只读整数，包含`gdb`希望反汇编的单个指令的地址。
        [变量] `DisassembleInfo.architecture`
            当前正在反汇编的`gdb.Architecture`（参见第 23.3.2.36 节[Python 中的体系结构]，第 497 页），此属性为只读。
        [变量] `DisassembleInfo.progspace`
            当前正在反汇编的`gdb.Progspace`（参见第 23.3.2.26 节[Python 中的程序空间]，第 474 页），此属性为只读。
        [函数] `DisassembleInfo.is_valid()`
            如果`DisassembleInfo`对象有效，则返回`True`，否则返回`False`。一旦创建`DisassembleInfo`的反汇编调用返回，`DisassembleInfo`对象将变为无效。如果`DisassembleInfo`无效，调用其他`DisassembleInfo`方法或访问`DisassembleInfo`属性将引发`RuntimeError`异常。
        [函数] `DisassembleInfo.__init__(info)`
            可用于创建一个新的`DisassembleInfo`对象，该对象是`info`的副本。副本将具有与`info`相同的地址、体系结构和程序空间值，并在与`info`相同的时间变为无效。
            此方法的存在是为了可以创建`DisassembleInfo`的子类，这些子类必须初始化为现有`DisassembleInfo`对象的副本，但子类可能选择重写`read_memory`方法，从而控制`gdb`在从内存读取时看到的内容（参见[内置反汇编]，第 510 页）。
        [函数] `DisassembleInfo.read_memory(length, offset)`
            此方法允许反汇编器读取要反汇编的指令的字节。该方法从`DisassembleInfo.address`的偏移量开始读取`length`字节。
            重要的是，反汇编器应使用此方法读取指令字节，而不是直接读取 inferior 内存，因为在某些情况下，`gdb`从内部缓冲区而不是直接从 inferior 内存进行反汇编，调用此方法可处理此细节。
            返回一个缓冲区对象，其行为类似于数组或字符串，就像`Inferior.read_memory`一样（参见[Inferior.read memory]，第 450 页）。返回的缓冲区的长度始终恰好为`length`。
            如果`gdb`无法读取所需的内存，则会引发`gdb.MemoryError`异常（参见第 23.3.2.3 节[异常处理]，第 410 页）。
            子类可以重写此方法以控制`gdb`在从内存读取时看到的内容（参见[内置反汇编]，第 510 页）。在重写此方法时，重要的是要理解`builtin_disassemble`如何使用此方法。
            在反汇编单个指令时，可能会对该方法进行多次调用，并且可能会多次读取相同的字节。任何单个调用可能仅读取整个指令字节的一个子集。
            如果`read_memory`的实现无法读取请求的内存内容，例如，请求从无效的内存地址读取，则应引发`gdb.MemoryError`。
            在`read_memory`内部引发`MemoryError`并不自动意味着`builtin_disassemble`将引发`MemoryError`。`gdb`的内置反汇编器可能会探测以查看有多少字节可用。当`read_memory`引发`MemoryError`时，内置反汇编器可能能够使用其可用的字节执行完整的反汇编，在这种情况下，`builtin_disassemble`本身不会引发`MemoryError`。
            `read_memory`中引发的任何其他异常类型将传播回来并由`builtin_disassemble`重新引发。
        [函数] `DisassembleInfo.text_part(style, string)`
            创建一个新的`DisassemblerTextPart`，表示反汇编指令的一部分。`string`应为非空字符串，`style`应为适当的样式常量（参见[反汇编器样式常量]，第 507 页）。
            在创建`DisassemblerResult`以表示指令中的样式时使用反汇编器部分（参见[DisassemblerResult 类]，第 505 页）。
        [函数] `DisassembleInfo.address_part(address)`
            创建一个新的`DisassemblerAddressPart`。`address`是此部分表示的绝对地址的值。`DisassemblerAddressPart`显示为绝对地址和相关符号，地址和符号的样式适当。

[类] `gdb.disassembler.Disassembler`
    这是所有用户实现的反汇编器必须继承的基类。
        [函数] `Disassembler.__init__(name)`
            构造函数接受`name`，一个字符串，应为此反汇编器的简短名称。
        [函数] `Disassembler.__call__(info)`
            子类必须重写`__call__`方法以执行反汇编。在基类上调用`__call__`将引发`NotImplementedError`异常。
            `info`参数是`DisassembleInfo`的实例，描述了`gdb`要反汇编的指令。
            如果此函数返回`None`，则向`gdb`指示此子类不希望反汇编请求的指令。`gdb`将然后使用其内置反汇编器执行反汇编。
            或者，此函数可以返回一个表示反汇编指令的`DisassemblerResult`，此类型在下面有更详细的描述。
            `__call__`方法可以引发`gdb.MemoryError`异常（参见第 23.3.2.3 节[异常处理]，第 410 页），以向`gdb`指示访问所需内存存在问题，然后`gdb`将在反汇编输出中显示此问题。
            理想情况下，调用`__call__`的唯一三种结果将是返回`None`、在`DisassemblerResult`中返回成功的反汇编或指示读取内存存在问题的`MemoryError`。
            然而，由于`__call__`的实现可能由于其他原因而失败，例如执行反汇编所需的某些外部资源暂时不可用，那么如果`__call__`引发`GdbError`，异常将转换为字符串并在反汇编输出的末尾打印，反汇编请求将停止。
            `__call__`方法引发的任何其他异常类型都被视为用户代码中的错误，异常将根据设置的`python print-stack`设置打印到错误流中（参见[设置`python print-stack]，第 402 页）。

[类] `gdb.disassembler.DisassemblerResult`
    此类表示反汇编单个指令的结果。此类的实例将从`builtin_disassemble`返回（参见[内置反汇编]，第 510 页），如果成功反汇编了一条指令，应从`Disassembler.__call__`返回此类的实例（参见[反汇编器类]，第 504 页）。
    无法对`DisassemblerResult`类进行子类化。
    `DisassemblerResult`类具有以下属性和方法：
        [函数] `DisassemblerResult.__init__(length, string, parts)`
            初始化此类的实例，`length`是以字节为单位的反汇编指令的长度，必须大于零。
            只能使用`string`或`parts`之一来初始化新的`DisassemblerResult`；另一个应传递`None`。
            或者，可以按名称传递参数，并忽略未使用的参数。
            `string`参数（如果不是`None`）是一个非空字符串，表示整个反汇编指令。使用`string`参数构建结果对象不允许在结果中包含任何样式信息。
            `gdb`将结果样式化为具有`STYLE_TEXT`样式的单个`DisassemblerTextPart`（参见[反汇编器样式部分]，第 506 页）。
            `parts`参数（如果不是`None`）是一个非空的`DisassemblerPart`对象序列。每个部分表示反汇编指令的一小部分以及该部分应如何样式化的信息。使用`parts`构建的结果对象可以通过`gdb`以完整的样式信息显示（参见[设置样式反汇编启用]，第 371 页）。
        [变量] `DisassemblerResult.length`
            一个只读属性，包含以字节为单位的反汇编指令的长度，此长度始终大于零。
        [变量] `DisassemblerResult.string`
            一个只读属性，包含表示反汇编指令的非空字符串。该字符串是反汇编指令的表示，不包含任何样式信息。要查看指令的样式，请使用`parts`属性。
            如果此实例是使用单独的`DisassemblerPart`对象初始化的，`string`属性仍然有效。字符串值是通过连接每个组件部分的`DisassemblerPart.string`值创建的（参见[反汇编器样式部分]，第 506 页）。
        [变量] `DisassemblerResult.parts`
            一个只读属性，包含一个非空的`DisassemblerPart`对象序列。每个`DisassemblerPart`对象包含指令的一小部分以及关于该部分应如何样式化的信息。`gdb`使用此信息创建样式化的反汇编输出（参见[设置样式反汇编启用]，第 371 页）。
            如果此实例是使用单个字符串而不是`DisassemblerPart`对象序列初始化的，`parts`属性仍然有效。在这种情况下，`parts`属性将包含一个包含单个`DisassemblerTextPart`对象的序列，其字符串将表示整个指令，其样式将为`STYLE_TEXT`。

[类] `gdb.disassembler.DisassemblerPart`
    这是不同部分子类继承的父类。仅以下详细说明的子类的实例将由 Python API 返回。
    无法直接创建此父类或以下列出的任何子类的实例。以下列出的子类的实例是通过调用`builtin_disassemble`（参见[内置反汇编]，第 510 页）并在`DisassemblerResult`对象中返回，或者可以通过调用`DisassembleInfo`类上的`text_part`和`address_part`方法创建（参见[DisassembleInfo 类]，第 503 页）。
    `DisassemblerPart`类具有一个属性：
        [变量] `DisassemblerPart.string`
            一个只读属性，包含定义的样式常量之一。gdb 在为反汇编指令的这一部分设置样式时将使用此样式（请参阅[反汇编器样式常量]，第 507 页）。

[类] gdb.disassembler.DisassemblerAddressPart
    `DisassemblerAddressPart` 类表示反汇编指令中的绝对地址。使用 `DisassemblerAddressPart` 而不是具有 `STYLE_ADDRESS` 的 `DisassemblerTextPart` 是首选的，gdb 将把地址显示为绝对地址，并查找一个合适的符号显示在地址旁边。使用 `DisassemblerAddressPart` 还可以确保尊重诸如 `set print max-symbolic-offset` 之类的用户设置 
    诸如`set print max-symbolic-offset`之类的设置将被尊重。
    这是一个 x86 - 64 指令的示例：
        `call 0x401136 <foo>`
    在这个指令中，`0x401136 <foo>`是由单个`DisassemblerAddressPart`生成的。`0x401136`将使用`STYLE_ADDRESS`样式进行样式设置，而`foo`将使用`STYLE_SYMBOL`样式进行样式设置。`<`和`>`将被样式化为`STYLE_TEXT`。
    如果不需要包含符号名称，则可以使用具有`STYLE_ADDRESS`样式的`DisassemblerTextPart`。
    此类的实例不能直接创建，而是调用`DisassembleInfo.address_part`来创建此类的新实例（请参阅[DisassembleInfo 类]，第 503 页）。
    除了其父类的属性之外，`DisassemblerAddressPart`还具有以下附加属性：
        [变量] `DisassemblerAddressPart.address`
            一个只读属性，包含传递给此对象的`__init__`方法的地址。
```

以下表格列出了所有可用的反汇编器样式。gdb 将这些样式常量映射到其样式设置（请参阅第 22.5 节[输出样式]，第 371 页）。在某些情况下，几个样式常量会产生相同的样式设置，因此在屏幕上会产生相同的视觉效果。这在 gdb 的未来版本中可能会有所变化，因此应注意选择正确的样式常量，以确保在 gdb 的未来版本中正确的输出样式。

```
`gdb.disassembler.STYLE_TEXT`
    这是 gdb 在对反汇编器输出进行样式设置时使用的默认样式。对于指令中不适合以下任何其他样式的任何部分，都应使用此样式。gdb 使用其默认样式对此样式的文本进行样式设置。

`gdb.disassembler.STYLE_MNEMONIC`
    此样式用于对主要指令助记符进行样式设置，该助记符通常出现在反汇编指令字符串的开头或附近。
    gdb 使用反汇编器助记符样式设置对此样式的文本进行样式设置。

`gdb.disassembler.STYLE_SUB_MNEMONIC`
    此样式用于对反汇编指令中的任何子助记符进行样式设置。子助记符是指令中控制指令功能的任何文本，但与主要助记符（将具有`STYLE_MNEMONIC`样式）不相交。
    例如，考虑此 AArch64 指令：
        `add w16, w7, w1, lsl #1`
    `add`是主要指令助记符，将被赋予`STYLE_MNEMONIC`样式，而`lsl`是子助记符，将被赋予`STYLE_SUB_MNEMONIC`样式。
    gdb 使用反汇编器助记符样式设置对此样式的文本进行样式设置。

`gdb.disassembler.STYLE_ASSEMBLER_DIRECTIVE`
    有时，一系列字节无法解码为有效的指令。在这种情况下，反汇编器可能会选择使用汇编指令来表示反汇编的结果，例如：
        `.word 0x1234`
    在这种情况下，`.word`将被赋予`STYLE_ASSEMBLER_DIRECTIVE`样式。
    汇编指令在许多方面类似于助记符，但它不是体系结构指令集的一部分。
    gdb 使用反汇编器助记符样式设置对此样式的文本进行样式设置。

`gdb.disassembler.STYLE_REGISTER`
    此样式用于对反汇编指令中表示寄存器名称或寄存器编号的任何文本进行样式设置。
    gdb 使用反汇编器寄存器样式设置对此样式的文本进行样式设置。

`gdb.disassembler.STYLE_ADDRESS`
    此样式用于对反汇编指令中表示绝对地址的数值进行样式设置。
    当创建具有此样式的`DisassemblerTextPart`时，应考虑`DisassemblerAddressPart`是否更合适。有关每个部分提供的内容的说明，请参阅[反汇编器样式部分]，第 506 页。
    gdb 使用反汇编器地址样式设置对此样式的文本进行样式设置。

`gdb.disassembler.STYLE_ADDRESS_OFFSET`
    此样式用于对反汇编指令中表示地址偏移量的数值进行样式设置。当指令本身要访问内存，并且该值用于偏移要访问的地址时，该值被视为地址偏移量。
    例如，一种体系结构可能有一条使用寄存器中的地址从内存加载的指令。如果该指令还允许将立即偏移量编码到指令中，这将是一个地址偏移量。类似地，分支指令可能跳转到寄存器中的地址加上编码到指令中的地址偏移量。
    gdb 使用反汇编器立即样式设置对此样式的文本进行样式设置。

`gdb.disassembler.STYLE_IMMEDIATE`
    对于反汇编指令中的任何数值，如果这些值不是地址、地址偏移量或寄存器编号（在这些情况下可以使用`STYLE_ADDRESS`、`STYLE_ADDRESS_OFFSET`或`STYLE_REGISTER`样式），则使用`STYLE_IMMEDIATE`。
    gdb 使用反汇编器立即样式设置对此样式的文本进行样式设置。

`gdb.disassembler.STYLE_SYMBOL`
    此样式用于对包含在反汇编指令中的符号的文本名称进行样式设置。符号名称通常与反汇编指令中的绝对地址相邻包含，以使用户更容易理解该地址所指的内容。例如：
        `call 0x401136 <foo>`
    这里`foo`是一个符号的名称，应被赋予`STYLE_SYMBOL`样式。
    像这样在绝对地址旁边添加符号是由`DisassemblerAddressPart`类自动处理的（请参阅[反汇编器样式部分]，第 506 页）。
    gdb 使用反汇编器符号样式设置对此样式的文本进行样式设置。

`gdb.disassembler.STYLE_COMMENT_START`
    此样式用于在反汇编输出中开始行注释。与其他仅应用于应用它们的单个`DisassemblerTextPiece`的样式不同，注释样式是粘性的，并覆盖此指令中任何其他部分的样式。
    这意味着，在看到`STYLE_COMMENT_START`部分之后，gdb 将应用注释样式，直到行结束，忽略部分中的特定样式。
    gdb 使用反汇编器注释样式设置对此样式的文本进行样式设置。
```
    
以下函数也包含在`gdb.disassembler`模块中：

```
[函数] `register_disassembler (disassembler, architecture)`
    反汇编器必须是`gdb.disassembler.Disassembler`的子类或`None`。
    可选的架构可以是字符串或`None`。如果是字符串，则应该是 gdb 已知的架构的名称，可通过`gdb.Architecture.name`（请参阅[gdb.Architecture.name]，第 497 页）或`gdb.architecture_names`（请参阅[gdb.architecture names]，第 408 页）返回。
    反汇编器将为架构名称`architecture`安装，或者如果`architecture`为`None`，则反汇编器将作为全局反汇编器安装，供所有架构使用。
    gdb 只为每个架构记录一个反汇编器和一个全局反汇编器。为架构或全局反汇编器调用`register_disassembler`将替换为此架构值注册的任何现有反汇编器。返回以前的反汇编器。
    如果`disassembler`为`None`，则将注销当前为架构注册的任何反汇编器并返回。
    当 gdb 寻找要使用的反汇编器时，gdb 首先查找特定于架构的反汇编器。如果未注册任何反汇编器，则 gdb 会查找全局反汇编器（一个架构设置为`None`的反汇编器）。仅调用一个反汇编器来执行反汇编，因此，如果既有特定于架构的反汇编器，又有注册的全局反汇编器，则将使用特定于架构的反汇编器。
    gdb 分别跟踪特定于架构的和全局的反汇编器，因此反汇编器的创建或注册顺序并不重要；如果存在特定于架构的反汇编器，它将始终优先于全局反汇编器使用。
    您可以使用`maint info python-disassemblers`命令（请参阅[maint info python-disassemblers]，第 758 页）来查看已注册的反汇编器。

[函数] `builtin_disassemble (info)`
    此函数回调到 gdb 的内置反汇编器，以反汇编由`info`（`DisassembleInfo`的实例或子类）标识的指令。
    当内置反汇编器需要读取内存时，将调用`info`上的`read_memory`方法。通过子类化`DisassembleInfo`并重写`read_memory`方法，可以拦截来自内置反汇编器的`read_memory`调用，并修改返回的值。
    重要的是要理解，即使`DisassembleInfo.read_memory`引发`gdb.MemoryError`，也是内部反汇编器本身将内存错误报告给 gdb。原因是反汇编器可能会探测内存以查看一个字节是否可读；如果无法读取该字节，则反汇编器可以选择不报告错误，而是反汇编它确实具有的字节。
    如果内置反汇编器成功，则从`builtin_disassemble`返回`DisassemblerResult`的实例；否则，如果出现问题，将引发异常。
    如果`builtin_disassemble`无法读取正确执行反汇编所需的某些内存，则将引发`MemoryError`。
    在`read_memory`调用中引发的任何不是`MemoryError`的异常都将通过`builtin_disassemble`传递，并对调用者可见。
    最后，在某些情况下，gdb 的内置反汇编器可能会因`MemoryError`未涵盖的原因而失败。在这些情况下，将引发`GdbError`。异常的内容将是描述反汇编器遇到的问题的字符串。
```

这是一个注册全局反汇编器的示例。新的反汇编器调用内置反汇编器，然后在每一行反汇编输出中添加一个注释，`## Comment`：

```python
class ExampleDisassembler(gdb.disassembler.Disassembler):
    def __init__(self):
        super().__init__("ExampleDisassembler")
    def __call__(self, info):
        result = gdb.disassembler.builtin_disassemble(info)
        length = result.length
        text = result.string + "\t## Comment"
        return gdb.disassembler.DisassemblerResult(length, text)
gdb.disassembler.register_disassembler(ExampleDisassembler())
```

以下示例创建了`DisassembleInfo`的子类，以便在`read_memory`中拦截`read_memory`调用，在`read_memory`中，从内存中读取的任何字节的两个 4 位半字节都将交换位置。这不是一个非常有用的调整，但可作为示例。

```python
class MyInfo(gdb.disassembler.DisassembleInfo):
    def __init__(self, info):
        super().__init__(info)
    def read_memory(self, length, offset):
        buffer = super().read_memory(length, offset)
        result = bytearray()
        for b in buffer:
            v = int.from_bytes(b, 'little')
            v = (v << 4) & 0xf0 | (v >> 4)
            result.append(v)
        return memoryview(result)
class NibbleSwapDisassembler(gdb.disassembler.Disassembler):
    def __init__(self):
        super().__init__("NibbleSwapDisassembler")
    def __call__(self, info):
        info = MyInfo(info)
        return gdb.disassembler.builtin_disassemble(info)
gdb.disassembler.register_disassembler(NibbleSwapDisassembler())
```

#### 23.3.2.41 Python 中缺失的调试信息

当 gdb 遇到一个新的目标文件（见第 23.3.2.27 节[Python 中的目标文件]，第 476 页），例如主可执行文件，或被调试程序使用的任何共享库，gdb 将尝试为该目标文件加载相应的调试信息。调试信息可能在目标文件本身中，也可能在 gdb 会自动定位和加载的单独的目标文件中。

然而，有时 gdb 可能无法为某个目标文件找到任何调试信息，在这种情况下，调试体验将受到限制。

如果 gdb 无法为特定的目标文件定位任何调试信息，那么 Python 扩展就有机会介入。Python 扩展可以使用一些特定于平台或项目的步骤来定位缺失的调试信息，并将其位置告知 gdb。或者，Python 扩展可以向用户提供一些特定于平台或项目的建议，说明如何获取缺失的调试信息。

一个缺失的调试信息 Python 扩展由一个处理程序对象组成，该对象具有`name`和`enabled`属性，并实现`__call__`方法。当 gdb 遇到一个它无法找到任何调试信息的目标文件时，它将调用`__call__`方法。关于如何编写处理程序的详细信息如下。

**gdb.missing_debug 模块**

gdb 附带了一个`gdb.missing_debug`模块，其中包含以下类和全局函数：

```
[类] `gdb.missing_debug.MissingDebugHandler`
    `MissingDebugHandler`是一个基类，用户创建的处理程序可以从中派生，尽管处理程序不一定要从这个类派生，只要任何用户创建的处理程序具有`name`和`enabled`属性，并实现`__call__`方法即可。

    [函数] `MissingDebugHandler.__init__(name)`
        `name`是一个字符串，用于在某些 gdb 命令中引用这个缺失的调试处理程序。有效的名称由字符`[-_a-zA-Z0-9]`组成，创建一个具有无效名称的处理程序将引发`ValueError`异常。
    [函数] `MissingDebugHandler.__call__(objfile)`
        子类必须重写`__call__`方法。`objfile`参数将是一个`gdb.Objfile`，这是 gdb 无法找到任何调试信息的目标文件。
        `__call__`方法的返回值指示 gdb 下一步应该做什么。可能的返回值是：
            • `None`
                这表示该处理程序无法帮助`objfile`，gdb 应该调用任何其他注册的处理程序。
            • `True`
                这表示该处理程序已将调试信息安装到 gdb 在查找单独的调试信息文件时通常期望找到的位置（见第 18.3 节[单独的调试文件]，第 300 页）。gdb 将重复正常的查找过程，现在应该能够找到单独的调试文件。
                如果 gdb 在第二次尝试后仍然找不到单独的调试信息文件，那么 Python 缺失的调试信息处理程序不会被第二次调用，这可以防止行为不良的处理程序导致 gdb 陷入循环。gdb 将在没有任何`objfile`调试信息的情况下继续。
            • `False`
                这表示该处理程序已经完成了它对`objfile`的所有操作，但找不到单独的调试信息。gdb 将不会为`objfile`调用任何其他注册的处理程序。gdb 将在没有调试信息的情况下继续处理`objfile`。
            • A string
                返回的字符串应该包含一个文件名。gdb 将不会调用任何其他注册的处理程序，而是从返回的文件名标识的文件中加载调试信息。
        从这个基类调用`__call__`方法将引发`NotImplementedError`异常。
    [变量] `MissingDebugHandler.name`
        一个只读属性，是一个字符串，传递给`__init__`方法的处理程序的名称。
    [变量] `MissingDebugHandler.enabled`
        一个可修改的属性，包含一个布尔值；当为`True`时，处理程序被启用，并将被 gdb 使用。当为`False`时，处理程序已被禁用，将不会被使用。

[函数] `gdb.missing_debug.register_handler(locus, handler, replace=False)`
    向 gdb 注册一个新的缺失调试处理程序。
    `handler`是`MissingDebugHandler`的子类的一个实例，或者至少是一个具有与`MissingDebugHandler`相同属性和方法的对象的实例。
    `locus`指定将`handler`前置到哪个处理程序列表中。它可以是一个`gdb.Progspace`（见第 23.3.2.26 节[Python 中的程序空间]，第 474 页）或`None`，在这种情况下，处理程序将在全局范围内注册。新注册的处理程序将在来自相同`locus`的任何其他处理程序之前被调用。同一`locus`中的两个处理程序不能具有相同的名称，除非`replace`为`True`，否则尝试添加一个具有已存在名称的处理程序将引发异常，在这种情况下，旧的处理程序将被删除，新的处理程序将前置到选定的处理程序列表中。
    gdb 首先调用当前程序空间的处理程序，然后调用全局注册的处理程序。一旦一个处理程序返回除`None`之外的值，就不会为这个目标文件调用其他处理程序。
```

#### 23.3.2.42 Python 中的缺失目标文件

当 gdb 打开一个核心文件时，例如使用`core-file`命令（见[`core-file`命令]，第 292 页），gdb 将尝试加载相应的可执行文件和共享库。通常这些文件可以在本地机器上找到，但有时这些文件无法找到，在这种情况下，调试体验将受到限制。

如果 gdb 无法定位到特定文件，那么 Python 扩展就有机会介入。Python 扩展可以使用一些特定于平台或项目的步骤来定位缺失的文件，并将其位置告知 gdb。或者，Python 扩展可以向用户提供一些特定于平台或项目的建议，告诉他们如何获取缺失的文件。

一个缺失目标文件的 Python 扩展由一个处理程序对象组成，该对象具有`name`和`enabled`属性，并实现了`__call__`方法。当 gdb 遇到无法找到文件的情况，但已知缺失文件的构建 ID（见[构建 ID]，第 301 页）时，将调用`__call__`方法尝试找到该文件。下面将详细介绍如何编写处理程序。

**gdb.missing_objfile 模块**

gdb 附带了一个`gdb.missing_objfile`模块，其中包含以下类和全局函数：

```
[类] `gdb.missing_objfile.MissingObjfileHandler`
    `MissingObjfileHandler`是一个基类，用户创建的处理程序可以从中派生，不过处理程序不一定要从这个类派生，只要任何用户创建的处理程序具有`name`和`enabled`属性，并实现了`__call__`方法即可。
    
    [函数] `MissingObjfileHandler.__init__(name)`
        `name`是一个字符串，用于在某些 gdb 命令中引用这个缺失目标文件处理程序。有效的名称由字符`[-_a-zA-Z0-9]`组成，创建一个具有无效名称的处理程序将引发`ValueError`异常。
    [函数] `MissingObjfileHandler.__call__(pspace, build id, filename)`
        子类必须重写`__call__`方法。`pspace`参数将是一个`gdb.Progspace`（见第 23.3.2.26 节[Python 中的 Progspaces]，第 474 页），这是 gdb 正在寻找缺失文件的程序空间。
        `build id`参数是一个包含缺失文件构建 ID 的字符串，它将与`Objfile.build_id`返回的格式相同（见[`Objfile.build id`]，第 478 页）。
        `filename`参数包含 gdb 正在寻找的文件的名称。提供此信息是为了允许处理程序为用户生成有信息性的消息。处理程序不需要将缺失的文件放在此位置。
        在此位置可能已经存在一个文件，但它可能与所需的构建 ID 不匹配，在这种情况下，gdb 将忽略它。在某些有限的情况下，gdb 可能无法确定它正在搜索的文件的文件名，在这种情况下，gdb 将使用字符串`with build-id build_id`作为替代。
        `__call__`方法的返回值指示 gdb 接下来应该做什么。可能的返回值是：
            • `None`
                这表示此处理程序无法找到缺失的文件，gdb 应调用任何其他注册的处理程序。
            • `True`
                这表示此处理程序已将缺失的文件安装到 gdb 通常期望找到它的位置。gdb 唯一会查找的位置是在调试文件目录中的`.build-id`子目录内（见[调试文件目录]，第 301 页）。
                gdb 将重复正常的查找过程，现在应该能找到之前缺失的文件。
                如果 gdb 在第二次尝试后仍然找不到文件，那么 Python 缺失目标文件处理程序不会被第二次调用，这可以防止行为不良的处理程序导致 gdb 陷入循环。gdb 将在没有缺失文件的情况下继续，尽管这将降低调试体验。
            • `False`
                这表示此处理程序已完成它打算做的一切，但仍未找到缺失的文件。gdb 将不会调用任何其他注册的处理程序来查找缺失的文件。gdb 将在没有缺失文件的情况下继续，尽管这将降低调试体验。
            • A string
                返回的字符串应包含一个文件名。gdb 将不会调用任何其他注册的处理程序，而是将返回的文件名用作缺失的文件。
        从这个基类调用`__call__`方法将引发`NotImplementedError`异常。

    [变量] `MissingObjfileHandler.name`
        一个只读属性，是一个字符串，传递给`__init__`方法的此处理程序的名称。

    [变量] `MissingObjfileHandler.enabled`
        一个可修改的属性，包含一个布尔值；当为`True`时，处理程序已启用，并将被 gdb 使用。当为`False`时，处理程序已被禁用，将不会被使用。

[函数] `gdb.missing_objfile.register_handler(locus, handler, replace=False)`
    向 gdb 注册一个新的缺失目标文件处理程序。
    `handler`是`MissingObjfileHandler`的子类的实例，或者至少是一个具有与`MissingObjfileHandler`相同属性和方法的对象的实例。
    `locus`指定将`handler`前置到哪个处理程序列表中。它可以是一个`gdb.Progspace`（见第 23.3.2.26 节[Python 中的 Progspaces]，第 474 页）或`None`，在这种情况下，处理程序将在全局范围内注册。新注册的处理程序将在来自相同`locus`的任何其他处理程序之前被调用。同一`locus`中的两个处理程序不能具有相同的名称，除非`replace`为`True`，否则尝试添加具有已存在名称的处理程序将引发异常，在这种情况下，旧的处理程序将被删除，新的处理程序将前置到所选的处理程序列表中。
    gdb 首先调用当前程序空间的处理程序，然后调用全局注册的处理程序。一旦一个处理程序返回除`None`之外的值，将不会再调用其他处理程序。
```

**管理缺失目标文件处理程序**

gdb 定义了以下命令来管理注册的缺失目标文件处理程序：

```
`info missing-objfile-handlers [locus [name-regexp]]`
    列出所有注册的缺失目标文件处理程序。参数`locus`和`name-regexp`都是可选的，可以用于过滤要列出的处理程序。
    `locus`参数应该是`global`、`progspace`或一个目标文件的名称。仅列出为指定`locus`注册的处理程序。
    `name-regexp`是一个用于匹配处理程序名称的正则表达式。

`disable missing-objfile-handler [locus [name-regexp]]`
    `locus`和`name-regexp`的解释与`info missing-objfile-handlers`上面的解释相同，但不是列出匹配的处理程序，而是禁用所有匹配的处理程序。每个匹配处理程序的`enabled`字段将设置为`False`。

`enable missing-objfile-handler [locus [name-regexp]]`
    `locus`和`name-regexp`的解释与`info missing-objfile-handlers`上面的解释相同，但不是列出匹配的处理程序，而是启用所有匹配的处理程序。每个匹配处理程序的`enabled`字段将设置为`True`。
```

### 23.3.3 Python 自动加载

当读取新的目标文件时（例如，由于`file`命令，或者因为被调试程序加载了共享库），`gdb`将通过以下几种方式查找 Python 支持脚本：`objfile-gdb.py`和`.debug_gdb_scripts`部分。请参见第 23.5 节[自动加载扩展]，第 574 页。

自动加载功能对于提供特定于应用程序的调试命令和脚本非常有用。

可以启用或禁用自动加载，并且可以打印自动加载脚本的列表。

```
`set auto-load python-scripts [on|off]`
    启用或禁用 Python 脚本的自动加载。

`show auto-load python-scripts`
    显示 Python 脚本的自动加载是否已启用或禁用。

`info auto-load python-scripts [regexp]`
    打印`gdb`自动加载的所有 Python 脚本的列表。
    还会打印在`.debug_gdb_scripts`部分中提到的 Python 脚本的列表，这些脚本要么未找到（请参见第 23.5.2 节[`.debug_gdb_scripts`部分]，第 576 页），要么由于自动加载安全路径拒绝而未自动加载（请参见第 22.8 节[自动加载]，第 376 页）。这很有用，因为当`gdb`尝试加载它们但失败时，不会打印它们的名称。
    如果提供了`regexp`，则仅打印匹配名称的 Python 脚本。
    示例：
        (gdb) info auto-load python-scripts
        Loaded Script
        Yes py-section-script.py
            full name: /tmp/py-section-script.py
        No  my-foo-pretty-printers.py
```

在读取自动加载的文件或脚本时，`gdb`会设置当前目标文件。这可通过`gdb.current_objfile`函数获得（请参见第 23.3.2.27 节[Python 中的目标文件]，第 476 页）。这对于注册特定于目标文件的漂亮打印机和帧过滤器很有用。

### 23.3.4 Python 模块

`gdb`附带了几个模块来辅助编写 Python 代码。

#### 23.3.4.1 gdb.printing

此模块提供了一组用于处理漂亮打印机的实用工具。

```
`PrettyPrinter(name, subprinters=None)`
    此类指定了使“info 漂亮打印机”、“启用漂亮打印机”和“禁用漂亮打印机”工作的 API。漂亮打印机通常应继承自此类。

`SubPrettyPrinter(name)`
    对于处理多种类型的打印机，此类指定了子打印机的相应 API。

`RegexpCollectionPrettyPrinter(name)`
    用于处理多个打印机的实用类，所有打印机都通过正则表达式识别。有关示例，请参阅第 23.3.2.8 节[编写漂亮打印机]，第 427 页。

`FlagEnumerationPrinter(name)`
    一个处理枚举值打印的漂亮打印机。与 gdb 的内置枚举打印不同，当枚举常量之间存在一些重叠时，此打印机尝试正常工作。参数`name`是打印机的名称，也是要查找的枚举类型的名称。

`register_pretty_printer(obj, printer, replace=False)`
    向`obj`的漂亮打印机列表注册打印机。如果`replace`为 True，则替换打印机的任何现有副本。否则，如果已经存在具有相同名称的打印机，则会引发`RuntimeError`异常。
```

#### 23.3.4.2 gdb.types

此模块提供了一系列用于处理`gdb.Type`对象的实用工具。

```
`get_basic_type (type)`
    返回去掉`const`和`volatile`限定符，并将`typedef`和 C++引用转换为基础类型的类型。
    C++示例：
        typedef const int const_int;
        const_int foo (3);
        const_int& foo_ref (foo);
        int main () { return 0; }

    在`gdb`中：
        (gdb) start
        (gdb) python import gdb.types
        (gdb) python foo_ref = gdb.parse_and_eval("foo_ref")
        (gdb) python print gdb.types.get_basic_type(foo_ref.type)
        int

`has_field (type, field)`
    如果类型（假定为具有字段的类型，例如结构或联合）具有字段`field`，则返回`True`。

`make_enum_dict (enum_type)`
    从枚举类型生成一个 Python 字典类型。

`deep_items (type)`
    返回一个类似于标准`gdb.Type.iteritems`方法的 Python 迭代器，但`deep_items`返回的迭代器将递归遍历匿名结构或联合字段。例如：
        struct A
        {
            int a;
            union {
                int b0;
                int b1;
            };
        };
    在`gdb`中：
        (gdb) python import gdb.types
        (gdb) python struct_a = gdb.lookup_type("struct A")
        (gdb) python print struct_a.keys ()
        {[’a’, ’’]}
        (gdb) python print [k for k,v in gdb.types.deep_items(struct_a)]
        {[’a’, ’b0’, ’b1’]}

`get_type_recognizers ()`
    返回当前上下文中已启用的类型识别器列表。这在`gdb`的类型打印过程中被调用（见第 23.3.2.9 节[类型打印 API]，第 429 页）。

`apply_type_recognizers (recognizers, type_obj)`
    将类型识别器`recognizers`应用于类型对象`type_obj`。如果任何识别器返回一个字符串，则返回该字符串。否则，返回`None`。这在`gdb`的类型打印过程中被调用（见第 23.3.2.9 节[类型打印 API]，第 429 页）。

`register_type_printer (locus, printer)`
    这是一个方便的函数，用于注册一个类型打印机`printer`。打印机必须实现类型打印机协议。`locus`参数可以是一个`gdb.Objfile`，在这种情况下，打印机将在该对象文件中注册；也可以是一个`gdb.Progspace`，在这种情况下，打印机将在该程序空间中注册；或者为`None`，在这种情况下，打印机将全局注册。

`TypePrinter`
    这是一个实现类型打印机协议的基类。鼓励但不是必须从这个类派生类型打印机。它定义了一个构造函数：
    [`TypePrinter`的方法] `__init__ (self, name)`
        用给定的名称初始化类型打印机。新打印机以启用状态开始。
```

#### 23.3.4.3 gdb.prompt

此模块提供了一种用于提示值替换的方法。

```
`substitute_prompt (string)`
    返回带有由值替换的转义序列的字符串。一些转义序列需要参数。您可以在紧跟转义序列的“{}”内指定参数。
    您可以传递给此函数的转义序列如下：
        \\      替换为反斜杠。
        \e      替换为 ESC 字符。
        \f      替换为所选帧；参数指定帧参数。
        \n      替换为换行符。
        \p      替换为参数的值；参数指定参数。
        \r      替换为回车。
        \t      替换为所选线程；参数指定线程参数。
        \v      替换为 GDB 的版本。
        \w      替换为当前工作目录。
        \[      开始一个非打印字符序列。这些序列通常与 ESC 字符一起使用，并且不计入字符串长度。示例：“\[\e[0;34m\](gdb)\[\e[0m\]”将返回一个蓝色的“(gdb)”提示，其长度为 5。
        \]      结束一个非打印字符序列。

    例如：
        substitute_prompt ("frame: \f, args: \p{print frame-arguments}")
    将返回字符串：
        "frame: main, args: scalars"
```

#### 23.3.4.4 gdb.ptwrite

此模块为记录使用`PTWRITE`指令的程序提供了额外的功能。`PTWRITE`是一个 x86 指令，允许将值写入英特尔处理器跟踪（参见第 7 章[进程记录和回放]，第 103 页）。其对应的 gcc 内联函数为：

```
void _ptwrite32 (unsigned int a)
void _ptwrite64 (unsigned __int64 a)
```
如果一个子进程使用了该指令，gdb 默认会将原始有效负载值作为辅助信息插入到执行历史中。辅助信息在默认情况下会在记录指令历史、记录函数调用历史以及所有单步命令期间打印，并且在 Python 中可以作为`RecordAuxiliary`对象访问（参见第 23.3.2.20 节[Python 中的记录]，第 459 页）。

示例程序：

```c
#include <immintrin.h>
void ptwrite64 (unsigned long long value)
{
    _ptwrite64 (value);
}
int main (void)
{
    ptwrite64 (0x42);
    return 0; /* break here. */
}
```

以 pt 格式记录示例程序后的 gdb 输出：

```
(gdb) record instruction-history 12,14
12 0x0040074c <ptwrite64+16>: ptwrite %rbx
13 [0x42]
14 0x00400751 <ptwrite64+21>: mov -0x8(%rbp),%rbx
(gdb) record function-call-history
1 main
2 ptwrite64
    [0x42]
3 main
```

`gdb.ptwrite`模块允许自定义`PTWRITE`辅助信息的默认输出。可以注册一个自定义的 Python 函数作为`PTWRITE`过滤器函数。在跟踪解码期间，该函数将以`PTWRITE`有效负载和程序计数器（PC）作为参数被调用。该函数可以返回一个字符串，该字符串将在上述命令期间由 gdb 打印，或者返回`None`，导致没有输出。要注册这样一个过滤器函数，用户需要提供一个过滤器工厂函数，该函数返回一个新的过滤器函数对象，供 gdb 调用。

```
[函数] `register_filter_factory (filter factory)`
    用于注册`PTWRITE`过滤器工厂。这个过滤器工厂可以是任何可调用对象，接受一个参数，即当前线程作为`gdb.InferiorThread`。它可以返回`None`或一个可调用对象。这个可调用对象是指定线程的`PTWRITE`过滤器函数。如果工厂函数返回`None`，则将打印默认的辅助信息。

[函数] `get_filter ()`
    返回当前活动的`PTWRITE`过滤器函数。
```

示例：

```python
(gdb) python-interactive
>>> class my_filter():
... def __init__(self):
... self.var = 0
... def __call__(self, payload, ip):
... self.var += 1
... return f"counter: {self.var}, ip: {ip:#x}"
...
>>> def my_filter_factory(thread):
... if thread.global_num == 1:
... return my_filter()
... else:
... return None
...
>>> import gdb.ptwrite
>>> gdb.ptwrite.register_filter_factory(my_filter_factory)
>>>

(gdb) record function-call-history 59,64
59 pthread_create@GLIBC_2.2.5
60 job()
61 task(void*)
62 ptwrite64(unsigned long)
    [counter: 1, ip: 0x401156]
63 task(void*)
64 ptwrite32(unsigned int)
    counter: 2, ip: 0x40116c]

(gdb) info threads
* 1 Thread 0x7ffff7fd8740 (LWP 25796) "ptw_threads" task ()
    at bin/ptwrite/ptw_threads.c:45
  2 Thread 0x7ffff6eb8700 (LWP 25797) "ptw_threads" task ()
    at bin/ptwrite/ptw_threads.c:45

(gdb) thread 2
[Switching to thread 2 (Thread 0x7ffff6eb8700 (LWP 25797))]
#0 task (arg=0x0) at ptwrite_threads.c:45
45      return NULL;

(gdb) record function-call-history 10,14
10 start_thread
11 task(void*)
12 ptwrite64(unsigned long)
    [0x42]
13 task(void*)
14 ptwrite32(unsigned int)
    [0x43]
```

此 gdb 功能依赖于硬件和操作系统支持，并且需要英特尔处理器跟踪解码器库版本 2.0.0 或更高版本。

## 23.4 使用 Guile 扩展 gdb

你可以使用 Scheme 编程语言的 Guile 实现来扩展 gdb（http://www.gnu.org/software/guile/）。此功能仅在使用`--with-guile`配置 gdb 时可用。

### 23.4.1 Guile 介绍

Guile 是 Scheme 编程语言的一种实现，并且是 GNU 项目的官方扩展语言。

gdb 中的 Guile 支持相当紧密地遵循了 gdb 中的 Python 支持，因此那里的概念应该可以转移过来。然而，在有意义的地方，一些事情的做法是不同的。

gdb 需要 Guile 版本 3.0、2.2 或 2.0。

gdb 使用的 Guile 脚本应该安装在 data-directory/guile 中，其中 data-directory 是在 gdb 启动时确定的数据目录（参见第 18.8 节[数据文件]，第 308 页）。这个目录，称为 guile 目录，会自动添加到 Guile 搜索路径中，以便允许 Guile 解释器定位在此位置安装的所有脚本。

### 23.4.2 Guile 命令

gdb 提供了两个用于访问 Guile 解释器的命令：

```
`guile-repl`
`gr` 
    `guile-repl` 命令可用于启动交互式 Guile 提示符或 `repl` 。要返回 gdb，请输入 `,q` 或 EOF 字符（例如，在空提示符上按 Ctrl-D）。这些命令不接受任何参数。

`guile [scheme-expression]`
`gu [scheme-expression]`
    `guile` 命令可用于评估一个 Scheme 表达式。
    如果给定参数，gdb 将把参数传递给 Guile 解释器进行评估。
        (gdb) guile (display (+ 20 3)) (newline)
        23
    
    表达式的结果将按照 Guile 的常规规则进行显示。
        (gdb) guile (+ 20 3)
        23
    
    如果您没有向 `guile` 提供参数，它将充当多行命令，类似于 `define` 。在这种情况下，Guile 脚本由 `guile` 命令之后的后续命令行组成。此命令列表使用包含 `end` 的行终止。例如：
        (gdb) guile
        >(display 23)
        >(newline)
        >end
        23

    也可以从 gdb 解释器执行 Guile 脚本：

`source script-name`
    脚本名称必须以 `.scm` 结尾，并且 gdb 必须使用 `script-extension` 设置配置为基于文件名扩展名识别脚本语言。请参阅第 23 章[扩展 GDB]，第 391 页。

`guile (load "script-name")`
    此方法使用 Guile 的 `load` 函数。它接受一个字符串参数，即要加载的脚本的名称。有关此函数的描述，请参阅 Guile 文档。（请参阅《GNU Guile 参考手册》中的“加载”部分） 
```

### 23.4.3 Guile API

你可以通过发出`help guile`命令，或者在交互式 Guile 会话中发出`,help`命令，来获取 gdb 的 Guile API 的快速在线帮助。此外，gdb 提供的大多数 Guile 过程都有文档字符串，可以通过在 Guile 交互提示符下发出`,describe procedure-name`或`,d procedure-name`来获取。

#### 23.4.3.1 基本 Guile

在启动时，gdb 会覆盖 Guile 的`current-output-port`和`current-error-port`，以使用 gdb 的输出分页流进行打印。输出到这些流之一的 Guile 程序的输出可能会被用户中断（见第 22.4 节[屏幕大小]，第 370 页）。在这种情况下，会抛出一个值为`SIGINT`的 Guile 信号异常。

Guile 的历史机制使用与 gdb 相同的命名，即使用美元变量（例如，$1、$2 等）。在 Guile 和 GDB 中的评估结果是分别计数的，Guile 中的$1 与 gdb 中的$1 不是相同的值。

gdb 不是线程安全的。如果你的 Guile 程序使用多个线程，你必须小心，仅在 gdb 线程中调用特定于 gdb 的函数。

在编写要在 gdb 中运行的 Guile 代码时，必须注意一些事项。特别值得注意的有两点：

• gdb 为`SIGCHLD`和`SIGINT`安装了处理程序。Guile 代码不得覆盖这些处理程序，甚至不得使用`sigaction`更改选项。如果你的程序更改了这些信号的处理方式，gdb 很可能会停止正常工作。请注意，不幸的是，GUI 工具包通常会安装`SIGCHLD`处理程序。

• gdb 会小心地将其内部文件描述符标记为在执行时关闭。然而，这在所有平台上都不能以线程安全的方式完成。你的 Guile 程序应该意识到这一点，并且在启动子进程之前，应该创建具有设置了在执行时关闭标志的新文件描述符，并安排关闭不需要的文件描述符。

gdb 引入了一个新的 Guile 模块，名为`gdb`。gdb 添加的所有方法和类都放在这个模块中。gdb 不会自动导入`gdb`模块，脚本必须自己执行此操作。有各种导入模块的选项，因此 gdb 将如何导入`gdb`模块的选择留给用户。为了简化交互使用，建议将以下内容之一添加到你的`~/.gdbinit`中。

```
guile (use-modules (gdb))
guile (use-modules ((gdb) #:renamer (symbol-prefix-proc ’gdb:)))
```

选择哪个取决于你的偏好。第二个将`gdb:`作为所有模块函数和变量的前缀添加。

本手册的其余部分假设已导入`gdb`模块，且没有任何前缀。有关`use-modules`的更多信息，请参阅 Guile 文档（见 GNU Guile 参考手册中的“使用 Guile 模块”部分）。

示例：
```
(gdb) guile (value-type (make-value 1))
ERROR: Unbound variable: value-type
在执行 Scheme 代码时出错。
(gdb) guile (use-modules (gdb))
(gdb) guile (value-type (make-value 1))
int
(gdb)
```

`(gdb)`模块提供了这些基本的 Guile 函数。

```
[Scheme Procedure] execute command [#:from-tty boolean] [#:to-string boolean]
    将命令（一个字符串）作为 gdb CLI 命令进行求值。如果在命令运行期间发生 gdb 异常，它将按照第 23.4.3.4 节[Guile 异常处理]，第 527 页中所述进行转换。
    `from-tty`指定 gdb 是否应将此命令视为由用户交互调用。它必须是一个布尔值。如果省略，它默认为`#f`。
    默认情况下，命令生成的任何输出都将发送到 gdb 的标准输出（如果打开了日志记录，则也发送到日志输出）。如果`to-string`参数为`#t`，则输出将由`execute`收集并作为字符串返回。默认值为`#f`，在这种情况下，返回值未指定。如果`to-string`为`#t`，gdb 虚拟终端将暂时设置为无限宽度和高度，并且其分页将被禁用；见第 22.4 节[屏幕大小]，第 370 页。

[Scheme Procedure] history-ref number
    从 gdb 的值历史记录中返回一个值（见第 10.11 节[值历史记录]，第 170 页）。数字参数指示要返回的历史元素。如果数字为负数，则 gdb 将取其绝对值并从最后一个元素（即最近的元素）开始向后计数以找到要返回的值。如果数字为零，则 gdb 将返回最近的元素。如果数字指定的元素在值历史记录中不存在，将引发`gdb:error`异常。
    如果没有引发异常，返回值始终是`<gdb:value>`的一个实例（见第 23.4.3.5 节[从 inferior 中获取值在 Guile 中]，第 528 页）。
    注意：gdb 的值历史记录与 Guile 的独立。gdb 的值历史记录中的$1 包含从 gdb 的命令行评估表达式的结果，而 Guile 的历史记录中的$1 包含从 Guile 的命令行评估表达式的结果。

[Scheme Procedure] history-append! value
    将`value`（一个`<gdb:value>`的实例）附加到 gdb 的值历史记录中。返回其在历史记录中的索引。
    将 Guile 扩展返回的值放入历史记录中，将允许用户通过 CLI 历史记录设施方便地访问这些值。

[Scheme Procedure] parse-and-eval expression
    将`expression`解析为当前语言中的表达式，对其求值，并将结果作为`<gdb:value>`返回。`expression`必须是一个字符串。
    在实现新命令时（见第 23.4.3.11 节[Guile 中的命令]，第 544 页），此函数很有用，因为它提供了一种将命令的参数解析为表达式的方法。在计算值时也很有用。例如，它是将便利变量的值（见第 10.12 节[便利变量]，第 171 页）作为`<gdb:value>`获取的唯一方法。
```

#### 23.4.3.2 Guile 配置

gdb 提供了这些 Scheme 函数来访问各种配置参数。

```
[Scheme 过程] data-directory
    返回一个包含 gdb 数据目录的字符串。此目录包含 gdb 的辅助文件。

[Scheme 过程] guile-data-directory
    返回一个包含 gdb 的 Guile 数据目录的字符串。此目录包含 gdb 提供的 Guile 模块。

[Scheme 过程] gdb-version
    返回一个包含 gdb 版本的字符串。

[Scheme 过程] host-config
    返回一个包含主机配置的字符串。这是在配置 gdb 时传递给 --host 的字符串。

[Scheme 过程] target-config
    返回一个包含目标配置的字符串。这是在配置 gdb 时传递给 --target 的字符串。
```

#### 23.4.3.3 GDB 方案数据类型

Gdb 向 Guile 公开的值被称为 gdb 对象。有几种 gdb 对象，每种对象都与 Guile 已知的所有其他类型不相交。

```
[Scheme 过程] gdb-object-kind 对象
    返回 gdb 对象的类型，例如 <gdb:breakpoint>，作为一个符号。
```

gdb 定义了以下对象类型：

```
<gdb:arch>
    见第 23.4.3.21 节 [Guile 中的体系结构]，第 567 页。

<gdb:block>
    见第 23.4.3.16 节 [Guile 中的块]，第 555 页。

<gdb:block-symbols-iterator>
    见第 23.4.3.16 节 [Guile 中的块]，第 555 页。

<gdb:breakpoint>
    见第 23.4.3.19 节 [Guile 中的断点]，第 562 页。

<gdb:command>
    见第 23.4.3.11 节 [Guile 中的命令]，第 544 页。

<gdb:exception>
    见第 23.4.3.4 节 [Guile 异常处理]，第 527 页。

<gdb:frame>
    见第 23.4.3.15 节 [Guile 中的帧]，第 552 页。

<gdb:iterator>
    见第 23.4.3.25 节 [Guile 中的迭代器]，第 571 页。

<gdb:lazy-string>
    见第 23.4.3.20 节 [Guile 中的懒字符串]，第 566 页。

<gdb:objfile>
    见第 23.4.3.14 节 [Guile 中的目标文件]，第 552 页。

<gdb:parameter>
    见第 23.4.3.12 节 [Guile 中的参数]，第 548 页。

<gdb:pretty-printer>
    见第 23.4.3.8 节 [Guile 漂亮打印 API]，第 539 页。

<gdb:pretty-printer-worker>
    见第 23.4.3.8 节 [Guile 漂亮打印 API]，第 539 页。

<gdb:progspace>
    见第 23.4.3.13 节 [Guile 中的程序空间]，第 551 页。

<gdb:symbol>
    见第 23.4.3.17 节 [Guile 中的符号]，第 557 页。

<gdb:symtab>
    见第 23.4.3.18 节 [Guile 中的符号表]，第 561 页。

<gdb:sal>
    见第 23.4.3.18 节 [Guile 中的符号表]，第 561 页。

<gdb:type>
    见第 23.4.3.7 节 [Guile 中的类型]，第 535 页。

<gdb:field>
    见第 23.4.3.7 节 [Guile 中的类型]，第 535 页。

<gdb:value>
    见第 23.4.3.5 节 [Guile 中从 inferior 获取的值]，第 528 页。
```

以下 gdb 对象在内部被管理，以便 Scheme 函数 eq? 可以应用于它们。

```
<gdb:arch>
<gdb:block>
<gdb:breakpoint>
<gdb:frame>
<gdb:objfile>
<gdb:progspace>
<gdb:symbol>
<gdb:symtab>
<gdb:type>
```

#### 23.4.3.4 Guile 异常处理

在执行`guile`命令时，Guile 代码中未捕获的 Guile 异常会被转换为对`gdb`错误报告机制的调用。如果调用`guile`的命令未处理该错误，`gdb`将终止它并根据`guile print-stack`参数的设置报告错误。

`guile print-stack`参数有三个设置：

```
`none`
    不打印任何内容。

`message`
    打印包含 Guile 异常名称、相关值以及在异常引发点的 Guile 调用栈回溯的错误消息。示例：
        (gdb) guile (display foo)
        ERROR: In procedure memoize-variable-access!:
        ERROR: Unbound variable: foo
        Error while executing Scheme code.

`full`
    除了错误消息外，还打印完整的回溯。
        (gdb) set guile print-stack full
        (gdb) guile (display foo)
        Guile Backtrace:
        In ice-9/boot-9.scm:
        157: 10 [catch #t #<catch-closure 2c76e20> ...]
        In unknown file:
        ?: 9 [apply-smob/1 #<catch-closure 2c76e20>]
        In ice-9/boot-9.scm:
        157: 8 [catch #t #<catch-closure 2c76d20> ...]
        In unknown file:
        ?: 7 [apply-smob/1 #<catch-closure 2c76d20>]
        ?: 6 [call-with-input-string "(display foo)" ...]
        In ice-9/boot-9.scm:
        2320: 5 [save-module-excursion #<procedure 2c2dc30 ... ()>]
        In ice-9/eval-string.scm:
        44: 4 [read-and-eval #<input: string 27cb410> #:lang ...]
        37: 3 [lp (display foo)]
        In ice-9/eval.scm:
        387: 2 [eval # ()]
        393: 1 [eval #<memoized foo> ()]
        In unknown file:
        ?: 0 [memoize-variable-access! #<memoized foo> ...]
        ERROR: In procedure memoize-variable-access!:
        ERROR: Unbound variable: foo
        Error while executing Scheme code.
```

在 Guile 代码调用的`gdb`命令中发生的`gdb`错误会转换为 Guile 异常。Guile 异常的类型取决于错误。

`gdb`提供的 Guile 过程可以抛出标准的 Guile 异常，如`wrong-type-arg`和`out-of-range`。

用户中断（通过`C-c`或在分页提示中输入`q`）被转换为值为`SIGINT`的 Guile 信号异常。

`gdb`的 Guile 过程还可以抛出以下异常：

```
`gdb:error`
    此异常是对`gdb`内部生成的错误的通配符捕获。

`gdb:invalid-object`
    当访问包装底层`gdb`对象的 Guile 对象已变为无效时抛出此异常。例如，如果用户从命令行删除一个`<gdb:breakpoint>`对象，该对象将变为无效。该对象在 Guile 中仍然存在，但它所表示的对象已消失。对该断点的进一步操作将抛出此异常。

`gdb:memory-error`
    当一个操作试图访问 inferior 中的无效内存时抛出此异常。

`gdb:pp-type-error`
    当 Guile 漂亮打印机将一个错误的对象传递给`gdb`时抛出此异常。
```

以下异常相关的过程由`(gdb)`模块提供。

```
[Scheme Procedure] make-exception key args
    根据其键和参数（这是异常的标准 Guile 参数）返回一个`<gdb:exception>`对象。有关更多信息，请参阅 Guile 文档（见《GNU Guile 参考手册》中的“异常”部分）。

[Scheme Procedure] exception? object
    如果`object`是一个`<gdb:exception>`对象，则返回`#t`。否则返回`#f`。

[Scheme Procedure] exception-key exception
    返回`<gdb:exception>`对象的`args`字段。

[Scheme Procedure] exception-args exception
    返回`<gdb:exception>`对象的`args`字段。
```

#### 23.4.3.5 Inferior 中的值

`gdb` 在类型为`<gdb:value>`的对象中提供从下级程序获取的值。

`gdb` 使用此对象来内部记录下级的值，并在必要时获取值。

`gdb` 不会记住`<gdb:value>`对象。`make-value`总是返回一个新的对象。

```gdb
(gdb) guile (eq? (make-value 1) (make-value 1))
$1 = #f
(gdb) guile (equal? (make-value 1) (make-value 1))
$1 = #t
```

表示函数的`<gdb:value>`可以通过带有`value-call`的下级函数调用来执行。提供给调用的任何参数必须与函数的原型匹配，并必须按照该原型指定的顺序提供。

例如，`some-val`是一个表示接受两个整数作为参数的函数的`<gdb:value>`实例。要执行此函数，请这样调用：

```
(define result (value-call some-val 10 20))
```

函数调用返回的任何值都是`<gdb:value>`对象。

注意：与`gdb`中的 Python 脚本不同，对于有效的值数据类型的 Scheme 表达式，不能直接使用简单标量的下级值。例如，`(+ (parse-and-eval "int_variable") 2)`不起作用。并且不能使用任何特殊语法访问是某些类的结构或实例的下级值，而是必须使用`value-field`。

以下是`(gdb)`模块提供的与值相关的过程：

```
[Scheme Procedure] value? object
    如果`object`是一个`<gdb:value>`对象，则返回`#t`。否则返回`#f`。

[Scheme Procedure] make-value value [#:type type]
    许多 Scheme 值可以通过此过程直接转换为`<gdb:value>`。如果指定了`type`，结果是此类型的值，如果`value`无法用此类型表示，则抛出异常。否则，结果的类型根据以下所述从`value`确定。
    有关架构的内置类型列表，请参阅第 23.4.3.21 节[Guile 中的架构]，第 567 页。
    以下是在未指定`make-value`的类型参数时如何转换 Scheme 值：
    Scheme boolean：         一个 Scheme boolean 被转换为当前语言的布尔类型。
    Scheme integer：         一个 Scheme integer 被转换为当前架构中可以表示该值的第一个`C int`、`unsigned int`、`long`、`unsigned long`、`long long`或`unsigned long long`类型。如果 Scheme integer 无法表示为目标整数，则抛出超出范围异常。
    Scheme real：            一个 Scheme real 被转换为当前架构的`C double`类型。
    Scheme string：          一个 Scheme string 被转换为当前目标语言中使用当前目标编码的字符串。当前目标编码中无法表示的字符将被替换为相应的转义序列。这是 Guile 的`SCM_FAILED_CONVERSION_ESCAPE_SEQUENCE`转换策略（请参阅 GNU Guile 参考手册中的“字符串”部分）。在这种情况下不支持传递类型，如果提供了错误类型参数，则抛出异常。
    `<gdb:lazy-string>`：    如果`value`是一个`<gdb:lazy-string>`对象（请参阅第 23.4.3.20 节[Guile 中的懒字符串]，第 566 页），则调用`lazy-string->value`过程，并使用其结果。在这种情况下不支持传递类型，如果提供了错误类型参数，则抛出异常。
    Scheme bytevector：      如果`value`是一个 Scheme bytevector 并且提供了`type`，则`value`必须与`type`类型的值具有相同的大小（以字节为单位），并且结果基本上是通过使用`memcpy`创建的。如果`value`是一个 Scheme bytevector 并且未提供`type`，则结果是一个具有相同长度的`uint8`类型的数组。

[Scheme Procedure] value-optimized-out? value
    如果编译器优化掉了`value`，则返回`#t`，因此无法从下级获取它。否则返回`#f`。

[Scheme Procedure] value-address value
	如果`value`是可寻址的，则返回一个表示地址的`<gdb:value>`对象。否则，返回`#f`。

[Scheme Procedure] value-type value
	返回`value`的类型作为一个`<gdb:type>`对象（请参阅第 23.4.3.7 节[Guile 中的类型]，第 535 页）。

[Scheme Procedure] value-dynamic-type value
	返回`value`的动态类型。这使用 C++运行时类型信息（RTTI）来确定值的动态类型。如果值是类类型，则它将返回嵌入该值的类（如果有）。如果值是指向类类型的指针或引用，则它将计算引用对象的动态类型，并分别返回指向该类型的指针或引用。在所有其他情况下，它将返回值的静态类型。
	请注意，此功能仅在调试包含针对所讨论对象的 RTTI 的 C++程序时才有效。否则，它将仅返回值的静态类型，如`ptype foo`。请参阅第 16 章[符号]，第 263 页。

[Scheme Procedure] value-cast value type
	返回一个新的`<gdb:value>`实例，它是将`value`转换为`type`所描述的类型的结果，`type`必须是一个`<gdb:type>`对象。如果由于某种原因无法执行转换，则此方法抛出异常。

[Scheme Procedure] value-dynamic-cast value type
	类似于`value-cast`，但工作方式类似于使用 C++`dynamic_cast`运算符。有关详细信息，请咨询 C++参考。

[Scheme Procedure] value-reinterpret-cast value type
	类似于`value-cast`，但工作方式类似于使用 C++`reinterpret_cast`运算符。有关详细信息，请咨询 C++参考。

[Scheme Procedure] value-dereference value
	对于指针数据类型，此方法返回一个新的`<gdb:value>`对象，其内容是`value`所指向的对象。例如，如果`foo`是 C 中的一个指向`int`的指针，在您的 C 程序中声明为：
		int *foo;
	那么您可以使用相应的`<gdb:value>`来访问`foo`所指向的内容，如下所示：
		(define bar (value-dereference foo))
	结果`bar`将是一个持有`foo`所指向值的`<gdb:value>`对象。
	存在一个类似的函数`value-referenced-value`，它也返回与指针值所指向的值对应的`<gdb:value>`对象（并且还返回由引用值引用的值对应的`<gdb:value>`对象）。然而，`value-dereference`的行为与`value-referenced-value`的不同之处在于，`value-dereference`的行为与在给定值上应用 C 一元运算符`*`相同。例如，考虑在 C++程序中声明的对指针`ptrref`的引用：
		typedef int *intptr;
		...
		int val = 10;
		intptr ptr = &val;
		intptr &ptrref = ptr;
	尽管`ptrref`是一个引用值，但可以对与之对应的`<gdb:value>`对象应用`value-dereference`方法，并获得与`val`对应的`<gdb:value>`。然而，如果应用`value-referenced-value`方法，结果将是与`ptr`对应的`<gdb:value>`对象。
		(define scm-ptrref (parse-and-eval "ptrref"))
		(define scm-val (value-dereference scm-ptrref))
		(define scm-ptr (value-referenced-value scm-ptrref))

	`<gdb:value>`对象`scm-val`与`val`对应的对象相同，`scm-ptr`与`ptr`对应的对象相同。通常，只要可以对相应的 C 值应用 C 一元运算符`*`，就可以应用`value-dereference`。对于允许同时应用`value-dereference`和`value-referenced-value`的情况，获得的结果不必相同（如我们在上面的示例中所见）。然而，当应用于 C/C++程序中与指针对应的`<gdb:value>`对象（类型代码为`TYPE_CODE_PTR`的`<gdb:value>`对象）时，结果是相同的。

[Scheme Procedure] value-referenced-value value
	对于指针或引用数据类型，此方法返回一个新的`<gdb:value>`对象，对应于指针/引用值所引用的值。对于指针数据类型，`value-dereference`和`value-referenced-value`产生相同的结果。
	这些方法之间的区别在于，`value-dereference`无法获取引用值所引用的值。例如，考虑在 C++程序中声明的对`int`的引用：
		int val = 10;
		int &ref = val;
	然后，对与`ref`对应的`<gdb:value>`对象应用`value-dereference`将导致错误，而应用`value-referenced-value`将导致与`val`对应的`<gdb:value>`对象。
		(define scm-ref (parse-and-eval "ref"))
		(define err-ref (value-dereference scm-ref)) ;; error
		(define scm-val (value-referenced-value scm-ref)) ;; ok
	`<gdb:value>`对象`scm-val`与`val`对应的对象相同。

[Scheme Procedure] value-reference-value value
	返回一个新的`<gdb:value>`对象，它是对`<gdb:value>`对象`value`所封装的值的引用。

[Scheme Procedure] value-rvalue-reference-value value
	返回一个新的`<gdb:value>`对象，它是对`<gdb:value>`对象`value`所封装的值的右值引用。

[Scheme Procedure] value-const-value value
	返回一个新的`<gdb:value>`对象，它是`<gdb:value>`对象`value`的“const”版本。

[Scheme Procedure] value-field value field-name
	从`<gdb:value>`对象`value`中返回字段`field-name`。

[Scheme Procedure] value-subscript value index
	返回数组`value`在索引`index`处的值。`value`参数必须是可下标访问的`<gdb:value>`对象。

[Scheme Procedure] value-call value arg-list
	执行下级函数调用，将`value`作为要调用的函数的指针。`arg-list`列表的每个元素必须是一个`<gdb:value>`对象或可以转换为值的对象。结果是函数返回的值。

[Scheme Procedure] value->bool value
	返回表示`<gdb:value>`值的 Scheme boolean。该值必须是“类似整数的”。指针是可以的。

[Scheme Procedure] value->integer
	返回表示`<gdb:value>`值的 Scheme integer。该值必须是“类似整数的”。指针是可以的。

[Scheme Procedure] value->real
	返回表示`<gdb:value>`值的 Scheme real 数。该值必须是一个数字。

[Scheme Procedure] value->bytevector
	返回一个包含`<gdb:value>`值的原始内容的 Scheme bytevector。不执行任何转换、字节序或其他操作。

[Scheme Procedure] value->string value [#:encoding encoding] [#:errors errors] [#:length length]
	如果`value`表示一个字符串，那么此方法将内容转换为 Guile 字符串。否则，此方法将抛出异常。
	根据当前语言的规则解释值为字符串。如果提供了可选的`length`参数，字符串将被转换为该长度，并将包含字符串可能包含的任何嵌入零。否则，对于字符串以零终止的语言，将转换整个字符串。
	例如，在类似 C 的语言中，如果值是指向字符或`wchar_t`、`char16_t`或`char32_t`类型的整数的指针或数组，则该值是一个字符串。
	如果提供了可选的`encoding`参数，它必须是一个命名`<gdb:value>`中字符串编码的字符串，例如"ascii"、"iso-8859-6"或"utf-8"。它接受与 Guile 的`scm_from_stringn`函数的相应参数相同的编码，并且将使用 Guile 编解码器机制来转换字符串。如果未提供`encoding`，或者`encoding`为空字符串，则将使用目标字符集（请参阅第 10.21 节[字符集]，第 187 页），或者如果当前语言能够提供一个，则使用特定于语言的编码。
	可选的`errors`参数为`#f`、`error`或`substitute`之一。`error`和`substitute`必须是符号。如果未指定`errors`，或者其值为`#f`，则使用默认转换策略，该策略由 Scheme 函数`set-port-conversion-strategy!`设置。如果值为`error`，则如果有任何转换错误，将抛出异常。如果值为`substitute`，则任何转换错误都将被替换为问号。请参阅 GNU Guile 参考手册中的“字符串”部分。
	如果提供了可选的`length`参数，将获取字符串并将其转换为给定的长度。长度必须是一个 Scheme integer，而不是`<gdb:value>`整数。

[Scheme Procedure] value->lazy-string value [#:encoding encoding] [#:length length]
	如果此 `<gdb:value>` 表示一个字符串，那么此方法将该值转换为一个
	`<gdb:lazy-string`（见第 23.4.3.20 节[Guile 中的惰性字符串]，第 566 页）。
	否则，此方法将抛出一个异常。
	如果提供了可选的编码参数，它必须是一个命名 `<gdb:lazy-string` 编码的字符串。一些示例是：“ascii”、“iso-8859-6”或“utf-8”。
	如果编码参数是 gdb 不识别的编码，gdb 将引发一个错误。
	当打印一个惰性字符串时，gdb 编码机制用于在打印期间转换该字符串。如果未提供可选的编码参数，或者是一个空字符串，gdb 将自动选择最适合字符串类型的编码。有关 gdb 中编码的更多信息，请参见第 10.21 节[字符集]，第 187 页。
	如果提供了可选的长度参数，该字符串将被获取并编码为指定的字符长度。如果未提供长度参数，该字符串将被获取并编码，直到找到一个适当宽度的空字符。长度必须是一个 Scheme 整数，而不是一个 `<gdb:value>` 整数。

[Scheme Procedure] value-lazy? value
	如果值尚未从低级获取，则返回 `#t`。否则返回 `#f`。
	gdb 为了提高效率，不会在必要之前获取值。例如：
		(define myval (parse-and-eval "somevar"))

	此时不会获取 `somevar` 的值。它将在需要该值时或调用 `fetch-lazy` 过程时被获取。

[Scheme Procedure] make-lazy-value type address
	返回一个将从目标中惰性获取的 `<gdb:value>`。要获取其值的类型为 `<gdb:type>` 的对象由其类型和其目标内存地址指定，该地址是一个 Scheme 整数。

[Scheme Procedure] value-fetch-lazy! value
	如果值是一个惰性值（`(value-lazy? value)` 为 `#t`），则从低级获取该值。在该过程中发生的任何错误都将产生一个 Guile 异常。
	如果值不是一个惰性值，此方法没有效果。
	此函数的结果是未指定的。

[Scheme Procedure] value-print value
	返回 `<gdb:value>` 值的字符串表示（打印形式）。
```

#### 23.4.3.6 Guile 中的算术运算

（gdb）模块提供了几个用于对<gdb:value>对象执行算术运算的函数。算术运算的执行方式就好像是由目标完成的一样，因此具有目标语义，不一定是 Scheme 的语义。例如，操作以固定精度工作，而不是 Scheme 的任意精度。

在任何函数将整数或指针作为操作数的地方，gdb 将转换适当的 Scheme 值以执行操作。

```
[Scheme 过程] value-add a b
[Scheme 过程] value-sub a b
[Scheme 过程] value-mul a b
[Scheme 过程] value-div a b
[Scheme 过程] value-rem a b
[Scheme 过程] value-mod a b
[Scheme 过程] value-pow a b
[Scheme 过程] value-not a
[Scheme 过程] value-neg a
[Scheme 过程] value-pos a
[Scheme 过程] value-abs a
[Scheme 过程] value-lsh a b
[Scheme 过程] value-rsh a b
[Scheme 过程] value-min a b
[Scheme 过程] value-max a b
[Scheme 过程] value-lognot a
[Scheme 过程] value-logand a b
[Scheme 过程] value-logior a b
value-logxor a b
[Scheme 过程] value=? a b
[Scheme 过程] value<? a b
[Scheme 过程] value<=? a b
[Scheme 过程] value>? a b
[Scheme 过程] value>=? a b
```

Scheme 没有提供不等于函数，因此 gdb 中的 Guile 支持也没有。

#### 23.4.3.7 Types In Guile

gdb用类型为<gdb:type>的对象来表示底层的类型。

以下是(gdb)模块提供的与类型相关的过程：

```
[Scheme Procedure] type? object
	如果object是类型为<gdb:type>的对象，则返回#t。否则返回#f。

[Scheme Procedure] lookup-type name [#:block block]
	此函数通过名称查找类型，名称必须是字符串。
	如果提供了block，它是类型为<gdb:block>的对象，并且在该作用域中查找name。否则，在全局范围内查找。
	通常，此函数将返回<gdb:type>的实例。如果找不到指定的类型，它将抛出异常。

[Scheme Procedure] type-code type
	返回type的类型代码。类型代码将是下面定义的TYPE_CODE_常量之一。

[Scheme Procedure] type-tag type
	返回type的标签名称。标签名称是 C 和 C++中struct、union或enum之后的名称；并非所有语言都有此概念。如果此类型没有标签名称，则返回#f。

[Scheme Procedure] type-name type
	返回type的名称。如果此类型没有名称，则返回#f。

[Scheme Procedure] type-print-name type
	返回type的打印名称。即使对于匿名类型，此函数也会返回一些内容。
	例如，对于匿名 C 结构，将返回“struct {...}”。

[Scheme Procedure] type-sizeof type
	以目标字符单位返回此类型的大小。通常，目标的char类型将是 8 位字节。然而，在某些不寻常的平台上，此类型可能具有不同的大小。

[Scheme Procedure] type-strip-typedefs type
	返回一个新的<gdb:type>，表示在删除所有typedef层之后type的真实类型。

[Scheme Procedure] type-array type n1 [n2]
	返回一个新的<gdb:type>对象，它表示此类型的数组。如果提供一个参数，它是数组的包含上限；在这种情况下，下限为零。如果提供两个参数，第一个参数是数组的下限，第二个参数是数组的上限。数组的长度不能为负，但边界可以为负。

[Scheme Procedure] type-vector type n1 [n2]
	返回一个新的<gdb:type>对象，它表示此类型的向量。如果提供一个参数，它是向量的包含上限；在这种情况下，下限为零。如果提供两个参数，第一个参数是向量的下限，第二个参数是向量的上限。向量的长度不能为负，但边界可以为负。
	数组和向量的区别在于，数组的行为类似于 C：在表达式中使用时，它们会衰减为指向第一个元素的指针，而向量被视为一等值。

[Scheme Procedure] type-pointer type
	返回一个新的<gdb:type>对象，它表示指向type的指针。

[Scheme Procedure] type-range type
	返回一个包含两个元素的列表：type的下限和上限。如果type没有范围，则抛出异常。

[Scheme Procedure] type-reference type
	返回一个新的<gdb:type>对象，它表示对type的引用。

[Scheme Procedure] type-target type
	返回一个新的<gdb:type>对象，它表示type的目标类型。
	对于指针类型，目标类型是指向的对象的类型。对于数组类型（意味着类似 C 的数组），目标类型是数组元素的类型。对于函数或方法类型，目标类型是返回值的类型。对于复杂类型，目标类型是元素的类型。对于typedef，目标类型是别名类型。
	如果类型没有目标，此方法将抛出异常。

[Scheme Procedure] type-const type
	返回一个新的<gdb:type>对象，它表示type的const限定变体。

[Scheme Procedure] type-volatile type
	返回一个新的<gdb:type>对象，它表示type的volatile限定变体。

[Scheme Procedure] type-unqualified type
	返回一个新的<gdb:type>对象，它表示type的非限定变体。即，结果既不是const也不是volatile。

[Scheme Procedure] type-num-fields
	返回<gdb:type> type的字段数量。

[Scheme Procedure] type-fields type
	返回type的字段作为一个列表。对于结构和联合类型，fields具有通常的含义。范围类型有两个字段，最小值和最大值。枚举类型每个枚举常量有一个字段。函数和方法类型每个参数有一个字段。C++类的基类型也作为字段表示。如果类型没有字段，或者不属于这些类别之一，则将返回一个空列表。请参阅[Guile 中的类型字段]，第 539 页。

[Scheme Procedure] make-field-iterator type
	返回type的字段作为一个<gdb:iterator>对象。请参阅第 23.4.3.25 节[Guile 中的迭代器]，第 571 页。

[Scheme Procedure] type-field type field-name
	返回type中名为field-name的字段。结果是类型为<gdb:field>的对象。请参阅[Guile 中的类型字段]，第 539 页。如果类型没有字段，或者field-name不是type的字段，则抛出异常。
	例如，如果some-type是一个持有结构类型的<gdb:type>实例，您可以使用以下方式访问其foo字段：
		(define bar (type-field some-type "foo"))
	bar将是一个<gdb:field>对象。

[Scheme Procedure] type-has-field? type name
	如果<gdb:type> type具有名为name的字段，则返回#t。否则返回#f。
```

每个类型都有一个代码，它指示此类型属于哪个类别。可用的类型类别由(gdb)模块中定义的常量表示：

```
TYPE_CODE_PTR
	类型是指针。
TYPE_CODE_ARRAY
	类型是数组。
TYPE_CODE_STRUCT
	类型是结构。
TYPE_CODE_UNION
	类型是联合。
TYPE_CODE_ENUM
	类型是枚举。
TYPE_CODE_FLAGS
	位标志类型，用于诸如状态寄存器之类的事物。
TYPE_CODE_FUNC
	类型是函数。
TYPE_CODE_INT
	类型是整数类型。
TYPE_CODE_FLT
	浮点类型。
TYPE_CODE_VOID
	特殊类型void。
TYPE_CODE_SET
	Pascal 集类型。
TYPE_CODE_RANGE
	范围类型，即具有边界的整数类型。
TYPE_CODE_STRING
	字符串类型。请注意，这仅用于具有语言定义的字符串类型的某些语言；C 字符串不是以这种方式表示的。
TYPE_CODE_BITSTRING
	位字符串。已弃用。
TYPE_CODE_ERROR
	未知或错误的类型。
TYPE_CODE_METHOD
	C++中的方法类型。
TYPE_CODE_METHODPTR
	指向成员函数的指针。
TYPE_CODE_MEMBERPTR
	指向成员的指针。
TYPE_CODE_REF
	引用类型。
TYPE_CODE_RVALUE_REF
	C++11 右值引用类型。
TYPE_CODE_CHAR
	字符类型。
TYPE_CODE_BOOL
	布尔类型。
TYPE_CODE_COMPLEX
	复数浮点类型。
TYPE_CODE_TYPEDEF
	指向其他类型的typedef。
TYPE_CODE_NAMESPACE
	C++命名空间。
TYPE_CODE_DECFLOAT
	十进制浮点类型。
TYPE_CODE_INTERNAL_FUNCTION
	gdb 内部的函数。这是用于表示便利函数的类型（请参阅第 10.13 节[便利函数]，第 174 页）。
gdb.TYPE_CODE_XMETHOD
	gdb 内部的方法。这是用于表示 x 方法的类型（请参阅第 23.3.2.16 节[编写 x 方法]，第 446 页）。
gdb.TYPE_CODE_FIXED_POINT
	定点数。
gdb.TYPE_CODE_NAMESPACE
	Fortran 命名列表。
```

在(gdb types)Guile 模块中提供了对类型的进一步支持（请参阅第 23.4.5.2 节[Guile 类型模块]，第 574 页）。

每个字段都表示为类型为<gdb:field>的对象。

以下是(gdb)模块提供的与字段相关的过程：

```
[Scheme Procedure] field? object
	如果object是类型为<gdb:field>的对象，则返回#t。否则返回#f。

[Scheme Procedure] field-name field
	返回字段的名称，对于匿名字段返回#f。

[Scheme Procedure] field-type field
	返回字段的类型。通常是<gdb:type>的实例，但在某些情况下可能是#f。

[Scheme Procedure] field-enumval field
	返回由<gdb:field> field表示的枚举值。

[Scheme Procedure] field-bitpos field
	返回<gdb:field> field的位位置。此属性不适用于静态字段（如 C++中的）。

[Scheme Procedure] field-bitsize field
	如果字段是打包的，或者是位域，则返回<gdb:field> field的大小（以位为单位）。否则，返回零；在这种情况下，字段的大小由其类型给出。

[Scheme Procedure] field-artificial? field
	如果字段是人工的，通常意味着它是由编译器提供而不是用户提供的，则返回#t。否则返回#f。

[Scheme Procedure] field-base-class? field
	如果字段表示 C++结构的基类，则返回#t。否则返回#f。
```

#### 23.4.3.8 Guile 美化打印 API

提供了一个示例输出（参见第 10.10 节[美化打印]，第 168 页）。

一个美化打印机由类型为 `<gdb:pretty-printer>` 的对象表示。美化打印机对象通过 `make-pretty-printer` 创建。

(gdb) 模块提供了以下与美化打印机相关的过程：

```
[Scheme 过程]  `make-pretty-printer name lookup-function`
	返回一个名为 `name` 的 `<gdb:pretty-printer>` 对象。
	`lookup-function` 是一个带有一个参数的函数：要打印的值。如果该值由这个美化打印机处理，那么 `lookup-function` 返回一个类型为 `<gdb:pretty-printer-worker>` 的对象来执行实际的美化打印。否则，`lookup-function` 返回 `#f`。

[Scheme 过程] `pretty-printer? object`
	如果 `object` 是一个 `<gdb:pretty-printer>` 对象，则返回 `#t`。否则返回 `#f`。

[Scheme 过程] `pretty-printer-enabled? pretty-printer`
	如果美化打印机已启用，则返回 `#t`。否则返回 `#f`。

[Scheme 过程] `set-pretty-printer-enabled! pretty-printer flag`
	将美化打印机的启用标志设置为 `flag`。返回的值未指定。

[Scheme 过程] `pretty-printers`
	返回	局美化打印机的列表。

[Scheme 过程] `set-pretty-printers! pretty-printers`
	将全局美化打印机的列表设置为 `pretty-printers`。返回的值未指定。

[Scheme 过程] `make-pretty-printer-worker display-hint to-string children`
	返回一个类型为 `<gdb:pretty-printer-worker>` 的对象。
	此函数接受三个参数：
	`display-hint`
		`display-hint` 通过 MI 向 gdb 或 gdb 前端提供一个提示，以更改正在打印的值的格式。该值必须是一个字符串或 `#f`（表示没有提示）。`display-hint` 的几个值由 gdb 预定义：
			- array		表示正在打印的对象是“类似数组的”。CLI 使用此来尊重诸如 `set print elements` 和 `set print array` 等参数。
			- map		表示正在打印的对象是“类似映射的”，并且该值的子节点可以假定在键和值之间交替。
			- string	表示正在打印的对象是“类似字符串的”。如果打印机的 `to-string` 函数返回某种 Guile 字符串，那么 gdb 将调用其内部特定于语言的字符串打印函数来格式化字符串。对于 CLI，这意味着添加引号，可能转义一些字符，尊重 `set print elements` 等。
	`to-string`
		`to-string` 要么是一个带有一个参数的函数，即 `<gdb:pretty-printer-worker>` 对象，要么是 `#f`。
		从 CLI 打印时，如果存在 `to-string` 方法，那么 gdb 将在子节点返回的结果之前添加其结果。这种格式化的具体方式取决于显示提示，并且随着更多提示的添加可能会发生变化。此外，根据打印设置（参见第 10.9 节[打印设置]，第 157 页），CLI 可能仅在堆栈跟踪中打印 `to-string` 的结果，而省略子节点的结果。
		如果此方法返回一个字符串，它将按原样打印。
		否则，如果此方法返回一个 `<gdb:value>` 的实例，那么 gdb 将打印此值。这可能会导致对另一个美化打印机的调用。
		如果该方法返回一个可转换为 `<gdb:value>` 的 Guile 值，那么 gdb 将执行转换并打印结果值。同样，这可能会导致对另一个美化打印机的调用。
		Guile 标量（整数、浮点数和布尔值）和字符串可转换为 `<gdb:value>`；其他类型则不行。
		最后，如果此方法返回 `#f`，则在此方法中不执行进一步的操作，也不打印任何内容。
		如果结果不是这些类型之一，则会引发异常。
		`to-string` 也可以是 `#f`，在这种情况下，将由子节点来打印值。
	`children`
	`children` 要么是一个带有一个参数的函数，即 `<gdb:pretty-printer-worker>` 对象，要么是 `#f`。
	gdb 将在美化打印机上调用此函数来计算美化打印机值的子节点。
	此函数必须返回一个 `<gdb:iterator>` 对象。迭代器返回的每个项必须是一个包含两个元素的元组。第一个元素是子节点的“名称”；第二个元素是子节点的值。该值可以是任何可转换为 gdb 值的 Guile 对象。
	如果 `children` 是 `#f`，gdb 将表现得好像该值没有子节点一样。
	根据 `'set print max-depth'` 的值（参见第 10.9 节[打印设置]，第 157 页），子节点可能会被隐藏而不显示。
```

gdb 提供了一个函数，可用于查找 `<gdb:value>` 的默认美化打印机：

```
[Scheme 过程] `default-visualizer value`
	此函数接受一个 `<gdb:value>` 对象作为参数。如果存在此值的美化打印机，则返回它。如果不存在这样的打印机，则返回 `#f`。
```

#### 23.4.3.9 选择 Guile 漂亮打印机

gdb 搜索有三组漂亮打印机：

• 每个对象文件的漂亮打印机列表（见第 23.4.3.14 节[Guile 中的对象文件]，第 552 页）。

• 每个程序空间的漂亮打印机列表（见第 23.4.3.13 节[Guile 中的程序空间]，第 551 页）。

• 全局漂亮打印机列表（见第 23.4.3.8 节[Guile 漂亮打印 API]，第 539 页）。在调试任何下级进程时，这些打印机都可用。

通过依次将要打印的值传递给每个启用对象的查找函数来进行漂亮打印机查找。当查找函数返回非#f 值或列表耗尽时，查找停止。查找函数必须返回一个<gdb:pretty-printer-worker>对象或#f。否则将抛出异常。

gdb 首先检查当前程序空间中每个<gdb:objfile>的 objfile-pretty-printers 的结果，并迭代调用该<gdb:objfile>列表中的每个启用查找函数，直到返回一个非#f 对象。如果在对象文件列表中未找到漂亮打印机，gdb 然后搜索当前程序空间的 progspace-pretty-printers 结果，调用每个启用函数，直到返回一个非#f 对象。在这些列表耗尽后，它尝试使用 pretty-printers 获得的全局漂亮打印机列表，再次调用每个启用函数，直到返回一个非#f 对象。

对象文件的搜索顺序未指定。对于给定的列表，总是从列表的头部调用函数，并顺序迭代直到列表结束或返回一个<gdb:pretty-printer-worker>对象。

由于各种原因，一个漂亮打印机可能无法工作。例如，底层数据结构可能已更改，而漂亮打印机已过时。

损坏的漂亮打印机的后果非常严重，以至于 gdb 提供了支持来启用和禁用单个打印机。例如，如果打印帧参数打开，并且任何参数使用损坏的打印机打印，则回溯可能会变得非常难以阅读。

通过调用 set-pretty-printer-enabled!从 Scheme 启用和禁用漂亮打印机。见第 23.4.3.8 节[Guile 漂亮打印 API]，第 539 页。

#### 23.4.3.10 编写 Guile 美化打印机

一个美化打印机由两个基本部分组成：一个查找函数，用于确定是否支持该类型；以及打印机本身。

以下是一个示例，展示如何编写一个`std::string`打印机。有关详细信息，请参阅第 23.4.3.8 节[Guile 美化打印 API]，第 539 页。

```
(define (make-my-string-printer value)
	"Print a my::string string"
	(make-pretty-printer-worker
	"string"
	(lambda (printer)
		(value-field value "_data"))
	#f))
```

以下是一个用于上述打印机示例的查找函数的示例：

```
(define (str-lookup-function pretty-printer value)
	(let ((tag (type-tag (value-type value))))
		(and tag
			(string-prefix? "std::string<" tag)
			(make-my-string-printer value))))
```

然后，要在全局打印机列表中注册此打印机：

```
(append-pretty-printer!
(make-pretty-printer "my-string" str-lookup-function))
```

示例查找函数提取值的类型，并尝试将其与它可以美化打印的类型匹配。如果它是打印机可以美化打印的类型，它将返回一个<gdb:pretty-printer-worker>对象。如果不是，它返回#f。

我们建议将您的核心美化打印机放入 Guile 包中。如果您的美化打印机用于与库一起使用，我们进一步建议将版本号嵌入到包名称中。这种做法将使 gdb 能够同时加载您的美化打印机的多个版本，因为它们将具有不同的名称。

您应该编写自动加载代码（请参阅第 23.4.4 节[Guile 自动加载]，第 573 页），以便可以多次评估而不改变其含义。一个理想的自动加载文件将仅包含对您的打印机模块的导入，然后是对具有当前 objfile 的注册美化打印机的调用。

总体而言，这种方法可以很好地扩展到多个 inferior，每个 inferior 可能使用不同的库版本。在 Guile 包名称中嵌入版本号将确保 gdb 能够同时加载两组打印机。然后，由于查找美化打印机是通过 objfile 完成的，并且由于您的自动加载代码负责将库的打印机注册到特定的 objfile，gdb 将为每个 inferior 使用的库的特定版本找到正确的打印机。

继续以 my::string 示例为例，此代码可能出现在(my-project my-library v1)中：

```
(use-modules (gdb))
(define (register-printers objfile)
  (append-objfile-pretty-printer!
  (make-pretty-printer "my-string" str-lookup-function)))
```

然后自动加载文件的相应内容将是：

```
(use-modules (gdb) (my-project my-library v1))
(register-printers (current-objfile))
```

前面的示例说明了一个基本的美化打印机。有一些可以改进的地方。打印机仅处理一种类型，而库通常有几种类型。可以为库中的每个所需类型安装一个查找函数，但也可以有一个单个查找函数识别多种类型。后者是处理此问题的常规方式。如果一个美化打印机可以处理多种数据类型，那么它的子打印机就是用于各个数据类型的打印机。

(gdb printing)模块提供了一种解决此问题的正式方法（请参阅第 23.4.5.1 节[Guile 打印模块]，第 573 页）。以下是另一个处理多种类型的示例。

这些是我们要美化打印的类型：

```
struct foo { int a, b; };
struct bar { struct foo x, y; };
```

以下是打印机：

```
(define (make-foo-printer value)
  "Print a foo object"
    (make-pretty-printer-worker
    "foo"
    (lambda (printer)
      (format #f "a=<~a> b=<~a>"
             (value-field value "a") (value-field value "a")))
    #f))

(define (make-bar-printer value)
  "Print a bar object"
    (make-pretty-printer-worker
    "foo"
    (lambda (printer)
      (format #f "x=<~a> y=<~a>"
             (value-field value "x") (value-field value "y")))
    #f))
```

此示例不需要查找函数，这由(gdb printing)模块处理。而是提供了一个函数来构建处理查找的对象。

```
(use-modules (gdb printing))

(define (build-pretty-printer)
  (let ((pp (make-pretty-printer-collection "my-library")))
	(pp-collection-add-tag-printer "foo" make-foo-printer)
	(pp-collection-add-tag-printer "bar" make-bar-printer)
	pp))
```

以下是自动加载支持：

```
(use-modules (gdb) (my-library))
(append-objfile-pretty-printer! (current-objfile) (build-pretty-printer))
```

最后，当此打印机加载到 gdb 中时，这是'info pretty-printer'的相应输出：

```
(gdb) info pretty-printer
my_library.so:
  my-library
    foo
	bar
```

#### 23.4.3.11 Commands In Guile

你可以在 Guile 中实现新的 gdb CLI 命令。CLI 命令对象是通过`make-command`Guile 函数创建的，并通过`register-command!`Guile 函数添加到 gdb 中。采取这种两步方法是为了将向 gdb 添加命令的副作用与`make-command`分离出来。

不支持多行命令，即由多行组成并以`end`终止的命令。

```
[Scheme Procedure] make-command name [#:invoke invoke] [#:command-class command-class] [#:completer-class completer] [#:prefix? prefix] [#:doc doc-string]
	参数name是命令的名称。如果name由多个单词组成，那么最初的单词将作为前缀命令进行查找。在这种情况下，如果其中一个前缀命令不存在，则会引发异常。
	结果是表示该命令的<gdb:command>对象。该命令在通过register-command!注册到 gdb 之前不可用。其余参数是可选的。
	参数invoke是一个具有三个参数的过程：self、args和from-tty。参数self是表示该命令的<gdb:command>对象。参数args是一个字符串，表示在去除开头和结尾的空白字符后传递给命令的参数。参数from-tty是一个布尔标志，用于指定命令是否应将自己视为由用户交互调用。如果此函数引发异常，它将被转换为 gdb 错误调用。否则，返回值将被忽略。
	参数command-class是下面定义的‘COMMAND_’常量之一。此参数告诉 gdb 如何在帮助系统中对新命令进行分类。默认值是COMMAND_NONE。
	参数completer可以是#f、下面定义的‘COMPLETE_’常量之一，或者是一个过程（也在下面定义）。此参数告诉 gdb 如何对该命令执行补全。如果未提供或值为#f，则不对该命令执行补全。
	参数prefix是一个布尔标志，指示新命令是否为前缀命令；可以注册此命令的子命令。
	参数doc-string是新命令的帮助文本。如果未提供文档字符串，则使用默认值“此命令未记录。”。

[Scheme Procedure] register-command! command
	将命令（一个<gdb:command>对象）添加到 gdb 的命令列表中。多次注册一个命令是错误的。结果未指定。

[Scheme Procedure] command? object
	如果object是一个<gdb:command>对象，则返回#t。否则返回#f。

[Scheme Procedure] dont-repeat
	默认情况下，当用户在命令提示符处输入空行时，gdb 命令将被重复执行。命令可以通过调用dont-repeat函数来抑制此行为。这类似于用户命令dont-repeat，请参阅第 23.1.1 节[定义]，第 391 页。

[Scheme Procedure] string->argv string
	根据 gdb 的argv解析规则将字符串转换为字符串列表。建议使用此方法以保持一致性。参数由空格分隔，并且可以被引号括起来。示例：
		scheme@(guile-user)> (string->argv "1 2\\ \\\"3 ’4 \"5’ \"6 ’7\"")
		$1 = ("1" "2 \"3" "4 \"5" "6 ’7")

[Scheme Procedure] throw-user-error message. args
	抛出一个 gdb:user-error 异常。参数message是错误消息作为格式字符串，类似于formatScheme 函数的fmt参数。请参阅 GNU Guile 参考手册中的“格式化输出”部分。参数args是消息的可选参数列表。
	当命令检测到某种用户错误时，例如错误的命令参数，就会使用此函数。
		(gdb) guile (use-modules (gdb))
		(gdb) guile
		(register-command! (make-command "test-user-error"
		#:command-class COMMAND_OBSCURE
		#:invoke (lambda (self arg from-tty)
			(throw-user-error "Bad argument ~a" arg))))
		end
		(gdb) test-user-error ugh
		ERROR: Bad argument ugh

[completer] self text word
	如果make-command的completer选项是一个过程，它接受三个参数：self（即<gdb:command>对象）、text（字符串，表示光标的位置之前的完整命令行）和word（字符串，表示命令行的最后一个单词；这是使用断字启发式算法计算的）。
	所有形式的补全都由此函数处理，即TAB和M-?键绑定（请参阅第 3.3 节[补全]，第 24 页）以及完整命令（请参阅第 3.6 节[帮助]，第 29 页）。
	此过程可以返回几种值：
	• 如果返回值是一个列表，列表的内容将用作补全结果。由补全器确保内容实际上完成了单词。允许空列表，这意味着没有可用的补全。仅使用列表的字符串元素；忽略列表中的其他元素。
	• 如果返回值是一个<gdb:iterator>对象，则对其进行迭代以获取补全结果。由补全过程确保结果实际上完成了单词。仅使用结果的字符串元素；忽略序列中的其他元素。
	• 所有其他结果都被视为没有可用的补全。
```

当注册一个新命令时，它将被声明为某些通用命令类的成员。这用于在在线帮助系统中对顶级命令进行分类；请注意，前缀命令不会在其自己的类别下列出，而是在其顶级命令的类别下列出。可用的分类由`gdb`模块中定义的常量表示：

```
COMMAND_NONE
	该命令不属于任何特定类别。此类别中的命令不会在任何帮助类别中显示。这是默认值。

COMMAND_RUNNING
	该命令与运行下级进程有关。例如，`start`、`step`和`continue`属于此类别。在 gdb 提示符下输入`help running`以查看此类别中的命令列表。

COMMAND_DATA
	该命令与数据或变量有关。例如，`call`、`find`和`print`属于此类别。在 gdb 提示符下输入`help data`以查看此类别中的命令列表。

COMMAND_STACK
	该命令与堆栈操作有关。例如，`backtrace`、`frame`和`return`属于此类别。在 gdb 提示符下输入`help stack`以查看此类别中的命令列表。

COMMAND_FILES
	此类用于与文件相关的命令。例如，`file`、`list`和`section`属于此类别。在 gdb 提示符下输入`help files`以查看此类别中的命令列表。

COMMAND_SUPPORT
	此命令应用于“支持设施”，通常意味着在与 gdb 交互时对用户有用的东西，但与下级进程的状态无关。例如，`help`、`make`和`shell`属于此类别。在 gdb 提示符下输入`help support`以查看此类别中的命令列表。

COMMAND_STATUS
	该命令是与“info”相关的命令，即与 gdb 本身的状态有关。例如，`info`、`macro`和`show`属于此类别。在 gdb 提示符下输入`help status`以查看此类别中的命令列表。

COMMAND_BREAKPOINTS
	该命令与断点有关。例如，`break`、`clear`和`delete`属于此类别。在 gdb 提示符下输入`help breakpoints`以查看此类别中的命令列表。

COMMAND_TRACEPOINTS
	该命令与跟踪点有关。例如，`trace`、`actions`和`tfind`属于此类别。在 gdb 提示符下输入`help tracepoints`以查看此类别中的命令列表。

COMMAND_USER
	该命令是供用户使用的通用命令，通常不适合其他类别之一。在 gdb 提示符下输入`help user-defined`以查看此类别中的命令列表，以及 gdb 宏列表（请参阅第 23.1 节[序列]，第 391 页）。

COMMAND_OBSCURE
	该命令仅在不寻常的情况下使用，或者对用户没有普遍兴趣。例如，`checkpoint`、`fork`和`stop`属于此类别。在 gdb 提示符下输入`help obscure`以查看此类别中的命令列表。

COMMAND_MAINTENANCE
	该命令仅对 gdb 维护者有用。`maintenance`和`flushregs`命令属于此类别。在 gdb 提示符下输入`help internals`以查看此类别中的命令列表。
```
	
一个新命令可以使用预定义的补全函数，要么通过在初始化时通过参数指定，要么从补全过程中返回。这些预定义的补全常量都在`gdb`模块中定义：

```
COMPLETE_NONE
	此常量表示不应进行任何补全。

COMPLETE_FILENAME
	此常量表示应执行文件名补全。

COMPLETE_LOCATION
	此常量表示应执行位置补全。请参阅第 9.2 节[位置规范]，第 126 页。

COMPLETE_COMMAND
	此常量表示补全应检查 gdb 命令名称。

COMPLETE_SYMBOL
	此常量表示补全应使用符号名称作为源。

COMPLETE_EXPRESSION
	此常量表示应在表达式上进行补全。通常这意味着在符号名称上完成，但某些语言解析器也支持在字段名称上完成。
```

以下代码片段展示了如何在 Guile 中实现一个简单的 CLI 命令：

```
(gdb) guile
(register-command! (make-command "hello-world"
  #:command-class COMMAND_USER
  #:doc "Greet the whole world."
  #:invoke (lambda (self args from-tty) (display "Hello, World!\n"))))
end
(gdb) hello-world
Hello, World!
```

#### 23.4.3.12 在 Guile 中的参数

你可以使用 Guile3 实现新的 gdb 参数。

gdb 中已经存在许多可以设置的参数。两个例子是：`set follow-fork` 和 `set charset`。设置这些参数会影响 gdb 中的某些行为。类似地，你可以定义可用于影响自定义 Guile 脚本和命令行为的参数。

一个新参数是使用 `make-parameter` Guile 函数定义的，并使用 `register-parameter!` Guile 函数添加到 gdb 中。采取这种两步方法是为了将向 gdb 添加参数的副作用与 `make-parameter` 分开。

参数通过 `set` 和 `show` 命令暴露给用户。请参阅第 3.6 节[帮助]，第 29 页。

```
[Scheme 过程] make-parameter name [#:command-class command-class]
				[#:parameter-type parameter-type] [#:enum-list enum-list]
				[#:set-func set-func] [#:show-func show-func] [#:doc doc]
				[#:set-doc set-doc] [#:show-doc show-doc] [#:initial-value initial-value]
	
	参数名称是新参数的名称。如果名称由多个单词组成，那么将查找初始单词作为前缀参数。这可以通过 set print 系列参数来说明。如果名称是 print foo，那么将搜索 print 作为前缀参数。在这种情况下，参数随后可以在 gdb 中作为 set print foo 访问。如果名称由多个单词组成，并且找不到前缀参数组，则会引发异常。
	结果是表示该参数的 <gdb:parameter> 对象。该参数在使用 register-parameter! 注册到 gdb 之前不可用。其余参数是可选的。
	参数 command-class 应该是一个 ‘COMMAND_’ 常量之一（请参阅第 23.4.3.11 节[Guile 中的命令]，第 544 页）。此参数告诉 gdb 在帮助系统中如何对新参数进行分类。默认值是 COMMAND_NONE。
	参数 parameter-type 应该是下面定义的 ‘PARAM_’ 常量之一。此参数告诉 gdb 新参数的类型；此信息用于输入验证和完成。默认值是 PARAM_BOOLEAN。
	如果 parameter-type 是 PARAM_ENUM，那么 enum-list 必须是一个字符串列表。这些字符串表示参数的可能值。
	如果 parameter-type 不是 PARAM_ENUM，那么 enum-list 的存在将导致引发异常。
	参数 set-func 是一个带有一个参数的函数：self，它是表示参数的 <gdb:parameter> 对象。当通过 set API（例如，set foo off）更改参数的值时，gdb 将调用此函数。参数的值已经设置为新值。此函数必须返回一个要显示给用户的字符串。如果字符串非空，gdb 将添加一个尾随换行符。当设置参数时，gdb 通常不会打印任何内容，因此通常此函数应返回 ""。非空字符串结果通常用于显示警告和错误。
	参数 show-func 是一个带有两个参数的函数：self，它是表示参数的 <gdb:parameter> 对象，svalue 是当前值的字符串表示形式。当调用参数的 show API（例如，show foo）时，gdb 将调用此函数。此函数必须返回一个字符串，并将显示给用户。gdb 将添加一个尾随换行符。
	参数 doc 是新参数的帮助文本。如果没有文档字符串，则使用默认值。
	参数 set-doc 是此参数的 set 命令的帮助文本。
	参数 show-doc 是此参数的 show 命令的帮助文本。
	参数 initial-value 指定参数的初始值。如果它是一个函数，它接受一个参数，即 <gdb:parameter> 对象，其结果用作参数的初始值。初始值必须对于参数类型有效，否则将引发异常。

[Scheme 过程] register-parameter! parameter
	将参数（一个 <gdb:parameter> 对象）添加到 gdb 的参数列表中。多次注册一个参数是错误的。结果未指定。

[Scheme 过程] parameter? object
	如果对象是一个 <gdb:parameter> object，则返回 #t。否则返回 #f。

[Scheme 过程] parameter-value parameter
	返回参数的值，该值可以是一个 <gdb:parameter> 对象或一个命名参数的字符串。

[Scheme 过程] set-parameter-value! parameter new-value
	将参数的值分配为新值。参数必须是 <gdb:parameter> 对象类型的对象。在进行赋值时，gdb 会进行验证。
```

当定义一个新参数时，必须指定其类型。可用类型由 gdb 模块中定义的常量表示：

```
PARAM_BOOLEAN
	值是一个普通的布尔值。Guile 布尔值 #t 和 #f 是唯一有效的值。

PARAM_AUTO_BOOLEAN
	值有三种可能的状态：真、假和“自动”。在 Guile 中，真和假使用布尔常量表示，“自动”使用 #:auto 表示。

PARAM_UINTEGER
	值是一个无符号整数。#:unlimited 的值应解释为“无限制”，‘0’ 的值是保留的，不应使用。

PARAM_ZINTEGER
	值是一个整数。

PARAM_ZUINTEGER
	值是一个无符号整数。

PARAM_ZUINTEGER_UNLIMITED
	值是在范围 ‘[0, INT_MAX]’ 内的整数。#:unlimited 的值表示“无限制”，‘-1’ 的值是保留的，不应使用，其他负数不允许。

PARAM_STRING
	值是一个字符串。当用户修改字符串时，任何转义序列，如 ‘\t’、‘\f’ 和八进制转义，都将转换为相应的字符并编码为当前主机字符集。

PARAM_STRING_NOESCAPE
	值是一个字符串。当用户修改字符串时，转义字符将原封不动地传递。

PARAM_OPTIONAL_FILENAME
	值是一个文件名（一个字符串）或 #f。

PARAM_FILENAME
	值是一个文件名。这与 PARAM_STRING_NOESCAPE 类似，但用于完成文件名。

PARAM_ENUM
	值是一个字符串，它必须是在创建参数时提供的字符串常量集合之一。
```

#### 23.4.3.13 在 Guile 中的程序空间

一个程序空间，或 progspace，代表一个地址空间的符号视图。它由程序的所有 objfiles 组成。请参阅第 23.4.3.14 节[Guile 中的 Objfiles]，第 552 页。有关程序空间的更多详细信息，请参阅第 4.9 节[下级连接和程序]，第 42 页。

每个 progspace 由一个`<gdb:progspace>`smob 的实例表示。请参阅第 23.4.3.3 节[GDB Scheme 数据类型]，第 525 页。

在 (gdb) 模块中提供了以下与 progspace 相关的函数：

```
[Scheme 过程] progspace? object
	如果 object 是一个`<gdb:progspace>`对象，则返回`#t`。否则返回`#f`。

[Scheme 过程] progspace-valid? progspace
	如果 progspace 有效，则返回`#t`，如果无效则返回`#f`。如果它所引用的程序不再在 gdb 中加载，`<gdb:progspace>`对象可能会变为无效。

[Scheme 过程] current-progspace
	此函数返回当前选定下级的程序空间。始终有一个当前的 progspace，此函数永远不会返回`#f`。请参阅第 4.9 节[下级连接和程序]，第 42 页。

[Scheme 过程] progspaces
	返回 gdb 当前已知的所有 progspaces 的列表。

[Scheme 过程] progspace-filename progspace
	将 progspace 的绝对文件名作为字符串返回。这是作为`file`或`symbol-file`命令的参数传递的文件的名称。如果程序空间没有关联的文件名，则返回`#f`。例如，当 gdb 在没有要调试的程序的情况下启动时，就会发生这种情况。
	如果 progspace 无效，则抛出`gdb:invalid-object-error`异常。

[Scheme 过程] progspace-objfiles progspace
	返回 progspace 的 objfiles 列表。结果中 objfiles 的顺序是任意的。每个元素都是`<gdb:objfile>`类型的对象。请参阅第 23.4.3.14 节[Guile 中的 Objfiles]，第 552 页。
	如果 progspace 无效，则抛出`gdb:invalid-object-error`异常。

[Scheme 过程] progspace-pretty-printers progspace
	返回 progspace 的漂亮打印机列表。每个元素都是`<gdb:pretty-printer>`类型的对象。有关更多信息，请参阅第 23.4.3.8 节[Guile 漂亮打印 API]，第 539 页。

[Scheme 过程] set-progspace-pretty-printers! progspace printer-list
	将 progspace 的已注册`<gdb:pretty-printer>`对象列表设置为`printer-list`。有关更多信息，请参阅第 23.4.3.8 节[Guile 漂亮打印 API]，第 539 页。
```

#### 23.4.3.14 在 Guile 中的目标文件

gdb 从各种包含符号的文件中为一个子进程加载符号（见第 18.1 节[文件]，第 291 页）。这些文件包括主可执行文件、子进程使用的任何共享库以及任何单独的调试信息文件（见第 18.3 节[单独的调试文件]，第 300 页）。gdb 将这些包含符号的文件称为目标文件。

每个目标文件都表示为类型为`<gdb:objfile>`的对象。

(gdb)模块提供了以下与目标文件相关的过程：

```
[Scheme 过程] objfile? object
	如果 object 是一个`<gdb:objfile>`对象，则返回`#t`。否则返回`#f`。

[Scheme 过程] objfile-valid? objfile
	如果 objfile 有效，则返回`#t`，如果无效则返回`#f`。如果它所引用的目标文件不再在 gdb 中加载，`<gdb:objfile>`对象可能会变为无效。在调用该过程时，如果它无效，所有其他`<gdb:objfile>`过程将抛出异常。

[Scheme 过程] objfile-filename objfile
	返回 objfile 的文件名作为字符串，并解析符号链接。

[Scheme 过程] objfile-progspace objfile
	返回此对象文件所在的`<gdb:progspace>`。有关 progspaces 的更多信息，请参见第 23.4.3.13 节[Guile 中的 progspaces]，第 551 页。

[Scheme 过程] objfile-pretty-printers objfile
	返回 objfile 的已注册`<gdb:pretty-printer>`对象的列表。有关更多信息，请参见第 23.4.3.8 节[Guile 漂亮打印 API]，第 539 页。

[Scheme 过程] set-objfile-pretty-printers! objfile printer-list
	将 objfile 的已注册`<gdb:pretty-printer>`对象的列表设置为 printer-list。printer-list 必须是`<gdb:pretty-printer>`对象的列表。有关更多信息，请参见第 23.4.3.8 节[Guile 漂亮打印 API]，第 539 页。

[Scheme 过程] current-objfile
	在自动加载 Guile 脚本时（见第 23.4.4 节[Guile 自动加载]，第 573 页），gdb 将“当前目标文件”设置为相应的目标文件。此函数返回当前目标文件。如果没有当前目标文件，此函数返回`#f`。

[Scheme 过程] objfiles
	返回当前程序空间中的所有目标文件的列表。
```
	
#### 23.4.3.15 从 Guile 访问下层栈帧。

当调试的程序停止时，gdb 能够分析其调用栈（见第 8.1 节[栈帧]，第 113 页）。`<gdb:frame>`类表示栈中的一个帧。一个`<gdb:frame>`对象仅在其对应的帧存在于下层栈中时才有效。如果您尝试使用无效的帧对象，gdb 将抛出一个`gdb:invalid-object`异常（见第 23.4.3.4 节[Guile 异常处理]，第 527 页）。

可以使用`equal?`函数比较两个`<gdb:frame>`对象是否相等，例如：

```
(gdb) guile (equal? (newest-frame) (selected-frame))
#t
```

以下是`(gdb)`模块提供的与帧相关的过程：

```
[Scheme 过程] frame? object
	如果object是一个<gdb:frame>对象，则返回#t。否则返回#f。

[Scheme 过程] frame-valid? frame
	如果frame有效，则返回#t，否则返回#f。如果frame所引用的帧在下层中不再存在，帧对象可能会变为无效。如果在调用过程时帧无效，所有<gdb:frame>过程都将抛出异常。

[Scheme 过程] frame-name frame
	返回frame的函数名，如果无法获取则返回#f。

[Scheme 过程] frame-arch frame
	返回与frame的架构对应的<gdb:architecture>对象。见第 23.4.3.21 节[Guile 中的架构]，第 567 页。

[Scheme 过程] frame-type frame
	返回frame的类型。该值可以是以下之一：
		NORMAL_FRAME
			一个普通的栈帧。
		DUMMY_FRAME
			在执行下层函数调用时，gdb 创建的一个假栈帧。
		INLINE_FRAME
			表示内联函数的帧。该函数被内联到一个比此帧更旧的NORMAL_FRAME中。
		TAILCALL_FRAME
			表示尾调用的帧。见第 11.2 节[尾调用帧]，第 196 页。
		SIGTRAMP_FRAME
			一个信号跳板帧。这是操作系统在调用信号处理程序时创建的帧。
		ARCH_FRAME
			表示跨架构调用的假栈帧。
		SENTINEL_FRAME
			这类似于NORMAL_FRAME，但仅用于最新的帧。

[Scheme 过程] frame-unwind-stop-reason frame
	返回一个整数，表示无法找到更多帧朝向最外层帧的原因。使用unwind-stop-reason-string将此函数返回的值转换为字符串。该值可以是以下之一：
		FRAME_UNWIND_NO_REASON
			没有特别的原因（应该可以访问到更旧的帧）。
		FRAME_UNWIND_NULL_ID
			前一个帧的分析器返回无效结果。
		FRAME_UNWIND_OUTERMOST
			此帧是最外层的。
		FRAME_UNWIND_UNAVAILABLE
			无法进一步展开，因为这需要知道尚未收集的寄存器或内存的值。
		FRAME_UNWIND_INNER_ID
			此帧 ID 看起来应该属于下一个帧，但我们从之前的帧中获取了它。通常，这是展开器失败的迹象。也可能表示栈损坏。
		FRAME_UNWIND_SAME_ID
			此帧与前一个帧具有相同的 ID。这意味着进一步展开几乎肯定会给我们另一个具有完全相同 ID 的帧，因此中断链。通常，这是展开器失败的迹象。也可能表示栈损坏。
		FRAME_UNWIND_NO_SAVED_PC
			帧展开器未找到任何保存的 PC，但我们需要一个来进一步展开。
		FRAME_UNWIND_MEMORY_ERROR
			帧展开器在尝试访问内存时导致错误。
		FRAME_UNWIND_FIRST_ERROR
			任何大于或等于此值的停止原因都表示某种错误。这个特殊值有助于编写代码来测试展开中的错误，即使在未来的 gdb 版本中修改了其他值的列表，该代码也能正常工作。使用它，您可以编写：
				(define reason (frame-unwind-stop-readon (selected-frame)))
				(define reason-str (unwind-stop-reason-string reason))
				(if (>= reason FRAME_UNWIND_FIRST_ERROR)
				(format #t "An error occurred: ~s\n" reason-str))

[Scheme 过程] frame-pc frame
	返回帧的恢复地址。

[Scheme 过程] frame-block frame
	返回帧的代码块作为一个<gdb:block>对象。见第 23.4.3.16 节[Guile 中的块]，第 555 页。

[Scheme 过程] frame-function frame
	返回与该帧对应的函数的符号作为一个<gdb:symbol>对象，如果没有则返回#f。见第 23.4.3.17 节[Guile 中的符号]，第 557 页。

[Scheme 过程] frame-older frame
	返回调用frame的帧。

[Scheme 过程] frame-newer frame
	返回被frame调用的帧。

[Scheme 过程] frame-sal frame
	返回帧的<gdb:sal>（符号表和行）对象。见第 23.4.3.18 节[Guile 中的符号表]，第 561 页。

[Scheme 过程] frame-read-register frame register
	返回frame中register的值。register应该是一个字符串，如'pc'。

[Scheme 过程] frame-read-var frame variable [#:block block]
	返回frame中variable的值。如果提供了可选参数block，则从该块中搜索变量；否则从帧的当前块开始（由帧的当前程序计数器确定）。变量必须作为字符串或<gdb:symbol>对象给出，block必须是一个<gdb:block>对象。

[Scheme 过程] frame-select frame
	将frame设置为所选帧。见第 8 章[检查栈]，第 113 页。

[Scheme 过程] selected-frame
	返回所选帧对象。见第 8.3 节[选择帧]，第 117 页。

[Scheme 过程] newest-frame
	返回所选线程的最新帧对象。

[Scheme 过程] unwind-stop-reason-string reason
	返回一个字符串，解释 gdb 停止展开帧的原因，如给定的原因代码（一个整数，见本节中的frame-unwind-stop-reason过程）所表示的那样。
```

#### 23.4.3.16 从 Guile 访问块。

在 gdb 中，符号存储在块中。一个块大致对应于源代码中的一个作用域。块是分层组织的，在 Guile 中以类型为`<gdb:block>`的对象单独表示。块依赖于调试信息的可用性。

一个帧有一个块。有关帧的更深入讨论，请参阅第 23.4.3.15 节[Guile 中的帧]，第 552 页。

最外层的块称为全局块。全局块通常包含公共全局变量和函数。

嵌套在全局块内部的块是静态块。静态块通常包含文件作用域的变量和函数。

gdb 提供了一种获取块的超块的方法，但目前没有办法检查块的子块，也无法遍历符号表中的所有块（请参阅第 23.4.3.18 节[Guile 中的符号表]，第 561 页）。

以下是一个简短的示例，应该有助于解释块：

```
/* 这在全局块中。 */
int global;

/* 这在静态块中。 */
static int file_scope;

/* 'function' 在全局块中，'argument' 在 'function' 内部的一个块中。 */
int function (int argument)
{
	/* 'local' 在 'function' 内部的一个块中。它可能在也可能不在与 'argument' 相同的块中。 */
	int local;
	{
		/* 'inner' 在一个其超块是包含 'local' 的块中的块中。 */
		int inner;
		/* 如果此调用被编译器展开，您可能会在此处看到一个嵌套块，其函数为 'inline_function'，其超块是包含 'inner' 的块。 */
		inline_function ();
	}
}
```

以下是(gdb)模块提供的与块相关的过程：

```
[Scheme 过程] block? object
	如果 object 是一个`<gdb:block>`对象，则返回#t。否则返回#f。

[Scheme 过程] block-valid? block
	如果`<gdb:block>`块有效，则返回#t，否则返回#f。如果它所引用的块在下级中不再存在，块对象可能会变为无效。在调用过程时，如果块无效，所有其他`<gdb:block>`方法将抛出异常。在遍历块的符号时也会检查块的有效性。

[Scheme 过程] block-start block
	返回`<gdb:block>`块的起始地址。

[Scheme 过程] block-end block
	返回`<gdb:block>`块的结束地址。

[Scheme 过程] block-function block
	将`<gdb:block>`块表示为一个`<gdb:symbol>`对象并返回其名称。如果块未命名，则返回#f。
	对于普通函数块，超块是静态块。但是，您应该注意，函数块的超块可能不是静态块 - 例如，内联函数就会发生这种情况。

[Scheme 过程] block-superblock block
	返回包含`<gdb:block>`块的块。如果父块不存在，则返回#f。

[Scheme 过程] block-global-block block
	返回与`<gdb:block>`块关联的全局块。

[Scheme 过程] block-static-block block
	返回与`<gdb:block>`块关联的静态块。

[Scheme 过程] block-global? block
	如果`<gdb:block>`块是全局块，则返回#t。否则返回#f。

[Scheme 过程] block-static? block
	如果`<gdb:block>`块是静态块，则返回#t。否则返回#f。

[Scheme 过程] block-symbols
	返回`<gdb:block>`块中的所有符号（作为`<gdb:symbol>`对象）的列表。

[Scheme 过程] make-block-symbols-iterator block
	返回一个类型为`<gdb:iterator>`的对象，该对象将遍历该块的所有符号。Guile 程序不应假设特定的块对象将始终包含给定的符号，因为 gdb 功能和基础结构的变化可能导致符号在符号表中跨块移动。请参阅第 23.4.3.25 节[Guile 中的迭代器]，第 571 页。

[Scheme 过程] block-symbols-progress?
	如果对象是一个`<gdb:block-symbols-progress>`对象，则返回#t。此对象将从 make-block-symbols-iterator 返回的`<gdb:iterator>`对象的 progress 元素中获得。

[Scheme 过程] lookup-block pc
	返回包含给定 pc 值的最内层`<gdb:block>`。如果找不到指定 pc 值的块，该函数将返回#f。
```

#### 23.4.3.17 Guile 中的符号表示。

gdb 将每个变量、函数和类型表示为符号表中的一个条目。请参阅

第 16 章 [检查符号表]，第 263 页。Guile 在 gdb 中使用 `<gdb:symbol>` 对象来表示这些符号。

以下是 (gdb) 模块提供的与符号相关的过程：

```
[Scheme 过程] symbol? object
	如果 object 是 `<gdb:symbol>` 类型的对象，则返回 #t。否则返回 #f。

[Scheme 过程] symbol-valid? symbol
	如果 `<gdb:symbol>` 对象有效，则返回 #t，否则返回 #f。如果它所引用的符号在 gdb 中不再存在，`<gdb:symbol>` 对象可能会变为无效。在调用该过程时，如果它无效，所有其他 `<gdb:symbol>` 过程将抛出异常。

[Scheme 过程] symbol-type symbol
	返回符号的类型或如果未记录类型则返回 #f。结果是 `<gdb:type>` 类型的对象。请参阅第 23.4.3.7 节 [Guile 中的类型]，第 535 页。

[Scheme 过程] symbol-symtab symbol
	返回符号出现的符号表。结果是 `<gdb:symtab>` 类型的对象。请参阅第 23.4.3.18 节 [Guile 中的符号表]，第 561 页。

[Scheme 过程] symbol-line symbol
	返回定义符号的源代码中的行号。这是一个整数。

[Scheme 过程] symbol-name symbol
	返回符号的名称作为字符串。

[Scheme 过程] symbol-linkage-name symbol
	返回符号的名称，如链接器所使用的（即，可能是混淆的）。

[Scheme 过程] symbol-print-name symbol
	以适合输出的形式返回符号的名称。这是 name 或 linkage_name，具体取决于用户要求 gdb 显示解混淆或混淆的名称。

[Scheme 过程] symbol-addr-class symbol
	返回符号的地址类。这对如何找到符号的值进行分类。每个地址类都是在 (gdb) 模块中定义的常量，并在本章后面进行描述。

[Scheme 过程] symbol-needs-frame? symbol
	如果评估符号的值需要一个框架（请参阅第 23.4.3.15 节 [Guile 中的框架]，第 552 页），则返回 #t，否则返回 #f。通常，局部变量将需要一个框架，但其他符号则不需要。

[Scheme 过程] symbol-argument? symbol
	如果符号是函数的参数，则返回 #t。否则返回 #f。

[Scheme 过程] symbol-constant? symbol
	如果符号是常量，则返回 #t。否则返回 #f。

[Scheme 过程] symbol-function? symbol
	如果符号是函数或方法，则返回 #t。否则返回 #f。

[Scheme 过程] symbol-variable? symbol
	如果符号是变量，则返回 #t。否则返回 #f。

[Scheme 过程] symbol-value symbol [#:frame frame]
	计算符号的值，作为一个 `<gdb:value>`。对于函数，这计算函数的地址，并转换为适当的类型。如果符号需要一个框架才能计算其值，则必须提供 frame。如果未提供 frame 或 frame 无效，则抛出异常。

[Scheme 过程] lookup-symbol name [#:block block] [#:domain domain]
	此函数按名称搜索符号。搜索范围可以限制在可选的 domain 和 block 参数中定义的参数。
	name 是符号的名称。它必须是一个字符串。可选的 block 参数将搜索限制在该块中可见的符号。block 参数必须是一个 `<gdb:block>` 对象。如果省略，则使用当前框架的块。可选的 domain 参数将搜索限制在域类型。domain 参数必须是在 (gdb) 模块中定义的域常量，并在本章后面进行描述。
	结果是一个包含两个元素的列表。第一个元素是一个 `<gdb:symbol>` 对象或如果未找到符号则返回 #f。如果找到符号，第二个元素如果符号是方法对象的字段（例如 C++ 中的 this）则为 #t，否则为 #f。如果未找到符号，第二个元素为 #f。

[Scheme 过程] lookup-global-symbol name [#:domain domain]
	此函数按名称搜索全局符号。搜索范围可以通过 domain 参数进行限制。
	name 是符号的名称。它必须是一个字符串。可选的 domain 参数将搜索限制在域类型。domain 参数必须是在 (gdb) 模块中定义的域常量，并在本章后面进行描述。
	如果未找到符号，则结果是一个 `<gdb:symbol>` 对象或 #f。
```

在 `<gdb:symbol>` 中的可用域类别在 (gdb) 模块中表示为常量：

```
SYMBOL_UNDEF_DOMAIN
	当未发现域或以下域都不适用时使用。这通常表示符号信息或 gdb 对符号的处理中存在错误。

SYMBOL_VAR_DOMAIN
	此域包含变量、函数名称、typedef 名称和枚举类型值。

SYMBOL_FUNCTION_DOMAIN
	此域包含函数。

SYMBOL_TYPE_DOMAIN
	此域包含类型。在类似 C 的语言中，使用标签（出现在 struct、union 或 enum 关键字之后的名称）的类型将不会出现在此处；在其他语言中，所有类型都在此域中。

SYMBOL_STRUCT_DOMAIN
	此域包含 struct、union 和 enum 标签名称。此域仅用于类似 C 的语言。例如，在以下代码中：
		struct type_one { int x; };
		typedef struct type_one type_two;
	这里 type_one 将在 SYMBOL_STRUCT_DOMAIN 中，但 type_two 将在 SYMBOL_TYPE_DOMAIN 中。

SYMBOL_LABEL_DOMAIN
	此域包含标签（用于 goto）的名称。

SYMBOL_VARIABLES_DOMAIN
	此域包含 SYMBOL_VAR_DOMAIN 的一个子集；它包含除函数和类型之外的所有内容。

SYMBOL_FUNCTIONS_DOMAIN
	此域包含所有函数。

SYMBOL_TYPES_DOMAIN
	此域包含所有类型。
```

在 `<gdb:symbol>` 中的可用地址类类别在 gdb 模块中表示为常量：

在搜索符号时，可以将所需的域常量按字面形式传递给查找函数。

对于更复杂的搜索，有一组相应的常量，每个常量都以前面的常量之一命名，但用“SEARCH”前缀替换“SYMBOL”前缀；例如，SEARCH_LABEL_DOMAIN。这些可以或在一起以形成搜索常量。

```
SYMBOL_LOC_UNDEF
	如果此值由地址类返回，则表示符号信息或 gdb 对符号的处理中存在错误。

SYMBOL_LOC_CONST
	值是常量整数。

SYMBOL_LOC_STATIC
	值位于固定地址。

SYMBOL_LOC_REGISTER
	值在寄存器中。

SYMBOL_LOC_ARG
	值是一个参数。此值位于框架的参数列表中符号内部存储的偏移量处。

SYMBOL_LOC_REF_ARG
	值的地址存储在框架的参数列表中。与 LOC_ARG 类似，只是值的地址存储在偏移量处，而不是值本身。

SYMBOL_LOC_REGPARM_ADDR
	值是一个指定的寄存器。与 LOC_REGISTER 类似，只是寄存器保存参数的地址而不是参数本身。

SYMBOL_LOC_LOCAL
	值是一个局部变量。

SYMBOL_LOC_TYPEDEF
	值未使用。SYMBOL_STRUCT_DOMAIN 中的符号都具有此类别。

SYMBOL_LOC_BLOCK
	值是一个块。

SYMBOL_LOC_CONST_BYTES
	值是一个字节序列。

SYMBOL_LOC_UNRESOLVED
	值位于固定地址，但每当引用该变量时，必须从最小符号表中确定变量的地址。

SYMBOL_LOC_OPTIMIZED_OUT
	该值实际上在程序中不存在。

SYMBOL_LOC_COMPUTED
	值的地址是一个计算位置。
```

#### 23.4.3.18 Guile 中的符号表表示

gdb 在下级进程中维护的符号表数据的访问通过两个对象暴露给 Guile：`<gdb:sal>`（符号表和行）和 `<gdb:symtab>` 。帧的符号表和行数据从 `frame-find-sal <gdb:frame>` 过程返回。请参阅第 23.4.3.15 节[Guile 中的帧]，第 552 页。

有关 gdb 的符号表管理的更多信息，请参阅第 16 章[检查符号表]，第 263 页。

(gdb) 模块提供了以下与符号表相关的过程：

```
`[Scheme Procedure] symtab? object`
	如果对象是 `<gdb:symtab>` 类型的对象，则返回 `#t` 。否则返回 `#f` 。

`[Scheme Procedure] symtab-valid? symtab`
	如果 `<gdb:symtab>` 对象有效，则返回 `#t` ，如果无效则返回 `#f` 。当 `<gdb:symtab>` 对象所引用的符号表在 gdb 中不再存在时，该对象将变为无效。在调用过程时，如果它无效，所有其他 `<gdb:symtab>` 过程将抛出异常。

`[Scheme Procedure] symtab-filename symtab`
	返回符号表的源文件名。

`[Scheme Procedure] symtab-fullname symtab`
	返回符号表的源绝对文件名。

`[Scheme Procedure] symtab-objfile symtab`
	返回符号表的支持对象文件。请参阅第 23.4.3.14 节[Guile 中的对象文件]，第 552 页。

`[Scheme Procedure] symtab-global-block symtab`
	返回底层符号表的全局块。请参阅第 23.4.3.16 节[Guile 中的块]，第 555 页。

`[Scheme Procedure] symtab-static-block symtab`
	返回底层符号表的静态块。请参阅第 23.4.3.16 节[Guile 中的块]，第 555 页。
```

(gdb) 模块提供了以下与符号表和行相关的过程：

```
`[Scheme Procedure] sal? object`
	如果对象是 `<gdb:sal>` 类型的对象，则返回 `#t` 。否则返回 `#f` 。

`[Scheme Procedure] sal-valid? sal`
	如果 `sal` 有效，则返回 `#t` ，如果无效则返回 `#f` 。当 `<gdb:sal>` 对象所引用的符号表对象在 gdb 中不再存在时，该对象将变为无效。在调用过程时，如果它无效，所有其他 `<gdb:sal>` 过程将抛出异常。

`[Scheme Procedure] sal-symtab sal`
	返回 `sal` 的符号表对象（`<gdb:symtab>`）。

`[Scheme Procedure] sal-line sal`
	返回 `sal` 的行号。

`[Scheme Procedure] sal-pc sal`
	返回 `sal` 代码占用的地址范围的起始位置。

`[Scheme Procedure] sal-last sal`
	返回 `sal` 代码占用的地址范围的结束位置。

`[Scheme Procedure] find-pc-line pc`
	返回与 `pc` 值对应的 `<gdb:sal>` 对象。如果传递的 `pc` 值无效，则返回的 `<gdb:sal>` 对象的符号表和行属性将分别为 `#f` 和 0 。
```

#### 23.4.3.19 使用 Guile 操作断点

Guile 中的断点由 `<gdb:breakpoint>` 类型的对象表示。可以使用 `make-breakpoint` Guile 函数创建新的断点，然后使用 `register-breakpoint!` Guile 函数将其添加到 gdb 中。采取这种两步法是为了将添加断点到 gdb 的副作用与 `make-breakpoint` 分离出来。

还提供了支持来查看和操作在 Guile 外部创建的断点。

`(gdb)` 模块提供了以下与断点相关的过程：

```
[Scheme 过程] make-breakpoint location [#:type type] [#:wp-class wp-class] [#:internal internal] [#:temporary temporary]
		在位置创建一个新的断点，位置是一个命名断点位置的字符串，或者是定义观察点的表达式。内容可以是 `break` 命令识别的任何位置，或者在观察点的情况下，由 `watch` 命令识别。
		断点最初标记为“无效”。在使用 `register-breakpoint!` 将其注册到 gdb 之前，断点不可用，此时它变为“有效”。结果是表示断点的 `<gdb:breakpoint>` 对象。
		可选的 `type` 表示要创建的断点类型。此参数可以是 `BP_BREAKPOINT` 或 `BP_WATCHPOINT`，默认值为 `BP_BREAKPOINT`。
		可选的 `wp-class` 参数定义要创建的观察点类，如果类型是 `BP_WATCHPOINT`。如果未提供观察点类，则假定为 `WP_WRITE` 类。
		可选的 `internal` 参数允许断点对用户不可见。注册时不会报告该断点，也不会在 `info breakpoints` 的输出中列出该断点（但会使用 `maint info breakpoints` 命令列出）。如果未提供内部标志，则断点是可见的（非内部）。
		可选的 `temporary` 参数使断点成为临时断点。临时断点在命中后被删除，之后 Guile 断点不再可用（尽管可以使用 `register-breakpoint!` 重新注册）。
		创建观察点时，gdb 将尝试创建硬件辅助观察点。如果成功，观察点的类型将从 `BP_WATCHPOINT` 更改为 `BP_HARDWARE_WATCHPOINT`（对于 `WP_WRITE`）、`BP_READ_WATCHPOINT`（对于 `WP_READ`）和 `BP_ACCESS_WATCHPOINT`（对于 `WP_ACCESS`）。如果不成功，观察点的类型将保留为 `WP_WATCHPOINT`。
	可用的类型由 `gdb` 模块中定义的常量表示：
	`BP_BREAKPOINT`
		普通代码断点。
	`BP_WATCHPOINT`
		观察点断点。
	`BP_HARDWARE_WATCHPOINT`
		硬件辅助观察点。创建断点时不能指定此值。
	`BP_READ_WATCHPOINT`
		硬件辅助读取观察点。创建断点时不能指定此值。
	`BP_ACCESS_WATCHPOINT`
		硬件辅助访问观察点。创建断点时不能指定此值。
	`BP_CATCHPOINT`
		捕获点。创建断点时不能指定此值。
	
	可用的观察点类型由 `(gdb)` 模块中定义的常量表示：
		`WP_READ`   只读观察点。
		`WP_WRITE`  只写观察点。
		`WP_ACCESS` 读/写观察点。

[Scheme 过程] `register-breakpoint!` 断点
	将断点（一个 `<gdb:breakpoint>` 对象）添加到 gdb 的断点列表中。断点必须使用 `make-breakpoint` 创建。不能注册在 Guile 外部创建的断点。一旦注册了断点，它就变为“有效”。注册已经注册的断点是错误的。结果未指定。

[Scheme 过程] `delete-breakpoint!` 断点
	从 gdb 的断点列表中删除断点。这也使 Guile 断点对象无效。任何进一步尝试访问该对象都将引发异常。
	如果断点是使用 `make-breakpoint` 从 Guile 创建的，则可以使用 gdb 重新注册，此时断点再次变为有效。

[Scheme 过程] `breakpoints`
	返回所有断点的列表。列表的每个元素都是一个 `<gdb:breakpoint>` 对象。

[Scheme 过程] `breakpoint?` 对象
	如果对象是 `<gdb:breakpoint>` 对象，则返回 `#t`，否则返回 `#f`。

[Scheme 过程] `breakpoint-valid?` 断点
	如果断点有效，则返回 `#t`，否则返回 `#f`。使用 `make-breakpoint` 创建的断点在使用 `register-breakpoint!` 注册到 gdb 之前标记为无效。如果用户删除了断点，`<gdb:breakpoint>` 对象可能会变为无效。在这种情况下，对象仍然存在，但底层断点不存在。在观察点范围的情况下，即使 inferior 的执行离开该观察点的范围，观察点仍然有效。

[Scheme 过程] `breakpoint-number` 断点
	返回断点的编号——用户用于操作断点的标识符。

[Scheme 过程] `breakpoint-temporary?` 断点
	如果断点是作为临时断点创建的，则返回 `#t`。临时断点在命中后自动删除。在断点命中后（因为它已自动删除）调用此过程以及除 `breakpoint-valid?` 和 `register-breakpoint!` 之外的所有其他过程都将导致错误。

[Scheme 过程] `breakpoint-type` 断点
	返回断点的类型——用于确定实际断点类型或用例的标识符。

[Scheme 过程] `breakpoint-visible?` 断点
	如果断点在命中时或运行“`info breakpoints`”命令时对用户可见，则返回 `#t`。否则返回 `#f`。

[Scheme 过程] `breakpoint-location` 断点
	返回断点的位置，由用户指定。它是一个字符串。如果断点没有位置（即它是一个观察点），则返回 `#f`。

[Scheme 过程] `breakpoint-expression` 断点
	返回断点表达式，由用户指定。它是一个字符串。如果断点没有表达式（断点不是观察点），则返回 `#f`。

[Scheme 过程] `breakpoint-enabled?` 断点
	如果断点已启用，则返回 `#t`，否则返回 `#f`。

[Scheme 过程] `set-breakpoint-enabled!` 断点 标志
	将断点的启用状态设置为标志。如果标志为 `#f`，则禁用断点，否则启用断点。

[Scheme 过程] `breakpoint-silent?` 断点
	如果断点是静默的，则返回 `#t`，否则返回 `#f`。
	请注意，如果断点有命令并且第一个命令是静默的，断点也可以是静默的。此信息不会通过静默属性报告。

[Scheme 过程] `set-breakpoint-silent!` 断点 标志
	将断点的静默状态设置为标志。如果标志为 `#f`，则使断点静默，否则使断点非静默（或嘈杂）。

[Scheme 过程] `breakpoint-ignore-count` 断点
	返回断点的忽略计数。

[Scheme 过程] `set-breakpoint-ignore-count!` 断点 计数
	将断点的忽略计数设置为计数。

[Scheme 过程] `breakpoint-hit-count` 断点
	返回断点的命中计数。

[Scheme 过程] `set-breakpoint-hit-count!` 断点 计数
	将断点的命中计数设置为计数。目前，计数必须为零。

[Scheme 过程] `breakpoint-thread` 断点
	返回特定于线程的断点的全局线程 ID。如果断点不是特定于线程的，则返回 `#f`。

[Scheme 过程] `set-breakpoint-thread!` 断点 全局线程 ID|#f
	将断点的线程 ID 设置为全局线程 ID。如果设置为 `#f`，则断点不再是特定于线程的。

[Scheme 过程] `breakpoint-task` 断点
	如果断点是特定于 Ada 任务的，则返回 Ada 任务 ID。如果断点不是特定于任务的（或基础语言不是 Ada），则返回 `#f`。

[Scheme 过程] `set-breakpoint-task!` 断点 任务
	将断点的 Ada 任务设置为任务。如果设置为 `#f`，则断点不再是特定于任务的。

[Scheme 过程] `breakpoint-condition` 断点
	返回断点的条件，由用户指定。它是一个字符串。如果没有条件，则返回 `#f`。

[Scheme 过程] `set-breakpoint-condition!` 断点 条件
	将断点的条件设置为条件，条件必须是一个字符串。如果设置为 `#f`，则断点变为无条件。

[Scheme 过程] `breakpoint-stop` 断点
	返回断点的停止谓词。请参阅本节下面的 `set-breakpoint-stop!`。

[Scheme 过程] `set-breakpoint-stop!` 断点 过程|#f
	设置断点的停止谓词。谓词过程接受一个参数：`<gdb:breakpoint>` 对象。如果将此谓词设置为一个过程，则每当 inferior 到达此断点时都会调用它。如果它返回 `#t` 或任何非 `#f` 值，则 inferior 将停止，否则 inferior 将继续。
	如果在同一位置有多个带有停止谓词的断点，每个断点都将被调用，而不管前一个的返回状态如何。这确保所有停止谓词都有机会在该位置执行。在这种情况下，如果其中一个方法返回 `#t` 而其他方法返回 `#f`，inferior 仍将停止。
	此时不应更改 inferior 的执行状态（即，单步执行、下一步等）、更改当前帧上下文（即，更改当前活动帧）或更改、添加或删除任何断点。一般来说，此时不应更改 gdb 或 inferior 中的任何数据。
	示例停止实现：
		(define (my-stop? bkpt)
		(let ((int-val (parse-and-eval "foo")))
		(value=? int-val 3)))
		(define bkpt (make-breakpoint "main.c:42"))
		(register-breakpoint! bkpt)
		(set-breakpoint-stop! bkpt my-stop?)

[Scheme 过程] `breakpoint-commands` 断点
	返回附加到断点的命令作为字符串，如果没有命令则返回 `#f`。
```

#### 23.4.3.20  Guile 中惰性字符串的表示

一个惰性字符串是指其内容在需要时才会被检索或编码的字符串。

一个`<gdb:lazy-string>`在 gdb 中表示为一个指向内存区域的地址、用于编码该内存区域的编码以及用于界定表示字符串的内存区域的长度。

`<gdb:lazy-string>`与包裹在`<gdb:value>`中的字符串的区别在于，在打印时，gdb 对`<gdb:lazy-string>`的处理方式不同。`<gdb:lazy-string>`在打印时会被检索和编码，而包裹字符串的`<gdb:value>`在创建时就会立即被检索和编码。

(gdb)模块提供了以下与惰性字符串相关的过程：

```
[Scheme Procedure] lazy-string? object
	如果`object`是`<gdb:lazy-string>`类型的对象，则返回`#t`。否则返回`#f`。

[Scheme Procedure] lazy-string-address lazy-sring
	返回`lazy-string`的地址。

[Scheme Procedure] lazy-string-length lazy-string
	返回`lazy-string`的字符长度。如果长度为 -1，则字符串将被获取并编码，直到遇到适当宽度的第一个空字符。

[Scheme Procedure] lazy-string-encoding lazy-string
	返回在 gdb 打印字符串时将应用于`lazy-string`的编码。如果编码未设置或包含空字符串，则在打印字符串时，gdb 将选择最合适的编码。

[Scheme Procedure] lazy-string-type lazy-string
	返回由`lazy-string`的类型表示的类型。对于惰性字符串，这是一个指针或数组类型。要将其解析为惰性字符串的字符类型，请使用`type-target-type`。请参阅第 23.4.3.7 节[Guile 中的类型]，第 535 页。

[Scheme Procedure] lazy-string->value lazy-string
	将`<gdb:lazy-string>`转换为`<gdb:value>`。此值将指向内存中的字符串，但将失去 gdb 应用于`<gdb:lazy-string>`的所有延迟检索、编码和处理。
```

#### 23.4.3.21 架构的 Guile 表示

`gdb` 在其许多不同的计算中使用特定于架构的参数和工件。架构由`<gdb:arch>`类的实例表示。

以下是`(gdb)`模块提供的与架构相关的过程：

```
[Scheme 过程] `arch?`对象
	如果对象是`<gdb:arch>`类型的对象，则返回`#t`。否则返回`#f`。

[Scheme 过程] `current-arch`
	返回当前架构作为`<gdb:arch>`对象。

[Scheme 过程] `arch-name`架构
	返回`<gdb:arch>`架构的名称（字符串值）。

[Scheme 过程] `arch-charset`架构
	返回`<gdb:arch>`架构的目标字符集的名称。

[Scheme 过程] `arch-wide-charset`
	返回`<gdb:arch>`架构的目标宽字符集的名称。
	每个架构都通过以下函数提供一组预定义类型。

[Scheme 过程] `arch-void-type`架构
	返回架构`arch`的`void`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-char-type`架构
	返回架构`arch`的`char`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-short-type`架构
	返回架构`arch`的`short`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-int-type`架构
	返回架构`arch`的`int`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-long-type`架构
	返回架构`arch`的`long`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-schar-type`架构
	返回架构`arch`的有符号`char`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-uchar-type`架构
	返回架构`arch`的无符号`char`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-ushort-type`架构
	返回架构`arch`的无符号`short`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-uint-type`架构
	返回架构`arch`的无符号`int`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-ulong-type`架构
	返回架构`arch`的无符号`long`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-float-type`架构
	返回架构`arch`的`float`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-double-type`架构
	返回架构`arch`的`double`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-longdouble-type`架构
	返回架构`arch`的`long double`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-bool-type`架构
	返回架构`arch`的`bool`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-longlong-type`架构
	返回架构`arch`的`long long`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-ulonglong-type`架构
	返回架构`arch`的无符号`long long`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-int8-type`架构
	返回架构`arch`的`int8`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-uint8-type`架构
	返回架构`arch`的`uint8`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-int16-type`架构
	返回架构`arch`的`int16`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-uint16-type`架构
	返回架构`arch`的`uint16`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-int32-type`架构
	返回架构`arch`的`int32`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-uint32-type`架构
	返回架构`arch`的`uint32`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-int64-type`架构
	返回架构`arch`的`int64`类型的`<gdb:type>`对象。

[Scheme 过程] `arch-uint64-type`架构
	返回架构`arch`的`uint64`类型的`<gdb:type>`对象。
	示例：
		(gdb) guile (type-name (arch-uchar-type (current-arch)))
		"unsigned char"
```

#### 23.4.3.22 在 Guile 中的反汇编

反汇编器可以从 Scheme 代码中调用。此外，反汇编器可以接受一个 Guile 端口作为输入，允许从任何源进行反汇编，而不仅仅是目标内存。

```
[Scheme 过程] arch-disassemble arch start-pc [#:port port] [#:offset offset] [#:size size] [#:count count]
	从内存地址 `start-pc` 开始返回反汇编指令的列表。
	可选参数 `port` 指定从中读取字节的输入端口。如果 `port` 为 `#f`，则从目标内存读取字节。
	可选参数 `offset` 指定 `port` 中第一个字节的地址偏移量。例如，当 `port` 指定一个“字节向量”并且你希望将字节向量反汇编为好像它来自该地址时，此参数很有用。传递给 `port` 的读取器的 `start-pc` 会增加相同的量。
	示例：
		(gdb) guile (use-modules (rnrs io ports))
		(gdb) guile (define pc (value->integer (parse-and-eval "$pc")))
		(gdb) guile (define mem (open-memory #:start pc))
		(gdb) guile (define bv (get-bytevector-n mem 10))
		(gdb) guile (define bv-port (open-bytevector-input-port bv))
		(gdb) guile (define arch (current-arch))
		(gdb) guile (arch-disassemble arch pc #:port bv-port #:offset pc)
		(((address. 4195516) (asm. "mov $0x4005c8,%edi") (length. 5)))
	可选参数 `size` 和 `count` 确定返回列表中的指令数量。如果 `size` 或 `count` 被指定为零，则不进行反汇编并返回一个空列表。如果同时指定了可选参数 `size` 和 `count`，则返回最多 `count` 条反汇编指令的列表，其起始地址落在从 `start-pc` 到 `(start-pc + size - 1)` 的封闭内存地址区间内。如果未指定 `size`，但指定了 `count`，则返回从地址 `start-pc` 开始的 `count` 条指令。如果未指定 `count` 但指定了 `size`，则返回起始地址落在从 `start-pc` 到 `(start-pc + size - 1)` 的封闭内存地址区间内的所有指令。如果未指定 `size` 也未指定 `count`，则返回起始地址为 `start-pc` 的单个指令。
	返回列表的每个元素都是一个关联列表（alist），具有以下键：
		`address` 与此键对应的值是指令内存地址的 Guile 整数。
		`asm` 与此键对应的值是一个字符串值，它用汇编语言助记符表示指令。使用的汇编语言风格与当前 CLI 变量 `disassembly-flavor` 指定的相同。请参阅第 9.6 节[机器代码]，第 135 页。
		`length` 与此键对应的值是指令的字节长度。
```

#### 23.4.3.23 在 Guile 中的 I/O 端口

```
[Scheme 过程] input-port
	将 gdb 的输入端口作为 Guile 端口对象返回。

[Scheme 过程] output-port
	将 gdb 的输出端口作为 Guile 端口对象返回。

[Scheme 过程] error-port
	将 gdb 的错误端口作为 Guile 端口对象返回。

[Scheme 过程] stdio-port? object
	如果 object 是 gdb 的标准输入/输出端口，则返回 #t。否则返回 #f。
```

#### 23.4.3.24 Guile 中的内存端口

gdb 为目标内存提供了一个端口接口。这允许 Guile 代码使用 Guile 的端口和字节向量功能来读取/写入目标内存。主要例程是`open-memory`，它返回一个端口对象。然后可以使用该对象读取/写入内存。

```
[Scheme 过程] open-memory [#:mode mode] [#:start address] [#:size size]
	返回一个可用于读取和写入内存的端口对象。该端口将根据模式打开，这是 Guile 端口打开例程的标准模式参数，但不支持“"a"”和“"l"”模式。请参阅 GNU Guile 参考手册中的“文件端口”部分。“"b"”（二进制）字符可能存在，但会被忽略：内存端口仅为二进制。如果附加“"0"”，则该端口将被标记为无缓冲。默认值是“"r"”，只读且缓冲。
	可以访问的内存块是有边界的。如果起始地址和大小都未指定，则可以访问所有内存。如果仅指定了起始地址，则可以从该点开始访问所有内存。如果仅指定了大小，则可以访问范围[0,size)内的所有内存。如果两者都指定，则可以访问范围[start,start+size)内的所有内存。

[Scheme 过程]`memory-port?`
	如果对象是<gdb:memory-port>类型的对象，则返回#t。否则返回#f。

[Scheme 过程]`memory-port-range memory-port`
	将<gdb:memory-port>内存端口的范围作为包含两个元素的列表返回：(开始 结束)。范围是包含开始和结束的。

[Scheme 过程]`memory-port-read-buffer-size memory-port`
	返回<gdb:memory-port>内存端口的读取缓冲区的大小。
	此过程已弃用，将在 gdb 11 中删除。在使用 Guile 2.2 或更高版本时，它返回 0。

[Scheme 过程]`set-memory-port-read-buffer-size! memory-port size`
	将<gdb:memory-port>内存端口的读取缓冲区的大小设置为 size。结果未指定。
	此过程已弃用，将在 gdb 11 中删除。当 gdb 使用 Guile 2.2 或更高版本构建时，您可以调用 setvbuf 代替（请参阅 GNU Guile 参考手册中的“缓冲”部分）。

[Scheme 过程]`memory-port-write-buffer-size memory-port`
	返回<gdb:memory-port>内存端口的写入缓冲区的大小。
	此过程已弃用，将在 gdb 11 中删除。当 gdb 使用 Guile 2.2 或更高版本构建时，它返回 0。

[Scheme 过程]`set-memory-port-write-buffer-size! memory-port size`
	将<gdb:memory-port>内存端口的写入缓冲区的大小设置为 size。结果未指定。
	此过程已弃用，将在 gdb 11 中删除。当 gdb 使用 Guile 2.2 或更高版本构建时，您可以调用 setvbuf 代替。
```

像关闭任何其他端口一样，使用`close-port`关闭内存端口。

结合 Guile 的字节向量，内存端口提供了很多实用功能。例如，要在内存中填充一个包含 10 个整数的缓冲区，可以执行以下操作。

```
;; In the program: int buffer[10];
(use-modules (rnrs bytevectors))
(use-modules (rnrs io ports))
(define addr (parse-and-eval "buffer"))
(define n 10)
(define byte-size (* n 4))
(define mem-port (open-memory #:mode "r+" #:start
(value->integer addr) #:size byte-size))
(define byte-vec (make-bytevector byte-size))
	(do ((i 0 (+ i 1)))
	((>= i n))
(bytevector-s32-native-set! byte-vec (* i 4) (* i 42)))
(put-bytevector mem-port byte-vec)
(close-port mem-port)
```

#### 23.4.3.25 Guile 中的迭代器

提供了一个简单的迭代器工具，例如允许迭代程序符号的集合，而无需首先构建它们的所有列表。一个有用的贡献将是添加对 SRFI 41 和 SRFI 45 的支持。

```
[Scheme 过程] make-iterator 对象 progress next!
	使用 make-iterator 过程构造一个 `<gdb:iterator>` 对象。它接受三个参数：要迭代的对象、记录迭代进度的对象以及返回迭代中的下一个元素的过程，或者表示迭代结束的实现选择值。
	按照约定，迭代结束用 (end-of-iteration) 标记，可以使用 end-of-iteration? 谓词进行测试。(end-of-iteration) 的结果是经过选择的，因此它不会被 (gdb) 模块其他地方使用。如果在你自己的代码中使用 `<gdb:iterator>`，你有责任维护这个不变量。
	为了说明的一个简单示例：
	(use-modules (gdb iterator))
	(define my-list (list 1 2 3))
	(define iter
		(make-iterator my-list my-list
		(lambda (iter)
			(let ((l (iterator-progress iter)))
				(if (eq? l ’())
					(end-of-iteration)
					(begin
						(set-iterator-progress! iter (cdr l))
						(car l)))))))
	这里有一个稍微更实际的示例，它计算 my-global-block 中的所有函数的列表。
		(use-modules (gdb iterator))
		(define this-sal (find-pc-line (frame-pc (selected-frame))))
		(define this-symtab (sal-symtab this-sal))
		(define this-global-block (symtab-global-block this-symtab))
		(define syms-iter (make-block-symbols-iterator this-global-block))
		(define functions (iterator-filter symbol-function? syms-iter))

[Scheme 过程] iterator? 对象
	如果对象是一个 `<gdb:iterator>` 对象，则返回 #t。否则返回 #f。

[Scheme 过程] iterator-object iterator
	返回传递给 make-iterator 的第一个参数。这是正在迭代的对象。

[Scheme 过程] iterator-progress iterator
	返回跟踪迭代进度的对象。

[Scheme 过程] set-iterator-progress! iterator new-value
	设置跟踪迭代进度的对象。

[Scheme 过程] iterator-next! iterator
	调用作为 make-iterator 的第三个参数传递的过程，将其传递一个参数，即 `<gdb:iterator>` 对象。结果要么是迭代中的下一个元素，要么是 next! 过程实现的结束标记。按照约定，结束标记是 (end-of-iteration) 的结果。

[Scheme 过程] end-of-iteration
	返回表示迭代结束的 Scheme 对象。

[Scheme 过程] end-of-iteration? object
	如果对象是迭代结束标记，则返回 #t。否则返回 #f。
```
	
这些函数由 (gdb iterator) 模块提供，以帮助使用迭代器。

```
[Scheme 过程] make-list-iterator list
	返回一个将迭代 list 的 `<gdb:iterator>` 对象。

[Scheme 过程] iterator->list iterator
	将 iterator 指向的元素作为列表返回。

[Scheme 过程] iterator-map proc iterator
	通过将 proc 应用于 iterator 指向的对象以及每个后续对象，返回获得的对象列表。

[Scheme 过程] iterator-for-each proc iterator
	将 proc 应用于 iterator 指向的每个元素。结果未指定。

[Scheme 过程] iterator-filter pred iterator
	返回 iterator 指向的满足 pred 的元素列表。

[Scheme 过程] iterator-until pred iterator
	运行 iterator，直到 (pred element) 的结果为真，并将其作为结果返回。否则返回 #f。
```

### 23.4.4 Guile 自动加载

当读取一个新的对象文件时（例如，由于`file`命令，或者因为被调试程序加载了一个共享库），gdb 将通过两种方式查找 Guile 支持脚本：`objfile-gdb.scm`和`.debug_gdb_scripts`部分。请参阅第 23.5 节[自动加载扩展]，第 574 页。

自动加载功能对于提供特定于应用程序的调试命令和脚本非常有用。

可以启用或禁用自动加载，并且可以打印自动加载脚本的列表。

```
`set auto-load guile-scripts [on|off]`
	启用或禁用 Guile 脚本的自动加载。

`show auto-load guile-scripts`
	显示是否启用或禁用 Guile 脚本的自动加载。

`info auto-load guile-scripts [regexp]`
	打印 gdb 自动加载的所有 Guile 脚本的列表。
	还会打印在`.debug_gdb_scripts`部分中提到但未找到的 Guile 脚本的列表。这很有用，因为当 gdb 尝试加载它们但失败时，它们的名称不会被打印。可能有很多这样的脚本，为每个脚本打印错误消息是有问题的。
	如果提供了`regexp`，则仅打印匹配名称的 Guile 脚本。
	示例：
		(gdb) info auto-load guile-scripts
		Loaded Script
		Yes scm-section-script.scm
		full name: /tmp/scm-section-script.scm
		No my-foo-pretty-printers.scm
```

在读取自动加载的文件时，gdb 设置当前对象文件。这可以通过`current-objfile`过程（请参阅第 23.4.3.14 节[Guile 中的对象文件]，第 552 页）获得。这对于注册特定于对象文件的漂亮打印机很有用。

### 23.4.5 Guile 模块

gdb 附带了几个模块来协助编写 Guile 代码。

#### 23.4.5.1 Guile 打印模块

此模块提供了一组用于处理美化打印机的实用工具。

```
用法：	
	(use-modules (gdb printing))

[Scheme 过程] prepend-pretty-printer! object printer
	将打印机添加到对象的美化打印机列表的前面。对象必须要么是一个 `<gdb:objfile>` 对象，要么是 `#f`，在这种情况下，打印机将被添加到全局打印机列表中。

[Scheme 过程] append-pretty-printer! object printer
	将打印机添加到对象的美化打印机列表的末尾。对象必须要么是一个 `<gdb:objfile>` 对象，要么是 `#f`，在这种情况下，打印机将被添加到全局打印机列表中。
```

#### 23.4.5.2 Guile 类型模块

此模块提供了一组用于处理 `<gdb:type>` 对象的实用工具。

```
用法：
	(use-modules (gdb types))

[Scheme 过程] get-basic-type type
	返回去掉 const 和 volatile 限定符，并将 typedef 和 C++ 引用转换为基础类型的类型。
	C++ 示例：
		typedef const int const_int;
		const_int foo(3);
		const_int& foo_ref(foo);
		int main() { return 0; }
	然后在 gdb 中：
		(gdb) start
		(gdb) guile (use-modules (gdb) (gdb types))
		(gdb) guile (define foo-ref (parse-and-eval "foo_ref"))
		(gdb) guile (get-basic-type (value-type foo-ref))
		#<gdb:type int>

[Scheme 过程] type-has-field-deep? type field
	如果类型（假定为具有字段的类型，例如结构或联合）具有字段`field`，则返回`#t`。否则返回`#f`。此函数搜索基类，而`type-has-field?`则不搜索。

[Scheme 过程] make-enum-hashtable enum-type
	从`enum-type`返回一个 Guile 哈希表。哈希表中的元素使用`hashq-ref`引用。
```

## 23.5 自动加载扩展

当读取新的目标文件时（例如，由于`file`命令，或者因为 inferior 加载了共享库），gdb 提供了两种自动加载扩展的机制：`objfile-gdb.ext`（见第 23.5.1 节[`objfile-gdb.ext`文件]，第 575 页）和现代文件格式（如 ELF）的`.debug_gdb_scripts`部分（见第 23.5.2 节[`.debug_gdb_scripts`部分]，第 576 页）。有关这两种方法之间差异的讨论，请见第 23.5.3 节[选择哪种风格？]，第 577 页。

自动加载功能对于提供特定于应用程序的调试命令和功能很有用。

可以启用或禁用自动加载，并且可以打印自动加载脚本的列表。有关更多信息，请参见每种扩展语言的“自动加载”部分。

对于 gdb 命令文件，请见第 23.1.5 节[自动加载序列]，第 399 页。对于 Python 文件，请见第 23.3.3 节[Python 自动加载]，第 516 页。

请注意，加载此脚本文件还需要相应配置的自动加载安全路径（见第 22.8.3 节[自动加载安全路径]，第 379 页）。

### 23.5.1 `objfile-gdb.ext` 文件

当读取一个新的对象文件时，`gdb` 会查找一个名为 `objfile-gdb.ext` 的文件（以下我们称其为 `script-name`），其中 `objfile` 是对象文件的名称，`ext` 是扩展语言的文件扩展名：

```
- `objfile-gdb.gdb`：`GDB` 自身的命令语言
- `objfile-gdb.py`：`Python`
- `objfile-gdb.scm`：`Guile`
```

`script-name` 的形成方式是确保 `objfile` 的文件名是绝对路径，跟随所有符号链接，并解析 `.` 和 `..` 组件，然后附加 `-gdb.ext` 后缀。如果此文件存在且可读，`gdb` 将以指定的扩展语言将其作为脚本进行评估。

如果此文件不存在，那么 `gdb` 将在下面指定的所有目录中查找 `script-name` 文件。（在 MS-Windows/MS-DOS 上，可执行文件的主目录的驱动器字母将转换为一个单字母子目录，即 `d:/usr/bin/` 转换为 `/d/usr/bin/`，因为 Windows 文件系统不允许在文件名中使用冒号。）

请注意，加载这些文件需要相应配置的自动加载安全路径（参见第 22.8.3 节 [自动加载安全路径]，第 379 页）。

对于使用 `.exe` 后缀的对象文件，`gdb` 首先尝试根据其 `.exe` 文件名正常加载脚本。但是，如果未找到脚本，`gdb` 也会尝试与对象文件匹配的脚本文件名，而不包括其 `.exe` 后缀。这种 `.exe` 剥离是不区分大小写的，并且在任何平台上都会尝试。这使得脚本文件名在 Unix 和 MS-Windows 主机之间兼容。

```
`set auto-load scripts-directory [directories]`
	控制 `gdb` 自动加载脚本的位置。多个目录项可以使用正在使用的主机平台路径分隔符（在 Unix 上为 `:`，在 MS-Windows 和 MS-DOS 上为 `;`）分隔。
	这里的每个条目也需要被安全设置 `set auto-load safe-path` 覆盖（参见 [set auto-load safe-path]，第 379 页）。
	此变量的默认值为 `$debugdir:$datadir/auto-load`。默认的 `set auto-load safe-path` 值也可以通过 `gdb` 配置选项 `--with-auto-load-dir` 覆盖。
	对 `$debugdir` 的任何引用将被替换为 `debug-file-directory` 值（参见第 18.3 节 [单独的调试文件]，第 300 页），对 `$datadir` 的任何引用将被替换为在 `gdb` 启动时确定的数据目录（参见第 18.8 节 [数据文件]，第 308 页）。`$debugdir` 和 `$datadir` 必须作为目录组件放置 - 单独或使用 `/` 或 `\` 目录分隔符分隔，具体取决于主机平台。
	目录列表使用路径分隔符（在 GNU 和 Unix 系统上为 `:`，在 MS-Windows 和 MS-DOS 上为 `;`）来分隔目录，类似于 `PATH` 环境变量。

`show auto-load scripts-directory`
	显示 `gdb` 自动加载脚本的位置。

`add-auto-load-scripts-directory [directories...]`
	向自动加载脚本位置列表中添加一个条目（或条目列表）。多个条目可以使用正在使用的主机平台路径分隔符分隔。
```

`gdb` 不会跟踪它已经以这种方式自动加载了哪些文件。每次打开相应的 `objfile` 时，`gdb` 都会加载相关的脚本。因此，如果 `-gdb.ext` 文件被多次评估，您的文件应该小心避免错误。

### 23.5.2 .debug_gdb_scripts 部分

对于使用诸如 ELF 和 COFF 等文件格式的系统，当 gdb 加载新的目标文件时，它将查找一个名为.debug_gdb_scripts 的特殊部分。如果此部分存在，其内容是一个以空字符终止的条目列表，指定要加载的脚本。每个条目以一个非空前缀字节开头，该字节指定条目的类型，通常是扩展语言以及脚本是在文件中还是内联在.debug_gdb_scripts 中。

支持以下条目：

```
SECTION_SCRIPT_ID_PYTHON_FILE = 1
SECTION_SCRIPT_ID_SCHEME_FILE = 3
SECTION_SCRIPT_ID_PYTHON_TEXT = 4
SECTION_SCRIPT_ID_SCHEME_TEXT = 6
```

#### 23.5.2.1 脚本文件条目

如果条目指定了一个文件，gdb 将首先在当前目录中查找该文件，然后沿着源搜索路径（参见第 9.5 节[指定源目录]，第 131 页）查找，

但不搜索`$cdir`，因为编译目录与脚本无关。

文件条目可以放在`.debug_gdb_scripts`节中，例如，对于 Python 脚本，可以使用以下 GCC 宏：

```
/* Note: The "MS" section flags are to remove duplicates. */
#define DEFINE_GDB_PY_SCRIPT(script_name) \
	asm("\
.pushsection \".debug_gdb_scripts\", \"MS\",@progbits,1\n\
.byte 1 /* Python */\n\
.asciz \"" script_name "\"\n\
.popsection \n\
");
```

对于 Guile 脚本，将`.byte 1`替换为`.byte 3`。然后，可以在头文件或源文件中像这样引用该宏：

```
DEFINE_GDB_PY_SCRIPT ("my-app-scripts.py")
```

如果需要，脚本名称可以包含目录。

请注意，加载此脚本文件还需要相应配置的自动加载安全路径（参见第 22.8.3 节[自动加载安全路径]，第 379 页）。

如果将宏调用放在头文件中，使用此头文件的任何应用程序或库都将获得对指定脚本的引用，并且使用节上的“MS”属性，链接器将删除重复项。

#### 23.5.2.2 脚本文本条目

脚本文本条目允许将可执行脚本放在条目本身中，而不是从文件中加载。条目 的第一行，前缀字节之后直到第一个换行符（0xa）字符之前的所有内容，是脚本名，并且不得包含任何类型的空格字符，例如空格或制表符。条目 的其余部分，直到尾随的空字节，是以指定语言执行的脚本。名称在所有脚本名称中必须是唯一的，因为 gdb 仅根据其名称执行每个脚本一次。

以下是 gdb 测试套件中的文件`py-section-script.c`中的一个示例。

```c
#include "symcat.h"
#include "gdb/section-scripts.h"
asm(
".pushsection \".debug_gdb_scripts\", \"MS\",@progbits,1\n"
".byte " XSTRING (SECTION_SCRIPT_ID_PYTHON_TEXT) "\n"
".ascii \"gdb.inlined-script\\n\"\n"
".ascii \"class test_cmd (gdb.Command):\\n\"\n"
".ascii \" def __init__ (self):\\n\"\n"
".ascii \" super (test_cmd, self).__init__ ("
	"\\\"test-cmd\\\", gdb.COMMAND_OBSCURE)\\n\"\n"
".ascii \" def invoke (self, arg, from_tty):\\n\"\n"
".ascii \" print (\\\"test-cmd output, arg = %s\\\" % arg)\\n\"\n"
".ascii \"test_cmd ()\\n\"\n"
".byte 0\n"
".popsection\n"
);
```

内联脚本的加载需要正确配置的自动加载安全路径（见第 22.8.3 节 [自动加载安全路径]，第 379 页）。在自动加载中要指定的路径,安全路径是包含`.debug_gdb_scripts`部分的文件的路径。

### 23.5.3 选择哪种风味？

鉴于自动加载扩展的多种方式，可能并不总是清楚应该选择哪一种。本节提供了一些指导。

-gdb.ext 方式的好处：

• 可用于不支持多个部分的文件格式。

• 便于为公共库查找脚本。

.debug_gdb_scripts 部分中指定的脚本在源搜索路径中进行搜索。对于公共安装的库，例如 libstdc++，通常没有一个源目录可以在其中找到脚本。

• 不需要添加源代码。

.debug_gdb_scripts 方式的好处：

• 适用于静态链接。

以 -gdb.ext 方式为库编写的脚本需要一个对象文件来触发它们的加载。当应用程序是静态链接时，唯一可用的对象文件是可执行文件，将所有输入库的所有脚本附加到可执行文件的 -gdb.ext 脚本中是很麻烦的。

• 适用于完全内联的类。

有些类可以完全内联，因此可能没有关联的共享库可以将 -gdb.ext 脚本附加到。

• 脚本无需从源代码树中复制出来。

在某些情况下，应用程序可以由大量内部库构建而成，并且在 gdb 可以找到它们的地方安装 -gdb.ext 脚本所需的构建基础设施很麻烦。将脚本指定为相对路径在.debug_gdb_scripts 部分中，并将源树顶部的路径添加到源搜索路径中可能会更容易。

## 23.6 多扩展语言

Guile 和 Python 扩展语言不共享任何状态，并且通常不会相互干扰。然而，有一些事情需要注意。

### 23.6.1 Python 居首位

Python 是 gdb 的第一种扩展语言，为避免破坏现有行为，Python 居首位。这通常通过“先来先得”原则来解决。gdb 维护一个启用的扩展语言列表，当它调用扩展语言时（例如，对一个值进行漂亮打印），它会依次尝试每个扩展语言，直到有一个扩展语言表示它已执行了请求（例如，已返回一个值的漂亮打印形式）。这也适用于执行此类请求时的错误：如果在尝试对一个对象进行漂亮打印等操作时发生错误，那么会报告该错误，并且不会尝试任何后续的扩展语言。