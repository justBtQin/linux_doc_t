**ARM Allwinner SoCs**

==================

本文档列出了当前在 Linux 内核主线中受支持的所有 ARM Allwinner SoC。本文档还将为这些 SoC 提供文档和/或数据手册的链接。

**SunXi 系列**

------------

 - Linux 内核 mach 目录：`arch/arm/mach-sunxi`

 - 种类：
 
```
    - **基于 ARM926 的 SoC**
      - Allwinner F20（sun3i）
        + 不支持
    - **基于 ARM Cortex-A8 的 SoC**
      - Allwinner A10（sun4i）
        + 数据手册：[http://dl.linux-sunxi.org/A10/A10%20Datasheet%20-%20v1.21%20%282012-04-06%29.pdf](http://dl.linux-sunxi.org/A10/A10%20Datasheet%20-%20v1.21%20%282012-04-06%29.pdf)
        + 用户手册：[http://dl.linux-sunxi.org/A10/A10%20User%20Manual%20-%20v1.20%20%282012-04-09%2c%20DECRYPTED%29.pdf](http://dl.linux-sunxi.org/A10/A10%20User%20Manual%20-%20v1.20%20%282012-04-09%2c%20DECRYPTED%29.pdf)
      - Allwinner A10s（sun5i）
        + 数据手册：[http://dl.linux-sunxi.org/A10s/A10s%20Datasheet%20-%20v1.20%20%282012-03-27%29.pdf](http://dl.linux-sunxi.org/A10s/A10s%20Datasheet%20-%20v1.20%20%282012-03-27%29.pdf)
      - Allwinner A13（sun5i）
        + 数据手册：[http://dl.linux-sunxi.org/A13/A13%20Datasheet%20-%20v1.12%20%282012-03-29%29.pdf](http://dl.linux-sunxi.org/A13/A13%20Datasheet%20-%20v1.12%20%282012-03-29%29.pdf)
        + 用户手册：[http://dl.linux-sunxi.org/A13/A13%20User%20Manual%20-%20v1.2%20%282013-01-08%29.pdf](http://dl.linux-sunxi.org/A13/A13%20User%20Manual%20-%20v1.2%20%282013-01-08%29.pdf)
    - **基于双 ARM Cortex-A7 的 SoC**
      - Allwinner A20（sun7i）
        + 用户手册：[http://dl.linux-sunxi.org/A20/A20%20User%20Manual%202013-03-22.pdf](http://dl.linux-sunxi.org/A20/A20%20User%20Manual%202013-03-22.pdf)
      - Allwinner A23
        + 数据手册：[http://dl.linux-sunxi.org/A23/A23%20Datasheet%20V1.0%2020130830.pdf](http://dl.linux-sunxi.org/A23/A23%20Datasheet%20V1.0%2020130830.pdf)
        + 用户手册：[http://dl.linux-sunxi.org/A23/A23%20User%20Manual%20V1.0%2020130830.pdf](http://dl.linux-sunxi.org/A23/A23%20User%20Manual%20V1.0%2020130830.pdf)
    - **基于四核 ARM Cortex-A7 的 SoC**
      - Allwinner A31（sun6i）
        + 数据手册：[http://dl.linux-sunxi.org/A31/A3x_release_document/A31/IC/A31%20datasheet%20V1.3%2020131106.pdf](http://dl.linux-sunxi.org/A31/A3x_release_document/A31/IC/A31%20datasheet%20V1.3%2020131106.pdf)
        + 用户手册：[http://dl.linux-sunxi.org/A31/A3x_release_document/A31/IC/A31%20user%20manual%20V1.1%2020130630.pdf](http://dl.linux-sunxi.org/A31/A3x_release_document/A31/IC/A31%20user%20manual%20V1.1%2020130630.pdf)
      - Allwinner A31s（sun6i）
        + 数据手册：[http://dl.linux-sunxi.org/A31/A3x_release_document/A31s/IC/A31s%20datasheet%20V1.3%2020131106.pdf](http://dl.linux-sunxi.org/A31/A3x_release_document/A31s/IC/A31s%20datasheet%20V1.3%2020131106.pdf)
        + 用户手册：[http://dl.linux-sunxi.org/A31/A3x_release_document/A31s/IC/A31s%20User%20Manual%20%20V1.0%2020130322.pdf](http://dl.linux-sunxi.org/A31/A3x_release_document/A31s/IC/A31s%20User%20Manual%20%20V1.0%2020130322.pdf)
    - **基于四核 ARM Cortex-A15、四核 ARM Cortex-A7 的 SoC**
      - Allwinner A80
        + 数据手册：[http://dl.linux-sunxi.org/A80/A80_Datasheet_Revision_1.0_0404.pdf](http://dl.linux-sunxi.org/A80/A80_Datasheet_Revision_1.0_0404.pdf)
```