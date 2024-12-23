**一、关于 /sys/module/pch_phub/drivers/.../pch_mac 的信息**

- **What**：/sys/module/pch_phub/drivers/.../pch_mac

- **Date**：August 2010

- **KernelVersion**：2.6.35

- **Contact**：masa-korg@dsn.okisemi.com

- **Description**：Write/read GbE MAC address. 

**二、关于 /sys/module/pch_phub/drivers/.../pch_firmware 的信息**

- **What**：/sys/module/pch_phub/drivers/.../pch_firmware

- **Date**：August 2010

- **KernelVersion**：2.6.35

- **Contact**：masa-korg@dsn.okisemi.com

- **Description**：Write/read Option ROM data. 

**三、关于 /sys/module/ehci_hcd/drivers/.../uframe_periodic_max 的信息**

- **What**：/sys/module/ehci_hcd/drivers/.../uframe_periodic_max

- **Date**：July 2011

- **KernelVersion**：3.1

- **Contact**：Kirill Smelkov <kirr@mns.spb.ru>

- **Description**：每个微帧的周期性传输允许的最大时间（μs）

    - [ USB 2.0 将每个微帧的周期性传输允许的最大时间设置为 80%，即在 125 微秒（完整微帧）中为 100 微秒。

      然而，在某些情况下，80%的最大等时带宽太有限。例如，两个视频流可能需要每个微帧 110 微秒的等时带宽才能一起工作。 ]

    - 通过此设置，可以提高限制，以便主机控制器允许每个微帧分配超过 100 微秒的周期性带宽。

    - 请注意，非标准模式通常未经过硬件设计人员的彻底测试，并且当此设置与默认的 100 不同时，硬件可能会出现故障。 

**四、关于 /sys/module/*/{coresize,initsize} 的信息**

- **What**：/sys/module/*/{coresize,initsize}

- **Date**：Jan 2012

- **KernelVersion**：3.3

- **Contact**：Kay Sievers <kay.sievers@vrfy.org>

- **Description**：Module size in bytes. 

**五、关于 /sys/module/*/taint 的信息**

- **What**：/sys/module/*/taint

- **Date**：Jan 2012

- **KernelVersion**：3.3

- **Contact**：Kay Sievers <kay.sievers@vrfy.org>

- **Description**：Module taint flags:

    - P - proprietary module

    - O - out-of-tree module

    - F - force-loaded module

    - C - staging driver module

    - E - unsigned module