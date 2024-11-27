# gdb 概述

调试器（如 gdb）的目的是让您在另一个程序执行时了解其“内部”情况，或者在程序崩溃时了解其崩溃瞬间的行为。

gdb 主要能做以下四类事情（以及其他相关支持）来帮助您及时发现错误：
1. 启动您的程序，并指定可能影响其行为的任何因素。
2. 让您的程序在特定条件下停止。
3. 当程序停止时，检查所发生的情况。
4. 更改程序中的内容，以便您尝试纠正一个错误的影响并继续查找其他错误。

您可以使用 gdb 调试用 C 和 C++编写的程序。更多信息，请参见第 15.4 节 [支持的语言]，第 231 页。更多信息，请参见第 15.4.1 节 [C 和 C++]，第 231 页。

对 D 的支持是部分的。有关 D 的信息，请参见第 15.4.2 节 [D]，第 238 页。

对 Modula-2 的支持是部分的。有关 Modula-2 的信息，请参见第 15.4.9 节 [Modula-2]，第 244 页。

对 OpenCL C 的支持是部分的。有关 OpenCL C 的信息，请参见第 15.4.5 节 [OpenCL C]，第 239 页。

调试使用集合、子范围、文件变量或嵌套函数的 Pascal 程序目前无法正常进行。gdb 不支持使用 Pascal 语法输入表达式、打印值或类似操作。

gdb 可用于调试用 Fortran 编写的程序，不过可能需要用尾随下划线来引用某些变量。

gdb 可用于调试用 Objective-C 编写的程序，使用的可以是 Apple/NeXT 或 GNU Objective-C 运行时。

**自由软件**

gdb 是自由软件，受 gnu 通用公共许可证（GPL）保护。GPL 赋予您复制或改编许可程序的自由，但每个获得副本的人也同时获得修改该副本的自由（这意味着他们必须能够访问源代码），以及分发更多副本的自由。典型的软件公司利用版权来限制您的自由；而自由软件基金会则通过 GPL 来维护这些自由。

从根本上说，通用公共许可证表明您拥有这些自由，并且您不能剥夺其他人的这些自由。

**自由软件需要自由文档**

当前自由软件社区最大的不足并非在软件方面，而是缺乏优质的自由文档，导致我们无法将其与自由软件一同提供。我们众多重要的程序都未附带自由参考手册和自由入门文本。文档是任何软件包的关键组成部分；当重要的自由软件包未附带自由手册和自由教程时，就形成了一个重大缺口。

以 Perl 为例，人们常用的教程手册并非自由的。这是为何？因为这些手册的作者以限制性条款发布，如禁止复制、禁止修改、源文件不可获取等，这使其被排除在自由软件世界之外。

这并非首次发生此类情况，也远非最后一次。

常有这种情况，我们听到一位 GNU 用户热情地描述他正在编写的手册，本是打算为社区做贡献，却发现他因签署了非自由出版合同而毁掉了一切。

自由文档和自由软件一样，关键在于自由，而非价格。非自由手册的问题不在于出版商对印刷副本收费（这本身是可行的）。（自由软件基金会也出售手册的印刷副本。）问题在于对手册使用的限制。自由手册以源代码形式提供，并允许复制和修改。非自由手册则不允许。

自由手册的自由标准与自由软件大致相同。必须允许重新分发（包括常见的商业重新分发），以便手册能随程序的每个副本一起提供，无论是在线还是纸质形式。对技术内容进行修改的许可也至关重要。当人们修改软件、添加或更改功能时，如果他们认真负责，就会同时修改手册，以便为修改后的程序提供准确清晰的文档。若某手册让您除了为修改后的程序编写新手册外别无选择，那它对我们的社区而言并非真正可用。

某些对修改处理方式的限制是可以接受的。例如，要求保留原始作者的版权声明、分发条款或作者列表是可以的。要求修改后的版本注明已被修改也没问题。甚至某些不可删除或更改的部分也是可以接受的，只要它们涉及非技术主题（如本部分）。这些限制是可以接受的，因为它们不妨碍社区对手册的正常使用。

然而，必须能够修改手册的所有技术内容，然后通过所有常见的媒体和渠道分发结果。否则，这些限制会妨碍手册的使用，它就不是自由的，我们就需要另一本手册来替代。

请传播此问题的相关信息。我们的社区不断失去专有出版的手册。如果我们宣传自由软件需要自由参考手册和自由教程，或许下一位想通过编写文档做贡献的人会在为时未晚之前意识到，只有自由手册才能为自由软件社区做贡献。

如果您正在编写文档，请坚持在 GNU 自由文档许可证或其他自由文档许可证下发布。记住，此决定需您批准，您不必让出版商决定。有些商业出版商会在您坚持的情况下使用自由许可证，但他们不会主动提出此选项，这取决于您提出并坚定表示这是您的需求。若您正在合作的出版商拒绝，请尝试其他出版商。若您不确定所提议的许可证是否自由，请写信至 licensing@gnu.org 。

您可以通过购买来鼓励商业出版商销售更多自由的、版权共享的手册和教程，尤其是从为编写或重大改进付费的出版商处购买副本。同时，尽量避免购买非自由文档。购买手册前请检查其分发条款，并坚持要求任何与您交易的商家必须尊重您的自由。查看书籍的历史，并尝试奖励那些为作者工作付费的出版商。

自由软件基金会在 [http://www.fsf.org/doc/other-free-books.html](http://www.fsf.org/doc/other-free-books.html) 维护了其他出版商发布的自由文档列表。

**gdb 的贡献者**

理查德・斯托曼（Richard Stallman）是 gdb 的最初作者，也是许多其他 gnu 程序的作者。许多其他人也为其发展做出了贡献。本节试图表彰主要的贡献者。自由软件的优点之一是每个人都可以自由地为其做出贡献；遗憾的是，我们无法在此真正感谢每一个人。gdb 发行版中的 ChangeLog 文件近似于详细的逐个记录。

在 2.0 版本之前的许多更改都已在时间的迷雾中遗失。

请求：特别欢迎对本节的补充。如果您或您的朋友（或敌人，为了公平起见）被不公平地从这个列表中遗漏，我们希望添加您的名字！

为了让他们不觉得自己的诸多努力没有得到感谢，我们特别感谢那些主导了 gdb 主要版本发布的人：安德鲁・卡尼（Andrew Cagney）（发布了 6.3、6.2、6.1、6.0、5.3、5.2、5.1 和 5.0 版本）；吉姆・布兰迪（Jim Blandy）（发布了 4.18 版本）；杰森・莫伦达（Jason Molenda）（发布了 4.17 版本）；斯坦・谢布斯（Stan Shebs）（发布了 4.14 版本）；弗雷德・菲什（Fred Fish）（发布了 4.16、4.15、4.13、4.12、4.11、4.10 和 4.9 版本）；斯图・格罗斯曼（Stu Grossman）和约翰・吉尔摩（John Gilmore）（发布了 4.8、4.7、4.6、4.5 和 4.4 版本）；约翰・吉尔摩（John Gilmore）（发布了 4.3、4.2、4.1、4.0 和 3.9 版本）；吉姆・金登（Jim Kingdon）（发布了 3.5、3.4 和 3.3 版本）；兰迪・史密斯（Randy Smith）（发布了 3.2、3.1 和 3.0 版本）。

理查德・斯托曼（Richard Stallman）在不同时期得到了彼得・特马特（Peter TerMaat）、克里斯・汉森（Chris Hanson）和理查德・姆利纳里克（Richard Mlynarik）的协助，处理了到 2.8 版本的发布。

迈克尔・蒂曼（Michael Tiemann）是 gdb 中大部分 gnu C++支持的作者，佩尔・博特纳（Per Bothner）和丹尼尔・柏林（Daniel Berlin）也做出了重要的额外贡献。詹姆斯・克拉克（James Clark）编写了 gnu C++的解混淆器。早期的 C++工作由彼得・特马特（Peter TerMaat）完成（他还做了许多导致 3.0 版本发布的一般性更新工作）。

gdb 使用 BFD 子程序库来检查多种目标文件格式；BFD 是大卫・V・亨克尔 - 华莱士（David V. Henkel-Wallace）、里奇・皮克斯利（Rich Pixley）、史蒂夫・张伯伦（Steve Chamberlain）和约翰・吉尔摩（John Gilmore）的联合项目。

大卫・约翰逊（David Johnson）编写了最初的 COFF 支持；佩斯・威利森（Pace Willison）完成了对封装 COFF 的最初支持。

哈里斯计算机系统的布伦特・本森（Brent Benson）贡献了 DWARF 2 支持。

亚当・德布尔（Adam de Boor）和布拉德利・戴维斯（Bradley Davis）贡献了 ISI 最优 V 支持。佩尔・博特纳（Per Bothner）、信木浩一（Noboyuki Hikichi）和亚历山德罗・福林（Alessandro Forin）贡献了 MIPS 支持。让 - 丹尼尔・费克特（Jean-Daniel Fekete）贡献了 Sun 386i 支持。克里斯・汉森（Chris Hanson）改进了 HP9000 支持。信木浩一（Noboyuki Hikichi）和长谷川智之（Tomoyuki Hasei）贡献了索尼 / 新闻 OS 3 支持。大卫・约翰逊（David Johnson）贡献了 Encore Umax 支持。于尔基・库普拉（Jyrki Kuoppala）贡献了 Altos 3068 支持。杰夫・劳（Jeff Law）贡献了 HP PA 和 SOM 支持。基思・帕卡德（Keith Packard）贡献了 NS32K 支持。

道格・拉布森（Doug Rabson）贡献了 Acorn Risc 机器支持。鲍勃・鲁斯克（Bob Rusk）贡献了 Harris Nighthawk CX-UX 支持。克里斯・史密斯（Chris Smith）贡献了 Convex 支持（和 Fortran 调试）。乔纳森・斯通（Jonathan Stone）贡献了 Pyramid 支持。迈克尔・蒂曼（Michael Tiemann）贡献了 SPARC 支持。蒂姆・塔克（Tim Tucker）贡献了对 Gould NP1 和 Gould Powernode 的支持。佩斯・威利森（Pace Willison）贡献了 Intel 386 支持。杰伊・沃斯伯格（Jay Vosburgh）贡献了 Symmetry 支持。马尔科・姆利纳尔（Marko Mlinar）贡献了 OpenRISC 1000 支持。

安德烈亚斯・施瓦布（Andreas Schwab）贡献了 M68K gnu/Linux 支持。

里奇・谢弗（Rich Schaefer）和彼得・绍尔（Peter Schauer）协助了对 SunOS 共享库的支持。

杰伊・芬拉森（Jay Fenlason）和罗兰・麦格拉斯（Roland McGrath）确保了 gdb 和 GAS 在几个机器指令集上的一致性。

帕特里克・杜瓦尔（Patrick Duval）、泰德・戈尔茨坦（Ted Goldstein）、维克拉姆・科卡（Vikram Koka）和格伦・恩格尔（Glenn Engel）帮助开发了远程调试。英特尔公司、风河系统公司、AMD 和 ARM 分别为 i960、VxWorks、A29K UDI 和 RDI 目标贡献了远程调试模块。

布莱恩・福克斯（Brian Fox）是提供命令行编辑和命令历史的 readline 库的作者。

纽约州立大学布法罗分校的安德鲁・比尔斯（Andrew Beers）编写了语言切换代码、Modula-2 支持，并为本手册的“语言”章节做出了贡献。

弗雷德・菲什（Fred Fish）编写了对 Unix System Vr4 的大部分支持。他还增强了命令完成支持，以涵盖 C++的重载符号。

日立美国（现瑞萨美国）有限公司赞助了对 H8/300、H8/500 和 Super-H 处理器的支持。

NEC 赞助了对 v850、Vr4xxx 和 Vr5xxx 处理器的支持。

三菱（现瑞萨）赞助了对 D10V、D30V 和 M32R/D 处理器的支持。

东芝赞助了对 TX39 Mips 处理器的支持。

松下赞助了对 MN10200 和 MN10300 处理器的支持。

富士通赞助了对 SPARClite 和 FR30 处理器的支持。

孔徐（Kung Hsu）、杰夫・劳（Jeff Law）和里克・斯拉德基（Rick Sladkey）添加了对硬件观察点的支持。

迈克尔・斯奈德（Michael Snyder）添加了对跟踪点的支持。

斯图・格罗斯曼（Stu Grossman）编写了 gdbserver。

吉姆・金登（Jim Kingdon）、彼得・绍尔（Peter Schauer）、伊恩・泰勒（Ian Taylor）和斯图・格罗斯曼（Stu Grossman）在 gdb 中进行了无数的错误修复和清理工作。

惠普公司的以下人员为 PARISC 2.0 架构、HP-UX 10.20、10.30 和 11.0（窄模式）、惠普的内核线程实现、惠普的 aC++编译器和文本用户界面（原终端用户界面）的支持做出了贡献：本・克雷普（Ben Krepp）、理查德・蒂特（Richard Title）、约翰・毕晓普（John Bishop）、苏珊・马基娅（Susan Macchia）、凯西・曼恩（Kathy Mann）、萨蒂什・派（Satish Pai）、印度・保罗（India Paul）、史蒂夫・雷劳尔（Steve Rehrauer）和埃琳娜・赞诺尼（Elena Zannoni）。金・哈斯（Kim Haase）在本手册中提供了惠普特定的信息。

DJ・德洛里（DJ Delorie）将 gdb 移植到了 MS-DOS，用于 DJGPP 项目。罗伯特・霍恩（Robert Hoehne）对 DJGPP 移植做出了重要贡献。

自 1991 年以来，Cygnus Solutions 赞助了 gdb 的维护和大部分开发工作。全职从事 gdb 工作的 Cygnus 工程师包括马克・亚历山大（Mark Alexander）、吉姆・布兰迪（Jim Blandy）、佩尔・博特纳（Per Bothner）、凯文・比蒂纳（Kevin Buettner）、伊迪丝・爱泼斯坦（Edith Epstein）、克里斯・法雷尔（Chris Faylor）、弗雷德・菲什（Fred Fish）、马丁・亨特（Martin Hunt）、吉姆・英厄姆（Jim Ingham）、约翰・吉尔摩（John Gilmore）、斯图・格罗斯曼（Stu Grossman）、孔徐（Kung Hsu）、吉姆・金登（Jim Kingdon）、约翰・梅茨勒（John Metzler）、费尔南多・纳赛尔（Fernando Nasser）、杰弗里・诺尔（Geoffrey Noer）、道恩・佩尔奇克（Dawn Perchik）、里奇・皮克斯利（Rich Pixley）、兹德内克・拉杜什（Zdenek Radouch）、基思・塞茨（Keith Seitz）、斯坦・谢布斯（Stan Shebs）、大卫・泰勒（David Taylor）和埃琳娜・赞诺尼（Elena Zannoni）。此外，戴夫・布罗利（Dave Brolley）、伊恩・卡迈克尔（Ian Carmichael）、史蒂夫・张伯伦（Steve Chamberlain）、尼克・克利夫顿（Nick Clifton）、JT・康克林（JT Conklin）、斯坦・考克斯（Stan Cox）、DJ・德洛里（DJ Delorie）、乌尔里希・德雷珀（Ulrich Drepper）、弗兰克・艾格勒（Frank Eigler）、道格・埃文斯（Doug Evans）、肖恩・费根（Sean Fagan）、大卫・亨克尔 - 华莱士（David Henkel-Wallace）、理查德・亨德森（Richard Henderson）、杰夫・霍尔科姆（Jeff Holcomb）、杰夫・劳（Jeff Law）、吉姆・莱姆克（Jim Lemke）、汤姆・洛德（Tom Lord）、鲍勃・曼森（Bob Manson）、迈克尔・迈斯纳（Michael Meissner）、杰森・梅里尔（Jason Merrill）、凯瑟琳・摩尔（Catherine Moore）、德鲁・莫斯利（Drew Moseley）、肯・雷伯恩（Ken Raeburn）、加文・罗米格 - 科赫（Gavin Romig-Koch）、罗布・萨沃伊（Rob Savoye）、杰米・史密斯（Jamie Smith）、迈克・斯塔姆（Mike Stump）、伊恩・泰勒（Ian Taylor）、安吉拉・托马斯（Angela Thomas）、迈克尔・蒂曼（Michael Tiemann）、汤姆・特罗米（Tom Tromey）、罗恩・昂劳（Ron Unrau）、吉姆・威尔逊（Jim Wilson）和大卫・祖恩（David Zuhn）都做出了大大小小的贡献。

安德鲁・卡尼（Andrew Cagney）、费尔南多・纳赛尔（Fernando Nasser）和埃琳娜・赞诺尼（Elena Zann