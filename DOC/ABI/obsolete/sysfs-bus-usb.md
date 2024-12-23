**What**: `/sys/bus/usb/devices/.../power/level`

**Date**: March 2007

**KernelVersion**: 2.6.21

**Contact**: Alan Stern <stern@rowland.harvard.edu>

**Description**:

  每个 USB 设备目录都将包含一个名为`power/level`的文件。此文件保存设备的电源级别设置，要么是“on”要么是“auto”。

  “on”意味着设备不允许自动挂起，尽管系统睡眠的正常挂起仍将被执行。“auto”意味着设备将按照其驱动程序的功能以通常的方式自动挂起和自动恢复。

  在正常使用期间，设备应保持在“auto”级别。“on”级别用于管理用途。如果您想立即挂起设备，但让它能够响应 I/O 请求而唤醒，则应将“0”写入`power/autosuspend`。

  无法正确进行挂起和恢复的设备应保持在“on”级别。尽管 USB 规范要求设备支持挂起/恢复，但许多设备并不支持。实际上，有很多设备不支持，以至于默认情况下，USB 核心将所有非集线器设备初始化为“on”级别。一些驱动程序在绑定时可能会更改此设置。

  此文件已弃用，将在 2010 年后删除。请使用`power/control`文件代替；它的作用完全相同。