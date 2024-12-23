# 注意：本文件记录了任何设备在 Documentation/sysfs-rules.txt 中未记录的其他属性

内容：

 - /sys/devices/*/of_path

 - 日期：2015 年 2 月

 - 联系人：设备树邮件列表 <devicetree@vger.kernel.org>

 - 描述：

    - 任何与设备树节点相关联的设备都将有一个 of_path 符号链接，指向 /sys/firmware/devicetree/ 中的相应设备节点。