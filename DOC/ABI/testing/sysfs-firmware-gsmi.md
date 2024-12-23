**What**: `/sys/firmware/gsmi`

**Date**: March 2011

**Contact**: Mike Waychison <mikew@google.com>

**Description**:

一些在谷歌内部使用的服务器具有固件，该固件通过显式的 SMI 触发提供回调功能。一些回调类似于 EFI 运行时服务页面提供的回调，但由于历史原因，使用了这个不同的入口点。

`gsmi`驱动程序实现了这些固件回调的内核抽象。目前，此功能仅限于处理系统事件日志和访问存储在 NVRAM 中的 EFI 风格变量。

**布局**:

`/sys/firmware/gsmi/vars`:

此目录具有相同的布局（和底层实现与`/sys/firmware/efi/vars`相同。有关如何与此结构交互的更多信息，请参阅`Documentation/ABI/*/sysfs-firmware-efi-vars`。

`/sys/firmware/gsmi/append_to_eventlog` - 只写:

此文件接受一个二进制块，并将其传递给固件以进行时间戳标记并附加到系统事件日志中。二进制格式由固件解释，可能因平台而异。内核强制的唯一要求是块以 32 位主机字节序类型为前缀，该类型用作固件调用的一部分。

`/sys/firmware/gsmi/clear_config` - 只写:

向此文件写入任何值都将导致整个固件配置重置为“出厂默认值”。调用者应假设需要重新启动才能清除配置。

`/sys/firmware/gsmi/clear_eventlog` - 只写:

此文件用于清除系统事件日志的一部分/全部。写入的值应为 1 到 100 之间的整数值（以 ASCII 表示），代表要清除的日志部分。并非所有平台都支持部分清除，并且如果固件不喜欢提交的部分，对此文件的写入将出错。

调用者应假设此操作完成需要重新启动。