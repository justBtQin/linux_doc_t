`struct request` 文档

Jens Axboe <jens.axboe@oracle.com>  27/05/02

1.0
索引

2.0 `struct request` 成员分类

    2.1 `struct request` 成员解释

3.0

2.0  请求成员的简短解释

分类标志：

D  驱动成员

B  块层成员

I  I/O 调度器成员

除非条目包含 D 分类，否则设备驱动程序不得访问此成员。某些成员可能包含 D 分类，但应仅通过某些宏或函数进行访问（例如 `->flags`）。

<linux/blkdev.h>

2.1

成员                标志    注释

------                ----    -------

`struct list_head queuelist`    BI  在各种内部队列上的组织

`void *elevator_private`        I  I/O 调度器私有数据

`unsigned char cmd[16]`         D  驱动程序可以在执行前使用此设置 cdb，见 `blk_queue_prep_rq`

`unsigned long flags`           DBI  包含有关数据方向、请求类型等的信息

`int rq_status`                 D  请求状态位

`kdev_t rq_dev`                 DBI  目标设备

`int errors`                    DB  错误计数

`sector_t sector`               DBI  目标位置

`unsigned long hard_nr_sectors` B  用于保持 `sector` 合理

`unsigned long nr_sectors`      DBI  请求中的扇区总数

`unsigned long hard_nr_sectors` B  用于保持 `nr_sectors` 合理

`unsigned short nr_phys_segments`  DB  请求中的物理分散/聚集段的数量

`unsigned short nr_hw_segments`    DB  请求中的硬件分散/聚集段的数量

`unsigned int current_nr_sectors`  DB  请求的第一个段中的扇区数量

`unsigned int hard_cur_sectors`   B  用于保持 `current_nr_sectors` 合理

`int tag`                       DB  TCQ 标签，如果已分配

`void *special`                 D  驱动程序可自由使用

`char *buffer`                  D  第一个段的映射，也请参阅关于回弹的部分

`struct completion *waiting`    D  驱动程序可用于在请求完成时获得信号

`struct bio *bio`               DBI  请求中的第一个 bio

`struct bio *biotail`           DBI  请求中的最后一个 bio

`struct request_queue *q`       DB  此请求所属的请求队列

`struct request_list *rl`       B  此请求来自的请求列表