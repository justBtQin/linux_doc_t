### /sys/devices/platform/asus_laptop/display

- **What**：/sys/devices/platform/asus_laptop/display

- **Date**：January 2007

- **KernelVersion**：2.6.20

- **Contact**："Corentin Chary" <corentincj@iksaif.net>

- **Description**：

  此文件允许进行显示切换。值由 4 位组成，定义如下：

  ```

  4321

  |||`- LCD

  ||`-- CRT

  |`--- TV

  `---- DVI

  ```

  例如：- 0（0000b）表示无显示

        - 3（0011b）表示 CRT + LCD。

### /sys/devices/platform/asus_laptop/gps

- **What**：/sys/devices/platform/asus_laptop/gps

- **Date**：January 2007

- **KernelVersion**：2.6.20

- **Contact**："Corentin Chary" <corentincj@iksaif.net>

- **Description**：

  控制 gps 设备。1 表示开启，0 表示关闭。

  **Users**：Lapsus

### /sys/devices/platform/asus_laptop/ledd

- **What**：/sys/devices/platform/asus_laptop/ledd

- **Date**：January 2007

- **KernelVersion**：2.6.20

- **Contact**："Corentin Chary" <corentincj@iksaif.net>

- **Description**：

  像 W1N 这样的一些型号有一个 LED 显示屏，可用于显示若干信息项。

  要控制 LED 显示屏，请使用以下命令：

  ```

  echo 0x0T000DDD > /sys/devices/platform/asus_laptop/

  ```

  其中 T 控制 3 个字母的显示，DDD 控制 3 位数字的显示。

  DDD 表可在 Documentation/laptops/asus-laptop.txt 中找到。

### /sys/devices/platform/asus_laptop/bluetooth

- **What**：/sys/devices/platform/asus_laptop/bluetooth

- **Date**：January 2007

- **KernelVersion**：2.6.20

- **Contact**："Corentin Chary" <corentincj@iksaif.net>

- **Description**：

  控制蓝牙设备。1 表示开启，0 表示关闭。

  这可能同时控制 led 和设备，或者仅控制其中之一。

  **Users**：Lapsus

### /sys/devices/platform/asus_laptop/wlan

- **What**：/sys/devices/platform/asus_laptop/wlan

- **Date**：January 2007

- **KernelVersion**：2.6.20

- **Contact**："Corentin Chary" <corentincj@iksaif.net>

- **Description**：

  控制 wlan 设备。1 表示开启，0 表示关闭。

  这可能同时控制 led 和设备，或者仅控制其中之一。

  **Users**：Lapsus

### /sys/devices/platform/asus_laptop/wimax

- **What**：/sys/devices/platform/asus_laptop/wimax

- **Date**：October 2010

- **KernelVersion**：2.6.37

- **Contact**："Corentin Chary" <corentincj@iksaif.net>

- **Description**：

  控制 wimax 设备。1 表示开启，0 表示关闭。

### /sys/devices/platform/asus_laptop/wwan

- **What**：/sys/devices/platform/asus_laptop/wwan

- **Date**：October 2010

- **KernelVersion**：2.6.37

- **Contact**："Corentin Chary" <corentincj@iksaif.net>

- **Description**：

  控制 wwan（3G）设备。1 表示开启，0 表示关闭。