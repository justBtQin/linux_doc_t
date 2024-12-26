<markdown_document>

CMA debugfs 接口对于从不同的 CMA 区域检索基本信息以及在每个区域测试分配/释放非常有用。

每个 CMA 区域在<debugfs>/cma/下表示一个目录，由内核的 CMA 索引索引。所以第一个 CMA 区域将是：

    `<debugfs>/cma/cma-0`

在该目录下创建的文件结构如下：

- [只读] base_pfn：区域的基本页帧号（Page Frame Number）。

- [只读] count：CMA 区域中的内存量。

- [只读] order_per_bit：一位表示的页面的阶数。

- [只读] bitmap：区域中的页面状态位图。

- [可写] alloc：从该 CMA 区域分配 N 个页面。例如：

    `echo 5 > <debugfs>/cma/cma-2/alloc`

将尝试从 cma-2 区域分配 5 个页面。

- [可写] free：从该 CMA 区域释放 N 个页面，类似于上述操作。

</markdown_document>