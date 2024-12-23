链接层验证设备是用于测试超高速链路层测试的标准设备。仅当 lvs 驱动程序与根集线器设备绑定时，这些节点才在 sysfs 中可用。

# 内容：

- **/sys/bus/usb/devices/.../get_dev_desc**

    - 日期：2014 年 3 月

    - 联系人：Pratyush Anand <pratyush.anand@st.com>

    - 描述：写入此节点以向链接层验证设备发出“获取设备描述符”命令。这是 TD.7.06 所需的。

- **/sys/bus/usb/devices/.../u1_timeout**

    - 日期：2014 年 3 月

    - 联系人：Pratyush Anand <pratyush.anand@st.com>

    - 描述：为连接链接层验证设备的下游端口设置“U1 超时”。超时值必须在 0 到 127 之间。这是 TD.7.18、TD.7.19、TD.7.20 和 TD.7.21 所需的。

- **/sys/bus/usb/devices/.../u2_timeout**

    - 日期：2014 年 3 月

    - 联系人：Pratyush Anand <pratyush.anand@st.com>

    - 描述：为连接链接层验证设备的下游端口设置“U2 超时”。超时值必须在 0 到 127 之间。这是 TD.7.18、TD.7.19、TD.7.20 和 TD.7.21 所需的。

- **/sys/bus/usb/devices/.../hot_reset**

    - 日期：2014 年 3 月

    - 联系人：Pratyush Anand <pratyush.anand@st.com>

    - 描述：写入此节点以向链接层验证设备发出“重置”命令。这是 TD.7.29、TD.7.31、TD.7.34 和 TD.7.35 所需的。

- **/sys/bus/usb/devices/.../u3_entry**

    - 日期：2014 年 3 月

    - 联系人：Pratyush Anand <pratyush.anand@st.com>

    - 描述：写入此节点以向链接层验证设备发出“U3 进入”命令。这是 TD.7.35 和 TD.7.36 所需的。

- **/sys/bus/usb/devices/.../u3_exit**

    - 日期：2014 年 3 月

    - 联系人：Pratyush Anand <pratyush.anand@st.com>

    - 描述：写入此节点以向链接层验证设备发出“U3 退出”命令。这是 TD.7.36 所需的。