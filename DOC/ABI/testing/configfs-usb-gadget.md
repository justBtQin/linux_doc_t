**一、/config/usb-gadget**

- **What**：/config/usb-gadget

- **Date**：Jun 2013

- **KernelVersion**：3.11

- **Description**：此组包含与创建的 USB 设备相对应的子组。

**二、/config/usb-gadget/gadget**

- **What**：/config/usb-gadget/gadget

- **Date**：Jun 2013

- **KernelVersion**：3.11

- **Description**：

    - 设备的属性：

    - UDC - 将设备绑定到 UDC/解绑设备；将 /sys/class/udc/* 中的 UDC 名称写入以绑定设备，空字符串 "" 以解绑。

    - bDeviceClass - USB 设备类代码

    - bDeviceSubClass - USB 设备子类代码

    - bDeviceProtocol - USB 设备协议代码

    - bMaxPacketSize0 - 最大端点 0 数据包大小

    - bcdDevice - bcd 设备版本号

    - bcdUSB - bcd USB 规范版本号

    - idProduct - 产品 ID

    - idVendor - 供应商 ID

**三、/config/usb-gadget/gadget/configs**

- **What**：/config/usb-gadget/gadget/configs

- **Date**：Jun 2013

- **KernelVersion**：3.11

- **Description**：此组包含 USB 设备的配置。

**四、/config/usb-gadget/gadget/configs/config**

- **What**：/config/usb-gadget/gadget/configs/config

- **Date**：Jun 2013

- **KernelVersion**：3.11

- **Description**：

    - 配置的属性：

    - bmAttributes - 配置特征

    - MaxPower - 从总线的最大功耗

**五、/config/usb-gadget/gadget/configs/config/strings**

- **What**：/config/usb-gadget/gadget/configs/config/strings

- **Date**：Jun 2013

- **KernelVersion**：3.11

- **Description**：此组包含此配置的特定于语言的字符串的子目录。

**六、/config/usb-gadget/gadget/configs/config/strings/language**

- **What**：/config/usb-gadget/gadget/configs/config/strings/language

- **Date**：Jun 2013

- **KernelVersion**：3.11

- **Description**：

    - 属性：

    - configuration - 配置描述

**七、/config/usb-gadget/gadget/functions**

- **What**：/config/usb-gadget/gadget/functions

- **Date**：Jun 2013

- **KernelVersion**：3.11

- **Description**：此组包含此 USB 设备可用的功能。

**八、/config/usb-gadget/gadget/functions/<func>.<inst>/interface.<n>**

- **What**：/config/usb-gadget/gadget/functions/<func>.<inst>/interface.<n>

- **Date**：May 2014

- **KernelVersion**：3.16

- **Description**：此组包含特定于一个设备的 USB 接口或由 IAD 描述的一个接口组的“功能描述符”。

    - 属性：

    - compatible_id - 8 字节字符串，用于“兼容 ID”

    - sub_compatible_id - 8 字节字符串，用于“子兼容 ID”

**九、/config/usb-gadget/gadget/functions/<func>.<inst>/interface.<n>/<property>**

- **What**：/config/usb-gadget/gadget/functions/<func>.<inst>/interface.<n>/<property>

- **Date**：May 2014

- **KernelVersion**：3.16

- **Description**：此组包含特定于一个设备的 USB 接口或由 IAD 描述的一个接口组的“扩展属性描述符”。

    - 属性：

    - type - 值 1..7 用于解释数据

        - 1：unicode 字符串

        - 2：带有环境变量的 unicode 字符串

        - 3：二进制

        - 4：小端 32 位

        - 5：大端 32 位

        - 6：带有符号链接的 unicode 字符串

        - 7：多个 unicode 字符串

    - data - 要根据 type 解释的数据块

**十、/config/usb-gadget/gadget/strings**

- **What**：/config/usb-gadget/gadget/strings

- **Date**：Jun 2013

- **KernelVersion**：3.11

- **Description**：此组包含此设备的特定于语言的字符串的子目录。

**十一、/config/usb-gadget/gadget/strings/language**

- **What**：/config/usb-gadget/gadget/strings/language

- **Date**：Jun 2013

- **KernelVersion**：3.11

- **Description**：

    - 属性：

    - serialnumber - 设备的序列号（字符串）

    - product - 设备的产品描述

    - manufacturer - 设备的制造商描述

**十二、/config/usb-gadget/gadget/os_desc**

- **What**：/config/usb-gadget/gadget/os_desc

- **Date**：May 2014

- **KernelVersion**：3.16

- **Description**：此组包含“OS 字符串”扩展处理属性。

    - use - 打开/关闭“OS 描述符”支持的标志

    - b_vendor_code - 用于自定义每个设备和每个接口请求的 1 字节值

    - qw_sign - 要报告为“OS 字符串”的标识符