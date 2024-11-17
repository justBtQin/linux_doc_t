# 内核驱动程序 `i2c-sis96x`

替代 2.4.x 中的 `i2c-sis645`

**支持的适配器**：
  * Silicon Integrated Systems Corp (SiS)
    以下这些主机桥的任意组合：
    645、645DX（又名 646）、648、650、651、655、735、745、746
    以及以下这些南桥：
    961、962、963(L)

**作者**：Mark M. Hoffman <mhoffman@lightlink.com>

**描述**

此仅 SMBus 驱动程序已知可在具有上述命名的芯片组组合的主板上工作。该驱动程序是在没有 SiS 提供的适当数据表的情况下开发的。SMBus 寄存器被假定与 SiS630 的兼容，尽管它们位于完全不同的位置。感谢 Alexander Malysh <amalysh@web.de> 提供 SiS630 的数据表（和驱动程序）。

以 root 身份运行的命令 `lspci` 应该产生类似于以下的行：

```
00:00.0 Host bridge: Silicon Integrated Systems [SiS]: Unknown device 0645
00:02.0 ISA bridge: Silicon Integrated Systems [SiS] 85C503/5513
00:02.1 SMBus: Silicon Integrated Systems [SiS]: Unknown device 0016
```

或者也许是这样...

```
00:00.0 Host bridge: Silicon Integrated Systems [SiS]: Unknown device 0645
00:02.0 ISA bridge: Silicon Integrated Systems [SiS]: Unknown device 0961
00:02.1 SMBus: Silicon Integrated Systems [SiS]: Unknown device 0016
```

（内核版本高于 2.4.18 可能会填充“Unknown”）

如果您看不到它，请查看 `quirk_sis_96x_smbus`（`drivers/pci/quirks.c`）（如果南桥检测失败也是如此）

我怀疑此驱动程序也可以用于以下 SiS 芯片组：635 和 635T。如果有人拥有带有这些芯片的主板并且愿意以进步的名义冒险使原本运行良好的内核崩溃......请通过 <mhoffman@lightlink.com> 或通过 Linux-I2C 邮件列表：<linux-i2c@vger.kernel.org> 与我联系。请发送错误报告和/或成功案例。

**待办事项**
------

* 驱动程序不支持 SMBus 块读/写；如果发现需要它们的场景，我可能会添加它们。

**感谢**
---------

Mark D. Studebaker <mdsxyz123@yahoo.com>
 - 设计提示和错误修复
Alexander Maylsh <amalysh@web.de>
 - 同上，加上一个重要的数据表...几乎是我真正想要的
Hans-Günter Lütke Uphues <hg_lu@t-online.de>
 - 针对 SiS735 的补丁
Robert Zwerus <arzie@dds.nl>
 - 对 SiS645DX 的测试
Kianusch Sayah Karadji <kianusch@sk-tech.net>
 - 针对 SiS645DX/962 的补丁
Ken Healy
 - 针对 SiS655 的补丁

感谢任何提供反馈的人！