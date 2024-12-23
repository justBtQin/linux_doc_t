Linux 内核 VFP 支持代码的发布说明

-----------------------------------------------

日期：2004 年 5 月 20 日

作者：Russell King

这是 Linux 内核 VFP 支持代码的第一个版本。它为在 ARM926EJ-S 上发现的从 VFP 硬件反弹的异常提供了支持。

此版本已通过 John R. Hauser 使用 TestFloat-2a 测试套件对 SoftFloat-2b 库进行了验证。有关此库和测试套件的详细信息，请访问：

   http://www.jhauser.us/arithmetic/SoftFloat.html

已使用此包测试的操作包括：

 - fdiv

 - fsub

 - fadd

 - fmul

 - fcmp

 - fcmpe

 - fcvtd

 - fcvts

 - fsito

 - ftosi

 - fsqrt

上述所有操作都通过了软浮点测试，但有以下例外情况：

- fadd/fsub 在输入操作数符号不同时，在处理 +0 / -0 结果方面显示出一些差异。- 下溢异常的处理略有不同。如果结果在舍入前下溢，但在舍入后变为规范化数，我们不会发出下溢异常。

其他已通过基本仅汇编测试的操作包括：

 - fcpy

 - fabs

 - fneg

 - ftoui

 - ftosiz

 - ftouiz

组合操作尚未经过测试：

 - fmac

 - fnmac

 - fmsc

 - fnmsc

 - fnmul