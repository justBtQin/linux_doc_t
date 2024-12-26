# 2: 开发流程如何运作

20 世纪 90 年代早期的 Linux 内核开发是一件相当松散的事情，涉及的用户和开发者数量相对较少。随着用户基础达到数百万，在一年的时间里有大约 2000 名开发者参与，内核此后不得不发展出一些流程，以保持开发的顺利进行。为了成为其中有效的一部分，需要对流程如何运作有一个扎实的理解。

## 2.1: 总体情况

内核开发者使用一个松散的基于时间的发布流程，每两到三个月发布一个新的主要内核版本。最近的发布历史如下：

- 2.6.38：2011 年 3 月 14 日

- 2.6.37：2011 年 1 月 4 日

- 2.6.36：2010 年 10 月 20 日

- 2.6.35：2010 年 8 月 1 日

- 2.6.34：2010 年 5 月 15 日

- 2.6.33：2010 年 2 月 24 日

每个 2.6.x 版本都是一个主要的内核版本，带有新功能、内部 API 更改等。一个典型的 2.6 版本可以包含近 10,000 个更改集，涉及数十万字的代码。因此，2.6 是 Linux 内核开发的前沿；内核使用一个滚动开发模型，不断集成主要更改。

对于每个版本的补丁合并，遵循了一个相对简单的规则。在每个开发周期的开始，所谓的“合并窗口”被打开。此时，被认为足够稳定（并且被开发社区接受）的代码将被合并到主线内核中。在这个时间内，新开发周期的大部分更改（以及所有主要更改）将被合并，每天的合并速度接近 1000 个更改（“补丁”或“更改集”）。

（顺便说一句，值得注意的是，在合并窗口期间集成的更改并非凭空而来；它们已经提前收集、测试和准备好。这个过程如何工作将在后面详细描述）。

合并窗口持续大约两周。在此期间结束时，Linus Torvalds 将宣布窗口关闭并发布第一个“rc”内核。例如，对于注定要成为 2.6.40 的内核，在合并窗口结束时发布的版本将被称为 2.6.40-rc1。-rc1 版本是合并新功能的时间已经过去，开始稳定下一个内核的信号。

在接下来的六到十周内，只有修复问题的补丁应该提交到主线。偶尔会允许更重大的更改，但这种情况很少；试图在合并窗口之外合并新功能的开发者往往会受到不友好的对待。一般来说，如果错过了某个功能的合并窗口，最好的做法是等待下一个开发周期。（对于以前不受支持的硬件的驱动程序偶尔会有例外；如果它们不触及树内代码，它们就不会导致回归，并且在任何时候添加都应该是安全的）。

随着修复进入主线，补丁率将随着时间的推移而减慢。Linus 大约每周发布一个新的 -rc 内核；在被认为足够稳定并发布最终的 2.6.x 版本之前，一个正常的系列将达到 -rc6 到 -rc9 之间的某个位置。此时，整个过程又重新开始。

例如，以下是 2.6.38 开发周期的情况（所有日期均为 2011 年）：

- 1 月 4 日：2.6.37 稳定版本

- 1 月 18 日：2.6.38-rc1，合并窗口关闭

- 1 月 21 日：2.6.38-rc2

- 2 月 1 日：2.6.38-rc3

- 2 月 7 日：2.6.38-rc4

- 2 月 15 日：2.6.38-rc5

- 2 月 21 日：2.6.38-rc6

- 3 月 1 日：2.6.38-rc7

- 3 月 7 日：2.6.38-rc8

- 3 月 14 日：2.6.38 稳定版本

开发者如何决定何时关闭开发周期并创建稳定版本？使用的最重要指标是以前版本的回归列表。任何错误都不受欢迎，但那些破坏过去工作系统的错误被认为特别严重。因此，导致回归的补丁不受欢迎，并且在稳定期间很可能会被回滚。

开发者的目标是在发布稳定版本之前修复所有已知的回归。在现实世界中，这种完美很难实现；在如此规模的项目中，变量太多。到了一定程度，延迟最终发布只会使问题更糟；等待下一个合并窗口的更改堆积会越来越大，下次回归会更多。因此，大多数 2.6.x 内核都有一些已知的回归，希望它们都不是严重的。

一旦创建了稳定版本，其持续维护就交给“稳定团队”，目前由 Greg Kroah-Hartman 负责。稳定团队将使用 2.6.x.y 编号方案发布稳定版本的偶尔更新。要被考虑用于更新版本，一个补丁必须（1）修复一个重要的错误，并且（2）已经合并到下一个开发内核的主线中。内核通常会在其初始发布后的一个多开发周期内收到稳定更新。例如，2.6.36 内核的历史如下：

- 10 月 10 日：2.6.36 稳定版本

- 11 月 22 日：2.6.36.1

- 12 月 9 日：2.6.36.2

- 1 月 7 日：2.6.36.3

- 2 月 17 日：2.6.36.4

2.6.36.4 是 2.6.36 版本的最终稳定更新。

一些内核被指定为“长期”内核；它们将获得更长时间的支持。截至撰写本文时，当前的长期内核及其维护者如下：

- 2.6.27：Willy Tarreau（深度冻结的稳定内核）

- 2.6.32：Greg Kroah-Hartman

- 2.6.35：Andi Kleen（嵌入式标志内核）

选择一个内核进行长期支持纯粹是维护者有需要和时间来维护该版本的问题。对于任何即将发布的特定版本，没有长期支持的计划。

## 2.2: 补丁的生命周期

补丁不会直接从开发者的键盘进入主线内核。相反，有一个有点复杂（如果有点非正式）的过程，旨在确保每个补丁都经过质量审查，并且每个补丁都实现了主线中需要的更改。这个过程对于小的修复可能很快，或者对于大的和有争议的更改可能会持续数年。许多开发者的挫折来自于对这个过程的缺乏理解或试图绕过它。

为了减少这种挫折感，本文将描述一个补丁如何进入内核。以下是一个以某种理想化的方式描述该过程的介绍。更详细的处理将在后面的部分中介绍。

补丁经历的阶段通常是：

- 设计。这是制定补丁的实际要求以及满足这些要求的方式的地方。设计工作通常在不涉及社区的情况下完成，但如果可能的话，在公开场合进行这项工作更好；它可以节省以后重新设计的大量时间。

- 早期审查。补丁被发布到相关的邮件列表中，该列表上的开发者会回复他们可能有的任何评论。如果一切顺利，这个过程应该会发现补丁的任何主要问题。

- 更广泛的审查。当补丁接近准备纳入主线时，它应该被相关的子系统维护者接受 - 尽管这种接受并不能保证补丁会一路进入主线。补丁将出现在维护者的子系统树中，并进入 -next 树（如下所述）。当这个过程起作用时，这一步会导致对补丁的更广泛审查，并发现由于这个补丁与其他人正在做的工作集成而导致的任何问题。

- 请注意，大多数维护者也有日常工作，所以合并你的补丁可能不是他们的首要任务。如果你的补丁收到关于需要更改的反馈，你应该要么进行这些更改，要么说明为什么不应该进行这些更改。如果你的补丁没有审查投诉，但没有被其适当的子系统或驱动程序维护者合并，你应该坚持不懈地将补丁更新到当前内核，以使其干净地应用，并继续发送它进行审查和合并。

- 合并到主线。最终，一个成功的补丁将被合并到 Linus Torvalds 管理的主线存储库中。此时可能会出现更多的评论和/或问题；开发者对这些问题做出响应并修复出现的任何问题非常重要。

- 稳定版本。受补丁影响的用户数量现在很大，因此，再次可能会出现新的问题。

- 长期维护。虽然开发者在合并代码后肯定有可能忘记代码，但这种行为在开发社区中往往会留下不好的印象。合并代码减轻了一些维护负担，因为其他人将修复 API 更改引起的问题。但如果要使代码在长期内保持有用，原始开发者应该继续对其负责。

内核开发者（或他们的雇主）犯的最大错误之一是试图将过程简化为一个“合并到主线”的步骤。这种方法总是会给所有相关人员带来挫折感。

## 2.3: 补丁如何进入内核

只有一个人可以将补丁合并到主线内核存储库中：Linus Torvalds。但是，在进入 2.6.38 内核的 9500 多个补丁中，只有 112 个（约 1.3％）是 Linus 自己直接选择的。内核项目早已发展到一个规模，没有一个开发者能够在没有帮助的情况下检查和选择每个补丁。内核开发者解决这个增长的方法是通过使用围绕信任链建立的中尉系统。

内核代码库在逻辑上被分解为一组子系统：网络、特定架构支持、内存管理、视频设备等。大多数子系统都有一个指定的维护者，一个对该子系统内的代码负有总体责任的开发者。这些子系统维护者是他们管理的内核部分的守门人（在一种松散的方式中）；他们是通常会接受补丁纳入主线内核的人。

子系统维护者各自管理自己的内核源代码树版本，通常（但肯定不是总是）使用 git 源代码管理工具。像 git（以及相关工具如 quilt 或 mercurial）这样的工具允许维护者跟踪补丁列表，包括作者信息和其他元数据。在任何给定时间，维护者都可以确定他或她的存储库中哪些补丁不在主线中。

当合并窗口打开时，顶级维护者将要求 Linus“拉取”他们为合并从他们的存储库中选择的补丁。如果 Linus 同意，补丁流将流入他的存储库，成为主线内核的一部分。Linus 对在拉取操作中收到的特定补丁的关注程度各不相同。很明显，有时他会非常仔细地查看。但是，一般来说，Linus 信任子系统维护者不会向上游发送不良补丁。

子系统维护者反过来可以从其他维护者那里拉取补丁。例如，网络树是从首先在专门用于网络设备驱动程序、无线网络等的树中积累的补丁构建的。这个存储库链可以任意长，尽管很少超过两三个链接。由于链中的每个维护者都信任管理较低级别树的人，这个过程被称为“信任链”。

显然，在这样的系统中，将补丁纳入内核取决于找到正确的维护者。直接将补丁发送给 Linus通常不是正确的方法。

## 2.4: -next 树

子系统树的链引导补丁流入内核，但也提出了一个有趣的问题：如果有人想查看为下一个合并窗口准备的所有补丁怎么办？开发者会对其他待处理的更改感兴趣，以查看是否有任何需要担心的冲突；例如，更改核心内核函数原型的补丁将与使用该函数旧形式的任何其他补丁冲突。审查者和测试人员希望在所有这些更改进入主线内核之前以集成形式访问这些更改。人们可以从所有有趣的子系统树中拉取更改，但这将是一项艰巨且容易出错的工作。

答案以 -next 树的形式出现，其中子系统树被收集用于测试和审查。这些树中较旧的树，由 Andrew Morton 维护，称为“-mm”（用于内存管理，这是它的起源）。-mm 树集成了来自一长串子系统树的补丁；它还有一些旨在帮助调试的补丁。

除此之外，-mm 包含 Andrew 直接选择的大量补丁。这些补丁可能已经发布在邮件列表上，或者它们可能适用于没有指定子系统树的内核部分。因此，-mm 作为一种最后的手段的子系统树运行；如果没有其他明显的路径将补丁纳入主线，它很可能最终会在 -mm 中。在 -mm 中积累的杂项补丁最终将要么转发到适当的子系统树，要么直接发送给 Linus。在一个典型的开发周期中，进入主线的大约 5-10％的补丁是通过 -mm 到达的。

当前的 -mm 补丁可在“mmotm”（当前的 -mm）目录中获得：

http://www.ozlabs.org/~akpm/mmotm/

使用 MMOTM 树可能是一次令人沮丧的经历；它肯定有不编译的可能性。

下一个周期补丁合并的主要树是 linux-next，由 Stephen Rothwell 维护。linux-next 树按设计是下一个合并窗口关闭后主线预期外观的快照。当 linux-next 树组装时，它们会在 linux-kernel 和 linux-next 邮件列表上宣布；可以从以下位置下载：

http://www.kernel.org/pub/linux/kernel/next/

linux-next 已成为内核开发过程的一个组成部分；在给定的合并窗口期间合并的所有补丁在合并窗口打开之前的某个时候真的应该已经进入了 linux-next。

## 2.4.1: 暂存树

内核源代码树包含 drivers/staging/ 目录，其中有许多正在添加到内核树中的驱动程序或文件系统的子目录。<markdown_document>

它们在 drivers/staging 中保持状态，直到仍需要更多工作；一旦完成，它们可以被移入内核本身。这是一种跟踪未达到 Linux 内核编码或质量标准的驱动程序的方法，但人们可能希望使用它们并跟踪开发。

Greg Kroah-Hartman 目前维护 staging 树。仍需要工作的驱动程序被发送给他，每个驱动程序在 drivers/staging/ 中都有自己的子目录。除了驱动程序源文件，目录中还应该有一个 TODO 文件。TODO 文件列出了驱动程序被接受进入内核本身所需的待处理工作，以及应该被抄送任何驱动程序补丁的人员列表。当前规则要求提交到 staging 的驱动程序至少必须能够正确编译。

Staging 是一种相对容易的方法，可以将新驱动程序引入主线，幸运的话，它们将引起其他开发人员的注意并迅速改进。然而，进入 staging 并不是故事的结束；在 staging 中没有看到定期进展的代码最终将被删除。发行商也往往相对不愿意启用 staging 驱动程序。因此，staging 充其量只是成为一个合适的主线驱动程序的一个步骤。

## 2.5：工具

正如从上述文本中可以看到的，内核开发过程在很大程度上依赖于将补丁集合引导到不同方向的能力。如果没有适当强大的工具，整个事情的工作效果将远不如现在。关于如何使用这些工具的教程远远超出了本文档的范围，但有一些指导意见。

到目前为止，内核社区使用的占主导地位的源代码管理系统是 git。Git 是在自由软件社区中开发的众多分布式版本控制系统之一。它在处理大型存储库和大量补丁时表现良好，非常适合内核开发。不过，它也有学习和使用困难的名声，尽管随着时间的推移已经有所改善。对于内核开发人员来说，对 git 的某种熟悉几乎是必需的；即使他们自己不使用它，他们也需要 git 来跟上其他开发人员（和主线）的工作。

Git 现在几乎被所有 Linux 发行版打包。其主页位于：

http://git-scm.com/

该页面有指向文档和教程的指针。

在不使用 git 的内核开发人员中，最受欢迎的选择几乎肯定是 Mercurial：

http://www.selenic.com/mercurial/

Mercurial 与 git 有许多相同的功能，但它提供了一个许多人认为更容易使用的界面。

另一个值得了解的工具是 Quilt：

http://savannah.nongnu.org/projects/quilt/

Quilt 是一个补丁管理系统，而不是源代码管理系统。它不跟踪随时间的历史；相反，它面向于在不断演变的代码库上跟踪一组特定的更改。一些主要的子系统维护者使用 quilt 来管理打算向上游提交的补丁。对于某些类型的树（例如 -mm）的管理，quilt 是最适合的工具。

## 2.6：邮件列表

大量的 Linux 内核开发工作是通过邮件列表完成的。如果不在某个地方加入至少一个列表，就很难成为社区的一个完全正常运作的成员。但是 Linux 邮件列表也对开发人员构成了潜在的危险，他们可能会被大量的电子邮件淹没，违反 Linux 列表上使用的约定，或者两者兼而有之。

大多数内核邮件列表在 vger.kernel.org 上运行；主列表可以在：

http://vger.kernel.org/vger-lists.html

找到。不过，也有在其他地方托管的列表；其中许多在 lists.redhat.com 上。

内核开发的核心邮件列表当然是 linux-kernel。这个列表是一个令人生畏的地方；每天的邮件量可能达到 500 条，噪音很大，对话可能非常技术化，参与者并不总是关心表现出高度的礼貌。但是，没有其他地方能让内核开发社区作为一个整体聚集在一起；避免这个列表的开发人员将错过重要信息。

有一些提示可以帮助在 linux-kernel 中生存：

- 将列表发送到一个单独的文件夹，而不是你的主邮箱。必须能够在一段时间内忽略信息流。

- 不要试图跟踪每一个对话 - 其他人也不会这样做。重要的是要根据感兴趣的主题（尽管要注意，长时间运行的对话可能会偏离原始主题而不改变电子邮件主题行）和参与的人员进行过滤。

- 不要激怒捣乱者。如果有人试图挑起愤怒的反应，忽略他们。

- 在回复 linux-kernel 电子邮件（或其他列表上的邮件）时，保留所有相关人员的 Cc：头。在没有强烈理由（如明确请求）的情况下，永远不要删除收件人。始终确保你要回复的人在 Cc：列表中。这种约定也使得没有必要明确要求在对您的帖子的回复中被抄送。

- 在提问之前搜索列表存档（和整个网络）。一些开发人员会对显然没有做功课的人感到不耐烦。

- 避免顶置发帖（将你的回答放在你要回复的引用文本之上的做法）。这会使你的回复更难阅读，并给人留下不好的印象。

- 在正确的邮件列表上提问。Linux-kernel 可能是一般的聚会点，但它不是找到所有子系统开发人员的最佳场所。

最后一点 - 找到正确的邮件列表 - 是初学者经常出错的地方。在 linux-kernel 上询问与网络相关的问题的人几乎肯定会收到一个礼貌的建议，让他们在 netdev 列表上提问，因为这是大多数网络开发人员经常访问的列表。还有其他列表用于 SCSI、video4linux、IDE、文件系统等子系统。查找邮件列表的最佳位置是在内核源代码包中的 MAINTAINERS 文件中。

## 2.7：开始内核开发

关于如何开始内核开发过程的问题很常见 - 无论是个人还是公司。同样常见的是错误的步骤，使关系的开始比必要的更困难。

公司经常希望聘请知名的开发人员来启动一个开发团队。实际上，这可以是一种有效的技术。但它也往往很昂贵，并且对增加有经验的内核开发人员的数量没有多大帮助。如果投入一些时间，将内部开发人员带入 Linux 内核开发的轨道是可能的。花时间可以使雇主拥有一组既了解内核又了解公司的开发人员，并且他们还可以帮助培训其他人。从中期来看，这通常是更有利可图的方法。

可以理解的是，个人开发人员常常不知道从哪里开始。从一个大项目开始可能会令人望而生畏；通常人们希望先通过一些较小的事情来试水。这就是一些开发人员开始创建修复拼写错误或小编码风格问题的补丁的地方。不幸的是，这样的补丁会在整个开发社区中造成一定程度的干扰，因此越来越不受重视。希望向社区介绍自己的新开发人员不会通过这些方式得到他们期望的那种接待。

Andrew Morton 为有抱负的内核开发人员提供了以下建议：

对于所有内核初学者来说，第一个项目肯定应该是“确保内核在您可以接触到的所有机器上始终完美运行”。通常，实现这一目标的方法是与他人合作来修复问题（这可能需要坚持不懈！），但这很好 - 这是内核开发的一部分。

（http://lwn.net/Articles/283982/）。

在没有明显的问题需要修复的情况下，建议开发人员查看当前的回归列表和一般的未解决错误。需要修复的问题永远不会短缺；通过解决这些问题，开发人员将在获得过程经验的同时，与其他开发人员建立尊重。

</markdown_document>