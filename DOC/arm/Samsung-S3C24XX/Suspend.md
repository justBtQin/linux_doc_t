# S3C24XX Suspend Support

=======================

## Introduction

The S3C24XX 支持低功耗暂停模式，在此模式下，SDRAM 保持自刷新模式，除必要的外围模块外，其他模块均断电。有关此模式工作原理的更多信息，请查看三星的相关 CPU 数据手册。

## Requirements

1. 能够支持必要的恢复操作的引导加载程序。

2. 支持至少 1 个用于恢复的源。

3. 内核中启用 CONFIG_PM。

4. 同时要断电的任何外围设备都需要暂停/恢复支持。

## Resuming

S3C2410 用户手册定义了将 CPU 发送至睡眠状态以及恢复的过程。Linux 代码的默认行为是将 GSTATUS3 寄存器设置为恢复 Linux 操作的代码的物理地址。

GSTATUS4 目前在睡眠代码中未被使用，可自由用于其他目的（例如，EB2410ITX 用于在此保存内存配置）。

## Machine Support

机器特定功能必须调用 s3c_pm_init()函数，以表明其引导加载程序能够恢复。这可以像在机器定义中添加以下内容一样简单：

```c
INITMACHINE(s3c_pm_init)
```
如果板需要为电源管理支持设置其他任何内容，则可以在调用 s3c_pm_init 之前进行自己的设置。

目前不支持覆盖保存恢复地址的默认方法，如果您的板需要，请联系维护者并讨论所需的内容。

注意，添加 late_initcall()的原始方法是错误的，并且最终将初始化所有已编译机器的 pm 初始化！

以下是用于测试从 IRQ_EINT0 的下降沿唤醒的代码示例：

```c
static irqreturn_t button_irq(int irq, void *pw) {
    return IRQ_HANDLED;
}

statuc void __init machine_init(void) {
   ... 
    request_irq(IRQ_EINT0, button_irq, IRQF_TRIGGER_FALLING,
               "button-irq-eint0", NULL);

    enable_irq_wake(IRQ_EINT0);

    s3c_pm_init();
}
```

## Debugging

使用 PM 暂停时，有几个重要事项需要记住：

1. UART 驱动程序在暂停时会禁用 UART 模块的时钟，这意味着使用 printascii()或类似的直接访问 UART 的方式将导致调试停止。

2. 虽然 pm 代码本身会尝试重新启用 UART 时钟，但应注意 UART 所依赖的任何外部时钟源在此时仍应启用。

3. 如果在恢复路径中进行任何调试，则在使用之前必须设置相关的时钟和外围设备（即引导加载程序）。例如，如果从 UART 发送一个字符，则必须事先设置波特率和 UART 控制。

## Configuration

`System Type`中的 S3C2410 特定配置定义了 S3C2410 暂停和恢复支持的各种配置方面。

`S3C2410 PM Suspend debug`

此选项在实际暂停之前和之后向串行控制台打印消息，提供有关正在发生的事情的详细信息。

`S3C2410 PM Suspend Memory CRC`

允许在暂停之前和之后对整个内存进行校验和计算，以查看内存内容是否有任何损坏。

注意，计算 CRC 的时间取决于 CPU 速度和内存大小。对于 200MHz 的 S3C2410 上的 64Mbyte RAM 区域，完成此操作大约需要 4 秒。

此支持需要启用 CRC32 函数。

`S3C2410 PM Suspend CRC Chunksize (KiB)`

定义每个 CRC 块覆盖的内存大小。较小的值将意味着 CRC 数据块将占用更多内存，但将以更高的精度识别任何故障。

## Document Author

Ben Dooks，版权 2004 Simtec Electronics