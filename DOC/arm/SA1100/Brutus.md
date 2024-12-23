**Brutus 是针对英特尔制造的 SA1100 的评估平台。**

更多细节，请查看：

[http://developer.intel.com](http://developer.intel.com)

要为 Brutus 进行编译，你必须发出以下命令：

```
make brutus_config
make config
[接受所有默认值]
make zImage
```

生成的内核将位于`linux/arch/arm/boot/zImage`。此文件必须在 Brutus 的内存中加载到 0xc0008000 位置，并且在入口处也从 0xc0008000 开始执行，同时寄存器`r0 = 0`和`r1 = 16`。

但在执行内核之前，还必须在内存中加载一个 ramdisk 映像。使用内存地址 0xd8000000 进行此操作。请注意，包含（压缩）ramdisk 映像的文件不得超过 4MB。

通常，你需要`angelboot`来加载内核。应使用以下`angelboot.opt`文件：

```
----- begin angelboot.opt -----
base 0xc0008000
entry 0xc0008000
r0 0x00000000
r1 0x00000010
device /dev/ttyS0
options "9600 8N1"
baud 115200
otherfile ramdisk_img.gz
otherbase 0xd8000000
----- end angelboot.opt -----
```

然后使用以下命令加载内核和 ramdisk：

```
angelboot -f angelboot.opt zImage
```

第一个 Brutus 串行端口（假定与主机 PC 上的/dev/ttyS0 相连）被`angel`用于加载内核和 ramdisk 映像。串行控制台通过第二个 Brutus 串行端口提供。要访问它，你可以使用配置为/dev/ttyS1、9600 波特率、8N1、无流控制的`minicom`。

当前支持：

- RS232 串行端口

- 音频输出

- LCD 屏幕

- 键盘

实际的 Brutus 支持可能在没有额外补丁的情况下不完整。如果存在此类补丁，应从`ftp.netwinder.org/users/n/nico`获取。仍然缺少完整的 PCMCIA 支持，尽管可以通过一些修改在启动时驱动已插入的卡来破解一些驱动程序。

欢迎任何贡献。请将补丁发送至`nico@fluxnic.net`。

祝玩得愉快！