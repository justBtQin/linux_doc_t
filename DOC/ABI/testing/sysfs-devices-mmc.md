**What**：`/sys/devices/.../mmc_host/mmcX/mmcX:XXXX/enhanced_area_offset`

**Date**：2011 年 1 月

**Contact**：Chuanxiao Dong <chuanxiao.dong@intel.com>

**Description**：增强区域是 eMMC4.4 标准中定义的新功能。eMMC4.4 或更高版本的卡可以支持此功能。这种区域有助于提高卡的性能。如果启用了该功能，此属性将指示增强数据区域的起始地址。如果未启用，此属性将为 -EINVAL。单位：字节。格式：十进制。

**What**：`/sys/devices/.../mmc_host/mmcX/mmcX:XXXX/enhanced_area_size`

**Date**：2011 年 1 月

**Contact**：Chuanxiao Dong <chuanxiao.dong@intel.com>

**Description**：增强区域是 eMMC4.4 标准中定义的新功能。eMMC4.4 或更高版本的卡可以支持此功能。这种区域有助于提高卡的性能。如果启用了该功能，此属性将指示增强数据区域的大小。如果未启用，此属性将为 -EINVAL。单位：千字节。格式：十进制。 