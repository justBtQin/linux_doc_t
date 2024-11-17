以下是为您转换成的 Markdown 格式：

# 内核驱动程序 `scx200_acb`

**作者**：Christer Weinigel <wingel@nano-system.com>

该驱动程序取代了较旧的、从未合并的名为 `i2c-nscacb` 的驱动程序。

**模块参数**

* base：最多 4 个整数
  在 SCx200 和 SC1100 设备上的 ACCESS.bus 控制器的基地址

  默认情况下，驱动程序使用两个基地址 0x820 和 0x840。如果您只想要一个基地址，请将第二个指定为 0 以覆盖此默认值。

**描述**

在 Geode SCx200 和 SC1100 处理器以及 CS5535 和 CS5536 Geode 配套设备上启用 ACCESS.bus 控制器的使用。

**设备特定说明**

已知 SC1100 WRAP 板使用基地址 0x810 和 0x820。如果 `scx200_acb` 驱动程序已构建到内核中，请在您的启动命令行中添加以下参数：
  `scx200_acb.base=0x810,0x820`
如果 `scx200_acb` 驱动程序被构建为模块，请在 `/etc/modprobe.d/` 中的配置文件中添加以下行：
  `options scx200_acb base=0x810,0x820` 