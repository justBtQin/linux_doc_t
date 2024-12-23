**一、关于 /sys/bus/firewire/devices/fw[0-9]+/ 的信息**

- **What**：/sys/bus/firewire/devices/fw[0-9]+/

- **Date**：May 2007

- **KernelVersion**：2.6.22

- **Contact**：linux1394-devel@lists.sourceforge.net

- **Description**：

    - IEEE 1394 节点设备属性。只读。在节点设备的生命周期内可修改。请参阅 IEEE 1212 以获取语义定义。

    - config_rom：配置 ROM 寄存器的内容。二进制属性；一个主机字节序 u32 数组。

    - guid：配置 ROM 总线信息块中的节点 EUI-64。u64 的十六进制字符串表示。

**二、关于 /sys/bus/firewire/devices/fw[0-9]+/units 的信息**

- **What**：/sys/bus/firewire/devices/fw[0-9]+/units

- **Date**：June 2009

- **KernelVersion**：2.6.31

- **Contact**：linux1394-devel@lists.sourceforge.net

- **Description**：

    - IEEE 1394 节点设备属性。只读。在节点设备的生命周期内可修改。请参阅 IEEE 1212 以获取语义定义。

    - units：IEEE 1394 节点中所有存在的单元的摘要。包含每个单元的规范 ID 和版本的以空格分隔的元组。规范 ID 和版本是各自单元目录条目的 u24 的十六进制字符串表示。每个元组中的规范 ID 和版本由冒号分隔。

- **Users**：udev 规则，用于设置 /dev/fw[0-9]+ 字符设备文件的所有权和访问权限或 ACLs。

**三、关于 /sys/bus/firewire/devices/fw[0-9]+/is_local 的信息**

- **What**：/sys/bus/firewire/devices/fw[0-9]+/is_local

- **Date**：July 2012

- **KernelVersion**：3.6

- **Contact**：linux1394-devel@lists.sourceforge.net

- **Description**：

    - IEEE 1394 节点设备属性。只读且不可变。值：

        - 1：sysfs 条目表示本地节点（控制器卡）。

        - 0：sysfs 条目表示远程节点。

**四、关于 /sys/bus/firewire/devices/fw[0-9]+[.][0-9]+/ 的信息**

- **What**：/sys/bus/firewire/devices/fw[0-9]+[.][0-9]+/

- **Date**：May 2007

- **KernelVersion**：2.6.22

- **Contact**：linux1394-devel@lists.sourceforge.net

- **Description**：

    - IEEE 1394 单元设备属性。只读。在单元设备的生命周期内不可变。请参阅 IEEE 1212 以获取语义定义。

    - modalias：与设备创建时的 uevent 中的 MODALIAS 相同。

    - rom_index：父设备（节点设备）的配置 ROM 中单元目录的偏移量，以四元组形式表示。十进制字符串表示。

**五、关于 /sys/bus/firewire/devices/*/ 的信息**

- **What**：/sys/bus/firewire/devices/*/

- **Date**：May 2007

- **KernelVersion**：2.6.22

- **Contact**：linux1394-devel@lists.sourceforge.net

- **Description**：

    - IEEE 1394 节点设备和单元设备共有的属性。只读。在节点设备的生命周期内可修改。在单元设备的生命周期内不可变。请参阅 IEEE 1212 以获取语义定义。

    - 这些属性仅在 IEEE 1394 节点的根目录或 IEEE 1394 单元的单元目录实际包含相应条目时创建。

    - hardware_version：u24 的十六进制字符串表示。

    - hardware_version_name：相应文本描述叶的内容。

    - model：u24 的十六进制字符串表示。

    - model_name：相应文本描述叶的内容。

    - specifier_id：u24 的十六进制字符串表示。根据 IEEE 1212，在单元目录中是必需的。

    - vendor：u24 的十六进制字符串表示。根据 IEEE 1212，在根目录中是必需的。

    - vendor_name：相应文本描述叶的内容。

    - version：u24 的十六进制字符串表示。根据 IEEE 1212，在单元目录中是必需的。

**六、关于 /sys/bus/firewire/drivers/sbp2/fw*/host*/target*/*:*:*:*/ieee1394_id 的信息（formerly /sys/bus/ieee1394/drivers/sbp2/fw*/host*/target*/*:*:*:*/ieee1394_id）**

- **What**：/sys/bus/firewire/drivers/sbp2/fw*/host*/target*/*:*:*:*/ieee1394_id（formerly /sys/bus/ieee1394/drivers/sbp2/fw*/host*/target*/*:*:*:*/ieee1394_id）

- **Date**：Feb 2004

- **KernelVersion**：2.6.4

- **Contact**：linux1394-devel@lists.sourceforge.net

- **Description**：

    - SCSI 目标端口标识符和 SBP-2 目标的逻辑单元标识符。标识符在 SAM-2...SAM-4 附件 A 中指定。它们是 SBP-2 连接目标的持久且全球唯一的属性。

    - 只读属性，在目标的生命周期内不可变。格式（自 2007 年 5 月 2.6.22 的 firewire-sbp2 起暴露）：

        - 冒号分隔的 u64 EUI-64 的十六进制字符串表示：u24 目录_ID：u16 LUN

        - 无 0x 前缀，无空格。以前的 sbp2 驱动程序（在被 firewire-sbp2 取代后于 2.6.37 中删除）使用了一种稍短的格式，与 SAM 不太接近。

- **Users**：udev 规则，用于创建 /dev/disk/by-id/ 符号链接。