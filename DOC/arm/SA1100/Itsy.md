Itsy 是由 Western Research Lab 和位于加利福尼亚州帕洛阿尔托的 Systems Research Center 完成的一个研究项目。Itsy 项目是康柏公司与口袋计算相关的几个研究项目之一。

更多信息，请参阅：

[http://www.hpl.hp.com/downloads/crl/itsy/](http://www.hpl.hp.com/downloads/crl/itsy/)

初始 2.4 Itsy 支持的注意事项（2000 年 8 月 27 日）：

该端口是在 Itsy 版本 1.5 机器上完成的，带有一个带有 64MB DRAM 和 32MB 闪存的子卡。初始工作包括支持串行控制台（以查看正在做的事情）。尚未启用其他设备。

要进行构建，请执行“make menuconfig”（或 xmenuconfig）并选择 Itsy 支持。禁用闪存和 LCD 支持，然后执行 make zImage。最后，您需要切换到 arch/arm/boot/tools 并在那里执行 make 以构建用于引导内核的 params-itsy 程序。

为了将 2.4 版本的端口安装到 Itsy 上，您需要在监视器中按如下方式设置配置参数：

参数 1：0x08340000，参数 2：0xC0000000，参数 3：18（0x12），参数 4：0

确保起始例程地址设置为 0x00060000。

接下来，将 params-itsy 程序闪存到 0x00060000（在闪存菜单中执行“p 1 0x00060000”）。将 arch/arm/boot/zImage 中的内核闪存到 0x08340000（“p 1 0x00340000”）。最后，将初始 ramdisk 闪存到 0xC8000000（“p 2 0x0”）。我们使用了 handhelds.org 上 0.11 版本目录中的 ramdisk-2-30.gz。

我们建立的串行连接为：

8 位数据，无奇偶校验，1 个停止位，115200.00 b/s。在监视器中、params-itsy 程序中以及内核本身中都是如此。这可以更改，但并不容易。监视器参数很容易更改，params 程序设置是汇编指令，而内核是 Itsy 特有的配置项。（即通过 grep 搜索 CONFIG_SA1100_ITSY，您将找到它的位置。）

这应该能让您在 Itsy 上正确引导 2.4 内核。