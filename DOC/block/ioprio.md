**Block io priorities**

===================

**Intro**

-----

随着 cfq v3（又名 cfq-ts 或时间切片 cfq）的引入，文件读取支持基本的 I/O 优先级。这使用户能够对进程或进程组进行 I/O 优化，类似于多年来在 CPU 调度中已经可能实现的那样。本文档主要详细介绍当前 cfq 的可能性；其他 I/O 调度程序迄今为止不支持 I/O 优先级。

**Scheduling classes**

------------------

CFQ 实现了三种通用的调度类，用于确定如何为进程提供 I/O 服务。

IOPRIO_CLASS_RT：这是实时 I/O 类。此调度类在系统中具有比任何其他类更高的优先级，每次此类中的进程都首先访问磁盘。因此，需要谨慎使用，一个 I/O RT 进程可能会使整个系统饥饿。在 RT 类中，有 8 级类数据，用于确定此进程在每次服务中需要磁盘的时间。将来，通过传入所需的数据速率，这可能会更直接地映射到性能。

IOPRIO_CLASS_BE：这是尽力而为调度类，是任何未设置特定 I/O 优先级的进程的默认类。类数据确定进程将获得多少 I/O 带宽，它直接映射到 CPU 优先级级别，但实现更粗略。0 是最高的 BE 优先级级别，7 是最低的。CPU 优先级级别和 I/O 优先级级别之间的映射确定为：io_nice = (cpu_nice + 20) / 5。

IOPRIO_CLASS_IDLE：这是空闲调度类，在此级别运行的进程仅在没有其他进程需要磁盘时才获得 I/O 时间。空闲类没有类数据，因为在此处并不真正适用。

**Tools**

-----

以下是一个 ionice 工具的示例。用法：

# ionice -c<类> -n<级别> -p<进程 ID>

如果未给出进程 ID，则假定为当前进程。I/O 优先级设置在 fork 时继承，因此您可以使用 ionice 在给定级别启动进程：

# ionice -c2 -n0 /bin/ls

将以最高优先级在尽力而为调度类中运行 ls。对于正在运行的进程，您可以提供进程 ID 代替：

# ionice -c1 -n2 -p100

将把进程 ID 为 100 的进程更改为在实时调度类中以优先级 2 运行。

---> 省略 ionice.c 工具 <---

```c
#include <stdio.h>
#include <stdlib.h>
#include <errno.h>
#include <getopt.h>
#include <unistd.h>
#include <sys/ptrace.h>
#include <asm/unistd.h>

extern int sys_ioprio_set(int, int, int);
extern int sys_ioprio_get(int, int);

#if defined(__i386__)
#define __NR_ioprio_set		289
#define __NR_ioprio_get		290
#elif defined(__ppc__)
#define __NR_ioprio_set		273
#define __NR_ioprio_get		274
#elif defined(__x86_64__)
#define __NR_ioprio_set		251
#define __NR_ioprio_get		252
#elif defined(__ia64__)
#define __NR_ioprio_set		1274
#define __NR_ioprio_get		1275
#else
#error "Unsupported arch"
#endif

static inline int ioprio_set(int which, int who, int ioprio)
{
	return syscall(__NR_ioprio_set, which, who, ioprio);
}

static inline int ioprio_get(int which, int who)
{
	return syscall(__NR_ioprio_get, which, who);
}

enum {
	IOPRIO_CLASS_NONE,
	IOPRIO_CLASS_RT,
	IOPRIO_CLASS_BE,
	IOPRIO_CLASS_IDLE,
};

enum {
	IOPRIO_WHO_PROCESS = 1,
	IOPRIO_WHO_PGRP,
	IOPRIO_WHO_USER,
};

#define IOPRIO_CLASS_SHIFT	13

const char *to_prio[] = { "none", "realtime", "best-effort", "idle", };

int main(int argc, char *argv[])
{
	int ioprio = 4, set = 0, ioprio_class = IOPRIO_CLASS_BE;
	int c, pid = 0;

	while ((c = getopt(argc, argv, "+n:c:p:"))!= EOF) {
		switch (c) {
		case 'n':
			ioprio = strtol(optarg, NULL, 10);
			set = 1;
			break;
		case 'c':
			ioprio_class = strtol(optarg, NULL, 10);
			set = 1;
			break;
		case 'p':
			pid = strtol(optarg, NULL, 10);
			break;
		}
	}

	switch (ioprio_class) {
		case IOPRIO_CLASS_NONE:
			ioprio_class = IOPRIO_CLASS_BE;
			break;
		case IOPRIO_CLASS_RT:
		case IOPRIO_CLASS_BE:
			break;
		case IOPRIO_CLASS_IDLE:
			ioprio = 7;
			break;
		default:
			printf("bad prio class %d\n", ioprio_class);
			return 1;
	}

	if (!set) {
		if (!pid && argv[optind])
			pid = strtol(argv[optind], NULL, 10);

		ioprio = ioprio_get(IOPRIO_WHO_PROCESS, pid);

		printf("pid=%d, %d\n", pid, ioprio);

		if (ioprio == -1)
			perror("ioprio_get");
		else {
			ioprio_class = ioprio >> IOPRIO_CLASS_SHIFT;
			ioprio = ioprio & 0xff;
			printf("%s: prio %d\n", to_prio[ioprio_class], ioprio);
		}
	} else {
		if (ioprio_set(IOPRIO_WHO_PROCESS, pid, ioprio | ioprio_class << IOPRIO_CLASS_SHIFT) == -1) {
			perror("ioprio_set");
			return 1;
		}

		if (argv[optind])
			execvp(argv[optind], &argv[optind]);
	}

	return 0;
}
```
---> 省略 ionice.c 工具 <---

March 11 2005, Jens Axboe <jens.axboe@oracle.com>