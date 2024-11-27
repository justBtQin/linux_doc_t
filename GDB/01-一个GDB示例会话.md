# 一个 gdb 示例会话

您可以在闲暇时使用本手册全面了解 gdb。然而，仅需少量命令即可开始使用调试器。本章将对这些命令进行说明。

在这个示例会话中，我们会像这样强调用户输入：`输入`，以便于从周围的输出中区分出来。

```
$ cd gnu/m4
$./m4
define(foo,0000)
foo
0000
define(bar,defn(‘foo’))
bar
0000
changequote(<QUOTE>,<UNQUOTE>)
define(baz,defn(<QUOTE>foo<UNQUOTE>))
baz
Ctrl-d
m4: End of input: 0: fatal error: EOF in string
```

让我们使用 gdb 来尝试了解发生了什么。

```
$ gdb m4
gdb is free software and you are welcome to distribute copies
of it under certain conditions; type "show copying" to see
the conditions.
There is absolutely no warranty for gdb; type "show warranty"
for details.
gdb 16.0.50.20241118-git, Copyright 1999 Free Software Foundation, Inc...
(gdb)
```

gdb 仅读取足够的符号数据，以便在需要时知道在哪里找到其余部分；因此，第一个提示符很快就会出现。现在我们告诉 gdb 使用比通常更窄的显示宽度，以便示例适合本手册。

```
(gdb) set width 70
```

我们需要了解 m4 内置的 `changequote` 是如何工作的。查看源代码后，我们知道相关的子例程是 `m4_changequote` ，因此我们使用 gdb 的 `break` 命令在此处设置断点。

```
(gdb) break m4_changequote
Breakpoint 1 at 0x62f4: file builtin.c, line 879.
```

使用 `run` 命令，我们在 gdb 控制下启动 m4 ；只要控制未到达 `m4_changequote` 子例程，程序就会正常运行：

```
(gdb) run
Starting program: /work/Editorial/gdb/gnu/m4/m4
define(foo,0000)
foo
0000
```

为触发断点，我们调用 `changequote` 。gdb 暂停 m4 的执行，并显示其停止位置的上下文信息。

```
changequote(<QUOTE>,<UNQUOTE>)
Breakpoint 1, m4_changequote (argc=3, argv=0x33c70)
at builtin.c:879
879 if (bad_argc(TOKEN_DATA_TEXT(argv[0]),argc,1,3))
```

现在我们使用命令 `n`（`next`）将执行推进到当前函数的下一行。

```
(gdb) n
882 set_quotes((argc >= 2)? TOKEN_DATA_TEXT(argv[1])\
: nil,
```

`set_quotes` 看起来是一个有前途的子例程。我们可以使用命令 `s`（`step`）而不是 `next` 进入它。`step` 会进入任何子例程中的下一行要执行的代码，因此它会进入 `set_quotes` 。

```
(gdb) s
set_quotes (lq=0x34c78 "<QUOTE>", rq=0x34c88 "<UNQUOTE>")
at input.c:530
530 if (lquote!= def_lquote)
```

显示当前 m4 暂停的子例程（及其参数）的显示被称为栈帧显示。它显示了栈的摘要。我们可以使用 `backtrace` 命令（也可以拼写为 `bt`）来查看我们在整个栈中的位置：`backtrace` 命令为每个活动的子例程显示一个栈帧。

```
(gdb) bt
#0 set_quotes (lq=0x34c78 "<QUOTE>", rq=0x34c88 "<UNQUOTE>")
at input.c:530
#1 0x6344 in m4_changequote (argc=3, argv=0x33c70)
at builtin.c:882
#2 0x8174 in expand_macro (sym=0x33320) at macro.c:242
#3 0x7a88 in expand_token (obs=0x0, t=209696, td=0xf7fffa30)
at macro.c:71
#4 0x79dc in expand_input () at macro.c:40
#5 0x2930 in main (argc=0, argv=0xf7fffb20) at m4.c:195
```

我们再逐步执行几行以查看会发生什么。前两次，我们可以使用 `s` ；接下来的两次我们使用 `n` 以避免陷入 `xstrdup` 子例程。

```
(gdb) s
0x3b5c 532 if (rquote!= def_rquote)
(gdb) s
0x3b80 535 lquote = (lq == nil || *lq == ’\0’)? \
def_lquote : xstrdup(lq);
(gdb) n
536 rquote = (rq == nil || *rq == ’\0’)? def_rquote\
: xstrdup(rq);
(gdb) n
538 len_lquote = strlen(rquote);
```

最后显示的这一行看起来有点奇怪；我们可以检查变量 `lquote` 和 `rquote` ，看看它们是否实际上是我们指定的新的左右引号。我们使用命令 `p`（`print`）来查看它们的值。

```
(gdb) p lquote
$1 = 0x35d40 "<QUOTE>"
(gdb) p rquote
$2 = 0x35d50 "<UNQUOTE>"
```

`lquote` 和 `rquote` 确实是新的左右引号。为了查看一些上下文，我们可以使用 `l`（`list`）命令显示当前行周围的十行源代码。

```
(gdb) l
533 xfree(rquote);
534
535 lquote = (lq == nil || *lq == ’\0’)? def_lquote\
: xstrdup (lq);
536 rquote = (rq == nil || *rq == ’\0’)? def_rquote\
: xstrdup (rq);
537
538 len_lquote = strlen(rquote);
539 len_rquote = strlen(lquote);
540 }
541
542 void
```

让我们越过设置 `len_lquote` 和 `len_rquote` 的这两行，然后检查这些变量的值。

```
(gdb) n
539 len_rquote = strlen(lquote);
(gdb) n
540 }
(gdb) p len_lquote
$3 = 9
(gdb) p len_rquote
$4 = 7
```

假设 `len_lquote` 和 `len_rquote` 分别表示 `lquote` 和 `rquote` 的长度，这看起来肯定是错误的。我们可以使用 `p` 命令将它们设置为更好的值，因为它可以打印任何表达式的值——并且该表达式可以包括子例程调用和赋值。

```
(gdb) p len_lquote=strlen(lquote)
$5 = 7
(gdb) p len_rquote=strlen(rquote)
$6 = 9
```

这是否足以解决在 m4 内置的 `defn` 中使用新引号的问题？我们可以使用 `c`（`continue`）命令让 m4 继续执行，然后尝试最初引起问题的示例：

```
(gdb) c
Continuing.
define(baz,defn(<QUOTE>foo<UNQUOTE>))
baz
0000
```

成功！新的引号现在和默认的引号一样工作良好。问题似乎只是定义错误长度的两个拼写错误。我们通过向 m4 输入 EOF 让其退出：

```
Ctrl-d
Program exited normally.
```

“`Program exited normally.`”这个消息来自 gdb ；它表示 m4 已完成执行。我们可以使用 gdb 的 `quit` 命令结束我们的 gdb 会话。

```
(gdb) quit
```