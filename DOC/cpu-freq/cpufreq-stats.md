<markdown_document>

Linux(TM) 内核中的 CPU 频率和电压缩放统计

Linux cpufreq-stats 驱动程序

- 给用户的信息 -

Venkatesh Pallipadi <venkatesh.pallipadi@intel.com>

目录

1. 介绍

2. 提供的统计信息（带示例）

3. 配置 cpufreq-stats

1. 介绍

cpufreq-stats 是一个为每个 CPU 提供 CPU 频率统计信息的驱动程序。这些统计信息在 /sysfs 中以一组只读接口的形式提供。这个接口（在配置后）将在 /sysfs 中的 cpufreq 下的一个单独目录中出现（<sysfs 根目录>/devices/system/cpu/cpuX/cpufreq/stats/），对于每个 CPU 都有。各种统计信息将在这个目录下形成只读文件。这个驱动程序被设计为独立于在你的 CPU 上运行的任何特定的 cpufreq_driver。因此，它将与任何 cpufreq_driver 一起工作。

2. 提供的统计信息（带示例）

cpufreq 统计提供以下统计信息（下面将详细解释）。- time_in_state

- total_trans

- trans_table

所有的统计信息将从统计驱动程序被插入的时间到读取特定统计信息的时间。显然，统计驱动程序在统计驱动程序插入之前将没有关于频率转换的任何信息。

--------------------------------------------------------------------------------

<mysystem>:/sys/devices/system/cpu/cpu0/cpufreq/stats # ls -l

总计 0

drwxr-xr-x  2 root root    0 5 月 14 日 16:06.

drwxr-xr-x  3 root root    0 5 月 14 日 15:58..

-r--r--r--  1 root root 4096 5 月 14 日 16:06 time_in_state

-r--r--r--  1 root root 4096 5 月 14 日 16:06 total_trans

-r--r--r--  1 root root 4096 5 月 14 日 16:06 trans_table

--------------------------------------------------------------------------------

- time_in_state

这给出了在这个 CPU 支持的每个频率上花费的时间。cat 输出将在每一行中有“<频率> <时间>”对，这将意味着这个 CPU 在<频率>上花费了<时间>微秒时间单位。输出将为每个支持的频率有一行。这里的微秒时间单位是 10 毫秒（类似于 /proc 中导出的其他时间）。

--------------------------------------------------------------------------------

<mysystem>:/sys/devices/system/cpu/cpu0/cpufreq/stats # cat time_in_state

3600000 2089

3400000 136

3200000 34

3000000 67

2800000 172488

--------------------------------------------------------------------------------

- total_trans

这给出了这个 CPU 上的频率转换的总数。cat 输出将有一个单一的计数，这是频率转换的总数。

--------------------------------------------------------------------------------

<mysystem>:/sys/devices/system/cpu/cpu0/cpufreq/stats # cat total_trans

20

--------------------------------------------------------------------------------

- trans_table

这将给出关于所有 CPU 频率转换的细粒度信息。这里的 cat 输出是一个二维矩阵，其中一个条目<i,j>（行 i，列 j）表示从 Freq_i 到 Freq_j 的转换次数。Freq_i 按行递减顺序排列，Freq_j 按列递减顺序排列。这里的输出还包含每行和每列的实际频率值，以便更好地可读性。

--------------------------------------------------------------------------------

<mysystem>:/sys/devices/system/cpu/cpu0/cpufreq/stats # cat trans_table

   从 :    到

         :   3600000   3400000   3200000   3000000   2800000

  3600000:         0         5         0         0         0

  3400000:         4         0         2         0         0

  3200000:         0         1         0         2         0

  3000000:         0         0         1         0         3

  2800000:         0         0         0         2         0

--------------------------------------------------------------------------------

3. 配置 cpufreq-stats

要在你的内核中配置 cpufreq-stats

配置主菜单

    电源管理选项（ACPI，APM）  --->

        CPU 频率缩放  --->

            [*] CPU 频率缩放

            <*>   CPU 频率转换统计

            [*]     CPU 频率转换统计细节

“CPU 频率缩放”（CONFIG_CPU_FREQ）应该被启用以配置 cpufreq-stats。

“CPU 频率转换统计”（CONFIG_CPU_FREQ_STAT）提供基本统计信息，包括 time_in_state 和 total_trans。

“CPU 频率转换统计细节”（CONFIG_CPU_FREQ_STAT_DETAILS）通过 trans_table 提供细粒度的 cpufreq 统计信息。有一个单独的配置选项用于 trans_table 的原因是：

- trans_table 违反了 /sysfs 中每个接口一个值的传统规则。它以二维矩阵的形式提供了一整堆值。

一旦这两个选项被启用并且你的 CPU 支持 cpufrequency，你将能够在 /sysfs 中看到 CPU 频率统计信息。

</markdown_document>