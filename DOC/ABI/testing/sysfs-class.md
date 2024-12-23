**What**: /sys/class/

**Date**: 二月 2006 年

**Contact**: Greg Kroah-Hartman <gregkh@linuxfoundation.org>

**Description**:

    /sys/class 目录将由一组子目录组成，用于描述内核中的各个设备类。各个目录将由子目录或指向其他目录的符号链接组成。

    所有使用此目录树的程序必须能够处理子目录或符号链接，以正常工作。

**Users**:

    udev <linux-hotplug-devel@lists.sourceforge.net>