**What**: `/sys/bus/bcma/devices/.../manuf`

**Date**: May 2011

**KernelVersion**: 3.0

**Contact**: Rafał Miłecki <zajec5@gmail.com>

**Description**: 每个 BCMA 核心都有其制造商 ID。请参阅 include/linux/bcma/bcma.h 以获取可能的值。

**What**: `/sys/bus/bcma/devices/.../id`

**Date**: May 2011

**KernelVersion**: 3.0

**Contact**: Rafał Miłecki <zajec5@gmail.com>

**Description**: 有几种类型的 BCMA 核心，它们可以通过 id 字段来识别。

**What**: `/sys/bus/bcma/devices/.../rev`

**Date**: May 2011

**KernelVersion**: 3.0

**Contact**: Rafał Miłecki <zajec5@gmail.com>

**Description**: 相同类型的 BCMA 核心在其修订版本上仍然可能略有不同。将其用于详细编程。

**What**: `/sys/bus/bcma/devices/.../class`

**Date**: May 2011

**KernelVersion**: 3.0

**Contact**: Rafał Miłecki <zajec5@gmail.com>

**Description**: 每个 BCMA 核心都通过包括其所属类别的几个字段来识别。请参阅 include/linux/bcma/bcma.h 以获取可能的值。