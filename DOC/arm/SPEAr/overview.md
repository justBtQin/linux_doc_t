**SPEAr ARM Linux 概述**

==========================

**介绍**

------------

SPEAr（结构化处理器增强架构）。网址：http://www.st.com/spear

意法半导体的 SPEAr 系列 ARM9/CortexA9 片上系统 CPU 由 ARM Linux 的“spear”平台支持。目前支持 SPEAr1310、SPEAr1340、SPEAr300、SPEAr310、SPEAr320 和 SPEAr600 等 SoC。SPEAr 的层次结构如下：

SPEAr（平台）

``
 - SPEAr3XX（基于 ARM9 的 3XX SoC 系列）
    - SPEAr300（SoC）
      - SPEAr300 评估板
    - SPEAr310（SoC）
      - SPEAr310 评估板
    - SPEAr320（SoC）
      - SPEAr320 评估板
 - SPEAr6XX（基于 ARM9 的 6XX SoC 系列）
    - SPEAr600（SoC）
      - SPEAr600 评估板
 - SPEAr13XX（基于 ARM CORTEXA9 的 13XX SoC 系列）
    - SPEAr1310（SoC）
      - SPEAr1310 评估板
    - SPEAr1340（SoC）
      - SPEAr1340 评估板
```

**配置**

-------------

为每个机器提供了通用配置，并且可以通过以下方式用作默认配置：

make spear13xx_defconfig

make spear3xx_defconfig

make spear6xx_defconfig

**布局**

------

多个机器系列（SPEAr3xx、SPEAr6xx 和 SPEAr13xx）的公共文件位于 arch/arm/plat-spear 中的平台代码中，头文件位于 plat/ 中。

每个机器系列都有一个以 arch/arm/mach-spear 后跟系列名称的目录。例如 mach-spear3xx、mach-spear6xx 和 mach-spear13xx。

SPEAr3xx 系列机器的公共文件是 mach-spear3xx/spear3xx.c，SPEAr6xx 是 mach-spear6xx/spear6xx.c，SPEAr13xx 系列是 mach-spear13xx/spear13xx.c。mach-spear* 还包含特定于 SoC/机器的文件，如 spear1310.c、spear1340.c、spear300.c、spear310.c、spear320.c 和 spear600.c。mach-spear* 不包含特定于电路板的文件，因为它们完全支持扁平化设备树。

**文档作者**

---------------

Viresh Kumar <viresh.linux@gmail.com>，（c）2010 - 2012 意法半导体