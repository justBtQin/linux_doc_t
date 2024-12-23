**What**: /sys/firmware/ibft/initiator

**Date**: 2007 年 11 月

**Contact**: Konrad Rzeszutek <ketuzsezr@darnok.org>

**Description**: /sys/firmware/ibft/initiator 目录将包含暴露 iSCSI 启动固件表发起者数据的文件。通常，这包含发起者名称。

**What**: /sys/firmware/ibft/targetX

**Date**: 2007 年 11 月

**Contact**: Konrad Rzeszutek <ketuzsezr@darnok.org>

**Description**: /sys/firmware/ibft/targetX 目录将包含暴露 iSCSI 启动固件表目标数据的文件。通常，这包含目标的 IP 地址、引导 LUN、目标名称以及它与之关联的 NIC。它还可以包含 CHAP 名称（和密码）、反向 CHAP 名称（和密码）。

**What**: /sys/firmware/ibft/ethernetX

**Date**: 2007 年 11 月

**Contact**: Konrad Rzeszutek <ketuzsezr@darnok.org>

**Description**: /sys/firmware/ibft/ethernetX 目录将包含暴露 iSCSI 启动固件表 NIC 数据的文件。通常，这包含 NIC 的 IP 地址、MAC 和网关。