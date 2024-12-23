**/sys/bus/pci/drivers/.../bind**

- **What**：将设备位置写入此文件将导致驱动程序尝试绑定到在此位置找到的设备。这对于覆盖默认绑定很有用。位置格式为：DDDD:BB:DD.F，即域：总线：设备.功能，与 /sys/bus/pci/devices/ 中的相同。例如：# echo 0000:00:19.0 > /sys/bus/pci/drivers/foo/bind（注意：2.6.28 之前的内核可能需要 echo -n）。

- **Date**：December 2003

- **Contact**：linux-pci@vger.kernel.org

- **Description**：无

**/sys/bus/pci/drivers/.../unbind**

- **What**：将设备位置写入此文件将导致驱动程序尝试从在此位置找到的设备解除绑定。在覆盖默认绑定时可能很有用。位置格式为：DDDD:BB:DD.F，即域：总线：设备.功能，与 /sys/bus/pci/devices/ 中的相同。例如：# echo 0000:00:19.0 > /sys/bus/pci/drivers/foo/unbind（注意：2.6.28 之前的内核可能需要 echo -n）。

- **Date**：December 2003

- **Contact**：linux-pci@vger.kernel.org

- **Description**：无

**/sys/bus/pci/drivers/.../new_id**

- **What**：将设备 ID 写入此文件将尝试动态地将新的设备 ID 添加到 PCI 设备驱动程序。这可能允许驱动程序在编译时支持比静态设备 ID 支持表中更多的硬件。设备 ID 的格式为：VVVV DDDD SVVV SDDD CCCC MMMM，即供应商 ID、设备 ID、子系统供应商 ID、子系统设备 ID、类、类掩码和私有驱动程序数据。供应商 ID 和设备 ID 字段是必需的，其余是可选的。成功添加 ID 后，驱动程序将探测设备并尝试绑定到它。例如：# echo "8086 10f5" > /sys/bus/pci/drivers/foo/new_id

- **Date**：December 2003

- **Contact**：linux-pci@vger.kernel.org

- **Description**：无

**/sys/bus/pci/drivers/.../remove_id**

- **What**：将设备 ID 写入此文件将删除通过 new_id sysfs 条目动态添加的 ID。设备 ID 的格式为：VVVV DDDD SVVV SDDD CCCC MMMM，即供应商 ID、设备 ID、子系统供应商 ID、子系统设备 ID、类和类掩码。供应商 ID 和设备 ID 字段是必需的，其余是可选的。成功删除 ID 后，驱动程序将不再支持该设备。这对于确保自动探测不会将驱动程序与设备匹配很有用。例如：# echo "8086 10f5" > /sys/bus/pci/drivers/foo/remove_id

- **Date**：February 2009

- **Contact**：Chris Wright <chrisw@sous-sol.org>

- **Description**：无

**/sys/bus/pci/rescan**

- **What**：向此属性写入非零值将强制对系统中的所有 PCI 总线进行重新扫描，并重新发现之前移除的设备。

- **Date**：January 2009

- **Contact**：Linux PCI developers <linux-pci@vger.kernel.org>

- **Description**：无

**/sys/bus/pci/devices/.../msi_bus**

- **What**：向此属性写入零值将禁止该设备的任何未来驱动程序的 MSI 和 MSI-X。如果该设备是桥接器，则将禁止该桥接器下所有子设备的未来驱动程序的 MSI 和 MSI-X。驱动程序必须重新加载才能使新设置生效。

- **Date**：September 2014

- **Contact**：Linux PCI developers <linux-pci@vger.kernel.org>

- **Description**：无

**/sys/bus/pci/devices/.../msi_irqs/**

- **What**：/sys/devices/.../msi_irqs 目录包含一组可变的文件，每个文件都以分配给该设备的相应 msi irq 向量命名。

- **Date**：September, 2011

- **Contact**：Neil Horman <nhorman@tuxdriver.com>

- **Description**：无

**/sys/bus/pci/devices/.../msi_irqs/<N>**

- **What**：此属性指示由文件命名的 irq 向量的模式（msi 与 msix）。

- **Date**：September 2011

- **Contact**：Neil Horman <nhorman@tuxdriver.com>

- **Description**：无

**/sys/bus/pci/devices/.../remove**

- **What**：向此属性写入非零值将热移除 PCI 设备及其所有子设备。

- **Date**：January 2009

- **Contact**：Linux PCI developers <linux-pci@vger.kernel.org>

- **Description**：无

**/sys/bus/pci/devices/.../pci_bus/.../rescan**

- **What**：向此属性写入非零值将强制对总线及其所有子总线进行重新扫描，并重新发现之前从设备树的此部分移除的设备。

- **Date**：May 2011

- **Contact**：Linux PCI developers <linux-pci@vger.kernel.org>

- **Description**：无

**/sys/bus/pci/devices/.../rescan**

- **What**：向此属性写入非零值将强制对设备的父总线及其所有子总线进行重新扫描，并重新发现之前从设备树的此部分移除的设备。

- **Date**：January 2009

- **Contact**：Linux PCI developers <linux-pci@vger.kernel.org>

- **Description**：无

**/sys/bus/pci/devices/.../reset**

- **What**：某些设备允许单独的功能被重置，而不会影响同一设备中的其他功能。对于具有此支持的设备，sysfs 中将存在一个名为 reset 的文件。向此文件写入 1 将执行重置。

- **Date**：July 2009

- **Contact**：Michael S. Tsirkin <mst@redhat.com>

- **Description**：无

**/sys/bus/pci/devices/.../vpd**

- **What**：设备目录中的 vpd 文件将是一个二进制文件，包含设备的重要产品数据。它应遵循 PCI 规范 2.1 或 2.2 中定义的 VPD 格式，但用户应考虑某些设备可能具有格式错误的数据。如果基础 VPD 有可写部分，则此文件的相应部分将是可写的。

- **Date**：February 2008

- **Contact**：Ben Hutchings <bwh@kernel.org>

- **Description**：无

**/sys/bus/pci/devices/.../virtfnN**

- **What**：当硬件支持 SR-IOV 功能并且物理功能驱动程序已启用它时，此符号链接将出现。符号链接指向索引为 N（0...MaxVFs-1）的虚拟功能的 PCI 设备 sysfs 条目。

- **Date**：March 2009

- **Contact**：Yu Zhao <yu.zhao@intel.com>

- **Description**：无

**/sys/bus/pci/devices/.../dep_link**

- **What**：当硬件支持 SR-IOV 功能并且物理功能驱动程序已启用它，并且此设备与其他设备有供应商特定的依赖关系时，此符号链接将出现。符号链接指向此设备所依赖的物理功能的 PCI 设备 sysfs 条目。

- **Date**：March 2009

- **Contact**：Yu Zhao <yu.zhao@intel.com>

- **Description**：无

**/sys/bus/pci/devices/.../physfn**

- **What**：当设备是虚拟功能时，此符号链接将出现。符号链接指向与此设备关联的物理功能的 PCI 设备 sysfs 条目。

- **Date**：March 2009

- **Contact**：Yu Zhao <yu.zhao@intel.com>

- **Description**：无

**/sys/bus/pci/slots/.../module**

- **What**：此符号链接指向管理热插拔插槽的 PCI 热插拔控制器驱动程序模块。

- **Date**：June 2009

- **Contact**：linux-pci@vger.kernel.org

- **Description**：无

**/sys/bus/pci/devices/.../label**

- **What**：读取此属性将提供设备的固件给定名称（SMBIOS 类型 41 字符串或 ACPI _DSM 字符串）。仅当固件为 PCI 设备提供了名称时，才会创建此属性。如果系统固件同时提供 SMBIOS 类型 41 字符串，则将优先使用 ACPI _DSM 字符串名称。用户：对了解 PCI 设备的固件分配名称感兴趣的用户空间应用程序。

- **Date**：July 2010

- **Contact**：Narendra K <narendra_k@dell.com>, linux-bugs@dell.com

- **Description**：无

**/sys/bus/pci/devices/.../index**

- **What**：读取此属性将提供设备的固件给定实例（SMBIOS 类型 41 设备类型实例）。仅当固件为 PCI 设备提供了实例编号时，才会创建此属性。用户：对了解 PCI 设备的固件分配设备类型实例感兴趣的用户空间应用程序，这有助于理解固件中 PCI 设备的预期顺序。

- **Date**：July 2010

- **Contact**：Narendra K <narendra_k@dell.com>, linux-bugs@dell.com

- **Description**：无**/sys/bus/pci/devices/.../acpi_index**

- **What**：获取 PCI 设备的固件给定实例（ACPI _DSM 实例编号）。

- **Date**：2010 年 7 月

- **Contact**：Narendra K <narendra_k@dell.com>，linux-bugs@dell.com

- **Description**：读取此属性将提供 PCI 设备的固件给定实例。仅当固件为 PCI 设备提供了实例编号时，才会创建该属性。如果系统固件也提供了 SMBIOS 类型 41 设备类型实例，则 ACPI _DSM 实例编号将具有优先级。

- **Users**：对了解 PCI 设备的固件分配实例编号感兴趣的用户空间应用程序，这有助于理解 PCI 设备的固件预期顺序。

**/sys/bus/pci/devices/.../d3cold_allowed**

- **What**：用于控制相应 PCI 设备是否可以进入 D3Cold 状态的位。

- **Date**：2012 年 7 月

- **Contact**：Huang Ying <ying.huang@intel.com>

- **Description**：d3cold_allowed 位用于控制相应的 PCI 设备是否可以进入 D3Cold 状态。如果清除，设备将永远不会进入 D3Cold 状态。如果设置，并且其他要求也满足，则设备可能会进入 D3Cold 状态。读取此属性将显示 d3cold_allowed 位的当前值。写入此属性将设置 d3cold_allowed 位的值。

**/sys/bus/pci/devices/.../sriov_totalvfs**

- **What**：当物理 PCIe 设备支持 SR-IOV 时出现的文件。

- **Date**：2012 年 11 月

- **Contact**：Donald Dutile <ddutile@redhat.com>

- **Description**：此文件在物理 PCIe 设备支持 SR-IOV 时出现。用户空间应用程序可以读取此文件以确定 PCIe 物理功能（PF）可以支持的最大虚拟函数（VFs）数量。通常，这是 PF 的 SR-IOV 扩展功能结构的 TotalVFs 元素中报告的值。驱动程序在探测时可以通过 pci_sriov_set_totalvfs()函数减少从此文件读取的值。

**/sys/bus/pci/devices/.../sriov_numvfs**

- **What**：当物理 PCIe 设备支持 SR-IOV 时出现的文件。

- **Date**：2012 年 11 月

- **Contact**：Donald Dutile <ddutile@redhat.com>

- **Description**：此文件在物理 PCIe 设备支持 SR-IOV 时出现。用户空间应用程序可以读取和写入此文件以确定和控制物理功能（PF）上虚拟函数（VFs）的启用或禁用。读取此文件将返回此 PF 上启用的 VFs 数量。写入此文件将启用指定数量的 VFs。用户空间应用程序通常会读取该文件并检查值是否为零，然后写入应在 PF 上启用的 VFs 数量；写入的值应小于或等于 sriov_totalvfs 文件中的值。用户空间应用程序想要禁用 VFs 时，将向此文件写入零。内核确保向此文件写入有效值，并在值无效时返回错误。例如，当 sriov_numvfs 不是 0 且不是 2 时，写入 2 到该文件将返回错误。写入 10 而 sriov_totalvfs 的值为 8 将返回错误。

**/sys/bus/pci/devices/.../driver_override**

- **What**：允许指定设备的驱动程序，以覆盖标准的静态和动态 ID 匹配。

- **Date**：2014 年 4 月

- **Contact**：Alex Williamson <alex.williamson@redhat.com>

- **Description**：此文件允许指定设备的驱动程序，以覆盖标准的静态和动态 ID 匹配。指定后，只有名称与写入 driver_override 的值匹配的驱动程序才有机会绑定到设备。通过向 driver_override 文件写入字符串（例如 echo pci-stub > driver_override）来指定覆盖，并且可以使用空字符串（echo > driver_override）清除覆盖。这将使设备返回到标准的匹配规则绑定。写入 driver_override 不会自动解除设备与当前驱动程序的绑定，也不会尝试自动加载指定的驱动程序。如果当前内核中没有匹配名称的驱动程序加载，则设备将不会绑定到任何驱动程序。这也允许设备使用 driver_override 名称（如“none”）退出驱动程序绑定。覆盖中只能指定一个驱动程序，不支持解析分隔符。

**/sys/bus/pci/devices/.../numa_node**

- **What**：包含 PCI 设备附加的 NUMA 节点，或如果节点未知则为 -1。

- **Date**：2014 年 10 月

- **Contact**：Prarit Bhargava <prarit@redhat.com>

- **Description**：此文件包含 PCI 设备附加的 NUMA 节点，或如果节点未知则为 -1。初始值来自 ACPI _PXM 方法或类似的固件源。如果该值缺失或不正确，可以写入此文件来覆盖节点。在这种情况下，请向系统供应商报告固件错误。写入此文件会使内核带有 TAINT_FIRMWARE_WORKAROUND 污点，这会降低系统的可支持性。