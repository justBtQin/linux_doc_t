# ACPI 扫描处理程序

版权所有 (C) 2012，英特尔公司

作者：拉斐尔·J·怀索基 <rafael.j.wysocki@intel.com>

在系统初始化和基于 ACPI 的设备热插拔过程中，会扫描 ACPI 命名空间，以查找通常代表各种硬件部件的设备对象。这会导致为 ACPI 命名空间中的每个设备对象创建一个`struct acpi_device`对象，并将其注册到驱动程序核心中，这些`struct acpi_device`对象的层次结构反映了命名空间的布局（即命名空间中的父设备对象由父`struct acpi_device`对象表示，其子设备对象也类似）。在接下来的内容中，这些`struct acpi_device`对象被称为“设备节点”，但不应与设备树解析代码使用的`struct device_node`对象混淆（尽管它们的作用类似于这些对象的作用）。在基于 ACPI 的设备热移除过程中，代表要移除的硬件部件的设备节点会被注销和删除。

驱动程序/acpi/scan.c 中的核心 ACPI 命名空间扫描代码对设备节点进行基本初始化，例如从它们所表示的设备对象中检索常见的配置信息，并为它们填充适当的数据，但其中一些设备节点在注册后需要额外的处理。例如，如果给定的设备节点表示一个 PCI 主机桥，其注册应导致该桥下面的 PCI 总线被枚举，并且该总线上的 PCI 设备被注册到驱动程序核心。同样，如果设备节点表示一个 PCI 中断链接，就必须配置该链接，以便内核可以使用它。

这些额外的配置任务通常取决于给定设备节点所代表的硬件组件的类型，这可以根据设备节点的硬件 ID（HID）来确定。它们由称为 ACPI 扫描处理程序的对象执行，由以下结构表示：

```
struct acpi_scan_handler {
	const struct acpi_device_id *ids;
	struct list_head list_node;
	int (*attach)(struct acpi_device *dev, const struct acpi_device_id *id);
	void (*detach)(struct acpi_device *dev);
};
```

其中`ids`是给定处理程序应该处理的设备节点的 ID 列表，`list_node`是 ACPI 核心维护的全局 ACPI 扫描处理程序列表的钩子，`.attach()`和`.detach()`回调分别在新设备节点注册后和处理程序先前附加的设备节点注销前执行。

命名空间扫描函数`acpi_bus_scan()`首先将给定命名空间范围内的所有设备节点注册到驱动程序核心。然后，它尝试使用可用扫描处理程序的`ids`数组将每个设备节点与一个扫描处理程序匹配。如果找到匹配的扫描处理程序，将为给定的设备节点执行其`.attach()`回调。如果该回调返回 1，这意味着处理程序已声明该设备节点，并现在负责执行与其相关的任何额外配置任务。在这种情况下，它还将负责为设备节点的注销做准备。然后，设备节点的`handler`字段将填充已声明它的扫描处理程序的地址。

如果`.attach()`回调返回 0，这意味着给定的扫描处理程序对该设备节点不感兴趣，可以与列表中的下一个扫描处理程序匹配。如果它返回一个（负）错误代码，这意味着由于严重错误，命名空间扫描应终止。返回的错误代码应反映错误的类型。

命名空间修剪函数`acpi_bus_trim()`首先执行给定命名空间范围内所有设备节点的扫描处理程序的`.detach()`回调（如果它们有扫描处理程序）。接下来，它注销该范围内的所有设备节点。

可以使用`acpi_scan_add_handler()`函数将 ACPI 扫描处理程序添加到 ACPI 核心维护的列表中，该函数将一个指向新扫描处理程序的指针作为参数。扫描处理程序添加到列表中的顺序是它们在命名空间扫描期间与设备节点匹配的顺序。

所有扫描处理程序都必须在首次运行`acpi_bus_scan()`之前添加到列表中，并且不能从列表中删除。