**What**：

- `/sys/bus/iio/devices/iio:deviceX/pll2_feedback_clk_present`

- `/sys/bus/iio/devices/iio:deviceX/pll2_reference_clk_present`

- `/sys/bus/iio/devices/iio:deviceX/pll1_reference_clk_a_present`

- `/sys/bus/iio/devices/iio:deviceX/pll1_reference_clk_b_present`

- `/sys/bus/iio/devices/iio:deviceX/pll1_reference_clk_test_present`

- `/sys/bus/iio/devices/iio:deviceX/vcxo_clk_present`

**KernelVersion**：3.4.0

**Contact**：linux-iio@vger.kernel.org

**Description**：

读取这些文件会返回“1”或“0”。“1”表示所提及的时钟存在，“0”表示时钟缺失。

**What**：

- `/sys/bus/iio/devices/iio:deviceX/pllY_locked`

**KernelVersion**：3.4.0

**Contact**：linux-iio@vger.kernel.org

**Description**：

读取这些文件会返回“1”或“0”。“1”表示 pllY 已锁定。

**What**：

- `/sys/bus/iio/devices/iio:deviceX/sync_dividers`

**KernelVersion**：3.4.0

**Contact**：linux-iio@vger.kernel.org

**Description**：

写入“1”会触发时钟分配同步功能。所有分频器将被重置，通道将从其预定义的相位偏移（out_altvoltageY_phase）开始。写入此文件的效果如同将外部/SYNC 引脚拉低。