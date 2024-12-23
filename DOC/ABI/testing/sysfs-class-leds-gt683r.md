**What**: `/sys/class/leds/<led>/gt683r/mode`

**Date**: Jun 2014

**KernelVersion**: 3.17

**Contact**: Janne Kanniainen <janne.kanniainen@gmail.com>

**Description**:

 设置 LED 的模式。你应该注意到，更改一个 LED 的模式也会更新其两个兄弟设备的模式。

 0 - 正常

 1 - 音频

 2 - 呼吸

 正常：启用时 LED 完全亮起

 音频：LED 亮度取决于声音级别

 呼吸：LED 亮度以人类呼吸速率变化