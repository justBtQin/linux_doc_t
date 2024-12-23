**What**: `/sys/bus/mdio_bus/devices/.../phy_id`

**Date**: November 2012

**KernelVersion**: 3.8

**Contact**: netdev@vger.kernel.org

**Description**:

此属性包含设备在总线枚举期间报告的 32 位 PHY 标识符，以十六进制编码。此 ID 用于将设备与适当的驱动程序匹配。

**What**: `/sys/bus/mdio_bus/devices/.../phy_interface`

**Date**: February 2014

**KernelVersion**: 3.15

**Contact**: netdev@vger.kernel.org

**Description**:

此属性包含以太网驱动程序在总线枚举期间配置的 PHY 接口，以字符串编码。此接口模式用于为以太网 MAC 配置与其到 PHY 硬件的数据线路相适应的模式。

**What**: `/sys/bus/mdio_bus/devices/.../phy_has_fixups`

**Date**: February 2014

**KernelVersion**: 3.15

**Contact**: netdev@vger.kernel.org

**Description**:

此属性包含一个布尔值，指示给定的 PHY 设备是否运行了任何“修复”变通方法，以布尔值编码。此信息用于帮助故障排除 PHY 配置。