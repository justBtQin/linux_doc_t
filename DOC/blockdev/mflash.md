本文档描述了 Linux 中的 m[g]flash 支持。

# 目录

1. 概述

2. 预留区域配置

3. mflash 平台驱动程序注册示例

# 1. 概述

Mflash 和 gflash 是嵌入式闪存驱动器。唯一的区别是 mflash 是 MCP（多芯片封装）设备。这两种设备的操作方式完全相同。因此，其余的 mflash 同时代表 mflash 和 gflash。

内部上，mflash 具有 NAND 闪存和其他硬件逻辑，并支持 2 种不同的操作（ATA、IO）模式。ATA 模式不需要任何新的驱动程序，目前在标准 IDE 子系统下运行良好。实际上它是一个芯片级固态硬盘。IO 模式是针对没有 IDE 接口的主机的类似 ATA 的自定义模式。

以下是关于 IO 模式的简要描述。

A. IO 模式基于 ATA 协议，并使用一些自定义命令。（读取确认、写入确认）

B. IO 模式使用 SRAM 总线接口。

C. IO 模式支持 4kB 的启动区域，因此主机可以从 mflash 启动。

#2. 预留区域配置

如果主机从 mflash 启动，通常需要原始区域用于引导加载程序映像。mflash 的所有块设备操作都将以此值作为起始偏移量。请注意，引导加载程序的预留区域大小和内核配置值必须相同。

# 3. mflash 平台驱动程序注册示例

使 mflash 正常工作非常直接。只需将平台设备相关内容添加到主板配置文件中即可。以下是一些伪示例。

```c
static struct mg_drv_data mflash_drv_data = {
    /* 如果要使用轮询驱动程序，请设置为 1 */
   .use_polling = 0,
    /* 设备属性 */
   .dev_attr = MG_BOOT_DEV
};

static struct resource mg_mflash_rsc[] = {
    /* mflash 的基地址 */
    [0] = {
       .start = 0x08000000,
       .end = 0x08000000 + SZ_64K - 1,
       .flags = IORESOURCE_MEM
    },
    /* mflash 的中断引脚 */
    [1] = {
       .start = IRQ_GPIO(84),
       .end = IRQ_GPIO(84),
       .flags = IORESOURCE_IRQ
    },
    /* mflash 的复位引脚 */
    [2] = {
       .start = 43,
       .end = 43,
       .name = MG_RST_PIN,
       .flags = IORESOURCE_IO
    },
    /* mflash 的复位输出引脚
     * 如果将 mflash 用作存储设备（即除 MG_BOOT_DEV 之外），
     * 应分配此引脚 */
    [3] = {
       .start = 51,
       .end = 51,
       .name = MG_RSTOUT_PIN,
       .flags = IORESOURCE_IO
    }
};

static struct platform_device mflash_dev = {
   .name = MG_DEV_NAME,
   .id = -1,
   .dev = {
       .platform_data = &mflash_drv_data,
    },
   .num_resources = ARRAY_SIZE(mg_mflash_rsc),
   .resource = mg_mflash_rsc
};

platform_device_register(&mflash_dev);
```