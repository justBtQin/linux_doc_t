## Supporting multiple CPU idle levels in kernel

cpuidle

通用信息：

当今的各种 CPU 支持多种空闲级别，这些空闲级别在空闲期间具有不同的退出延迟和功耗。cpuidle 是一个通用的内核基础设施，它将空闲策略（调节器）与空闲机制（驱动程序）分离，并提供了一个标准化的基础设施来支持调节器和驱动程序的独立开发。

cpuidle 位于 drivers/cpuidle 下。

引导选项：

"cpuidle_sysfs_switch"

启用 /sys/devices/system/cpu/cpuidle/ 中的当前调节器接口，

可用于在运行时切换调节器。此引导选项仅用于开发者测试。在正常使用中，内核根据调节器评级选择最佳调节器。另请参阅本目录中的 sysfs.txt。