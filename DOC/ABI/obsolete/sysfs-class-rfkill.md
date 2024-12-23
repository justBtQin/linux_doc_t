`rfkill - 射频（RF）连接器关闭开关支持`

关于此子系统的详细信息，请查看 Documentation/rfkill.txt。

**什么：** `/sys/class/rfkill/rfkill[0-9]+/state`

**日期：** 09-Jul-2007

**内核版本：** v2.6.22

**联系人：** linux-wireless@vger.kernel.org

**描述：** 发射器的当前状态。此文件已弃用，计划在 2014 年删除，

因为无法表达射频关闭驱动程序的“软阻塞和硬阻塞”状态。值：

一个数字值。

```
0：RFKILL_STATE_SOFT_BLOCKED
	发射器被软件关闭

1：RFKILL_STATE_UNBLOCKED
	发射器（潜在地）处于活动状态

2：RFKILL_STATE_HARD_BLOCKED
	发射器被驱动程序控制之外的某些东西强制关闭。
```

**什么：** `/sys/class/rfkill/rfkill[0-9]+/claim`

**日期：** 09-Jul-2007

**内核版本：** v2.6.22

**联系人：** linux-wireless@vger.kernel.org

**描述：** 此文件已弃用，因为不再有一种方法可以仅对单个射频关闭实例进行控制。此文件计划在 2012 年删除。值：

0：内核处理事件