**What**：`devfs`

**日期**：2005 年 7 月（计划），最终在内核 v2.6.18 中移除

**联系人**：Greg Kroah-Hartman <gregkh@linuxfoundation.org>

**描述**：

    `devfs` 已经多年未维护，存在无法修复的竞争条件，内核中的命名策略违反了 LSB，并且可以使用 `udev` 替代。

    文件 `fs/devfs/*` 、`include/linux/devfs_fs*.h` 已被移除，以及整个内核树中的各种 `devfs` 函数调用。

**用户**： 