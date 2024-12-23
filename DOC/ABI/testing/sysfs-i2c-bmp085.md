**What**: `/sys/bus/i2c/devices/<busnum>-<devaddr>/pressure0_input`

**Date**: June 2010

**Contact**: Christoph Mair <christoph.mair@gmail.com>

**Description**: 开始压力测量并读取结果。值表示环境气压，单位为帕斯卡（0.01 毫巴）。

读取：返回当前气压。

**What**: `/sys/bus/i2c/devices/<busnum>-<devaddr>/temp0_input`

**Date**: June 2010

**Contact**: Christoph Mair <christoph.mair@gmail.com>

**Description**: 测量环境温度。返回的值以 0.1 摄氏度为单位表示环境温度。

读取：返回当前温度。

**What**: `/sys/bus/i2c/devices/<busnum>-<devaddr>/oversampling`

**Date**: June 2010

**Contact**: Christoph Mair <christoph.mair@gmail.com>

**Description**: 告诉 bmp085 使用更多样本计算压力值。写入此文件时，芯片将使用 2^x 个样本计算下一个压力值，其中 x 是写入的值。使用此功能将降低均方根噪声并增加测量时间。

读取：返回当前过采样设置。

写入：设置新的过采样设置。可接受的值：0 到 3。