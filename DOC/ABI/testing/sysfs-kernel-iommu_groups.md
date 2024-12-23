**What**：`/sys/kernel/iommu_groups/`

**Date**：2012 年 5 月

**KernelVersion**：v3.5

**Contact**：Alex Williamson <alex.williamson@redhat.com>

**Description**：`/sys/kernel/iommu_groups/` 包含多个子目录，每个子目录代表一个 IOMMU 组。子目录的名称与该组的 `iommu_group_id()` 相匹配，这是一个整数值。在每个子目录内还有一个名为 "devices" 的目录，其中包含指向该组中包含的 sysfs 设备的链接。如果 IOMMU 驱动程序选择为该组注册一个更常见的名称，则组目录还可选地包含一个 "name" 文件。
