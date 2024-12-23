**What**：`/sys/devices/platform/at91_can/net/<iface>/mb0_id`

**Date**：2011 年 1 月

**KernelVersion**：2.6.38

**Contact**：Marc Kleine-Budde <kernel@pengutronix.de>

**Description**：

- 表示邮箱 0 的 can_id 的值。

- 默认值：0x7ff（标准帧）

由于芯片缺陷（“AT91SAM9263 Preliminary 6249H-ATARM-27-Jul-09”中的勘误 50.2.6.3 和 50.3.5.3），在某些条件下，邮箱 0 的内容可能会被发送（即使已禁用或处于接收模式）。勘误中的解决方法建议不要使用该邮箱，并为其加载一个未使用的标识符。为了使用扩展的 can_id，将 CAN_EFF_FLAG（0x80000000U）添加到 can_id 中。例如：

- 标准 id 0x7ff：

```
echo 0x7ff      > /sys/class/net/can0/mb0_id
```

- 扩展 id 0x1fffffff：

```
echo 0x9fffffff > /sys/class/net/can0/mb0_id
``` 