**What**: `/sys/module/xen_blkfront/parameters/max`

**Date**: 六月 2013

**KernelVersion**: 3.11

**Contact**: Konrad Rzeszutek Wilk <konrad.wilk@oracle.com>

**Description**:

前端将与后端协商用于间接描述符的最大段数。默认值为 32 - 较高的值意味着更高的潜在吞吐量但也会占用更多内存。后端会选择前端和其默认后端值中的最小值。