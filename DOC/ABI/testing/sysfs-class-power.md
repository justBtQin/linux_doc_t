**关于电池监控芯片相关文件的说明**

- **/sys/class/power/ds2760-battery.*/charge_now**：

    - 日期：2010 年 5 月

    - 内核版本：2.6.35

    - 联系人：Daniel Mack <daniel@caiaq.de>

    - 描述：此文件可写，可用于设置电池监控芯片内的当前库仑计数器值。这对于不可避免的电池老化校正非常必要。用户空间守护进程可以监控电池充电逻辑，一旦计数器超出合理范围，就会采取适当的行动。

- **/sys/class/power_supply/max14577-charger/device/charge_full**：

    - 日期：2010 年 5 月

    - 内核版本：2.6.35

    - 联系人：Daniel Mack <daniel@caiaq.de>

    - 描述：此文件可写，可用于设置假定的电池“满电水平”。随着电池老化，此值需要随时间进行修正。

- **/sys/class/power_supply/max77693-charger/device/fast_charge_timer**：

    - 日期：2015 年 1 月

    - 内核版本：3.19.0

    - 联系人：Krzysztof Kozlowski <k.kozlowski@samsung.com>

    - 描述：此条目显示并设置 max77693 充电器在快速充电模式下的运行最大时间。当定时器到期时，设备将终止快速充电模式（充电电流将降至 0A）并触发中断。

    - 有效值：

        - 5 - 6 或 7（小时），按 2 向下取整；

        - 0：禁用。

- **/sys/class/power_supply/max77693-charger/device/top_off_threshold_current**：

    - 日期：2015 年 1 月

    - 内核版本：3.19.0

    - 联系人：Krzysztof Kozlowski <k.kozlowski@samsung.com>

    - 描述：此条目显示并设置 max77693 充电器在过充模式下的充电电流阈值。当快速充电模式下的充电电流降至该值以下时，充电器将触发中断并开始过充模式。

    - 有效值：

        - 100000 - 200000（微安），按 25000 向下取整；

        - 200000 - 350000（微安），按 50000 向下取整；

        - 0：禁用。