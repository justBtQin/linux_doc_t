**What**: `/sys/kernel/mm/hugepages/`

**Date**: 六月 2008

**Contact**: Nishanth Aravamudan <nacc@us.ibm.com>, hugetlb 维护者

**Description**:

`/sys/kernel/mm/hugepages/`包含一些形如`hugepages-<size>kB`的子目录，其中`<size>`是内核/CPU 组合支持的大页的页面大小。在这些目录下有一些文件：

- `nr_hugepages`

- `nr_overcommit_hugepages`

- `free_hugepages`

- `surplus_hugepages`

- `resv_hugepages`

有关详细信息，请参阅 Documentation/vm/hugetlbpage.txt。