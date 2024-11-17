# Open Firmware 设备树单元测试
----------------------------------

作者：Gaurav Minocha <gaurav.minocha.os@gmail.com>

1. 介绍

本文档解释了执行 OF 单元测试所需的测试数据如何动态地附加到活动树，与机器的架构无关。

在继续之前，建议阅读以下文档。

[1] Documentation/devicetree/usage-model.txt
[2] http://www.devicetree.org/Device_Tree_Usage

OF 自测试旨在测试提供给设备驱动程序开发人员的接口（include/linux/of.h），以便从未扁平化的设备树数据结构中获取设备信息等。大多数设备驱动程序在各种用例中都使用此接口。

2. 测试数据

设备树源文件（drivers/of/unittest-data/testcases.dts）包含在 drivers/of/unittest.c 中自动执行单元测试所需的测试数据。目前，以下设备树源包含文件（.dtsi）包含在 testcases.dts 中：

drivers/of/unittest-data/tests-interrupts.dtsi
drivers/of/unittest-data/tests-platform.dtsi
drivers/of/unittest-data/tests-phandle.dtsi
drivers/of/unittest-data/tests-match.dtsi

当内核构建启用 OF_SELFTEST 时，使用以下 make 规则

$(obj)/%.dtb: $(src)/%.dts FORCE
    $(call if_changed_dep, dtc)

将 DT 源文件（testcases.dts）编译为二进制 blob（testcases.dtb），也称为扁平化的 DT。

之后，使用以下规则将上述二进制 blob 包装为汇编文件（testcases.dtb.S）。

$(obj)/%.dtb.S: $(obj)/%.dtb
    $(call cmd, dt_S_dtb)

汇编文件被编译为目标文件（testcases.dtb.o），并链接到内核映像中。

2.1. 添加测试数据

未扁平化的设备树结构：

未扁平化的设备树由以树形结构连接的设备节点组成，如下所述。

// 以下结构体成员用于构建树
struct device_node {
   ...
    struct  device_node *parent;
    struct  device_node *child;
    struct  device_node *sibling;
   ...
 };

图 1 描述了仅考虑子节点和兄弟节点指针的机器未扁平化设备树的通用结构。还有另一个指针，*parent，用于反向遍历树。因此，在特定级别，子节点和所有兄弟节点将有一个指向共同节点的父指针（例如，child1、sibling2、sibling3、sibling4 的父指针指向根节点）

```
root ('/')
   |
child1 -> sibling2 -> sibling3 -> sibling4 -> null
   |         |           |           |
   |         |           |          null
   |         |           |
   |         |        child31 -> sibling32 -> null
   |         |           |          |
   |         |          null       null
   |         |
   |      child21 -> sibling22 -> sibling23 -> null
   |         |          |            |
   |        null       null         null
   |
child11 -> sibling12 -> sibling13 -> sibling14 -> null
   |           |           |            |
   |           |           |           null
   |           |           |
  null        null       child131 -> null
                           |
                          null
```
图 1：未扁平化设备树的通用结构

在执行 OF 单元测试之前，需要将测试数据附加到机器的设备树（如果存在）。因此，当调用 selftest_data_add() 时，首先它通过以下内核符号读取链接到内核映像中的扁平化设备树数据：

__dtb_testcases_begin - 标记测试数据 blob 开始的地址
__dtb_testcases_end   - 标记测试数据 blob 结束的地址

其次，它调用 of_fdt_unflatten_tree() 来扁平化扁平化的 blob。最后，如果机器的设备树（即活动树）存在，则将未扁平化的测试数据树附加到活动树，否则将其自身作为活动设备树附加。

attach_node_and_children() 使用 of_attach_node() 将节点附加到活动树中，如下所述。为了解释这一点，将图 2 中描述的测试数据树附加到图 1 中描述的活动树。

```
root ('/')
    |
 testcase-data
    |
 test-child0 -> test-sibling1 -> test-sibling2 -> test-sibling3 -> null
    |               |                |                |
 test-child01      null             null             null
```

图 2：要附加到活动树的示例测试数据树。

根据上述场景，活动树已经存在，因此不需要附加根('/') 节点。通过对每个节点调用 of_attach_node() 来附加所有其他节点。

在 of_attach_node() 函数中，新节点作为活动树中给定父节点的子节点附加。但是，如果父节点已经有一个子节点，则新节点将替换当前子节点并将其转换为兄弟节点。因此，当将测试用例数据节点附加到上述活动树（图 1）时，最终结构如图 3 所示。

```
root ('/')
   |
testcase-data -> child1 -> sibling2 -> sibling3 -> sibling4 -> null
   |               |          |           |           |
   |             (...)      (...)       (...)        null
   |
test-sibling3 -> test-sibling2 -> test-sibling1 -> test-child0 -> null
   |                |                   |                |
  null             null                null         test-child01
```
-----------------------------------------------------------------------

```
root ('/')
   |
testcase-data -> child1 -> sibling2 -> sibling3 -> sibling4 -> null
   |               |          |           |           |
   |             (...)      (...)       (...)        null
   |
test-sibling3 -> test-sibling2 -> test-sibling1 -> test-child0 -> null
   |                |                   |                |
  null             null                null         test-child01
```
图 3：附加测试用例数据后的活动设备树结构。

敏锐的读者会注意到，与之前的结构（图 2）相比，test-child0 节点成为了最后一个兄弟节点。在附加第一个 test-child0 之后，附加 test-sibling1，这将子节点（即 test-child0）推为兄弟节点，并使自身成为子节点，如上所述。

如果发现重复节点（即，如果具有相同 full_name 属性的节点已经存在于活动树中），则不附加该节点，而是通过调用 update_node_properties() 函数将其属性更新到活动树的节点。

2.2. 移除测试数据

一旦测试用例执行完成，调用 selftest_data_remove 以移除最初附加的设备节点（首先分离叶节点，然后向上移除父节点，最终移除整个树）。selftest_data_remove() 调用 detach_node_and_children()，它使用 of_detach_node() 从活动设备树中分离节点。

要分离一个节点，of_detach_node() 会根据情况更新给定节点父节点的子指针到其兄弟节点，或者将给定节点的前一个兄弟节点附加到给定节点的兄弟节点。就是这样 