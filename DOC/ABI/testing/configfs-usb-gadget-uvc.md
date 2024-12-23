**What: /config/usb-gadget/gadget/functions/uvc.name**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: UVC 功能目录

    - **streaming_maxburst**: - 0..15 (仅 ss)

    - **streaming_maxpacket**: - 1..1023 (fs)，1..3072 (hs/ss)

    - **streaming_interval**: - 1..16

**What: /config/usb-gadget/gadget/functions/uvc.name/control**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 控制描述符

**What: /config/usb-gadget/gadget/functions/uvc.name/control/class**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 类描述符

**What: /config/usb-gadget/gadget/functions/uvc.name/control/class/ss**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 超级速度控制类描述符

**What: /config/usb-gadget/gadget/functions/uvc.name/control/class/fs**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 全速控制类描述符

**What: /config/usb-gadget/gadget/functions/uvc.name/control/terminal**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 终端描述符

**What: /config/usb-gadget/gadget/functions/uvc.name/control/terminal/output**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 输出终端描述符

**What: /config/usb-gadget/gadget/functions/uvc.name/control/terminal/output/default**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 默认输出终端描述符

    - 所有属性为只读：

    - **iTerminal**: - 字符串描述符的索引

    - **bSourceID**: - 此终端连接到的终端的 ID

    - **bAssocTerminal**: - 此输出终端关联的输入终端的 ID

    - **wTerminalType**: - 终端类型

    - **bTerminalID**: - 此终端的非零 ID

**What: /config/usb-gadget/gadget/functions/uvc.name/control/terminal/camera**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 相机终端描述符

**What: /config/usb-gadget/gadget/functions/uvc.name/control/terminal/camera/default**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 默认相机终端描述符

    - 所有属性为只读：

    - **bmControls**: - 指定视频流支持的控制的位图

    - **wOcularFocalLength**: - Locular 的值

    - **wObjectiveFocalLengthMax**: - Lmin 的值

    - **wObjectiveFocalLengthMin**: - Lmax 的值

    - **iTerminal**: - 字符串描述符的索引

    - **bAssocTerminal**: - 此终端连接到的输出终端的 ID

    - **wTerminalType**: - 终端类型

    - **bTerminalID**: - 此终端的非零 ID

**What: /config/usb-gadget/gadget/functions/uvc.name/control/processing**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 处理单元描述符

**What: /config/usb-gadget/gadget/functions/uvc.name/control/processing/default**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 默认处理单元描述符

    - 所有属性为只读：

    - **iProcessing**: - 字符串描述符的索引

    - **bmControls**: - 指定视频流支持的控制的位图

    - **wMaxMultiplier**: - 最大数字放大倍数 x100

    - **bSourceID**: - 此单元连接到的终端的 ID

    - **bUnitID**: - 此单元的非零 ID

**What: /config/usb-gadget/gadget/functions/uvc.name/control/header**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 控制头描述符

**What: /config/usb-gadget/gadget/functions/uvc.name/control/header/name**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 特定控制头描述符

    - dwClockFrequency

    - bcdUVC

**What: /config/usb-gadget/gadget/functions/uvc.name/streaming**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 流描述符

**What: /config/usb-gadget/gadget/functions/uvc.name/streaming/class**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 流类描述符

**What: /config/usb-gadget/gadget/functions/uvc.name/streaming/class/ss**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 超级速度流类描述符

**What: /config/usb-gadget/gadget/functions/uvc.name/streaming/class/hs**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 高速流类描述符

**What: /config/usb-gadget/gadget/functions/uvc.name/streaming/class/fs**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 全速流类描述符

**What: /config/usb-gadget/gadget/functions/uvc.name/streaming/color_matching**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 颜色匹配描述符

**What: /config/usb-gadget/gadget/functions/uvc.name/streaming/color_matching/default**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 默认颜色匹配描述符

    - 所有属性为只读：

    - **bMatrixCoefficients**: - 用于从颜色原色计算亮度和色度值的矩阵

    - **bTransferCharacteristics**: - 源图像的光电转换特性，也称为伽马函数

    - **bColorPrimaries**: - 颜色原色和参考白色

**What: /config/usb-gadget/gadget/functions/uvc.name/streaming/mjpeg**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: MJPEG 格式描述符

**What: /config/usb-gadget/gadget/functions/uvc.name/streaming/mjpeg/name**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 特定 MJPEG 格式描述符

    - 除 bmaControls 和 bDefaultFrameIndex 外，所有属性为只读：

    - **bmaControls**: - 此格式在流头中的 bmaControls 的数据

    - **bmInterfaceFlags**: - 指定隔行信息，只读

    - **bAspectRatioY**: - 图片纵横比的 X 维度，只读

    - **bAspectRatioX**: - 图片纵横比的 Y 维度，只读

    - **bmFlags**: - 此格式的特征，只读

    - **bDefaultFrameIndex**: - 此流的最佳帧索引

**What: /config/usb-gadget/gadget/functions/uvc.name/streaming/mjpeg/name/name**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 特定 MJPEG 帧描述符

    - **dwFrameInterval**: - 指示如何编程帧间隔；可以指定由换行符分隔的多个值

    - **dwDefaultFrameInterval**: - 设备希望用作默认的帧间隔

    - **dwMaxVideoFrameBufferSize**: - 压缩器为视频帧或静止图像生成的最大字节数

    - **dwMaxBitRate**: - 最短帧间隔的最大比特率（bps）

    - **dwMinBitRate**: - 最长帧间隔的最小比特率（bps）

    - **wHeight**: - 解码位图帧的高度（px）

    - **wWidth**: - 解码位图帧的宽度（px）

    - **bmCapabilities**: - 静止图像支持，固定帧率支持

**What: /config/usb-gadget/gadget/functions/uvc.name/streaming/uncompressed**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 未压缩格式描述符

**What: /config/usb-gadget/gadget/functions/uvc.name/streaming/uncompressed/name**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 特定未压缩格式描述符

    - **bmaControls**: - 此格式在流头中的 bmaControls 的数据

    - **bmInterfaceFlags**: - 指定隔行信息，只读

    - **bAspectRatioY**: - 图片纵横比的 X 维度，只读

    - **bAspectRatioX**: - 图片纵横比的 Y 维度，只读

    - **bDefaultFrameIndex**: - 此流的最佳帧索引

    - **bBitsPerPixel**: - 解码视频帧中用于指定颜色的每像素位数

    - **guidFormat**: - 用于标识流编码格式的全局唯一 ID

**What: /config/usb-gadget/gadget/functions/uvc.name/streaming/uncompressed/name/name**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 特定未压缩帧描述符

    - **dwFrameInterval**: - 指示如何编程帧间隔；可以指定由换行符分隔的多个值

    - **dwDefaultFrameInterval**: - 设备希望用作默认的帧间隔

    - **dwMaxVideoFrameBufferSize**: - 压缩器为视频帧或静止图像生成的最大字节数

    - **dwMaxBitRate**: - 最短帧间隔的最大比特率（bps）

    - **dwMinBitRate**: - 最长帧间隔的最小比特率（bps）

    - **wHeight**: - 解码位图帧的高度（px）

    - **wWidth**: - 解码位图帧的宽度（px）

    - **bmCapabilities**: - 静止图像支持，固定帧率支持

**What: /config/usb-gadget/gadget/functions/uvc.name/streaming/header**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 流头描述符

**What: /config/usb-gadget/gadget/functions/uvc.name/streaming/header/name**

- **Date**: Dec 2014

- **KernelVersion**: 3.20

- **Description**: 特定流头描述符

    - 所有属性为只读：

    - **bTriggerUsage**: - 主机软件将如何响应硬件触发中断事件

    - **bTriggerSupport**: - 指定是否支持硬件触发的标志

    - **bStillCaptureMethod**: - 支持的静止图像捕获方法

    - **bTerminalLink**: - 此接口的视频端点连接到的输出终端的 ID

    - **bmInfo**: - 此视频流接口的功能