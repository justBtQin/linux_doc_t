**What**：`/sys/kernel/livepatch`

**Date**：2014 年 11 月

**KernelVersion**：3.19.0

**Contact**：live-patching@vger.kernel.org

**Description**：

内核实时补丁的接口

`/sys/kernel/livepatch` 目录包含每个已加载的实时补丁模块的子目录。

**What**：`/sys/kernel/livepatch/<patch>`

**Date**：2014 年 11 月

**KernelVersion**：3.19.0

**Contact**：live-patching@vger.kernel.org

**Description**：

补丁目录包含它在其中修补函数的每个内核对象（vmlinux 或模块）的子目录。

**What**：`/sys/kernel/livepatch/<patch>/Enabled`

**Date**：2014 年 11 月

**KernelVersion**：3.19.0

**Contact**：live-patching@vger.kernel.org

**Description**：

一个可写的属性，指示当前是否应用了修补的代码。写入 0 将禁用补丁，而写入 1 将重新启用补丁。

**What**：`/sys/kernel/livepatch/<patch>/<object>`

**Date**：2014 年 11 月

**KernelVersion**：3.19.0

**Contact**：live-patching@vger.kernel.org

**Description**：

对象目录包含对象内每个被修补函数的子目录。

**What**：`/sys/kernel/livepatch/<patch>/<object>/<function>`

**Date**：2014 年 11 月

**KernelVersion**：3.19.0

**Contact**：live-patching@vger.kernel.org

**Description**：

函数目录包含有关被修补函数的属性和状态。

目前没有此类属性。