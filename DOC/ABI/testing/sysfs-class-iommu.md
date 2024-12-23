**What: /sys/class/iommu/<iommu>/devices/**

**Date: June 2014**

**KernelVersion: 3.17**

**Contact: Alex Williamson <alex.williamson@redhat.com>**

**Description: IOMMU 驱动程序能够将由给定 IOMMU 管理的设备在此处进行链接，以允许 IOMMU 与设备关联。**

**What: /sys/devices/.../iommu**

**Date: June 2014**

**KernelVersion: 3.17**

**Contact: Alex Williamson <alex.williamson@redhat.com>**

**Description: IOMMU 驱动程序能够将给定设备的 IOMMU 在此处进行链接，以允许设备与 IOMMU 关联。**