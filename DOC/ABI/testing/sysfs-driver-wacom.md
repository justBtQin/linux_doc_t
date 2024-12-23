**关于 /sys/bus/hid/devices/<bus>:<vid>:<pid>.<n>/speed**

- **日期**：2010 年 4 月

- **内核版本**：2.6.35

- **联系**：linux-bluetooth@vger.kernel.org

- **描述**：/sys/bus/hid/devices/<bus>:<vid>:<pid>.<n>/speed 文件控制着 Wacom 蓝牙平板电脑的报告速度。从该文件读取，如果平板电脑以高速模式报告则返回 1，否则返回 0。向该文件写入这些值之一可切换报告速度。

**关于 /sys/bus/hid/devices/<bus>:<vid>:<pid>.<n>/wacom_led/led**

- **日期**：2014 年 8 月

- **联系**：linux-input@vger.kernel.org

- **描述**：用于控制状态指示灯和 OLED 的属性组。此属性组仅适用于 Intuos 4 M、L 和 XL（带指示灯和 OLED）、Intuos 4 WL、Intuos 5（仅指示灯）、Intuos Pro（仅指示灯）以及 Cintiq 21UX2 和 Cintiq 24HD（仅指示灯）。因此，其存在隐含地表示平板电脑设备上存在所述的指示灯和 OLED。

**关于 /sys/bus/hid/devices/<bus>:<vid>:<pid>.<n>/wacom_led/status0_luminance**

- **日期**：2014 年 8 月

- **联系**：linux-input@vger.kernel.org

- **描述**：向此文件写入可设置在 stylus 未触摸平板电脑表面且 stylus 上未按下任何按钮时的状态 LED 亮度（1..127）。此亮度级别通常低于按下按钮时的级别。

**关于 /sys/bus/hid/devices/<bus>:<vid>:<pid>.<n>/wacom_led/status1_luminance**

- **日期**：2014 年 8 月

- **联系**：linux-input@vger.kernel.org

- **描述**：向此文件写入可设置在 stylus 触摸平板电脑表面或 stylus 上按下任何按钮时的状态 LED 亮度（1..127）。

**关于 /sys/bus/hid/devices/<bus>:<vid>:<pid>.<n>/wacom_led/status_led0_select**

- **日期**：2014 年 8 月

- **联系**：linux-input@vger.kernel.org

- **描述**：向此文件写入可设置四个（对于 Intuos 4 和 Intuos 5）或右侧四个（对于 Cintiq 21UX2 和 Cintiq 24HD）状态 LED 中哪个处于活动状态（0..3）。同一侧的其他三个 LED 始终处于非活动状态。

**关于 /sys/bus/hid/devices/<bus>:<vid>:<pid>.<n>/wacom_led/status_led1_select**

- **日期**：2014 年 8 月

- **联系**：linux-input@vger.kernel.org

- **描述**：向此文件写入可设置左侧四个（对于 Cintiq 21UX2 和 Cintiq 24HD）状态 LED 中哪个处于活动状态（0..3）。左侧的其他三个 LED 始终处于非活动状态。

**关于 /sys/bus/hid/devices/<bus>:<vid>:<pid>.<n>/wacom_led/buttons_luminance**

- **日期**：2014 年 8 月

- **联系**：linux-input@vger.kernel.org

- **描述**：向此文件写入可设置所有八个按钮 OLED 显示器的整体亮度级别（0..15）。

**关于 /sys/bus/hid/devices/<bus>:<vid>:<pid>.<n>/wacom_led/button<n>_rawimg**

- **日期**：2014 年 8 月

- **联系**：linux-input@vger.kernel.org

- **描述**：当以 Wacom Intuos 4 交错格式向该文件写入 1024 字节的原始图像时，该图像会出现在设备的按钮 N 上。该图像是一个 64x32 像素的 4 位灰度图像。1024 字节的二进制数据被分成 16 个 64 字节的块。每个 64 字节的块编码显示器上连续两行的图像数据。每个字节的低四位包含第一行，高四位包含第二行。当 Wacom Intuos 4 通过蓝牙连接时，图像必须包含 256 字节（64x32 像素 1 位颜色）。该格式也像在 USB 模式中一样进行了打乱，可以通过将 76543210 转换为 GECA6420 来总结。

    - HGFEDCBA

    - HFDB7531