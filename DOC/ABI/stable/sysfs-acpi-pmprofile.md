**What**: /sys/firmware/acpi/pm_profile

**Date**: 03-Nov-2011

**KernelVersion**: v3.2

**Contact**: linux-acpi@vger.kernel.org

**Description**:  ACPI pm_profile sysfs 接口将平台电源管理（和性能）需求预期作为 BIOS 提供的内容进行导出。整数值直接从 FADT ACPI 表中检索得到。

**Values**: 有关可能的值，请参阅 ACPI 规范：

- 5.2.9 固定 ACPI 描述表（FADT）中的“Field: Preferred_PM_Profile”。

- 目前这些值由规范定义：

    - 0 未指定

    - 1 桌面

    - 2 移动

    - 3 工作站

    - 4 企业服务器

    - 5 SOHO 服务器

    - 6 设备 PC

    - 7 性能服务器

    - >7 保留