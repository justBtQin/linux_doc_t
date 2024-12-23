**What**：`/sys/bus/xen-backend/devices/*/devtype`

**日期**：2009 年 2 月

**内核版本**：2.6.38

**联系人**：Konrad Rzeszutek Wilk <konrad.wilk@oracle.com>

**描述**：

    设备的类型。例如，“vbd”（块设备）、“vif”（网络设备）或“vfb”（帧缓冲设备）中的一种。

**What**：`/sys/bus/xen-backend/devices/*/nodename`

**日期**：2009 年 2 月

**内核版本**：2.6.38

**联系人**：Konrad Rzeszutek Wilk <konrad.wilk@oracle.com>

**描述**：

    此后端设备的 XenStore 节点（在 `/local/domain/NNN/` 下）。

**What**：`/sys/bus/xen-backend/devices/vbd-*/physical_device`

**日期**：2011 年 4 月

**内核版本**：3.0

**联系人**：Konrad Rzeszutek Wilk <konrad.wilk@oracle.com>

**描述**：

    为该后端块设备提供存储的物理设备的主设备号:次设备号（十六进制）。

**What**：`/sys/bus/xen-backend/devices/vbd-*/mode`

**日期**：2011 年 4 月

**内核版本**：3.0

**联系人**：Konrad Rzeszutek Wilk <konrad.wilk@oracle.com>

**描述**：

    块设备是只读（'r'）还是读写（'w'）。

**What**：`/sys/bus/xen-backend/devices/vbd-*/statistics/f_req`

**日期**：2011 年 4 月

**内核版本**：3.0

**联系人**：Konrad Rzeszutek Wilk <konrad.wilk@oracle.com>

**描述**：

    前端的刷新请求数量。

**What**：`/sys/bus/xen-backend/devices/vbd-*/statistics/oo_req`

**日期**：2011 年 4 月

**内核版本**：3.0

**联系人**：Konrad Rzeszutek Wilk <konrad.wilk@oracle.com>

**描述**：

    由于后端忙于处理之前的请求而延迟的请求数量。

**What**：`/sys/bus/xen-backend/devices/vbd-*/statistics/rd_req`

**日期**：2011 年 4 月

**内核版本**：3.0

**联系人**：Konrad Rzeszutek Wilk <konrad.wilk@oracle.com>

**描述**：

    前端的读请求数量。

**What**：`/sys/bus/xen-backend/devices/vbd-*/statistics/rd_sect`

**日期**：2011 年 4 月

**内核版本**：3.0

**联系人**：Konrad Rzeszutek Wilk <konrad.wilk@oracle.com>

**描述**：

    前端读取的扇区数量。

**What**：`/sys/bus/xen-backend/devices/vbd-*/statistics/wr_req`

**日期**：2011 年 4 月

**内核版本**：3.0

**联系人**：Konrad Rzeszutek Wilk <konrad.wilk@oracle.com>

**描述**：

    前端的写请求数量。

**What**：`/sys/bus/xen-backend/devices/vbd-*/statistics/wr_sect`

**日期**：2011 年 4 月

**内核版本**：3.0

**联系人**：Konrad Rzeszutek Wilk <konrad.wilk@oracle.com>

**描述**：

    前端写入的扇区数量。