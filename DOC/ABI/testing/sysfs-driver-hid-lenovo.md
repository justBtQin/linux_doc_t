**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/press_to_select`

**Date**: July 2011

**Contact**: linux-input@vger.kernel.org

**Description**: 此控制着如果指点杆被快速按下时是否应生成鼠标点击。此按下必须有多快由 press_speed 控制。值为 0 或 1。适用于带有指点杆的 Thinkpad USB 键盘。

**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/dragging`

**Date**: July 2011

**Contact**: linux-input@vger.kernel.org

**Description**: 如果此设置被启用，通过按下指点杆可以进行拖动。这需要 press_to_select 被启用。值为 0 或 1。适用于带有指点杆的 Thinkpad USB 键盘。

**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/release_to_select`

**Date**: July 2011

**Contact**: linux-input@vger.kernel.org

**Description**: 关于此设置的详细信息请参考 http://www.pc.ibm.com/ww/healthycomputing/trkpntb.html。值为 0 或 1。适用于带有指点杆的 Thinkpad USB 键盘。

**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/select_right`

**Date**: July 2011

**Contact**: linux-input@vger.kernel.org

**Description**: 此设置控制通过按下指点杆（如果 press_to_select 被启用）生成的鼠标点击事件是生成左键还是右键点击。值为 0 或 1。适用于带有指点杆的 Thinkpad USB 键盘。

**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/sensitivity`

**Date**: July 2011

**Contact**: linux-input@vger.kernel.org

**Description**: 此文件包含指点杆的灵敏度。值为从 1（最低灵敏度）到 255（最高灵敏度）的十进制整数。适用于带有指点杆的 Thinkpad USB 键盘。

**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/press_speed`

**Date**: July 2011

**Contact**: linux-input@vger.kernel.org

**Description**: 此设置控制如果 press_to_select 被启用，指点杆需要多快被按下才能生成鼠标点击。值为从 1（最慢）到 255（最快）的十进制整数。适用于带有指点杆的 Thinkpad USB 键盘。

**What**: `/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/fn_lock`

**Date**: July 2014

**Contact**: linux-input@vger.kernel.org

**Description**: 此设置控制键盘上的 Fn 锁是否被启用（即如果 F1 是静音或 F1）。值为 0 或 1。适用于带有指点杆的 ThinkPad Compact（USB|Bluetooth）键盘。