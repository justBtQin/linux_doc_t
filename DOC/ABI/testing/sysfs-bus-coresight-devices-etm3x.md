**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/enable_source`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 启用或禁用此特定跟踪实体的跟踪。启用一个源意味着该源已正确配置，并且已为其标识了一个接收器。连接源和接收器的 coresight 组件的路径由 coresight 框架自动配置和管理。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/status`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (R) 列出各种控制和状态寄存器。具体布局和内容是驱动程序特定的。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/addr_idx`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: 选择要处理的地址比较器或比较器对。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/addr_acctype`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 与 @addr_idx 结合使用。指定正在配置的地址比较器的特征，例如访问类型、要跟踪的指令类型、要触发的处理器上下文 ID 等。访问类型寄存器中的各个字段可能因跟踪实体的版本而异。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/addr_range`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 与 @addr_idx 结合使用。指定要触发的地址范围。包含或排除在相应的访问类型寄存器中指定。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/addr_single`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 与 @addr_idx 结合使用。指定要触发的单个地址，受相应访问类型寄存器的配置选项高度影响。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/addr_start`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 与 @addr_idx 结合使用。指定要开始跟踪的单个地址，受相应访问类型寄存器的配置选项高度影响。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/addr_stop`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 与 @addr_idx 结合使用。指定要停止跟踪的单个地址，受相应访问类型寄存器的配置选项高度影响。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/cntr_idx`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 指定要处理的计数器。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/cntr_event`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 与 cntr_idx 结合使用，提供对计数器事件寄存器的访问。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/cntr_val`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 与 cntr_idx 结合使用，提供对计数器值寄存器的访问。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/cntr_rld_val`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 与 cntr_idx 结合使用，提供对计数器重载值寄存器的访问。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/cntr_rld_event`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 与 cntr_idx 结合使用，提供对计数器重载事件寄存器的访问。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/ctxid_idx`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 指定要选择的上下文 ID 寄存器的索引。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/ctxid_mask`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 应用于所有上下文 ID 比较器的掩码。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/ctxid_val`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 与 ctxid_idx 一起使用，指定要触发的上下文 ID。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/enable_event`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 定义触发跟踪的事件。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/etmsr`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 提供对 ETM 状态寄存器的访问，该寄存器保存有关某些事件的编程信息和状态。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/fifofull_level`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 在认为 FIFO 已满之前剩余的字节数。根据跟踪器的版本，还可以持有数据抑制的阈值。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/mode`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 与驱动程序的“模式”字段接口，控制跟踪实体的各个方面，如时间戳、上下文 ID 大小和周期精确跟踪。驱动程序特定，并且可能根据驱动程序而变化。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/nr_addr_cmp`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (R) 提供在跟踪单元上可访问的地址比较器对的数量，如寄存器 ETMCCR 的位 3:0 所指定。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/nr_cntr`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (R) 提供在跟踪单元上可访问的计数器的数量，如寄存器 ETMCCR 的位 15:13 所指定。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/nr_ctxid_cmp`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (R) 提供在跟踪单元上可用的上下文 ID 比较器的数量，如寄存器 ETMCCR 的位 25:24 所指定。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/reset`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (W) 取消跟踪单元上的所有配置，并将其设置回其启动配置。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/seq_12_event`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 定义导致定序器从状态 1 转换到状态 2 的事件。

**What**: `/sys/bus/coresight/devices/<memory_map>.[etm|ptm]/seq_13_event`

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 定义导致定序器从状态 1 转换到状态 3 的事件。**What**: /sys/bus/coresight/devices/<memory_map>.[etm|ptm]/seq_21_event

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 定义导致定序器从状态 2 转换到状态 1 的事件。

**What**: /sys/bus/coresight/devices/<memory_map>.[etm|ptm]/seq_23_event

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 定义导致定序器从状态 2 转换到状态 3 的事件。

**What**: /sys/bus/coresight/devices/<memory_map>.[etm|ptm]/seq_31_event

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 定义导致定序器从状态 3 转换到状态 1 的事件。

**What**: /sys/bus/coresight/devices/<memory_map>.[etm|ptm]/seq_32_event

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 定义导致定序器从状态 3 转换到状态 2 的事件。

**What**: /sys/bus/coresight/devices/<memory_map>.[etm|ptm]/curr_seq_state

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (R) 保存定序器的当前状态。

**What**: /sys/bus/coresight/devices/<memory_map>.[etm|ptm]/sync_freq

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 保存跟踪同步频率值 - 必须考虑各种实现行为进行编程。

**What**: /sys/bus/coresight/devices/<memory_map>.[etm|ptm]/timestamp_event

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 定义请求在跟踪流中插入时间戳的事件。

**What**: /sys/bus/coresight/devices/<memory_map>.[etm|ptm]/traceid

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 保存将出现在来自此跟踪实体的跟踪流中的跟踪 ID。

**What**: /sys/bus/coresight/devices/<memory_map>.[etm|ptm]/trigger_event

**Date**: November 2014

**KernelVersion**: 3.19

**Contact**: Mathieu Poirier <mathieu.poirier@linaro.org>

**Description**: (RW) 定义控制触发的事件。