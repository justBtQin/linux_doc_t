**/sys/bus/rpmsg/devices/.../name**

- **What**：/sys/bus/rpmsg/devices/.../name

- **Date**：June 2011

- **KernelVersion**：3.3

- **Contact**：Ohad Ben-Cohen <ohad@wizery.com>

- **Description**：每个 rpmsg 设备都是与远程处理器的通信通道。通道通过（文本形式的）名称进行标识，名称最长为 32 字节（在 rpmsg.h 中定义为 RPMSG_NAME_SIZE）。此 sysfs 条目包含此通道的名称。

**/sys/bus/rpmsg/devices/.../src**

- **What**：/sys/bus/rpmsg/devices/.../src

- **Date**：June 2011

- **KernelVersion**：3.3

- **Contact**：Ohad Ben-Cohen <ohad@wizery.com>

- **Description**：每个 rpmsg 设备都是与远程处理器的通信通道。通道有本地（“源”）rpmsg 地址和远程（“目的地”）rpmsg 地址。当一个实体在通道的一端开始监听时，它会为其分配一个唯一的 rpmsg 地址（一个 32 位整数）。这样，当入站消息到达此地址时，rpmsg 核心会将它们分派到监听实体（一个内核驱动程序）。此 sysfs 条目包含此通道的 src（本地）rpmsg 地址。如果它包含 0xffffffff，则未分配地址（如果此通道没有驱动程序存在，可能会发生这种情况）。

**/sys/bus/rpmsg/devices/.../dst**

- **What**：/sys/bus/rpmsg/devices/.../dst

- **Date**：June 2011

- **KernelVersion**：3.3

- **Contact**：Ohad Ben-Cohen <ohad@wizery.com>

- **Description**：每个 rpmsg 设备都是与远程处理器的通信通道。通道有本地（“源”）rpmsg 地址和远程（“目的地”）rpmsg 地址。当一个实体在通道的一端开始监听时，它会为其分配一个唯一的 rpmsg 地址（一个 32 位整数）。这样，当入站消息到达此地址时，rpmsg 核心会将它们分派到监听实体。此 sysfs 条目包含此通道的 dst（远程）rpmsg 地址。如果它包含 0xffffffff，则未分配地址（如果连接到此通道的内核驱动程序正在向远程处理器公开服务，可能会发生这种情况。这使其成为本地 rpmsg 服务器，并正在监听可能从任何远程 rpmsg 客户端发送的入站消息；它未绑定到单个远程实体）。

**/sys/bus/rpmsg/devices/.../announce**

- **What**：/sys/bus/rpmsg/devices/.../announce

- **Date**：June 2011

- **KernelVersion**：3.3

- **Contact**：Ohad Ben-Cohen <ohad@wizery.com>

- **Description**：每个 rpmsg 设备都是与远程处理器的通信通道。通道通过文本名称（参见上面的 /sys/bus/rpmsg/devices/.../name）进行标识，并具有本地（“源”）rpmsg 地址和远程（“目的地”）rpmsg 地址。当一个实体（无论是本地还是远程）开始在通道上监听消息（因此被称为 rpmsg 服务器）时，通道首次创建。此时，会向另一个处理器发送“名称服务”公告，以便让它知道通道的创建（这样远程客户端就知道它们可以开始发送消息）。此 sysfs 条目告诉我们该通道是否是已公告的本地服务器通道（值为 true 或 false）。