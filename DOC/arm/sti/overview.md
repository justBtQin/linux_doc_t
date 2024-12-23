# STi ARM Linux 概述

==========================

## 引言

------------

STMicroelectronics 的多媒体和应用处理器系列 CortexA9 片上系统由“STi”ARM Linux 平台支持。目前，STiH415、STiH416 系统级芯片均受 B2000 和 B2020 参考板的支持。

## 配置

-------------

为 STiH415/416 提供了通用配置，通过`make stih41x_defconfig`可将其用作默认配置。

## 布局

------

多个机器系列（STiH415、STiH416 和 STiG125）的所有文件都位于`arch/arm/mach-sti`中的平台代码中。

在`mach`文件夹中有一个通用的板级`board-dt.c`，它支持扁平化设备树，这意味着它可与任何具有设备树的兼容板一起使用。

## 文档作者

---------------

Srinivas Kandagatla <srinivas.kandagatla@st.com>，（c）2013 ST Microelectronics