**What: /sys/module/hid_logitech/drivers/hid:logitech/<dev>/range**

**Date: July 2011**

**KernelVersion: 3.2**

**Contact: Michal Malý <madcatxster@gmail.com>**

**Description: 显示方向盘的最小、最大和当前范围。在最小和最大边界内写入一个值将设置轮子的范围。**

**What: /sys/bus/hid/drivers/logitech/<dev>/alternate_modes**

**Date: Feb 2015**

**KernelVersion: 4.1**

**Contact: Michal Malý <madcatxster@gmail.com>**

**Description: 显示轮子支持的一组备用模式。每个模式如下列出：

  - Tag: 模式名称

  - 当前活动模式用星号标记。列表还包含一个抽象项“native”，它始终表示轮子的本机模式。回显模式标签将轮子切换到相应模式。根据轮子的确切型号，并非所有列出的模式都始终可选。如果轮子无法切换到所需模式，将返回 -EINVAL，并在内核日志中伴有解释性消息。

  - 此条目不会为只有一种模式的设备创建。

  - 当前支持的模式切换：

    - Driving Force Pro:

      - DF-EX --> DFP

    - G25:

      - DF-EX --> DFP --> G25

    - G27:

      - DF-EX <*> DFP <-> G25 <-> G27

      - DF-EX <*--------> G25 <-> G27

      - DF-EX <*----------------> G27

    - DFGT:

      - DF-EX <*> DFP <-> DFGT

      - DF-EX <*--------> DFGT

  - * hid_logitech 模块必须加载 lg4ff_no_autoswitch=1 参数集，以便切换到 DF-EX 模式才能工作。**

**What: /sys/bus/hid/drivers/logitech/<dev>/real_id**

**Date: Feb 2015**

**KernelVersion: 4.1**

**Contact: Michal Malý <madcatxster@gmail.com>**

**Description: 无论轮子可能切换到何种备用模式，都显示轮子的真实型号。

  - 这是一个只读值。

  - 此条目不会为只有一种模式的设备创建。**