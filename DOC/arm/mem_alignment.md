由于最近内核代码中未被注意到的内存访问未对齐，出现了太多问题。因此，现在在基于 SA11x0 的目标中无条件地配置了对齐修复。据 Alan Cox 称，配置它为关闭是个坏主意，但 Russell King 在某些像 EBSA110 这样混乱的 ARM 架构上有一些很好的理由这样做。然而，在我所知道的许多设计中，如所有基于 SA11x0 的设计，情况并非如此。

当然，一般来说依赖对齐陷阱来执行未对齐的内存访问是个坏主意。如果这些访问是可预测的，你最好使用 include/asm/unaligned.h 中提供的宏。对齐陷阱可以修复异常情况下的未对齐访问，但性能成本很高。最好这种情况很少发生。

现在对于用户空间应用程序，可以将对齐陷阱配置为对执行未对齐访问的任何代码发送 SIGBUS（对于调试错误代码很有用），甚至可以像内核代码一样通过软件修复访问。出于性能原因，不推荐使用后一种模式（只需想想以类似方式工作的浮点模拟）。修复你的代码！

请注意，未经深思熟虑随意更改行为是非常糟糕的 - 它会更改用户空间中所有未对齐指令的行为，并可能导致程序意外失败。

要更改对齐陷阱行为，只需将一个数字回显到 /proc/cpu/alignment 中。该数字由各种位组成：

```
位	设置时的行为
---	-----------------
0		执行未对齐内存访问的用户进程将导致内核打印一条消息，指示进程名称、pid、pc、指令、地址和故障代码。
1		内核将尝试修复执行未对齐访问的用户进程。这当然很慢（想想浮点模拟器），不推荐用于生产环境。
2		内核将向执行未对齐访问的用户进程发送 SIGBUS 信号。
```

请注意，并非所有组合都受支持 - 仅支持值 0 到 5。（6 和 7 没有意义）。

例如，以下将打开警告，但不进行修复或发送 SIGBUS 信号：

`echo 1 > /proc/sys/debug/alignment`

你还可以读取同一文件的内容，以获取关于未对齐访问发生情况的统计信息以及用户空间代码的当前操作模式。

Nicolas Pitre，2001 年 3 月 13 日。Russell King 修改，2001 年 11 月 30 日。