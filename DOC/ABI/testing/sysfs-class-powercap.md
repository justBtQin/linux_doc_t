什么：/sys/class/powercap/

日期：2013 年 9 月

内核版本：3.13

联系人：linux-pm@vger.kernel.org

描述：

    /sys/class/powercap/ 类子目录属于 power cap 子系统。有关详细信息，请参考 Documentation/power/powercap.txt。

什么：/sys/class/powercap/<控制类型>/

日期：2013 年 9 月

内核版本：3.13

联系人：linux-pm@vger.kernel.org

描述：

    <控制类型> 是 /sys/class/powercap 下的唯一名称。这里的 <控制类型> 决定了如何控制电源。一个 <控制类型> 可以包含多个电源区域。

什么：/sys/class/powercap/<控制类型>/<电源区域>/

日期：2013 年 9 月

内核版本：3.13

联系人：linux-pm@vger.kernel.org

描述：

    电源区域是一个或一组设备，可独立进行监控和控制。电源区域的 sysfs 条目以 <控制类型> 的名称进行限定。例如，intel-rapl:0:1:1。

什么：/sys/class/powercap/<控制类型>/<电源区域>/<子电源区域>

日期：2013 年 9 月

内核版本：3.13

联系人：linux-pm@vger.kernel.org

描述：

    指定此电源区域的名称。

什么：/sys/class/powercap/<控制类型>/<电源区域>/energy_uj

日期：2013 年 9 月

内核版本：3.13

联系人：linux-pm@vger.kernel.org

描述：

    当前的能量计数器，以微焦耳为单位。写入“0”可重置。如果计数器无法重置，则此属性为只读。

什么：/sys/class/powercap/<控制类型>/<电源区域>/max_energy_range_uj

日期：2013 年 9 月

内核版本：3.13

联系人：linux-pm@vger.kernel.org

描述：

    上述能量计数器的范围，以微焦耳为单位。

什么：/sys/class/powercap/<控制类型>/<电源区域>/max_power_range_uw

日期：2013 年 9 月

内核版本：3.13

联系人：linux-pm@vger.kernel.org

描述：

    此约束的最大允许功率，以微瓦特为单位。这里的“X”可以取 0 到最大整数的值。

什么：/sys/class/powercap/<控制类型>/<电源区域>/constraint_X_min_power_uw

日期：2013 年 9 月

内核版本：3.13

联系人：linux-pm@vger.kernel.org

描述：

    此约束的最小允许功率，以微瓦特为单位。这里的“X”可以取 0 到最大整数的值。