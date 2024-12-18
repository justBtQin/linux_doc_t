# Linux ACPI 自定义控制方法指南

=======================================

作者：张锐 <rui.zhang@intel.com>

Linux 支持在运行时自定义 ACPI 控制方法。用户可以利用此功能：

1. 覆盖可能无法正常工作的现有方法，或者仅用于调试目的。

2. 插入全新的方法，以创建缺失的方法，如 _OFF、_ON、_STA、_INI 等。对于这些情况，动态安装单个控制方法比覆盖整个 DSDT 要简单得多，因为不需要重新构建/重新启动内核，并且可以在几分钟内获得测试结果。

注意：仅可覆盖 ACPI 方法，其他对象类型，如“Device”、“OperationRegion”，不受识别。

注意：相同的 ACPI 控制方法可以被多次覆盖，并且始终是 Linux/内核使用的最新版本。

注意：要获取 ACPI 调试对象输出（Store (AAAA, Debug)），请运行“echo 1 > /sys/module/acpi/parameters/aml_debug_output”。

## 1. 覆盖现有方法

    a) 通过 ACPI sysfs 接口获取 ACPI 表。例如，要获取 DSDT，只需运行“cat /sys/firmware/acpi/tables/DSDT > /tmp/dsdt.dat”。

    b) 通过运行“iasl -d dsdt.dat”反汇编表。

    c) 重写该方法的 ASL 代码并将其保存到新文件中。

    d) 将新文件（psr.asl）打包为 ACPI 表格式。

这里是一个自定义的\_SB._AC._PSR 方法的示例：

```
    DefinitionBlock ("", "SSDT", 1, "", "", 0x20080715)
    {
        External (ACON)
        Method (\_SB_.AC._PSR, 0, NotSerialized)
        {
            Store ("In AC _PSR", Debug)
            Return (ACON)
        }
    }
```

注意，应在 ACPI 命名空间中使用该方法的完整路径名。

并且记住使用“External”来声明外部对象。

    e) 组装文件以生成该方法的 AML 代码。

例如“iasl psr.asl”（结果生成 psr.aml）。

    f) 通过“mount -t debugfs none /sys/kernel/debug”挂载 debugfs。

    g) 通过运行“cat /tmp/psr.aml > /sys/kernel/debug/acpi/custom_method”在 debugfs 中覆盖旧方法。

## 2. 插入新方法

这比覆盖现有方法更容易。

我们只需要创建要插入的方法的 ASL 代码，然后按照第 1 节中的步骤 c) 到 g) 进行操作。

## 3. 撤销更改

目前不支持对新插入的方法进行“撤销”操作，即我们目前无法删除一个方法。

对于覆盖的方法，为了撤销更改，请在第 1 节的步骤 c) 中保存该方法原始 ASL 代码的副本，并重新执行步骤 c) 到 g)，以使用原始方法覆盖该方法。

注意：我们可以使用具有多个自定义 ACPI 方法运行的内核，

但是每次向 debugfs 的单独写入都可以实现单个方法覆盖。即，如果我们要插入/覆盖多个 ACPI 方法，我们需要多次重复步骤 c) 到 g)。

注意：请注意，如果 root 的权限受到限制（例如，如果在启动后不允许 root 加载其他模块），root 可能会滥用此驱动程序来修改任意内存并获得额外的权限。