**What**: `/sys/kernel/vmcoreinfo`

**Date**: 2007 年 10 月

**KernelVersion**: 2.6.24

**Contact**: Ken'ichi Ohmichi <oomichi@mxs.nes.nec.co.jp>

        Kexec Mailing List <kexec@lists.infradead.org>

        Vivek Goyal <vgoyal@redhat.com>

**Description**：

展示 vmcoreinfo ELF 注释的物理地址和大小。第一个值包含注释的十六进制物理地址，第二个值包含注释的十六进制大小。此 ELF 注释信息由第二个内核解析，并作为 /proc/vmcore 文件中 ELF 注释的一部分导出到用户空间。此注释包含各种信息，如结构大小、符号值、页面大小等。