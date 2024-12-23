**关于 /sys/bus/usb/drivers/wusb_cbaf/.../wusb_* 的信息：**

- **What**：/sys/bus/usb/drivers/wusb_cbaf/.../wusb_*

- **Date**：August 2008

- **KernelVersion**：2.6.27

- **Contact**：David Vrabel <david.vrabel@csr.com>

- **Description**：用于管理基于电缆的（无线）USB 设备关联的各种文件。操作顺序如下：

    1. 设备插入。

    2. 连接管理器（CM）看到具有 CBA 能力的设备。（位于 /sys/devices/blah/OURDEVICE 中的 wusb_chid 等文件）。

    3. CM 将主机名、支持的频段组和 CHID（主机 ID）写入 wusb_host_name、wusb_host_band_groups 和 wusb_chid 文件。这些信息将发送到设备，并请求此主机的 CDID（如果有）。

    4. CM 可以验证设备支持的频段组（wusb_device_band_groups）与主机兼容。

    5. CM 读取 wusb_cdid 文件。

    6. CM 在其数据库中查找。

        - 如果有匹配的 CHID、CDID 条目，则设备之前已被授权，无需进一步操作。

        - 如果 CDID 为零（或 CM 在其数据库中未找到匹配的 CDID），则假定设备未知。CM 可以通过以下方式将主机与设备关联：将随机生成的 CDID 写入 wusb_cdid，然后将随机 CK 写入 wusb_ck（这将新的 CC 上传到设备）。CM 可以选择在与新设备关联之前提示用户。

    7. 设备拔出。

- **References**：[WUSB-AM] 认证无线通用串行总线规范的关联模型补充，版本 1.0。

**关于 /sys/bus/usb/drivers/wusb_cbaf/.../wusb_chid 的信息：**

- **What**：/sys/bus/usb/drivers/wusb_cbaf/.../wusb_chid

- **Date**：August 2008

- **KernelVersion**：2.6.27

- **Contact**：David Vrabel <david.vrabel@csr.com>

- **Description**：主机的 CHID，格式为 16 个以空格分隔的十六进制八位字节。写入可获取设备支持的频段组以及与此主机的任何现有关联的 CDID。

**关于 /sys/bus/usb/drivers/wusb_cbaf/.../wusb_host_name 的信息：**

- **What**：/sys/bus/usb/drivers/wusb_cbaf/.../wusb_host_name

- **Date**：August 2008

- **KernelVersion**：2.6.27

- **Contact**：David Vrabel <david.vrabel@csr.com>

- **Description**：主机的友好名称，为 UTF-8 编码字符串。

**关于 /sys/bus/usb/drivers/wusb_cbaf/.../wusb_host_band_groups 的信息：**

- **What**：/sys/bus/usb/drivers/wusb_cbaf/.../wusb_host_band_groups

- **Date**：August 2008

- **KernelVersion**：2.6.27

- **Contact**：David Vrabel <david.vrabel@csr.com>

- **Description**：主机支持的频段组，格式在 [WUSB-AM] 中定义。

**关于 /sys/bus/usb/drivers/wusb_cbaf/.../wusb_device_band_groups 的信息：**

- **What**：/sys/bus/usb/drivers/wusb_cbaf/.../wusb_device_band_groups

- **Date**：August 2008

- **KernelVersion**：2.6.27

- **Contact**：David Vrabel <david.vrabel@csr.com>

- **Description**：设备支持的频段组，格式在 [WUSB-AM] 中定义。

**关于 /sys/bus/usb/drivers/wusb_cbaf/.../wusb_cdid 的信息：**

- **What**：/sys/bus/usb/drivers/wusb_cbaf/.../wusb_cdid

- **Date**：August 2008

- **KernelVersion**：2.6.27

- **Contact**：David Vrabel <david.vrabel@csr.com>

- **Description**：设备的 CDID，格式为 16 个以空格分隔的十六进制八位字节。

**关于 /sys/bus/usb/drivers/wusb_cbaf/.../wusb_ck 的信息：**

- **What**：/sys/bus/usb/drivers/wusb_cbaf/.../wusb_ck

- **Date**：August 2008

- **KernelVersion**：2.6.27

- **Contact**：David Vrabel <david.vrabel@csr.com>

- **Description**：写入 16 个以空格分隔的随机十六进制八位字节以与设备关联。