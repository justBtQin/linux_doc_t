**Note**: 设备之间共享的属性存储在由符号链接`device/`指向的目录中。例如：属性`/sys/class/cxl/afu0.0s/irqs_max`的真实路径是`/sys/class/cxl/afu0.0s/device/irqs_max`，即`/sys/class/cxl/afu0.0/irqs_max`。

**从设备上下文（例如 /sys/class/cxl/afu0.0s）：**

**内容**：`/sys/class/cxl/<afu>/irqs_max`

**日期**：2014 年 9 月

**联系人**：linuxppc-dev@lists.ozlabs.org

**描述**：可读/写

                用户空间可以请求的最大中断数的十进制值。探测时的默认值是硬件可以支持的最大值（例如 2037）。写入值将使用户空间应用程序限制为该数量的用户空间中断。必须大于等于`irqs_min`。 

**内容**：`/sys/class/cxl/<afu>/irqs_min`

**日期**：2014 年 9 月

**联系人**：linuxppc-dev@lists.ozlabs.org

**描述**：只读

                用户空间在 CXL_START_WORK ioctl 中必须请求的最小中断数的十进制值。用户空间可以在 START_WORK IOCTL 中省略`num_interrupts`字段以自动获取此最小值。 

**内容**：`/sys/class/cxl/<afu>/mmio_size`

**日期**：2014 年 9 月

**联系人**：linuxppc-dev@lists.ozlabs.org

**描述**：只读

                用户空间可以映射的 MMIO 空间大小的十进制值。 

**内容**：`/sys/class/cxl/<afu>/modes_supported`

**日期**：2014 年 9 月

**联系人**：linuxppc-dev@lists.ozlabs.org

**描述**：只读

                此 AFU 支持的模式列表。每行一个。有效条目为："dedicated_process"和"afu_directed"

**内容**：`/sys/class/cxl/<afu>/mode`

**日期**：2014 年 9 月

**联系人**：linuxppc-dev@lists.ozlabs.org

**描述**：可读/写

                AFU 当前使用的当前模式。将是`modes_supported`中给出的模式之一。写入将更改模式，前提是没有用户上下文附加。 

**内容**：`/sys/class/cxl/<afu>/prefault_mode`

**日期**：2014 年 9 月

**联系人**：linuxppc-dev@lists.ozlabs.org

**描述**：可读/写

                在执行 START_WORK ioctl 时，设置段表中预取的模式。可能的值：

                        无：无预取（默认）

                        work_element_descriptor：将工作元素描述符视为有效地址并预取它指向的内容。

                        所有：所有调用 START_WORK 的段进程映射。 

**内容**：`/sys/class/cxl/<afu>/reset`

**日期**：2014 年 9 月

**联系人**：linuxppc-dev@lists.ozlabs.org

**描述**：只写

                在此处写入 1 将在 AFU 上没有活动上下文的情况下重置 AFU。 

**内容**：`/sys/class/cxl/<afu>/api_version`

**日期**：2014 年 9 月

**联系人**：linuxppc-dev@lists.ozlabs.org

**描述**：只读

                内核/用户 API 的当前版本的十进制值。 

**内容**：`/sys/class/cxl/<afu>/api_version_compatible`

**日期**：2014 年 9 月

**联系人**：linuxppc-dev@lists.ozlabs.org

**描述**：只读

                此内核支持的用户空间 API 的最低版本的十进制值。 

**AFU 配置记录（例如 /sys/class/cxl/afu0.0/cr0）：**

AFU 可以可选地导出一个或多个类似 PCIe 的配置记录，称为 AFU 配置记录，它们将在此处显示（如果存在）。 

**内容**：`/sys/class/cxl/<afu>/cr<config num>/vendor`

**日期**：2015 年 2 月

**联系人**：linuxppc-dev@lists.ozlabs.org

**描述**：只读

                此 AFU 配置记录中找到的供应商 ID 的十六进制值。 

**内容**：`/sys/class/cxl/<afu>/cr<config num>/device`

**日期**：2015 年 2 月

**联系人**：linuxppc-dev@lists.ozlabs.org

**描述**：只读

                此 AFU 配置记录中找到的设备 ID 的十六进制值。 

**内容**：`/sys/class/cxl/<afu>/cr<config num>/class`

**日期**：2015 年 2 月

**联系人**：linuxppc-dev@lists.ozlabs.org

**描述**：只读

                此 AFU 配置记录中找到的类代码的十六进制值。 

**内容**：`/sys/class/cxl/<afu>/cr<config num>/config`

**日期**：2015 年 2 月

**联系人**：linuxppc-dev@lists.ozlabs.org

**描述**：只读

                此二进制文件提供对 AFU 配置记录的原始访问。格式预计与 PCIe 规范定义的标准或扩展配置空间匹配。 

**主设备上下文（例如 /sys/class/cxl/afu0.0m）：**

**内容**：`/sys/class/cxl/<afu>m/mmio_size`

**日期**：2014 年 9 月

**联系人**：linuxppc-dev@lists.ozlabs.org

**描述**：只读

                用户空间可以映射的 MMIO 空间大小的十进制值。这包括所有从设备上下文的空间。 

**内容**：`/sys/class/cxl/<afu>m/pp_mmio_len`

**日期**：2014 年 9 月

**联系人**：linuxppc-dev@lists.ozlabs.org

**描述**：只读

                每个进程 MMIO 空间长度的十进制值。 

**内容**：`/sys/class/cxl/<afu>m/pp_mmio_off`

**日期**：2014 年 9 月

**联系人**：linuxppc-dev@lists.ozlabs.org

**描述**：只读

                每个进程 MMIO 空间偏移的十进制值。 

**卡信息（例如 /sys/class/cxl/card0）：**

**内容**：`/sys/class/cxl/<card>/caia_version`

**日期**：2014 年 9 月

**联系人**：linuxppc-dev@lists.ozlabs.org

**描述**：只读

                标识卡实现的 CAIA 版本。 

**内容**：`/sys/class/cxl/<card>/psl_revision`

**日期**：2014 年 9 月

**联系人**：linuxppc-dev@lists.ozlabs.org

**描述**：只读

                标识 PSL 的修订级别。 

**内容**：`/sys/class/cxl/<card>/base_image`

**日期**：2014 年 9 月

**联系人**：linuxppc-dev@lists.ozlabs.org

**描述**：只读

                对于支持可加载 PSL 的设备，标识基础映像的修订级别。对于 FPGA，此字段标识在初始程序加载期间加载到适配器闪存中的映像。 

**内容**：`/sys/class/cxl/<card>/image_loaded`

**日期**：2014 年 9 月

**联系人**：linuxppc-dev@lists.ozlabs.org

**描述**：只读

                将根据卡上加载的映像返回"user"或"factory"。 

**内容**：`/sys/class/cxl/<card>/load_image_on_perst`

**日期**：2014 年 12 月

**联系人**：linuxppc-dev@lists.ozlabs.org

**描述**：可读/写

                有效条目为"none"、"user"和"factory"。

                "none"表示 PERST 不会导致映像加载到卡上。需要电源循环才能加载映像。

                "none"在调试时可能很有用，因为跟踪数组得以保留。

                "user"和"factory"表示 PERST 将导致加载用户或工厂映像。

                默认是在 PERST 时重新加载卡上已加载的任何映像。 

**内容**：`/sys/class/cxl/<card>/reset`

**日期**：2014 年 10 月

**联系人**：linuxppc-dev@lists.ozlabs.org

**描述**：只写

                写入 1 将向卡发出 PERST，这可能会导致卡根据`load_image_on_perst`重新加载 FPGA。