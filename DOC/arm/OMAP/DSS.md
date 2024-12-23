目录：

enabled  0=关闭，1=打开

output_size 宽度,高度

rotate  0-3 旋转，对应 0 度、90 度、180 度、270 度

name

input_type 输入类型，如 dpi、dsi 等

panel_type 面板类型

panel_id 面板 ID

timing  显示时序相关参数

backlight 背光相关参数

power_mode 电源模式

gamma 伽马值相关参数

edid 扩展显示标识数据

hpd 热插拔检测相关参数

v_sync 垂直同步相关参数

h_sync 水平同步相关参数

de 数据使能相关参数

pixel_clock 像素时钟相关参数

以上是 OMAP2/3 显示子系统的相关内容转换为 Markdown 格式后的内容。如果你还有其他需求，请继续向我提问。目录：

ctrl_name	控制器名称

mirror		0=关闭，1=开启

update_mode	0=关闭，1=自动，2=手动

enabled		0=关闭，1=开启

name

rotate		旋转 0-3 对应 0、90、180、270 度

timings		显示时序（像素时钟，x 分辨率/水平前肩/水平后肩/水平同步宽度，y 分辨率/垂直前肩/垂直后肩/垂直同步宽度）

		写入时，对于电视输出接受两种特殊时序：

		"pal"和"ntsc"

panel_name

tear_elim	撕裂消除 0=关闭，1=开启

output_type	输出类型（仅视频编码器）："composite"或"svideo"

在<debugfs>/omapdss/ 下还有一些 debugfs 文件，它们显示关于时钟和寄存器的信息。

# 示例

--------

以下定义是为下面的示例而制定的：

ovl0=/sys/devices/platform/omapdss/overlay0

ovl1=/sys/devices/platform/omapdss/overlay1

ovl2=/sys/devices/platform/omapdss/overlay2

mgr0=/sys/devices/platform/omapdss/manager0

mgr1=/sys/devices/platform/omapdss/manager1

lcd=/sys/devices/platform/omapdss/display0

dvi=/sys/devices/platform/omapdss/display1

tv=/sys/devices/platform/omapdss/display2

fb0=/sys/class/graphics/fb0

fb1=/sys/class/graphics/fb1

fb2=/sys/class/graphics/fb2

# OMAP3 SDP 上的默认设置

--------------------------

这是 OMAP3 SDP 板上的默认设置。所有平面都连接到 LCD。DVI 和电视输出未使用。从左到右的列是：

帧缓冲区，覆盖层，覆盖层管理器，显示器。帧缓冲区由 omapfb 处理，其余由 DSS 处理。

FB0 --- GFX  -\            DVI

FB1 --- VID1 --+- LCD ---- LCD

FB2 --- VID2 -/   TV ----- TV

# 示例：从 LCD 切换到 DVI

----------------------

w=`cat $dvi/timings | cut -d "," -f 2 | cut -d "/" -f 1`

h=`cat $dvi/timings | cut -d "," -f 3 | cut -d "/" -f 1`

echo "0" > $lcd/enabled

echo "" > $mgr0/display

fbset -fb /dev/fb0 -xres $w -yres $h -vxres $w -vyres $h

在这一点上，你必须从 omap 板上切换 dvi/lcd 双掷开关

echo "dvi" > $mgr0/display

echo "1" > $dvi/enabled

之后，配置如下：

FB0 --- GFX  -\         -- DVI

FB1 --- VID1 --+- LCD -/   LCD

FB2 --- VID2 -/   TV ----- TV

# 示例：将 GFX 覆盖层克隆到 LCD 和 TV

-------------------------------

w=`cat $tv/timings | cut -d "," -f 2 | cut -d "/" -f 1`

h=`cat $tv/timings | cut -d "," -f 3 | cut -d "/" -f 1`

echo "0" > $ovl0/enabled

echo "0" > $ovl1/enabled

echo "" > $fb1/overlays

echo "0,1" > $fb0/overlays

echo "$w,$h" > $ovl1/output_size

echo "tv" > $ovl1/manager

echo "1" > $ovl0/enabled

echo "1" > $ovl1/enabled

echo "1" > $tv/enabled

之后，配置如下（仅显示相关部分）：

FB0 +-- GFX  ---- LCD ---- LCD

     \- VID1 ---- TV  ---- TV

# 杂项注意事项

----------

OMAP FB 使用标准的 DMA 分配器分配帧缓冲区内存。你可以启用连续内存分配器（CONFIG_CMA）来改进 DMA 分配器，如果启用了 CMA，你可以使用"cma="内核参数来增加 CMA 的全局内存区域。

使用 DSI DPLL 生成像素时钟，可以产生 86.5MHz 的像素时钟（最大可能），并且使用该时钟，你可以从 DVI 获得 1280x1024@57 的输出。

旋转和镜像目前仅支持 RGB565 和 RGB8888 模式。VRFB 不支持镜像。

VRFB 旋转比非旋转帧缓冲区需要更多的内存，因此在使用 VRFB 旋转之前，你可能需要增加你的 vram 设置。此外，如果许多应用程序不注意所有帧缓冲区参数，它们可能无法与 VRFB 一起工作。

# 内核引导参数

---------------------

omapfb.mode=<显示>:<模式>[,...]

	- 指定显示的默认视频模式。例如，

	  "dvi:800x400MR-24@60"。 请参阅 drivers/video/modedb.c。

	  还有两种特殊模式："pal"和"ntsc"，可用于电视输出。

omapfb.vram=<fbnum>:<大小>[@<物理地址>][,...]

	- 为帧缓冲区分配的 VRAM。通常，omapfb 根据显示大小分配 vram。使用此参数，你可以手动分配更多或定义每个帧缓冲区的物理地址。例如，

	  "1:4M"为 fb1 分配 4M。

omapfb.debug=<y|n>

	- 启用调试打印。你必须在内核配置中启用 OMAPFB 调试支持。

omapfb.test=<y|n>

	- 每当帧缓冲区设置更改时，将测试模式绘制到帧缓冲区。 你需要在内核配置中启用 OMAPFB 调试支持。

omapfb.vrfb=<y|n>

	- 对所有帧缓冲区使用 VRFB 旋转。

omapfb.rotate=<角度>

	- 应用于所有帧缓冲区的默认旋转。

	  0 - 0 度旋转

	  1 - 90 度旋转

	  2 - 180 度旋转

	  3 - 270 度旋转

omapfb.mirror=<y|n>

	- 所有帧缓冲区的默认镜像。仅适用于 DMA 旋转。

omapdss.def_disp=<显示>

	- 默认显示的名称，所有覆盖层都将连接到该显示。 常见示例是"lcd"或"tv"。

omapdss.debug=<y|n>

	- 启用调试打印。你必须在内核配置中启用 DSS 调试支持。

# 待办事项

----

DSS 锁定

错误检查

- 缺少很多检查或仅实现为 BUG()

DSI 的系统 DMA 更新

- 可用于 RGB16 和 RGB24P 模式。可能不适用于 RGB24U（如何跳过空字节？）

OMAP1 支持

- 不确定是否需要