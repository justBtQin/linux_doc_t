**What**: `/sys/devices/*/<our-device>/fuse`

**Date**:  February 2014

**Contact**: Peter De Schrijver <pdeschrijver@nvidia.com>

**Description**: 对 NVIDIA 的 Tegra20、Tegra30、Tegra114 和 Tegra124 SoC 上的 efuses 的只读访问。这些 efuses 包含在工厂编程的一次写入数据。数据以 32 位字的形式以 LSB 优先格式布局。每个位代表从熔丝寄存器解码的单个值。位的顺序/分配与硬件寄存器完全匹配，包括任何未使用的位。

**Users**: 任何希望读取 Tegra SoC 上的 efuses 的用户空间应用程序。