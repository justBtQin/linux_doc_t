**Where**: `/sys/bus/event_source/devices/<dev>/format`

**Date**: January 2012

**Kernel Version**: 3.3

**Contact**: Jiri Olsa <jolsa@redhat.com>

**Description**:

属性组，用于描述特定 pmu 中放入`perf_event_attr::config[012]`的魔术位。该组的每个属性都定义了我们要导出的“硬件”位掩码，以便用户空间可以处理合理的名称/值对。

用户空间必须准备好应对属性定义重叠位范围的可能性。例如：

`attr1 = 'config:0-23'`

`attr2 = 'config:0-7'`

`attr3 = 'config:12-35'`

示例：`config1:1,6-10,44`

定义了占用`perf_event_attr::config1`的位 1、6-10、44 的属性内容。