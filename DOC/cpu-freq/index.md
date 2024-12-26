<markdown_document>

# CPU frequency and voltage scaling code in the Linux(TM) kernel

Dominik Brodowski  <linux@brodo.de>

时钟缩放允许你在运行时更改 CPU 的时钟速度。这是一种节省电池电量的好方法，因为时钟速度越低，CPU 消耗的电量就越少。

本目录中的文档：

- core.txt - CPUFreq 核心的一般描述和 CPUFreq 通知器

- cpu-drivers.txt - 如何实现新的 cpufreq 处理器驱动程序

- governors.txt - 什么是 cpufreq 调节器以及如何实现它们？

- index.txt - 文件索引、邮件列表和链接（此文档）

- user-guide.txt - CPUFreq 用户指南

邮件列表

- 有一个 CPU 频率更改的 CVS 提交和通用列表，你可以在其中报告错误、问题或提交补丁。要发布消息，请发送电子邮件至 linux-pm@vger.kernel.org，要订阅请访问 http://vger.kernel.org/vger-lists.html#linux-pm 并按照那里的说明进行操作。

链接

- FTP 存档：

    - `ftp://ftp.linux.org.uk/pub/linux/cpufreq/`

- 如何访问 CVS 存储库：

    - `http://cvs.arm.linux.org.uk/`

- CPUFreq 邮件列表：

    - `http://vger.kernel.org/vger-lists.html#cpufreq`

SA-1100 的时钟和电压缩放：

- `http://www.lartmaker.nl/projects/scaling`