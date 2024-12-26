<markdown_document>

# 8: 更多信息

关于 Linux 内核开发及相关主题，有许多信息来源。首先始终是在内核源代码分发中找到的 Documentation 目录。顶级的 HOWTO 文件是一个重要的起点；SubmittingPatches 和 SubmittingDrivers 也是所有内核开发者应该阅读的内容。许多内部内核 API 是使用 kerneldoc 机制记录的；可以使用“make htmldocs”或“make pdfdocs”以 HTML 或 PDF 格式生成这些文档（尽管某些发行版附带的 TeX 版本遇到内部限制，无法正确处理文档）。

各种网站以各种详细程度讨论内核开发。作者想谦虚地推荐 http://lwn.net/作为一个来源；关于许多特定内核主题的信息可以通过 LWN 内核索引在以下位置找到：

http://lwn.net/Kernel/Index/

除此之外，对于内核开发者来说，一个有价值的资源是：

http://kernelnewbies.org/

当然，也不应该忘记 http://kernel.org/，这是内核发布信息的权威位置。

有一些关于内核开发的书籍：

《Linux Device Drivers》，第 3 版（Jonathan Corbet、Alessandro Rubini 和 Greg Kroah-Hartman）。在线地址为 http://lwn.net/Kernel/LDD3/。

《Linux Kernel Development》（Robert Love）。

《Understanding the Linux Kernel》（Daniel Bovet 和 Marco Cesati）。

不过，所有这些书籍都有一个共同的缺点：它们在上架时往往已经有些过时，并且已经上架有一段时间了。尽管如此，在那里仍然可以找到很多好的信息。

git 的文档可以在以下位置找到：

http://www.kernel.org/pub/software/scm/git/docs/

http://www.kernel.org/pub/software/scm/git/docs/user-manual.html

# 9: 结论

祝贺任何读完这篇冗长文档的人。希望它为理解 Linux 内核如何开发以及如何参与该过程提供了帮助。

归根结底，参与才是重要的。任何开源软件项目都只不过是其贡献者投入的总和。Linux 内核之所以能够如此快速和良好地发展，是因为有一个令人印象深刻的大量开发者的帮助，他们都在努力使其更好。内核是数千人共同朝着一个共同目标努力时所能做到的一个卓越例子。

然而，内核总是可以从更大的开发者基础中受益。总是有更多的工作要做。但同样重要的是，Linux 生态系统中的大多数其他参与者可以通过为内核做出贡献而受益。将代码纳入主线是提高代码质量、降低维护和分发成本、对内核开发方向有更高影响力等的关键。这是一个涉及到每个人都能获胜的情况。启动你的编辑器并加入我们；你将非常受欢迎。

</markdown_document>