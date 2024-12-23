**/sys/class/net/<iface>/name_assign_type**

- **What**：表示名称分配类型。

- **Date**：2014 年 7 月

- **KernelVersion**：3.17

- **Contact**：netdev@vger.kernel.org

- **Description**：可能的值有：

    - 1：由内核枚举，可能以不可预测的方式

    - 2：由内核可预测地命名

    - 3：由用户空间命名

    - 4：重命名

**/sys/class/net/<iface>/addr_assign_type**

- **What**：表示地址分配类型。

- **Date**：2010 年 7 月

- **KernelVersion**：3.2

- **Contact**：netdev@vger.kernel.org

- **Description**：可能的值有：

    - 0：永久地址

    - 1：随机生成

    - 2：从另一个设备窃取

    - 3：使用 dev_set_mac_address 设置

**/sys/class/net/<iface>/addr_len**

- **What**：表示硬件地址的字节大小。

- **Date**：2005 年 4 月

- **KernelVersion**：2.6.12

- **Contact**：netdev@vger.kernel.org

- **Description**：值根据接口使用的低级协议（以太网、FDDI、ATM、IEEE 802.15.4 等）而变化。请参阅 include/uapi/linux/if_*.h 以获取实际值。

**/sys/class/net/<iface>/address**

- **What**：当前分配给此接口的硬件地址。

- **Date**：2005 年 4 月

- **KernelVersion**：2.6.12

- **Contact**：netdev@vger.kernel.org

- **Description**：格式为字符串，例如：以太网 MAC 地址为 00:11:22:33:44:55。

**/sys/class/net/<iface>/broadcast**

- **What**：此接口的硬件广播地址。

- **Date**：2005 年 4 月

- **KernelVersion**：2.6.12

- **Contact**：netdev@vger.kernel.org

- **Description**：格式为字符串，例如：以太网广播 MAC 地址为 ff:ff:ff:ff:ff:ff。

**/sys/class/net/<iface>/carrier**

- **What**：表示接口的当前物理链路状态。

- **Date**：2005 年 4 月

- **KernelVersion**：2.6.12

- **Contact**：netdev@vger.kernel.org

- **Description**：可能的值有：

    - 0：物理链路关闭

    - 1：物理链路开启

    注意：一些特殊设备，例如绑定和团队驱动程序，将允许写入此属性以强制链路状态以正确操作并指定另一个备用接口。

**/sys/class/net/<iface>/dev_id**

- **What**：表示设备唯一标识符。

- **Date**：2008 年 4 月

- **KernelVersion**：2.6.26

- **Contact**：netdev@vger.kernel.org

- **Description**：格式为十六进制值。用于消除可能堆叠（例如：VLAN 接口）但仍具有与其父设备相同的 MAC 地址的接口的歧义。

**/sys/class/net/<iface>/dormant**

- **What**：表示接口是否处于休眠状态。

- **Date**：2006 年 3 月

- **KernelVersion**：2.6.17

- **Contact**：netdev@vger.kernel.org

- **Description**：可能的值有：

    - 0：接口未休眠

    - 1：接口处于休眠状态

    此属性可被请求者软件用于指示设备不可用，除非执行了一些基于请求者的身份验证（例如：802.1x）。“link_mode”属性也将反映休眠状态。

**/sys/class/net/<iface>/duplex**

- **What**：表示接口的最新或当前双工值。

- **Date**：2009 年 10 月

- **KernelVersion**：2.6.33

- **Contact**：netdev@vger.kernel.org

- **Description**：可能的值有：

    - half：半双工

    - full：全双工

    注意：此属性仅对实现 ethtool get_settings 方法（主要是以太网）的接口有效。

**/sys/class/net/<iface>/flags**

- **What**：表示接口标志作为十六进制位掩码。

- **Date**：2005 年 4 月

- **KernelVersion**：2.6.12

- **Contact**：netdev@vger.kernel.org

- **Description**：请参阅 include/uapi/linux/if.h 以获取所有可能值和标志语义的列表。

**/sys/class/net/<iface>/ifalias**

- **What**：表示/存储接口别名名称作为字符串。

- **Date**：2008 年 9 月

- **KernelVersion**：2.6.28

- **Contact**：netdev@vger.kernel.org

- **Description**：可用于系统管理目的。

**/sys/class/net/<iface>/ifindex**

- **What**：表示系统范围的接口唯一索引标识符作为十进制数。

- **Date**：2005 年 4 月

- **KernelVersion**：2.6.12

- **Contact**：netdev@vger.kernel.org

- **Description**：此属性用于将接口标识符映射到接口名称。它在整个网络堆栈中用于指定接口特定的请求/事件。

**/sys/class/net/<iface>/iflink**

- **What**：表示此接口链接到的系统范围的接口唯一索引标识符。

- **Date**：2005 年 4 月

- **KernelVersion**：2.6.12

- **Contact**：netdev@vger.kernel.org

- **Description**：格式为十进制。此属性用于解析接口链接、链接和堆叠。物理接口具有相同的“ifindex”和“iflink”值。

**/sys/class/net/<iface>/link_mode**

- **What**：表示接口链接模式，作为十进制数。

- **Date**：2006 年 3 月

- **KernelVersion**：2.6.17

- **Contact**：netdev@vger.kernel.org

- **Description**：应与“dormant”属性结合使用以确定接口的可用性。可能的值有：

    - 0：默认链接模式

    - 1：休眠链接模式

**/sys/class/net/<iface>/mtu**

- **What**：表示接口当前配置的 MTU 值，以字节为单位，格式为十进制。

- **Date**：2005 年 4 月

- **KernelVersion**：2.6.12

- **Contact**：netdev@vger.kernel.org

- **Description**：具体值取决于使用的低级接口协议。以太网设备除非更改，否则将显示“mtu”属性值为 1500。

**/sys/class/net/<iface>/netdev_group**

- **What**：表示接口网络设备组，作为十进制整数。

- **Date**：2011 年 1 月

- **KernelVersion**：2.6.39

- **Contact**：netdev@vger.kernel.org

- **Description**：默认值为 0，对应于初始网络设备组。可以更改该组以影响路由决策（请参阅：net/ipv4/fib_rules 和 net/ipv6/fib6_rules.c）。

**/sys/class/net/<iface>/operstate**

- **What**：表示接口 RFC2863 操作状态作为字符串。

- **Date**：2006 年 3 月

- **KernelVersion**：2.6.17

- **Contact**：netdev@vger.kernel.org

- **Description**：可能的值有：

    - "unknown"

    - "notpresent"

    - "down"

    - "lowerlayerdown"

    - "testing"

    - "dormant"

    - "up"

**/sys/class/net/<iface>/phys_port_id**

- **What**：表示在 NIC 内接口的唯一物理端口标识符，作为字符串。

- **Date**：2013 年 7 月

- **KernelVersion**：3.12

- **Contact**：netdev@vger.kernel.org

- **Description**：

**/sys/class/net/<iface>/phys_port_name**

- **What**：表示在 NIC 内接口的物理端口名称，作为字符串。

- **Date**：2015 年 3 月

- **KernelVersion**：4.0

- **Contact**：netdev@vger.kernel.org

- **Description**：

**/sys/class/net/<iface>/speed**

- **What**：表示接口的最新或当前速度值。值是表示链路速度的整数，单位为 Mbits/sec。

- **Date**：2009 年 10 月

- **KernelVersion**：2.6.33

- **Contact**：netdev@vger.kernel.org

- **Description**：注意：此属性仅对实现 ethtool get_settings 方法（主要是以太网）的接口有效。

**/sys/class/net/<iface>/tx_queue_len**

- **What**：表示接口的传输队列长度，以数据包数量为单位，作为整数值。

- **Date**：2005 年 4 月

- **KernelVersion**：2.6.12

- **Contact**：netdev@vger.kernel.org

- **Description**：值取决于接口的类型，以太网网络适配器的默认值为 1000，除非另行配置。

**/sys/class/net/<iface>/type**

- **What**：表示接口协议类型作为十进制值。

- **Date**：2005 年 4 月

- **KernelVersion**：2.6.12

- **Contact**：netdev@vger.kernel.org

- **Description**：请参阅 include/uapi/linux/if_arp.h 以获取所有可能的值。

**/sys/class/net/<iface>/phys_switch_id**

- **What**：表示此端口所属交换机的唯一物理交换机标识符，作为字符串。

- **Date**：2014 年 11 月

- **KernelVersion**：3.19

- **Contact**：netdev@vger.kernel.org

- **Description**：