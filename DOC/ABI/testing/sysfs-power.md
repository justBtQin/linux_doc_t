**/sys/power/**

- **What**: /sys/power/ 目录将包含为电源管理子系统提供统一接口的文件。

- **Date**: 2006 年 8 月

- **Contact**: Rafael J. Wysocki <rjw@rjwysocki.net>

**/sys/power/state**

- **What**: /sys/power/state 文件控制系统睡眠状态。

- **Date**: 2014 年 5 月

- **Contact**: Rafael J. Wysocki <rjw@rjwysocki.net>

- **Description**: 读取此文件返回可用的睡眠状态标签，可能是“mem”、“standby”、“freeze”和“disk”（休眠）。前三个标签的含义取决于 relative_sleep_states 命令行参数，如下所示：

    - relative_sleep_states = 1：“mem”、“standby”、“freeze”表示非休眠睡眠状态，从最深（“mem”，始终存在）到最浅（“freeze”）。“standby”和“freeze”可能存在也可能不存在，取决于平台的功能。“freeze”只有在“standby”存在时才存在。

    - relative_sleep_states = 0（默认）：“mem” - “suspend-to-RAM”，如果支持则存在。“standby” - “power-on suspend”，如果支持则存在。“freeze” - “suspend-to-idle”，始终存在。

    - 写入此文件其中一个字符串会使系统转换为相应状态（如果可用）。请参阅 Documentation/power/states.txt 了解“suspend-to-RAM”、“power-on suspend”和“suspend-to-idle”的含义。

**/sys/power/disk**

- **What**: /sys/power/disk 文件控制 suspend-to-disk 机制的操作模式。

- **Date**: 2006 年 9 月

- **Contact**: Rafael J. Wysocki <rjw@rjwysocki.net>

- **Description**: 读取此文件返回系统在下一次挂起时将进入睡眠的方法名称。支持四种方法：

    - 'firmware'：意味着内存映像将由某些固件保存到磁盘，在这种情况下，我们还假设固件将处理系统挂起。

    - 'platform'：内存映像将由内核保存，系统将由平台驱动程序（如 ACPI 或其他 PM 寄存器）进入睡眠。

    -'shutdown'：内存映像将由内核保存，系统将关闭电源。

    -'reboot'：内存映像将由内核保存，系统将重新启动。

    此外，/sys/power/disk 可用于打开 suspend-to-disk 机制的两种测试模式之一：'testproc'或'test'。如果 suspend-to-disk 机制处于'testproc'模式，写入“disk”到 /sys/power/state 将导致内核禁用非启动 CPU 并冻结任务，等待 5 秒，解冻任务并启用非启动 CPU。如果处于'test'模式，写入“disk”到 /sys/power/state 将导致内核禁用非启动 CPU 并冻结任务，收缩内存，挂起设备，等待 5 秒，恢复设备，解冻任务并启用非启动 CPU。然后，我们可以查看日志消息并找出例如哪些代码缓慢以及哪些设备驱动程序行为异常。

    可以通过写入以下接受的字符串之一来选择 suspend-to-disk 方法：

    - 'firmware'

    - 'platform'

    -'shutdown'

    -'reboot'

    - 'testproc'

    - 'test'

    只有当系统支持时，它才会更改为'firmware'或'platform'。

**/sys/power/image_size**

- **What**: /sys/power/image_size 文件控制 suspend-to-disk 机制创建的映像大小。

- **Date**: 2006 年 8 月

- **Contact**: Rafael J. Wysocki <rjw@rjwysocki.net>

- **Description**: 可以写入一个表示非负整数的字符串，该整数将用作映像大小的上限（以字节为单位）。内核的 suspend-to-disk 代码将尽力确保映像大小不超过此数字。然而，如果发现不可能，内核将尝试使用可能的最小映像进行挂起。特别地，如果向此文件写入“0”，则挂起映像将尽可能小。读取此文件将显示当前的映像大小限制，默认设置为 500 MB。

**/sys/power/pm_trace**

- **What**: /sys/power/pm_trace 文件控制在重启期间将最后一个 PM 事件点保存到 RTC 的代码，以便您可以调试在挂起期间（或更常见的是在恢复期间）挂起的机器。

- **Date**: 2006 年 8 月

- **Contact**: Rafael J. Wysocki <rjw@rjwysocki.net>

- **Description**: 只有当此文件包含“1”时，RTC 才会用于保存最后一个 PM 事件点。最初它包含“0”，可以通过写入一个表示非零整数的字符串将其更改为“1”。

    - 要使用此调试功能，您应该尝试挂起机器，然后重新启动它并运行“dmesg -s 1000000 | grep 'hash matches'”。如果没有匹配项（或它们似乎是误报），则最后一个 PM 事件点可能引用了由可加载内核模块创建的设备。在这种情况下，在系统启动并加载内核模块后，cat /sys/power/pm_trace_dev_match（见下文）。

    - 注意：使用它会导致机器的实时（CMOS）时钟在恢复后设置为随机无效时间。

**/sys/power/pm_trace_dev_match**

- **What**: /sys/power/pm_trace_dev_match 文件包含在重启期间保存到 RTC 中的最后一个 PM 事件点相关的设备名称，当使用 pm_trace 时。

- **Date**: 2010 年 10 月

- **Contact**: James Hogan <james@albanarts.com>

- **Description**: 它包含自启动以来与启动时 RTC 中的设备哈希匹配的当前设备列表（包括由可加载内核模块注册的设备），每个设备后有一个换行符。

    - 此文件相对于打印到内核日志中的哈希匹配的优势在于，它包括在启动后由可加载内核模块创建的设备。

    - 由于 RTC 中所需的哈希大小较小，可能有多个设备与哈希匹配，在这种情况下，需要进一步调查以确定导致问题的设备。请注意，真正的 RTC 时钟值（例如当未使用 pm_trace 时）仍然可以匹配设备并在此处输出其名称。

**/sys/power/pm_async**

- **What**: /sys/power/pm_async 文件控制允许用户空间启用或禁用设备的异步挂起和恢复的开关。

- **Date**: 2009 年 1 月

- **Contact**: Rafael J. Wysocki <rjw@rjwysocki.net>

- **Description**: 如果启用，此功能将导致一些设备驱动程序的挂起和恢复回调与彼此以及主挂起线程并行执行。如果此文件包含“1”，则启用此功能，这是默认值。可以通过写入“0”到该文件来禁用它，在这种情况下，所有设备将同步挂起和恢复。

**/sys/power/wakeup_count**

- **What**: /sys/power/wakeup_count 文件允许用户空间在考虑并发到达的唤醒事件的情况下将系统置于睡眠状态。

- **Date**: 2010 年 7 月

- **Contact**: Rafael J. Wysocki <rjw@rjwysocki.net>

- **Description**: 读取此文件返回当前注册的唤醒事件数量，并且如果在读取文件时正在处理某些唤醒事件，则会阻塞。写入此文件只有在当前唤醒事件数量等于写入的值时才会成功，如果成功，将使内核在写入返回后如果报告任何唤醒事件则中止后续的睡眠状态转换。

**/sys/power/reserved_size**

- **What**: /sys/power/reserved_size 文件允许用户空间控制在休眠的“设备冻结”阶段设备驱动程序分配保留的内存量。

- **Date**: 2011 年 5 月

- **Contact**: Rafael J. Wysocki <rjw@rjwysocki.net>**/sys/power/autosleep**

- 可以写入一个表示非负整数的字符串，该整数将用作设备驱动程序的“冻结”回调分配的预留内存量，以字节为单位。

- 读取此文件将显示当前值，默认设置为 1MB。

**日期：April 2012**

**联系人：Rafael J. Wysocki <rjw@rjwysocki.net>**

**描述：**

- /sys/power/autosleep 文件可以写入从 /sys/power/state 读取返回的字符串之一。如果发生这种情况，一个尝试将系统转换为该字符串表示的睡眠状态的工作项将被排队。只有在当时系统中没有活动的唤醒源时，此尝试才会成功。每次执行后，无论将系统置于睡眠的尝试是否成功，工作项都会重新排队，直到用户空间将“off”写入 /sys/power/autosleep。

- 读取此文件会返回最后成功写入它的字符串。

**/sys/power/wake_lock**

- 日期：February 2012

- 联系人：Rafael J. Wysocki <rjw@rjwysocki.net>

- 描述：

- /sys/power/wake_lock 文件允许用户空间创建唤醒源对象并按需激活它们（如果其中一个唤醒源处于活动状态，则从 /sys/power/wakeup_count 文件读取将阻塞或返回 false）。当向 /sys/power/wake_lock 写入一个无空格的字符串时，将假定它表示一个唤醒源名称。如果存在具有该名称的唤醒源对象，它将被激活（除非已经处于活动状态）。否则，将注册一个新的唤醒源对象，分配给定的名称并激活它。

- 如果写入 /sys/power/wake_lock 的字符串包含空格，则字符串中位于空格之前的部分将被视为唤醒源名称，并按照上述方式处理。字符串的另一部分将被视为超时（以纳秒为单位），以便在超时过期后自动取消激活唤醒源。如果存在超时，无论所讨论的唤醒源对象的当前状态如何，都会设置超时。

- 读取此文件将返回一个由当前借助它创建的处于活动状态的唤醒源名称组成的字符串，用空格分隔。

**/sys/power/wake_unlock**

- 日期：February 2012

- 联系人：Rafael J. Wysocki <rjw@rjwysocki.net>

- 描述：

- /sys/power/wake_unlock 文件允许用户空间取消激活借助 /sys/power/wake_lock 创建的唤醒源。

- 当向 /sys/power/wake_unlock 写入一个字符串时，将假定它表示要取消激活的唤醒源的名称。如果存在具有该名称的唤醒源对象并且当前处于活动状态，它将被取消激活。

- 读取此文件将返回一个由当前借助 /sys/power/wake_lock 创建的处于非活动状态的唤醒源名称组成的字符串，用空格分隔。

**/sys/power/pm_print_times**

- 日期：May 2012

- 联系人：Sameer Nanda <snanda@chromium.org>

- 描述：

- /sys/power/pm_print_times 文件允许用户空间控制是否打印设备挂起和恢复所花费的时间。这些打印对于查找挂起或恢复时间过长的设备很有用。

- 写入“1”启用此打印，写入“0”禁用它。默认值为“0”。读取此文件将显示当前值。