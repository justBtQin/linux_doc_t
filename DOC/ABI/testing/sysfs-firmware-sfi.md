**What**: /sys/firmware/sfi/tables/

**Date**: May 2010

**Contact**: Len Brown <lenb@kernel.org>

**Description**:

SFI 定义了一些小型静态内存表，以便内核能够从固件中获取平台信息。

这些表在最新的 SFI 规范中定义：http://simplefirmware.org/documentation。

虽然这些表被内核使用，但用户空间可以通过以下方式观察它们：

```
# cd /sys/firmware/sfi/tables
# cat $TABLENAME > $TABLENAME.bin
```