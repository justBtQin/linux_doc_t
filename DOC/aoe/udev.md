```
# 这些规则告诉 udev 为 aoe 支持创建哪些设备节点。
# 它们可以按照以下方式安装。请查看第 8 节 udev 手册页，以确定您的 udev 是否支持 SUBSYSTEM，
# 以及它对 SUBSYSTEM 和 KERNEL 使用一个还是两个等号。

# `ecashin@makki ~$ su`
# `Password:`
# `bash# find /etc -type f -name udev.conf`
# `/etc/udev/udev.conf`
# `bash# grep udev_rules= /etc/udev/udev.conf`
# `udev_rules="/etc/udev/rules.d/"`
# `bash# ls /etc/udev/rules.d/`
# `10-wacom.rules  50-udev.rules`
# `bash# cp /path/to/linux-2.6.xx/Documentation/aoe/udev.txt \
#           /etc/udev/rules.d/60-aoe.rules`
```

# aoe 字符设备

```
SUBSYSTEM=="aoe", KERNEL=="discover", NAME="etherd/%k", GROUP="disk", MODE="0220"
SUBSYSTEM=="aoe", KERNEL=="err", NAME="etherd/%k", GROUP="disk", MODE="0440"
SUBSYSTEM=="aoe", KERNEL=="interfaces", NAME="etherd/%k", GROUP="disk", MODE="0220"
SUBSYSTEM=="aoe", KERNEL=="revalidate", NAME="etherd/%k", GROUP="disk", MODE="0220"
SUBSYSTEM=="aoe", KERNEL=="flush", NAME="etherd/%k", GROUP="disk", MODE="0220"
```

# aoe 块设备

```
KERNEL=="etherd*", GROUP="disk"
```