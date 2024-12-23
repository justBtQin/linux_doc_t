**What**: `/sys/fs/f2fs/<disk>/gc_max_sleep_time`

**Date**: July 2013

**Contact**: "Namjae Jeon" <namjae.jeon@samsung.com>

**Description**: 控制 gc_thread 的最大睡眠时间。时间以毫秒为单位。

**What**: `/sys/fs/f2fs/<disk>/gc_min_sleep_time`

**Date**: July 2013

**Contact**: "Namjae Jeon" <namjae.jeon@samsung.com>

**Description**: 控制 gc_thread 的最小睡眠时间。时间以毫秒为单位。

**What**: `/sys/fs/f2fs/<disk>/gc_no_gc_sleep_time`

**Date**: July 2013

**Contact**: "Namjae Jeon" <namjae.jeon@samsung.com>

**Description**: 控制 gc_thread 的默认睡眠时间。时间以毫秒为单位。

**What**: `/sys/fs/f2fs/<disk>/gc_idle`

**Date**: July 2013

**Contact**: "Namjae Jeon" <namjae.jeon@samsung.com>

**Description**: 控制垃圾收集的受害者选择策略。

**What**: `/sys/fs/f2fs/<disk>/reclaim_segments`

**Date**: October 2013

**Contact**: "Jaegeuk Kim" <jaegeuk.kim@samsung.com>

**Description**: 控制段丢弃命令的发出速率。

**What**: `/sys/fs/f2fs/<disk>/ipu_policy`

**Date**: November 2013

**Contact**: "Jaegeuk Kim" <jaegeuk.kim@samsung.com>

**Description**: 控制就地更新策略。

**What**: `/sys/fs/f2fs/<disk>/min_ipu_util`

**Date**: November 2013

**Contact**: "Jaegeuk Kim" <jaegeuk.kim@samsung.com>

**Description**: 控制就地更新策略的文件系统利用率条件。

**What**: `/sys/fs/f2fs/<disk>/min_fsync_blocks`

**Date**: September 2014

**Contact**: "Jaegeuk Kim" <jaegeuk@kernel.org>

**Description**: 控制就地更新策略的脏页计数条件。

**What**: `/sys/fs/f2fs/<disk>/max_small_discards`

**Date**: November 2013

**Contact**: "Jaegeuk Kim" <jaegeuk.kim@samsung.com>

**Description**: 控制小丢弃命令的发出速率。

**What**: `/sys/fs/f2fs/<disk>/max_victim_search`

**Date**: January 2014

**Contact**: "Jaegeuk Kim" <jaegeuk.kim@samsung.com>

**Description**: 控制查找受害者段的尝试次数。

**What**: `/sys/fs/f2fs/<disk>/dir_level`

**Date**: March 2014

**Contact**: "Jaegeuk Kim" <jaegeuk.kim@samsung.com>

**Description**: 控制大目录的目录级别。

**What**: `/sys/fs/f2fs/<disk>/ram_thresh`

**Date**: March 2014

**Contact**: "Jaegeuk Kim" <jaegeuk.kim@samsung.com>

**Description**: 控制 f2fs 使用的内存占用。

**What**: `/sys/fs/f2fs/<disk>/trim_sections`

**Date**: February 2015

**Contact**: "Jaegeuk Kim" <jaegeuk@kernel.org>

**Description**: 控制批量模式下的修剪速率。