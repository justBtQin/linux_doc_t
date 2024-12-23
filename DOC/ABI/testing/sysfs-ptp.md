**一、/sys/class/ptp/**

- **What**：/sys/class/ptp/

- **Date**：September 2010

- **Contact**：Richard Cochran <richardcochran@gmail.com>

- **Description**：此目录包含提供对 PTP 硬件时钟辅助功能的标准化接口的文件和目录。

**二、/sys/class/ptp/ptpN/**

- **What**：/sys/class/ptp/ptpN/

- **Date**：September 2010

- **Contact**：Richard Cochran <richardcochran@gmail.com>

- **Description**：此目录包含注册到 PTP 类驱动子系统中的第 N 个 PTP 硬件时钟的属性。

**三、/sys/class/ptp/ptpN/clock_name**

- **What**：/sys/class/ptp/ptpN/clock_name

- **Date**：September 2010

- **Contact**：Richard Cochran <richardcochran@gmail.com>

- **Description**：此文件包含 PTP 硬件时钟的名称，为人类可读的字符串。此属性的目的是为用户提供一个“友好名称”，并帮助区分基于 PHY 的设备和基于 MAC 的设备。该字符串不一定是任何类型的唯一 ID。

**四、/sys/class/ptp/ptpN/max_adjustment**

- **What**：/sys/class/ptp/ptpN/max_adjustment

- **Date**：September 2010

- **Contact**：Richard Cochran <richardcochran@gmail.com>

- **Description**：此文件包含 PTP 硬件时钟的最大频率调整值（一个正整数），以十亿分之一为单位。

**五、/sys/class/ptp/ptpN/n_alarms**

- **What**：/sys/class/ptp/ptpN/n_alarms

- **Date**：September 2010

- **Contact**：Richard Cochran <richardcochran@gmail.com>

- **Description**：此文件包含 PTP 硬件时钟提供的定期或一次性警报的数量。

**六、/sys/class/ptp/ptpN/n_external_timestamps**

- **What**：/sys/class/ptp/ptpN/n_external_timestamps

- **Date**：September 2010

- **Contact**：Richard Cochran <richardcochran@gmail.com>

- **Description**：此文件包含 PTP 硬件时钟提供的外部时间戳通道的数量。

**七、/sys/class/ptp/ptpN/n_periodic_outputs**

- **What**：/sys/class/ptp/ptpN/n_periodic_outputs

- **Date**：September 2010

- **Contact**：Richard Cochran <richardcochran@gmail.com>

- **Description**：此文件包含 PTP 硬件时钟提供的可编程定期输出通道的数量。

**八、/sys/class/ptp/ptpN/n_pins**

- **What**：/sys/class/ptp/ptpN/n_pins

- **Date**：March 2014

- **Contact**：Richard Cochran <richardcochran@gmail.com>

- **Description**：此文件包含 PTP 硬件时钟提供的可编程引脚的数量。

**九、/sys/class/ptp/ptpN/pins**

- **What**：/sys/class/ptp/ptpN/pins

- **Date**：March 2014

- **Contact**：Richard Cochran <richardcochran@gmail.com>

- **Description**：此目录包含 PTP 硬件时钟提供的每个可编程引脚的一个文件。文件名是硬件相关的引脚名称。读取此文件会产生两个数字，分配的功能（请参阅 linux/ptp_clock.h 中的 PTP_PF_枚举值）和通道编号。可以通过向文件中写入两个数字来更改功能和通道分配。

**十、/sys/class/ptp/ptpN/pps_available**

- **What**：/sys/class/ptp/ptpN/pps_available

- **Date**：September 2010

- **Contact**：Richard Cochran <richardcochran@gmail.com>

- **Description**：此文件指示 PTP 硬件时钟是否支持向主机 CPU 的每秒脉冲。读取“1”表示支持 PPS，读取“0”表示不支持。

**十一、/sys/class/ptp/ptpN/extts_enable**

- **What**：/sys/class/ptp/ptpN/extts_enable

- **Date**：September 2010

- **Contact**：Richard Cochran <richardcochran@gmail.com>

- **Description**：此只写文件启用或禁用外部时间戳。要启用外部时间戳，将通道索引后跟“1”写入文件。要禁用外部时间戳，将通道索引后跟“0”写入文件。

**十二、/sys/class/ptp/ptpN/fifo**

- **What**：/sys/class/ptp/ptpN/fifo

- **Date**：September 2010

- **Contact**：Richard Cochran <richardcochran@gmail.com>

- **Description**：此文件以三个整数的形式提供外部事件的时间戳：通道索引、秒和纳秒。

**十三、/sys/class/ptp/ptpN/period**

- **What**：/sys/class/ptp/ptpN/period

- **Date**：September 2010

- **Contact**：Richard Cochran <richardcochran@gmail.com>

- **Description**：此只写文件启用或禁用定期输出。要启用定期输出，将五个整数写入文件：通道索引、开始时间秒、开始时间纳秒、周期秒和周期纳秒。要禁用定期输出，将所有秒和纳秒值设置为零。

**十四、/sys/class/ptp/ptpN/pps_enable**

- **What**：/sys/class/ptp/ptpN/pps_enable

- **Date**：September 2010

- **Contact**：Richard Cochran <richardcochran@gmail.com>

- **Description**：此只写文件启用或禁用向 Linux PPS 子系统的 PPS 事件传递。要启用 PPS 事件，将“1”写入文件。要禁用事件，将“0”写入文件。