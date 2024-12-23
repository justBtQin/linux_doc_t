**What**: `/sys/devices/pnp0/<bus-num>/ppi/`

**Date**: August 2012

**Kernel Version**: 3.6

**Contact**: xiaoyan.zhang@intel.com

**Description**:

此文件夹包含与 PPI（物理存在接口）相关的属性。仅当 BIOS 支持 TPM 时，此文件夹才有意义。可以通过命令“find /sys/ -name 'pcrs'”获取文件夹路径。有关 PPI 的详细信息，请参考来自 http://www.trustedcomputinggroup.org/ 的 PPI 规范。

**What**: `/sys/devices/pnp0/<bus-num>/ppi/version`

**Date**: August 2012

**Contact**: xiaoyan.zhang@intel.com

**Description**:

此属性显示平台支持的 PPI 版本。此文件为只读。

**What**: `/sys/devices/pnp0/<bus-num>/ppi/request`

**Date**: August 2012

**Contact**: xiaoyan.zhang@intel.com

**Description**:

此属性显示在预操作系统环境中要执行的操作请求。它是操作系统到预操作系统环境的唯一输入。请求应为 1 到 160 的整数值，0 表示无请求。此文件可读写。

**What**: `/sys/devices/pnp0/00:<bus-num>/ppi/response`

**Date**: August 2012

**Contact**: xiaoyan.zhang@intel.com

**Description**:

此属性显示对最近操作请求的响应。格式为“<请求> <响应编号>: <响应描述>”。此文件为只读。

**What**: `/sys/devices/pnp0/<bus-num>/ppi/transition_action`

**Date**: August 2012

**Contact**: xiaoyan.zhang@intel.com

**Description**:

此属性显示为了转换到 BIOS 以执行请求的操作而应采取的平台特定操作。格式为“<操作编号>: <操作描述>”。此文件为只读。

**What**: `/sys/devices/pnp0/<bus-num>/ppi/tcg_operations`

**Date**: August 2012

**Contact**: xiaoyan.zhang@intel.com

**Description**:

此属性显示 BIOS 是否允许在预操作系统环境中针对 TCG 定义的请求（即 1 到 22 的请求）执行操作。格式为“<请求> <状态编号>: <状态描述>”。此属性仅由 PPI 版本 1.2+支持。此文件为只读。

**What**: `/sys/devices/pnp0/<bus-num>/ppi/vs_operations`

**Date**: August 2012

**Contact**: xiaoyan.zhang@intel.com

**Description**:

此属性显示 BIOS 是否允许在预操作系统环境中针对特定于供应商的请求（即 128 到 255 的请求）执行操作。格式与 tcg_operations 相同。此属性也仅由 PPI 版本 1.2+支持。此文件为只读。