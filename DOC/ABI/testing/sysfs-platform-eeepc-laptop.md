**一、/sys/devices/platform/eeepc/disp**

- **What**：/sys/devices/platform/eeepc/disp

- **Date**：May 2008

- **KernelVersion**：2.6.26

- **Contact**："Corentin Chary" <corentincj@iksaif.net>

- **Description**：此文件允许进行显示切换。- 1 = LCD，- 2 = CRT，- 3 = LCD + CRT。如果运行 X11，应使用 xrandr 代替。

**二、/sys/devices/platform/eeepc/camera**

- **What**：/sys/devices/platform/eeepc/camera

- **Date**：May 2008

- **KernelVersion**：2.6.26

- **Contact**："Corentin Chary" <corentincj@iksaif.net>

- **Description**：控制摄像头。1 表示打开，0 表示关闭。

**三、/sys/devices/platform/eeepc/cardr**

- **What**：/sys/devices/platform/eeepc/cardr

- **Date**：May 2008

- **KernelVersion**：2.6.26

- **Contact**："Corentin Chary" <corentincj@iksaif.net>

- **Description**：控制读卡器。1 表示打开，0 表示关闭。

**四、/sys/devices/platform/eeepc/cpufv**

- **What**：/sys/devices/platform/eeepc/cpufv

- **Date**：Jun 2009

- **KernelVersion**：2.6.31

- **Contact**："Corentin Chary" <corentincj@iksaif.net>

- **Description**：更改 CPU 时钟配置。在 Eee PC 1000H 上有三种可用的时钟配置：

    - *0 -> 超级性能模式*

    - *1 -> 高性能模式*

    - *2 -> 节能模式*

在 Eee PC 701 上只有 2 种可用的时钟配置。可用配置列在 available_cpufv 文件中。读取此文件将显示原始十六进制值，其定义如下：

| 8 位 | 8 位 |

| ---- | ---- |

| `---- 当前模式 | `------------ 可用模式 |

例如，0x301 表示：选择模式 1，有 3 种可用模式。

**五、/sys/devices/platform/eeepc/available_cpufv**

- **What**：/sys/devices/platform/eeepc/available_cpufv

- **Date**：Jun 2009

- **KernelVersion**：2.6.31

- **Contact**："Corentin Chary" <corentincj@iksaif.net>

- **Description**：列出可用的 cpufv 模式。