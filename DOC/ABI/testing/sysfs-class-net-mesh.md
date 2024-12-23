**/sys/class/net/<mesh_iface>/mesh/aggregated_ogms**

- **What**：指示是否聚合 mesh <mesh_iface> 的 batman 协议消息。

- **Date**：2010 年 5 月

- **Contact**：Marek Lindner <mareklindner@neomailbox.ch>

- **Description**：指示是否聚合 mesh <mesh_iface> 的 batman 协议消息。

**/sys/class/net/<mesh_iface>/mesh/<vlan_subdir>/ap_isolation**

- **What**：指示从无线客户端到另一个无线客户端的数据流量是否将被静默丢弃。

- **Date**：2011 年 5 月

- **Contact**：Antonio Quartulli <antonio@meshcoding.com>

- **Description**：指示从无线客户端到另一个无线客户端的数据流量是否将被静默丢弃。<vlan_subdir> 在引用未标记的 lan 时为空。

**/sys/class/net/<mesh_iface>/mesh/bonding**

- **What**：指示通过 mesh 传输的数据流量是否将在同一时间使用多个接口发送（如果可用）。

- **Date**：2010 年 6 月

- **Contact**：Simon Wunderlich <sw@simonwunderlich.de>

- **Description**：指示通过 mesh 传输的数据流量是否将在同一时间使用多个接口发送（如果可用）。

**/sys/class/net/<mesh_iface>/mesh/bridge_loop_avoidance**

- **What**：指示网桥环路避免功能是否启用。

- **Date**：2011 年 11 月

- **Contact**：Simon Wunderlich <sw@simonwunderlich.de>

- **Description**：指示网桥环路避免功能是否启用。此功能检测并避免 mesh 与通过软接口 <mesh_iface> 桥接的设备之间的环路。

**/sys/class/net/<mesh_iface>/mesh/fragmentation**

- **What**：指示通过 mesh 传输的数据流量在数据包大小超过传出接口 MTU 时是否将被分片或静默丢弃。

- **Date**：2010 年 10 月

- **Contact**：Andreas Langer <an.langer@gmx.de>

- **Description**：指示通过 mesh 传输的数据流量在数据包大小超过传出接口 MTU 时是否将被分片或静默丢弃。

**/sys/class/net/<mesh_iface>/mesh/gw_bandwidth**

- **What**：如果 gw_mode 设置为“server”，则定义此节点传播的带宽。

- **Date**：2010 年 10 月

- **Contact**：Marek Lindner <mareklindner@neomailbox.ch>

- **Description**：如果 gw_mode 设置为“server”，则定义此节点传播的带宽。

**/sys/class/net/<mesh_iface>/mesh/gw_mode**

- **What**：定义网关功能的状态。可以是“off”、“client”或“server”。

- **Date**：2010 年 10 月

- **Contact**：Marek Lindner <mareklindner@neomailbox.ch>

- **Description**：定义网关功能的状态。可以是“off”、“client”或“server”。

**/sys/class/net/<mesh_iface>/mesh/gw_sel_class**

- **What**：定义当 gw_mode 设置为“client”时，此节点将使用的选择标准。

- **Date**：2010 年 10 月

- **Contact**：Marek Lindner <mareklindner@neomailbox.ch>

- **Description**：定义当 gw_mode 设置为“client”时，此节点将使用的选择标准。

**/sys/class/net/<mesh_iface>/mesh/hop_penalty**

- **What**：定义在每跳中应用于发起者消息的 tq 字段的惩罚。

- **Date**：2010 年 10 月

- **Contact**：Linus Lüssing <linus.luessing@web.de>

- **Description**：定义在每跳中应用于发起者消息的 tq 字段的惩罚。

**/sys/class/net/<mesh_iface>/mesh/isolation_mark**

- **What**：定义用于通过扩展隔离功能将客户端分类为“隔离”的隔离标记（及其位掩码）。

- **Date**：2013 年 11 月

- **Contact**：Antonio Quartulli <antonio@meshcoding.com>

- **Description**：定义用于通过扩展隔离功能将客户端分类为“隔离”的隔离标记（及其位掩码）。

**/sys/class/net/<mesh_iface>/mesh/multicast_mode**

- **What**：指示多播优化是否启用或禁用。如果设置为零，则 mesh 中的所有节点将对任何没有优化的多播数据包使用经典泛洪。

- **Date**：2014 年 2 月

- **Contact**：Linus Lüssing <linus.luessing@web.de>

- **Description**：指示多播优化是否启用或禁用。如果设置为零，则 mesh 中的所有节点将对任何没有优化的多播数据包使用经典泛洪。

**/sys/class/net/<mesh_iface>/mesh/network_coding**

- **What**：控制是否启用网络编码（使用一些魔法发送更少的 wifi 数据包但仍包含相同内容）。

- **Date**：2012 年 11 月

- **Contact**：Martin Hundeboll <martin@hundeboll.net>

- **Description**：控制是否启用网络编码（使用一些魔法发送更少的 wifi 数据包但仍包含相同内容）。

**/sys/class/net/<mesh_iface>/mesh/orig_interval**

- **What**：定义 batman 发送其协议消息的毫秒间隔。

- **Date**：2010 年 5 月

- **Contact**：Marek Lindner <mareklindner@neomailbox.ch>

- **Description**：定义 batman 发送其协议消息的毫秒间隔。

**/sys/class/net/<mesh_iface>/mesh/routing_algo**

- **What**：定义此 mesh 实例用于通过 mesh 找到最佳路径的路由协议。

- **Date**：2011 年 12 月

- **Contact**：Marek Lindner <mareklindner@neomailbox.ch>

- **Description**：定义此 mesh 实例用于通过 mesh 找到最佳路径的路由协议。