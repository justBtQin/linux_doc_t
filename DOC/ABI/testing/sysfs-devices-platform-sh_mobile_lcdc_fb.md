**What**: 

- `/sys/devices/platform/sh_mobile_lcdc_fb.[0-3]/graphics/fb[0-9]/ovl_alpha`

- `/sys/devices/platform/sh_mobile_lcdc_fb.[0-3]/graphics/fb[0-9]/ovl_mode`

- `/sys/devices/platform/sh_mobile_lcdc_fb.[0-3]/graphics/fb[0-9]/ovl_position`

- `/sys/devices/platform/sh_mobile_lcdc_fb.[0-3]/graphics/fb[0-9]/ovl_rop3`

**Date**: May 2012

**Contact**: Laurent Pinchart <laurent.pinchart@ideasonboard.com>

**Description**: 

- 对于对应于覆盖层平面的`fb[0-9]`设备，此文件可用。

- 存储覆盖层的 alpha 混合值。值的范围从 0（透明）到 255（不透明）。如果模式未设置为 Alpha 混合，则该值将被忽略。

- 对于对应于覆盖层平面的`fb[0-9]`设备，此文件可用。

- 选择覆盖层的合成模式。可能的值有：

    - 0 - Alpha 混合

    - 1 - ROP3

- 对于对应于覆盖层平面的`fb[0-9]`设备，此文件可用。

- 存储覆盖层的光栅操作（ROP3）。值的范围从 0 到 255。如果模式未设置为 ROP3，则该值将被忽略。