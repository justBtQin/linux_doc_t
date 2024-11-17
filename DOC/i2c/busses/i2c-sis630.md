# 内核驱动程序 `i2c-sis630`

**支持的适配器**：
  * Silicon Integrated Systems Corp (SiS)
    630 芯片组（数据表：可在 http://www.sfr-fresh.com/linux 获取）
    730 芯片组
    964 芯片组
  * 可能的其他 SiS 芯片组？

**作者**：Alexander Malysh <amalysh@web.de>
    Amaury Decr锚me <amaury.decreme@gmail.com> - SiS964 支持

**模块参数**

* `force = [1|0]` 强制启用 SIS630。危险！
    对于上述未命名的芯片组，这可能有助于检查它是否适用于您的芯片组，但很危险！
* `high_clock = [1|0]` 强制将主机主时钟设置为 56KHz（默认值，即您的 BIOS 使用的值）。危险！这应该会快一点，但可能会使某些系统冻结（例如我的笔记本电脑）。仅适用于 SIS630/730 芯片。

**描述**

此仅 SMBus 驱动程序已知可在具有上述命名的芯片组的主板上工作。

如果您看到如下内容：

```
00:00.0 Host bridge: Silicon Integrated Systems [SiS] 630 Host (rev 31)
00:01.0 ISA bridge: Silicon Integrated Systems [SiS] 85C503/5513
```

或者如下内容：

```
00:00.0 Host bridge: Silicon Integrated Systems [SiS] 730 Host (rev 02)
00:01.0 ISA bridge: Silicon Integrated Systems [SiS] 85C503/5513
```

或者如下内容：

```
00:00.0 Host bridge: Silicon Integrated Systems [SiS] 760/M760 Host (rev 02)
00:02.0 ISA bridge: Silicon Integrated Systems [SiS] SiS964 [MuTIOL Media IO]
							LPC Controller (rev 36)
```

在您的 `lspci` 输出中，那么此驱动程序适用于您的芯片组。

**感谢**
---------

Philip Edelbrock <phil@netroedge.com>
 - 测试 SiS730 支持
Mark M. Hoffman <mhoffman@lightlink.com>
 - 错误修复

感谢任何被我遗忘在此处的人！ 