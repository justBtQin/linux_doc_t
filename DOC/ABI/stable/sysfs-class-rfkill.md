`rfkill` - 射频（RF）连接器关闭开关支持

关于此子系统的详细信息，请查看`Documentation/rfkill.txt`。

对于此接口已弃用的`/sys/class/rfkill/*/state`和`/sys/class/rfkill/*/claim`旋钮，请查看`Documentation/ABI/obsolete/sysfs-class-rfkill`。

内容：

- `/sys/class/rfkill`

日期：2007 年 7 月 9 日

内核版本：v2.6.22

联系人：linux-wireless@vger.kernel.org

描述：`rfkill`类子系统文件夹。每个注册的`rfkill`驱动程序由一个`rfkillX`子文件夹表示（X 为大于 0 的整数）。

- `/sys/class/rfkill/rfkill[0-9]+/name`

日期：2007 年 7 月 9 日

内核版本：v2.6.22

联系人：linux-wireless@vger.kernel.org

描述：驱动程序为此键分配的名称（接口或驱动程序名称）。值：任意字符串。

- `/sys/class/rfkill/rfkill[0-9]+/type`

日期：2007 年 7 月 9 日

内核版本：v2.6.22

联系人：linux-wireless@vger.kernel.org

描述：驱动程序类型字符串（"wlan"，"bluetooth"等）。值：请参阅`include/linux/rfkill.h`。

- `/sys/class/rfkill/rfkill[0-9]+/persistent`

日期：2007 年 7 月 9 日

内核版本：v2.6.22

联系人：linux-wireless@vger.kernel.org

描述：软阻止状态是否在启动时从非易失性存储初始化。值：一个数字值。0：false，1：true。

- `/sys/class/rfkill/rfkill[0-9]+/hard`

日期：2010 年 3 月 12 日

内核版本：v2.6.34

联系人：linux-wireless@vger.kernel.org

描述：当前硬阻止状态。此文件为只读。值：一个数字值。0：非活动，发射器（可能）处于活动状态。1：活动，发射器被驱动程序控制之外的某些东西强制关闭。

- `/sys/class/rfkill/rfkill[0-9]+/soft`

日期：2010 年 3 月 12 日

内核版本：v2.6.34

联系人：linux-wireless@vger.kernel.org

描述：当前软阻止状态。此文件为可读可写。值：一个数字值。0：非活动，发射器（可能）处于活动状态。1：活动，发射器被软件关闭。