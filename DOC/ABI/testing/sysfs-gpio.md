**What**: `/sys/class/gpio/`

**Date**: 七月 2008

**KernelVersion**: 2.6.27

**Contact**: David Brownell <dbrownell@users.sourceforge.net>

**Description**:

  作为一个 Kconfig 选项，单个的 GPIO 信号可以从用户空间访问。GPIO 仅通过显式的“导出”操作对用户空间可用。如果给定的 GPIO 未被内核代码占用用于使用，它可以由用户空间导出（稍后再取消导出）。内核代码可以为完全或部分访问而导出它。

  GPIO 在内核内部是通过整数在 0..INT_MAX 范围内进行标识的。更多信息请参阅 Documentation/gpio.txt。

    `/sys/class/gpio`

    `/export`...请求内核将一个 GPIO 导出到用户空间

    `/unexport`...将一个 GPIO 返回给内核

    `/gpioN`...对于每个导出的 GPIO #N

        `/value`...始终可读，对于输入 GPIO 写入失败

        `/direction`...读写为：in，out（默认低）；写入：high，low

        `/edge`...读写为：none，falling，rising，both

    `/gpiochipN`...对于每个 gpiochip；#N 是其第一个 GPIO

        `/base`...（只读）与 N 相同

        `/label`...（只读）描述性的，不一定唯一

        `/ngpio`...（只读）GPIO 的数量；编号为 N 到 N + (ngpio - 1)