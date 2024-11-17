# 设备树覆盖笔记
-------------------------

本文档描述了内核中设备树覆盖功能的实现，位于 `drivers/of/overlay.c` 中，并且是 `Documentation/devicetree/dt-object-internal.txt[1]` 和 `Documentation/devicetree/dynamic-resolution-notes.txt[2]` 的配套文档。

覆盖的工作原理
-----------------

设备树的覆盖目的是修改内核的活动树，并使修改以反映更改的方式影响内核的状态。

由于内核主要处理设备，任何导致活动设备的新设备节点都应该被创建，而如果设备节点被禁用或完全删除，受影响的设备应该被注销。

以我们有一个 `foo` 板为例，其基础树如下（取自 [1]）。

---- foo.dts -----------------------------------------------------------------
    /* FOO 平台 */
    / {
        compatible = "corp,foo";

        /* 共享资源 */
        res: res {
        };

        /* 片上外设 */
        ocp: ocp {
            /* 始终实例化的外设 */
            peripheral1 {... };
        }
    };
---- foo.dts -----------------------------------------------------------------

加载（并如 [2] 中所述解析）覆盖 `bar.dts` 时，应该

---- bar.dts -----------------------------------------------------------------
/plugin/;    /* 允许未定义的标签引用并记录它们 */
/ {
   ....    /* 供加载器使用的各种属性；即部件 ID 等 */
    fragment@0 {
        target = <&ocp>;
        __overlay__ {
            /* bar 外设 */
            bar {
                compatible = "corp,bar";
               ... /* 各种属性和子节点 */
            }
        };
    };
};
---- bar.dts -----------------------------------------------------------------

结果为 `foo+bar.dts`

---- foo+bar.dts -------------------------------------------------------------
    /* FOO 平台 + bar 外设 */
    / {
        compatible = "corp,foo";

        /* 共享资源 */
        res: res {
        };

        /* 片上外设 */
        ocp: ocp {
            /* 始终实例化的外设 */
            peripheral1 {... };

            /* bar 外设 */
            bar {
                compatible = "corp,bar";
               ... /* 各种属性和子节点 */
            }
        }
    };
---- foo+bar.dts -------------------------------------------------------------

由于覆盖，创建了一个新的设备节点（bar），因此将注册一个 `bar` 平台设备，如果加载了匹配的设备驱动程序，设备将按预期创建。

内核中的覆盖 API
--------------------------------

该 API 非常易于使用。

1. 调用 `of_overlay_create()` 创建并应用覆盖。返回值是标识此覆盖的 cookie。

2. 调用 `of_overlay_destroy()` 移除并清理先前通过调用 `of_overlay_create()` 创建的覆盖。不允许移除被另一个覆盖堆叠的覆盖。

最后，如果需要一次性移除所有覆盖，只需调用 `of_overlay_destroy_all()` ，它将以正确的顺序移除每一个覆盖。

覆盖 DTS 格式
------------------

覆盖的 DTS 应该具有以下格式：

```
{
    /* 被覆盖忽略的属性 */

    fragment@0 {    /* 第一个子节点 */

        target=<phandle>;    /* 覆盖的 phandle 目标 */
        或
        target-path="/path";    /* 覆盖的目标路径 */

        __overlay__ {
            property-a;    /* 向目标添加 property-a */
            node-a {    /* 添加到现有节点，或创建 node-a */
               ...
            };
        };
    }
    fragment@1 {    /* 第二个子节点 */
       ...
    };
    /* 更多片段跟随 */
}
```

使用基于非 phandle 的目标方法允许使用不包含 `__symbols__` 节点的基础 DT，即它不是使用 `-@` 选项编译的。`__symbols__` 节点仅对于 `target=<phandle>` 方法是必需的，因为它包含从 phandle 到树位置的映射所需的信息。