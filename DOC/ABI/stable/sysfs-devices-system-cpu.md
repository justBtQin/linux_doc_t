**What: /sys/devices/system/cpu/dscr_default**

**Date: 13-May-2014**

**KernelVersion: v3.15.0**

**Contact: 无具体联系人信息**

**Description: 写入操作等效于对所有 CPU 上的 /sys/devices/system/cpu/cpuN/dscr 进行写入。读取操作返回最后写入的值或 0。此值不是全局默认值：它是一种同时设置所有 per-CPU 默认值的方式。**

**Values: 64 位无符号整数（位域）**

**What: /sys/devices/system/cpu/cpu[0-9]+/dscr**

**Date: 13-May-2014**

**KernelVersion: v3.15.0**

**Contact: 无具体联系人信息**

**Description: CPU 上的数据流控制寄存器（DSCR）的默认值。当内核正在执行且任何进程尚未自行设置 DSCR 时，将使用此默认值。如果一个进程通过直接访问 SPR 设置了 DSCR，该值将为该进程持久化，并在其执行的任何 CPU 上使用（覆盖此处描述的值）。如果由进程设置，它将被子进程继承。**

**Values: 64 位无符号整数（位域）**