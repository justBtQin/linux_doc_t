**What**: /proc/sys/vm/nr_pdflush_threads

**Date**: June 2012

**Contact**: Wanpeng Li <liwp@linux.vnet.ibm.com>

**Description**: 由于 pdflush 已被 per-BDI 刷新器替代，/proc/sys/vm/ 中导出的旧 pdflush 接口应被移除。