**/sys/bus/rbd/**

- **Date**：November 2010

- **Contact**：Yehuda Sadeh <yehuda@newdream.net>, Sage Weil <sage@newdream.net>

- **Description**：用于添加和删除 rbd 块设备。

- **Usage**：<mon ip addr> <options> <pool name> <rbd image name> [snap name]

  - `echo "192.168.0.1 name=admin rbd foo" > /sys/bus/rbd/add`

  - 快照名称可以为“-”或省略，以映射图像为读写模式。将为任何注册的块设备分配一个<dev-id>。如果使用了快照，它将被映射为只读模式。

- **Removal of a device**：

  - `echo <dev-id> > /sys/bus/rbd/remove`

**/sys/bus/rbd/add_single_major**

- **Date**：December 2013

- **KernelVersion**：3.14

- **Contact**：Sage Weil <sage@inktank.com>

- **Description**：仅在 rbd 模块以 single_major 参数设置为 true 插入时可用。用法与 /sys/bus/rbd/add 相同。如果存在，应使用它而不是后者：出于向后兼容的原因，如果 /sys/bus/rbd/add_single_major 可用，而尝试使用 /sys/bus/rbd/add 将失败。

**/sys/bus/rbd/remove_single_major**

- **Date**：December 2013

- **KernelVersion**：3.14

- **Contact**：Sage Weil <sage@inktank.com>

- **Description**：仅在 rbd 模块以 single_major 参数设置为 true 插入时可用。用法与 /sys/bus/rbd/remove 相同。如果存在，应使用它而不是后者：出于向后兼容的原因，如果 /sys/bus/rbd/remove_single_major 可用，而尝试使用 /sys/bus/rbd/remove 将失败。

**Entries under /sys/bus/rbd/devices/<dev-id>/**

- **client_id**：为该特定会话分配的 ceph 唯一客户端 ID。

- **features**：此图像的功能位的十六进制编码。

- **major**：块设备的主设备号。

- **minor**：（2013 年 12 月，自 3.14 版本起）块设备的次设备号。

- **name**：rbd 图像的名称。

- **image_id**：rbd 图像的唯一 ID。（对于 rbd 图像格式 1，此为空。）

- **pool**：此 rbd 图像所在的存储池的名称。rbd 图像名称在其池中是唯一的。

- **pool_id**：rbd 图像的池的唯一标识符。这是池的永久属性。池的 ID 永远不会更改。

- **size**：映射的块设备的大小（以字节为单位）。

- **refresh**：写入此文件将重新读取图像头数据，并相应地设置所有相关的数据结构。

- **current_snap**：设备映射到的当前快照。

- **parent**：标识分层 rbd 图像中父图像链的信息。条目以空行分隔。