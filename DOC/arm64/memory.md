# Memory Layout on AArch64 Linux

==============================

作者：Catalin Marinas <catalin.marinas@arm.com>

此文档描述了 AArch64 Linux 内核使用的虚拟内存布局。该架构允许最多 4 级转换表，页面大小为 4KB，最多 3 级转换表，页面大小为 64KB。AArch64 Linux 使用 3 级或 4 级转换表，页面配置为 4KB，分别允许 39 位（512GB）或 48 位（256TB）虚拟地址，适用于用户和内核。使用 64KB 页面时，仅使用 2 级转换表，允许 42 位（4TB）虚拟地址，但内存布局相同。用户地址的位 63:48 设置为 0，而内核地址的相同位设置为 1。TTBRx 选择由虚拟地址的位 63 给出。swapper_pg_dir 仅包含内核（全局）映射，而用户 pgd 仅包含用户（非全局）映射。swapper_pg_dir 地址写入 TTBR1，从不写入 TTBR0。

AArch64 Linux 内存布局，4KB 页面 + 3 级：

```
开始  结束  大小  使用
-----------------------------------------------------------------------
0000000000000000  0000007fffffffff  512GB  用户
ffffff8000000000  ffffffffffffffff  512GB  内核
```

AArch64 Linux 内存布局，4KB 页面 + 4 级：

```
开始  结束  大小  使用
-----------------------------------------------------------------------
0000000000000000  0000ffffffffffff  256TB  用户
ffff000000000000  ffffffffffffffff  256TB  内核
```

AArch64 Linux 内存布局，64KB 页面 + 2 级：

```
开始  结束  大小  使用
-----------------------------------------------------------------------
0000000000000000  000003ffffffffff  4TB  用户
fffffc0000000000  ffffffffffffffff  4TB  内核
```

AArch64 Linux 内存布局，64KB 页面 + 3 级：

```
开始  结束  大小  使用
-----------------------------------------------------------------------
0000000000000000  0000ffffffffffff  256TB  用户
ffff000000000000  ffffffffffffffff  256TB  内核
```

有关虚拟内核内存布局的详细信息，请参阅内核启动日志。

使用 4KB 页面的转换表查找：

```
+--------+--------+--------+--------+--------+--------+--------+--------+
|63    56|55    48|47    40|39    32|31    24|23    16|15     8|7      0|
+--------+--------+--------+--------+--------+--------+--------+--------+
 |                 |         |         |         |         |
 |                 |         |         |         |         v
 |                 |         |         |         |   [11:0] 页内偏移
 |                 |         |         |         +-> [20:12] L3 索引
 |                 |         |         +-----------> [29:21] L2 索引
 |                 |         +---------------------> [38:30] L1 索引
 |                 +-------------------------------> [47:39] L0 索引
 +-------------------------------------------------> [63] TTBR0/1
```
 
使用 64KB 页面的转换表查找：

```
+--------+--------+--------+--------+--------+--------+--------+--------+
|63    56|55    48|47    40|39    32|31    24|23    16|15     8|7      0|
+--------+--------+--------+--------+--------+--------+--------+--------+
 |                 |    |               |              |
 |                 |    |               |              v
 |                 |    |               |            [15:0] 页内偏移
 |                 |    |               +----------> [28:16] L3 索引
 |                 |    +--------------------------> [41:29] L2 索引
 |                 +-------------------------------> [47:42] L1 索引
 +-------------------------------------------------> [63] TTBR0/1
```

当使用 KVM 时，虚拟机监控程序在 EL2 中映射内核页面，与内核 VA 有固定偏移（内核 VA 的高 24 位设置为 0）：

```
开始  结束  大小  使用
-----------------------------------------------------------------------
0000004000000000  0000007fffffffff  256GB  在 HYP 中映射的内核对象
```