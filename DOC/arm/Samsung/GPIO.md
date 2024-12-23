# 三星 GPIO 实现

===========================

# 介绍

------------

本文概述了三星 GPIO 的实现以及与驱动程序/gpio 核心一起提供的特定于架构的调用。

# S3C24XX（传统）

----------------

有关这些设备的更多信息，请参阅 Documentation/arm/Samsung-S3C24XX/GPIO.txt。它们的实现已与本文档中描述的核心三星实现保持一致。

# GPIOLIB 集成

-------------------

gpio 实现尽可能使用 gpiolib，仅为需要三星特定处理的项目提供特定调用，例如引脚特殊功能或上拉电阻控制。

GPIO 编号在三星和 gpiolib 系统之间是同步的。

# 引脚配置

-----------------

引脚配置特定于三星架构，每个 SoC 注册核心 gpio 配置实现所需的必要信息，以根据需要配置引脚。

s3c_gpio_cfgpin() 和 s3c_gpio_setpull() 为驱动程序或机器提供了更改 gpio 配置的方法。

有关这些函数的更多信息，请参阅 arch/arm/plat-samsung/include/plat/gpio-cfg.h。