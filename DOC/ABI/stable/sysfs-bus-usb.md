**关于 /sys/bus/usb/devices/.../power/persist：**

- **What**：/sys/bus/usb/devices/.../power/persist

- **Date**：May 2007

- **KernelVersion**：2.6.23

- **Contact**：Alan Stern <stern@rowland.harvard.edu>

- **Description**：USB 设备目录中可能包含一个名为 power/persist 的文件。该文件持有一个布尔值（0 或 1），指示设备的“USB-Persist”功能是否已启用。对于集线器，此功能始终启用，其设备目录将不包含此文件。更多信息请参阅 Documentation/usb/persist.txt。

**关于 /sys/bus/usb/devices/.../power/autosuspend：**

- **What**：/sys/bus/usb/devices/.../power/autosuspend

- **Date**：March 2007

- **KernelVersion**：2.6.21

- **Contact**：Alan Stern <stern@rowland.harvard.edu>

- **Description**：每个 USB 设备目录都将包含一个名为 power/autosuspend 的文件。此文件持有设备在被自动挂起之前必须处于空闲状态的时间（以秒为单位）。0 表示设备将尽快被自动挂起。负值将完全阻止设备被自动挂起，写入负值将在设备已挂起时恢复它。新创建设备的自动挂起延迟设置为 usbcore.autosuspend 模块参数的值。

**关于 /sys/bus/usb/device/.../power/connected_duration：**

- **What**：/sys/bus/usb/device/.../power/connected_duration

- **Date**：January 2008

- **KernelVersion**：2.6.25

- **Contact**：Sarah Sharp <sarah.a.sharp@intel.com>

- **Description**：如果 CONFIG_PM 已启用，则存在此文件。读取时，它返回 USB 设备连接到计算机的总时间（以毫秒为单位）。此文件为只读。用户：PowerTOP <powertop@lists.01.org>，https://01.org/powertop/

**关于 /sys/bus/usb/device/.../power/active_duration：**

- **What**：/sys/bus/usb/device/.../power/active_duration

- **Date**：January 2008

- **KernelVersion**：2.6.25

- **Contact**：Sarah Sharp <sarah.a.sharp@intel.com>

- **Description**：如果 CONFIG_PM 已启用，则存在此文件。读取时，它返回 USB 设备处于活动状态（即未处于挂起状态）的总时间（以毫秒为单位）。此文件为只读。工具可以使用此文件和 connected_duration 文件来计算设备处于活动状态的时间百分比。例如，echo $((100 * `cat active_duration` / `cat connected_duration`)) 将给出一个整数百分比。请注意，这不会考虑计数器溢出。用户：PowerTOP <powertop@lists.01.org>，https://01.org/powertop/

**关于 /sys/bus/usb/devices/<busnum>-<port[.port]>...:<config num>-<interface num>/supports_autosuspend：**

- **What**：/sys/bus/usb/devices/<busnum>-<port[.port]>...:<config num>-<interface num>/supports_autosuspend

- **Date**：January 2008

- **KernelVersion**：2.6.27

- **Contact**：Sarah Sharp <sarah.a.sharp@intel.com>

- **Description**：读取时，如果此接口的接口驱动程序支持自动挂起，则此文件返回 1。如果没有驱动程序声明此接口，它也将返回 1，因为未声明的接口在所有其他接口驱动程序空闲时不会阻止设备被自动挂起。如果尚未向驱动程序添加自动挂起支持，该文件将返回 0。用户：USB PM 工具，git://git.moblin.org/users/sarah/usb-pm-tool/

**关于 /sys/bus/usb/devices/.../avoid_reset_quirk：**

- **What**：/sys/bus/usb/devices/.../avoid_reset_quirk

- **Date**：December 2009

- **Contact**：Oliver Neukum <oliver@neukum.org>

- **Description**：向此文件写入 1 告诉内核，此设备在重置时将转换为另一种模式。驱动程序将不会将重置用于此类设备的错误处理。用户：usb_modeswitch

**关于 /sys/bus/usb/devices/.../devnum：**

- **KernelVersion**：since at least 2.6.18

- **Description**：USB 总线上的设备地址。用户：libusb

**关于 /sys/bus/usb/devices/.../bConfigurationValue：**

- **KernelVersion**：since at least 2.6.18

- **Description**：设备的*活动*配置的 bConfigurationValue。将 0 或 -1 写入 bConfigurationValue 将重置活动配置（取消配置设备）。写入其他值将更改活动配置。请注意，一些设备违反 USB 规范，具有值等于 0 的配置。将 0 写入这些设备的 bConfigurationValue 将安装该配置，而不是取消配置设备。写入 -1 将始终取消配置设备。用户：libusb

**关于 /sys/bus/usb/devices/.../busnum：**

- **KernelVersion**：2.6.22

- **Description**：设备连接到的 USB 总线的总线编号。用户：libusb

**关于 /sys/bus/usb/devices/.../descriptors：**

- **KernelVersion**：2.6.26

- **Description**：包含设备缓存描述符的二进制文件。二进制数据由设备描述符后跟设备每个配置的描述符组成。请注意，config 描述符的 wTotalLength 不可信，因为设备可能具有比其宣传的更小的 config 描述符。每个（子）描述符的 bLength 字段可以信任，并且可以用于一次向前查找一个（子）描述符，直到找到下一个 config 描述符。从该文件读取的所有描述符都采用总线端序格式。用户：libusb

**关于 /sys/bus/usb/devices/.../speed：**

- **KernelVersion**：since at least 2.6.18

- **Description**：设备连接到 usb-host 的速度，以 Mbit/秒为单位。即 1.5 / 12 / 480 / 5000 之一。用户：libusb