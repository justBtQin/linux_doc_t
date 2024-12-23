**what：** `/sys/class/backlight/<backlight>/<ambient light zone>_max`

**what：** `/sys/class/backlight/<backlight>/l1_daylight_max`

**what：** `/sys/class/backlight/<backlight>/l2_bright_max`

**what：** `/sys/class/backlight/<backlight>/l3_office_max`

**what：** `/sys/class/backlight/<backlight>/l4_indoor_max`

**what：** `/sys/class/backlight/<backlight>/l5_dark_max`

**日期：** 2011 年 5 月

**内核版本：** 3.0

**联系人：** device-drivers-devel@blackfin.uclinux.org

**描述：** 控制此<背光>上<环境光区域>的最大亮度。值介于 0 到 127 之间。此文件还将显示为此<环境光区域>存储的亮度级别。

**what：** `/sys/class/backlight/<backlight>/<ambient light zone>_dim`

**what：** `/sys/class/backlight/<backlight>/l2_bright_dim`

**what：** `/sys/class/backlight/<backlight>/l3_office_dim`

**what：** `/sys/class/backlight/<backlight>/l4_indoor_dim`

**what：** `/sys/class/backlight/<backlight>/l5_dark_dim`

**日期：** 2011 年 5 月

**内核版本：** 3.0

**联系人：** device-drivers-devel@blackfin.uclinux.org

**描述：** 控制此<背光>上<环境光区域>的暗淡亮度。值介于 0 到 127 之间，通常设置为 0。当背光被禁用时完全关闭。此文件还将显示为此<环境光区域>存储的暗淡亮度级别。

**what：** `/sys/class/backlight/<backlight>/ambient_light_level`

**日期：** 2011 年 5 月

**内核版本：** 3.0

**联系人：** device-drivers-devel@blackfin.uclinux.org

**描述：** 获取光传感器的转换值。此值每 80 毫秒更新一次（当光传感器启用时）。返回介于 0（暗）和 8000（最大环境光亮度）之间的整数。

**what：** `/sys/class/backlight/<backlight>/ambient_light_zone`

**日期：** 2011 年 5 月

**内核版本：** 3.0

**联系人：** device-drivers-devel@blackfin.uclinux.org

**描述：** 获取/设置当前环境光区域。读取返回介于 1 到 5 之间的整数（1 = 日光，2 = 明亮，...，5 = 暗）。写入介于 1 到 5 之间的值将迫使背光控制器进入相应的环境光区域。写入 0 将返回正常/自动环境光级别操作。这些设备上的环境光感应功能是 Documentation/ABI/stable/sysfs-class-backlight 中记录的 API 的扩展。可以通过将存储在 /sys/class/backlight/<backlight>/max_brightness 中的值写入 /sys/class/backlight/<backlight>/brightness 来启用它。