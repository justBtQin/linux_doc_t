**/sys/class/iommu/<iommu>/intel-iommu/address**

日期：June 2014

内核版本：3.17

联系人：Alex Williamson <alex.williamson@redhat.com>

描述：

此 IOMMU 的 VT-d DRHD 的物理地址。格式：%llx。这允许将 sysfs 中的 intel-iommu 与 DMAR DRHD 表项相关联。

**/sys/class/iommu/<iommu>/intel-iommu/cap**

日期：June 2014

内核版本：3.17

联系人：Alex Williamson <alex.williamson@redhat.com>

描述：

此 DRHD 单元的缓存硬件能力寄存器值。格式：%llx。

**/sys/class/iommu/<iommu>/intel-iommu/ecap**

日期：June 2014

内核版本：3.17

联系人：Alex Williamson <alex.williamson@redhat.com>

描述：

此 DRHD 单元的缓存硬件扩展能力寄存器值。格式：%llx。

**/sys/class/iommu/<iommu>/intel-iommu/version**

日期：June 2014

内核版本：3.17

联系人：Alex Williamson <alex.williamson@redhat.com>

描述：

从 VT-d VER_REG 报告的架构版本。格式：%d:%d，主版本：次版本