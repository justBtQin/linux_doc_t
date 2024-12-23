**/sys/devices/platform/kim/dev_name**

- **Date**：January 2010

- **KernelVersion**：2.6.38

- **Contact**："Pavan Savoy" <pavan_savoy@ti.com>

- **Description**：UART 设备的名称，WL128x 芯片连接在此设备上。例如："/dev/ttyS0"。该设备名称会传递到特定架构的板级初始化文件中，从 SFI/ATAGS 引导加载程序固件中传递。从用户空间守护进程中读取该名称，并在请求安装时打开该设备。

**/sys/devices/platform/kim/baud_rate**

- **Date**：January 2010

- **KernelVersion**：2.6.38

- **Contact**："Pavan Savoy" <pavan_savoy@ti.com>

- **Description**：主机可以支持的最大可靠波特率。不同平台往往具有不同的高速 UART 配置，因此需要在本地设置波特率，并通过 HCI-VS 命令将其发送到 WL128x。在请求安装 ldisc 时，用户空间守护进程会读取并使用该条目。

**/sys/devices/platform/kim/flow_cntrl**

- **Date**：January 2010

- **KernelVersion**：2.6.38

- **Contact**："Pavan Savoy" <pavan_savoy@ti.com>

- **Description**：WL128x 使用流量控制机制，此条目通常应为 1，主机的 UART 需要具备流量控制功能，否则此条目可用于异常情况。

**/sys/devices/platform/kim/install**

- **Date**：January 2010

- **KernelVersion**：2.6.38

- **Contact**："Pavan Savoy" <pavan_savoy@ti.com>

- **Description**：当蓝牙、FM 或 GPS 协议之一想要使用共享的 UART 传输时，它会向共享传输驱动程序注册，该驱动程序将通过此 sysfs 条目向用户空间发出打开、配置波特率和安装线路规程的信号。管理 UART 的用户空间守护进程将轮询此条目，并通过 sysfs_notify 通知有关更改。当 UART 需要打开/安装 ldisc 时，该值将为'1'，当 UART 不再需要并需要关闭时，该值将为'0'。