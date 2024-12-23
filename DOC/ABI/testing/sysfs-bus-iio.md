# 设备信息

- **What**：/sys/bus/iio/devices/iio:deviceX

    - **KernelVersion**：2.6.35

    - **Contact**：linux-iio@vger.kernel.org

    - **Description**：通过一个通信端口访问的硬件芯片或设备。对应于传感器通道的分组。X 是设备的 IIO 索引。

- **What**：/sys/bus/iio/devices/triggerX

    - **KernelVersion**：2.6.35

    - **Contact**：linux-iio@vger.kernel.org

    - **Description**：到内核缓冲区的数据捕获的事件驱动驱动程序。可能由具有基于硬件生成事件（例如数据准备）的 IIO 设备的设备驱动程序提供，或者由用于其他硬件（例如定期定时器、GPIO 或高精度定时器）的单独驱动程序提供。包含特定于触发类型的元素。这些元素不能很好地概括，因此不在此文件中记录。X 是触发的 IIO 索引。

- **What**：/sys/bus/iio/devices/iio:deviceX/buffer

    - **KernelVersion**：2.6.35

    - **Contact**：linux-iio@vger.kernel.org

    - **Description**：与设备的缓冲区相关的属性目录。

- **What**：/sys/bus/iio/devices/iio:deviceX/name

    - **KernelVersion**：2.6.35

    - **Contact**：linux-iio@vger.kernel.org

    - **Description**：设备 X 的物理芯片/设备的描述。通常是零件编号。

- **What**：/sys/bus/iio/devices/iio:deviceX/sampling_frequency

- **What**：/sys/bus/iio/devices/iio:deviceX/buffer/sampling_frequency

- **What**：/sys/bus/iio/devices/triggerX/sampling_frequency

    - **KernelVersion**：2.6.35

    - **Contact**：linux-iio@vger.kernel.org

    - **Description**：一些设备具有内部时钟。此参数设置所得的采样频率。在许多设备中，此参数对输入滤波器等有影响，而不仅仅是控制何时进行输入采样。由于这会影响数据就绪触发、硬件缓冲区和 sysfs 直接访问接口，因此它可能在任何相关目录中找到。如果它影响上述所有内容，则应在基本设备目录中找到。

- **What**：/sys/bus/iio/devices/iio:deviceX/sampling_frequency_available

- **What**：/sys/.../iio:deviceX/buffer/sampling_frequency_available

- **What**：/sys/bus/iio/devices/triggerX/sampling_frequency_available

    - **KernelVersion**：2.6.35

    - **Contact**：linux-iio@vger.kernel.org

    - **Description**：当内部采样时钟只能采用一小部分离散值时，此文件列出了那些可用的值。

- **What**：/sys/bus/iio/devices/iio:deviceX/oversampling_ratio

    - **KernelVersion**：2.6.38

    - **Contact**：linux-iio@vger.kernel.org

    - **Description**：硬件相关的 ADC 过采样。如果可用，控制数字滤波器的采样率。

- **What**：/sys/bus/iio/devices/iio:deviceX/oversampling_ratio_available

    - **KernelVersion**：2.6.38

    - **Contact**：linux-iio@vger.kernel.org

    - **Description**：过采样滤波器支持的硬件相关值。

- **What**：/sys/bus/iio/devices/iio:deviceX/in_voltageY_raw

- **What**：/sys/bus/iio/devices/iio:deviceX/in_voltageY_supply_raw

    - **KernelVersion**：2.6.35

    - **Contact**：linux-iio@vger.kernel.org

    - **Description**：来自通道 Y 的原始（未缩放、无偏置去除等）电压测量。在特殊情况下，当通道不对应于外部可用输入时，可以使用其中一个命名版本。必须始终指定编号且唯一，以允许与事件代码关联。应用比例和偏移后的单位是毫伏。

- **What**：/sys/bus/iio/devices/iio:deviceX/in_voltageY-voltageZ_raw

    - **KernelVersion**：2.6.35

    - **Contact**：linux-iio@vger.kernel.org

    - **Description**：原始（未缩放）差分电压测量，相当于通道 Y - 通道 Z，当非差分读数单独可用时，这些通道编号适用于物理等效输入。在仅差分部分中，只需要一致的标签。应用比例和偏移后的单位是毫伏。

- **What**：/sys/bus/iio/devices/iio:deviceX/in_currentY_raw

- **What**：/sys/bus/iio/devices/iio:deviceX/in_currentY_supply_raw

    - **KernelVersion**：3.17

    - **Contact**：linux-iio@vger.kernel.org

    - **Description**：来自通道 Y 的原始（未缩放、无偏置去除等）电流测量。在特殊情况下，当通道不对应于外部可用输入时，可以使用其中一个命名版本。必须始终指定编号且唯一，以允许与事件代码关联。应用比例和偏移后的单位是毫安。

- **What**：/sys/bus/iio/devices/iio:deviceX/in_capacitanceY_raw

    - **KernelVersion**：3.2

    - **Contact**：linux-iio@vger.kernel.org

    - **Description**：来自通道 Y 的原始电容测量。应用比例和偏移后的单位是纳法。

- **What**：/sys/.../iio:deviceX/in_capacitanceY-in_capacitanceZ_raw

    - **KernelVersion**：3.2

    - **Contact**：linux-iio@vger.kernel.org

    - **Description**：原始差分电容测量，相当于通道 Y - 通道 Z，当非差分读数单独可用时，这些通道编号适用于物理等效输入。在仅差分部分中，只需要一致的标签。应用比例和偏移后的单位是纳法。

- **What**：/sys/bus/iio/devices/iio:deviceX/in_temp_raw

- **What**：/sys/bus/iio/devices/iio:deviceX/in_tempX_raw

- **What**：/sys/bus/iio/devices/iio:deviceX/in_temp_x_raw

- **What**：/sys/bus/iio/devices/iio:deviceX/in_temp_y_raw

- **What**：/sys/bus/iio/devices/iio:deviceX/in_temp_ambient_raw

- **What**：/sys/bus/iio/devices/iio:deviceX/in_temp_object_raw

    - **KernelVersion**：2.6.35

    - **Contact**：linux-iio@vger.kernel.org

    - **Description**：原始（未缩放、无偏置去除等）温度测量。如果指定了轴，通常意味着温度传感器与复合设备的一部分相关（例如陀螺仪轴）。环境和对象修饰符区分接触式测量的环境（参考）和远距离温度。应用比例和偏移后的单位是毫摄氏度。

- **What**：/sys/bus/iio/devices/iio:deviceX/in_tempX_input

    - **KernelVersion**：2.6.38

    - **Contact**：linux-iio@vger.kernel.org

    - **Description**：以毫摄氏度为单位的缩放温度测量。

- **What**：/sys/bus/iio/devices/iio:deviceX/in_accel_x_raw

- **What**：/sys/bus/iio/devices/iio:deviceX/in_accel_y_raw

- **What**：/sys/bus/iio/devices/iio:deviceX/in_accel_z_raw

    - **KernelVersion**：2.6.35

    - **Contact**：linux-iio@vger.kernel.org

    - **Description**：沿 x、y 或 z 方向的加速度（可能是任意分配的，但应与设备上的其他此类分配匹配）。具有与 voltageY 相同的所有等效参数。应用比例和偏移后的单位是 m/s²。

- **What**：/sys/bus/iio/devices/iio:deviceX/in_anglvel_x_raw

- **What**：/sys/bus/iio/devices/iio:deviceX/in_anglvel_y_raw

- **What**：/sys/bus/iio/devices/iio:deviceX/in_anglvel_z_raw

    - **KernelVersion**：2.6.35

    - **Contact**：linux-iio@vger.kernel.org

    - **Description**：绕 x、y 或 z 轴的角速度（可能是任意分配的）。具有与 voltageY 相同的所有等效参数。应用比例和偏移后的单位是弧度/秒。

- **What**：/sys/bus/iio/devices/iio:deviceX/in_incli_x_raw

- **What**：/sys/bus/iio/devices/iio:deviceX/in_incli_y_raw

- **What**：/sys/bus/iio/devices/iio:deviceX/in_incli_z_raw

    - **KernelVersion**：2.6.35

    - **Contact**：linux-iio@vger.kernel.org

    - **Description**：绕 x、y 或 z 轴的倾斜原始读数（可能是任意分配的）。数据通过应用偏移和比例转换为度。

- **What**：/sys/bus/iio/devices/iio:deviceX/in_magn_x_raw

- **What**：/sys/bus/iio/devices/iio:deviceX/in_magn_y_raw

- **What**：/sys/bus/iio/devices/iio:deviceX/in_magn_z_raw

    - **KernelVersion**：2.6.35

    - **Contact**：linux-iio@vger.kernel.org

    - **Description**：沿 x、y 或 z 轴的磁场（可能是任意分配的）。数据通过应用偏移然后比例转换为高斯。**What**：`/sys/bus/iio/devices/iio:deviceX/in_accel_x_peak_raw`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_accel_y_peak_raw`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_accel_z_peak_raw`

**KernelVersion**：2.6.36

**Contact**：linux-iio@vger.kernel.org

**Description**：自某个复位条件以来的最高值。这些属性允许对此进行访问，否则它们是`<type>Y[_name]_raw`属性的直接等效项。

**What**：`/sys/bus/iio/devices/iio:deviceX/in_accel_xyz_squared_peak_raw`

**KernelVersion**：2.6.36

**Contact**：linux-iio@vger.kernel.org

**Description**：基于指定方向上基础值的平方和幅度的计算峰值值。

**What**：`/sys/bus/iio/devices/iio:deviceX/in_pressureY_raw`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_pressure_raw`

**KernelVersion**：3.8

**Contact**：linux-iio@vger.kernel.org

**Description**：通道 Y 的原始压力测量。应用比例和偏移后的单位是千帕。

**What**：`/sys/bus/iio/devices/iio:deviceX/in_pressureY_input`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_pressure_input`

**KernelVersion**：3.8

**Contact**：linux-iio@vger.kernel.org

**Description**：通道 Y 的缩放压力测量，以千帕为单位。

**What**：`/sys/bus/iio/devices/iio:deviceX/in_humidityrelative_raw`

**KernelVersion**：3.14

**Contact**：linux-iio@vger.kernel.org

**Description**：空气的原始湿度测量。应用比例和偏移后的单位是毫百分比。

**What**：`/sys/bus/iio/devices/iio:deviceX/in_humidityrelative_input`

**KernelVersion**：3.14

**Contact**：linux-iio@vger.kernel.org

**Description**：缩放后的湿度测量，以毫百分比为单位。

**What**：`/sys/bus/iio/devices/iio:deviceX/in_X_mean_raw`

**KernelVersion**：3.5

**Contact**：linux-iio@vger.kernel.org

**Description**：通道 X 的平均原始测量。用于平均的数值数量因设备而异。常规原始值的转换规则也适用于平均后的原始值。

**What**：`/sys/bus/iio/devices/iio:deviceX/in_accel_offset`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_accel_x_offset`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_accel_y_offset`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_accel_z_offset`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_voltageY_offset`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_voltage_offset`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_currentY_offset`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_current_offset`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_tempY_offset`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_temp_offset`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_pressureY_offset`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_pressure_offset`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_humidityrelative_offset`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_magn_offset`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_rot_offset`

**KernelVersion**：2.6.35

**Contact**：linux-iio@vger.kernel.org

**Description**：如果设备已知，则在按`<type>[Y]_scale`缩放之前要添加到`<type>[Y]_raw`的偏移量，以获得`<type>`单位中的值，如`<type>[Y]_raw`文档中所指定。如果偏移量始终为 0 或未知，则不存在。如果 Y 或轴`<x|y|z>`不存在，则偏移量适用于`<type>`的所有输入通道。如果可以在设备上应用可变偏移量，则可以写入。请注意，这与`calibbias`不同，`calibbias`用于应用偏移量以补偿零件不同实例之间的变化，通常通过使用某些硬件支持的校准程序进行调整。`calibbias`在内部应用，偏移量在用户空间中应用于`_raw`输出。

**What**：`/sys/bus/iio/devices/iio:deviceX/in_voltageY_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_voltageY_supply_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_voltage_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_voltage-voltage_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/out_voltageY_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/out_altvoltageY_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_currentY_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_currentY_supply_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_current_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_accel_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_accel_peak_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_anglvel_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_energy_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_distance_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_magn_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_magn_x_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_magn_y_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_magn_z_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_rot_from_north_magnetic_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_rot_from_north_true_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_rot_from_north_magnetic_tilt_comp_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_rot_from_north_true_tilt_comp_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_pressureY_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_pressure_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_humidityrelative_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_velocity_sqrt(x^2+y^2+z^2)_scale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_illuminance_scale`

**KernelVersion**：2.6.35

**Contact**：linux-iio@vger.kernel.org

**Description**：如果设备已知，则在添加`<type>[Y][_name]_offset`之后要应用于`<type>Y[_name]_raw`的比例，以获得`<type>`单位中的测量值，如`<type>[Y][_name]_raw`文档中所指定。如果在所有输入通道中共享，则 Y 和`<x|y|z>`不存在，并且该值称为`<type>[Y][_name]_scale`。峰值修饰符意味着此值应用于`<type>Y[_name]_peak_raw`值。

**What**：`/sys/bus/iio/devices/iio:deviceX/in_accel_x_calibbias`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_accel_y_calibbias`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_accel_z_calibbias`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_anglvel_x_calibbias`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_anglvel_y_calibbias`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_anglvel_z_calibbias`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_illuminance0_calibbias`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_proximity0_calibbias`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_pressureY_calibbias`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_pressure_calibbias`

**KernelVersion**：2.6.35

**Contact**：linux-iio@vger.kernel.org

**Description**：硬件应用的校准偏移量（假定用于修复生产误差）。

**What**：`/sys/bus/iio/devices/iio:deviceX/in_voltageY_calibscale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_voltageY_supply_calibscale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_voltage_calibscale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_accel_x_calibscale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_accel_y_calibscale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_accel_z_calibscale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_anglvel_x_calibscale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_anglvel_y_calibscale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_anglvel_z_calibscale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_illuminance0_calibscale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_proximity0_calibscale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_pressureY_calibscale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_pressure_calibscale`

**What**：`/sys/bus/iio/devices/iio:deviceX/in_illuminance_calibscale`

**KernelVersion**：2.6.35

**Contact**：linux-iio@vger.kernel.org

**Description**：硬件应用的校准比例因子（假定用于修复生产误差）。如果在所有通道中共享，则使用`<type>_calibscale`。**硬件相关信息**

- **What**: /sys/bus/iio/devices/iio:deviceX/in_activity_calibgender

- **What**: /sys/bus/iio/devices/iio:deviceX/in_energy_calibgender

- **What**: /sys/bus/iio/devices/iio:deviceX/in_distance_calibgender

- **What**: /sys/bus/iio/devices/iio:deviceX/in_velocity_calibgender

- **KernelVersion**: 4.0

- **Contact**: linux-iio@vger.kernel.org

- **Description**: 某些计步器用于计算步长、距离、速度和活动类型的用户性别（例如：男性、女性）。

- **What**: /sys/bus/iio/devices/iio:deviceX/in_activity_calibgender_available

- **What**: /sys/bus/iio/devices/iio:deviceX/in_energy_calibgender_available

- **What**: /sys/bus/iio/devices/iio:deviceX/in_distance_calibgender_available

- **What**: /sys/bus/iio/devices/iio:deviceX/in_velocity_calibgender_available

- **KernelVersion**: 4.0

- **Contact**: linux-iio@vger.kernel.org

- **Description**: 列出所有可用的性别值（例如：男性、女性）。

- **What**: /sys/bus/iio/devices/iio:deviceX/in_activity_calibheight

- **What**: /sys/bus/iio/devices/iio:deviceX/in_energy_calibheight

- **What**: /sys/bus/iio/devices/iio:deviceX/in_distance_calibheight

- **What**: /sys/bus/iio/devices/iio:deviceX/in_velocity_calibheight

- **KernelVersion**: 3.19

- **Contact**: linux-iio@vger.kernel.org

- **Description**: 某些计步器用于计算步长、距离、速度和活动类型的用户身高（以米为单位）。

- **What**: /sys/bus/iio/devices/iio:deviceX/in_energy_calibweight

- **KernelVersion**: 4.0

- **Contact**: linux-iio@vger.kernel.org

- **Description**: 用户体重（以千克为单位）。某些计步器需要此信息来计算用户燃烧的卡路里。

- **What**: /sys/bus/iio/devices/iio:deviceX/in_accel_scale_available

- **What**: /sys/.../iio:deviceX/in_voltageX_scale_available

- **What**: /sys/.../iio:deviceX/in_voltage-voltage_scale_available

- **What**: /sys/.../iio:deviceX/out_voltageX_scale_available

- **What**: /sys/.../iio:deviceX/out_altvoltageX_scale_available

- **What**: /sys/.../iio:deviceX/in_capacitance_scale_available

- **What**: /sys/.../iio:deviceX/in_pressure_scale_available

- **What**: /sys/.../iio:deviceX/in_pressureY_scale_available

- **KernelVersion**: 2.6.35

- **Contact**: linux-iio@vger.kernel.org

- **Description**: 如果有一组离散的比例值可用，则它们在此属性中列出。

- **What**: /sys/bus/iio/devices/iio:deviceX/out_voltageY_hardwaregain

- **What**: /sys/bus/iio/devices/iio:deviceX/in_intensity_red_hardwaregain

- **What**: /sys/bus/iio/devices/iio:deviceX/in_intensity_green_hardwaregain

- **What**: /sys/bus/iio/devices/iio:deviceX/in_intensity_blue_hardwaregain

- **What**: /sys/bus/iio/devices/iio:deviceX/in_intensity_clear_hardwaregain

- **KernelVersion**: 2.6.35

- **Contact**: linux-iio@vger.kernel.org

- **Description**: 硬件应用的增益因子。如果在所有通道中共享，则使用 <type>_hardwaregain。

- **What**: /sys/.../in_accel_filter_low_pass_3db_frequency

- **What**: /sys/.../in_magn_filter_low_pass_3db_frequency

- **What**: /sys/.../in_anglvel_filter_low_pass_3db_frequency

- **KernelVersion**: 3.2

- **Contact**: linux-iio@vger.kernel.org

- **Description**: 如果对底层数据通道应用了已知或可控制的低通滤波器，则此参数给出滤波器的 3dB 频率（以 Hz 为单位）。

# 输出相关信息

- **What**: /sys/bus/iio/devices/iio:deviceX/out_voltageY_raw

- **What**: /sys/bus/iio/devices/iio:deviceX/out_altvoltageY_raw

- **KernelVersion**: 2.6.37

- **Contact**: linux-iio@vger.kernel.org

- **Description**: 通道 Y 的原始（未缩放、无偏置等）输出电压。如果输出对应单个通道，则该数字必须始终指定且唯一。而像 DAC 这样的设备通常使用 out_voltage，连续频率生成设备（如 DDS 或 PLL）应使用 out_altvoltage。

- **What**: /sys/bus/iio/devices/iio:deviceX/out_voltageY&Z_raw

- **What**: /sys/bus/iio/devices/iio:deviceX/out_altvoltageY&Z_raw

- **KernelVersion**: 2.6.37

- **Contact**: linux-iio@vger.kernel.org

- **Description**: 通道 Y、通道 Z 等的聚合的原始（未缩放、无偏置等）输出电压。在单个输出同时设置多个通道值的情况下，此接口可用。

- **What**: /sys/bus/iio/devices/iio:deviceX/out_voltageY_powerdown_mode

- **What**: /sys/bus/iio/devices/iio:deviceX/out_voltage_powerdown_mode

- **What**: /sys/bus/iio/devices/iio:deviceX/out_altvoltageY_powerdown_mode

- **What**: /sys/bus/iio/devices/iio:deviceX/out_altvoltage_powerdown_mode

- **KernelVersion**: 2.6.38

- **Contact**: linux-iio@vger.kernel.org

- **Description**: 指定输出断电模式。DAC 输出级与放大器断开连接，1kohm_to_gnd：通过 1kOhm 电阻连接到地，6kohm_to_gnd：通过 6kOhm 电阻连接到地，20kohm_to_gnd：通过 20kOhm 电阻连接到地，100kohm_to_gnd：通过 100kOhm 电阻连接到地，500kohm_to_gnd：通过 500kOhm 电阻连接到地，三态：悬空。要查看可用的输出断电选项，请读取 outX_powerdown_mode_available。如果 Y 不存在，则模式在所有输出中共享。

- **What**: /sys/.../iio:deviceX/out_votlageY_powerdown_mode_available

- **What**: /sys/.../iio:deviceX/out_voltage_powerdown_mode_available

- **What**: /sys/.../iio:deviceX/out_altvotlageY_powerdown_mode_available

- **What**: /sys/.../iio:deviceX/out_altvoltage_powerdown_mode_available

- **KernelVersion**: 2.6.38

- **Contact**: linux-iio@vger.kernel.org

- **Description**: 列出所有可用的输出断电模式。如果 Y 不存在，则模式在所有输出中共享。

- **What**: /sys/bus/iio/devices/iio:deviceX/out_voltageY_powerdown

- **What**: /sys/bus/iio/devices/iio:deviceX/out_voltage_powerdown

- **What**: /sys/bus/iio/devices/iio:deviceX/out_altvoltageY_powerdown

- **What**: /sys/bus/iio/devices/iio:deviceX/out_altvoltage_powerdown

- **KernelVersion**: 2.6.38

- **Contact**: linux-iio@vger.kernel.org

- **Description**: 写入 1 会使输出 Y 进入由相应 outY_powerdown_mode 指定的断电模式。DAC 输出级与放大器断开连接。清除后恢复正常操作。如果所有输出一起控制，则 Y 可能被抑制。

- **What**: /sys/bus/iio/devices/iio:deviceX/out_altvoltageY_frequency

- **KernelVersion**: 3.4.0

- **Contact**: linux-iio@vger.kernel.org

- **Description**: 通道 Y 的输出频率（以 Hz 为单位）。如果输出对应单个通道，则该数字必须始终指定且唯一。

- **What**: /sys/bus/iio/devices/iio:deviceX/out_altvoltageY_phase

- **KernelVersion**: 3.4.0

- **Contact**: linux-iio@vger.kernel.org

- **Description**: 设备 X 的一个频率/时钟输出 Y（out_altvoltageY）相对于另一个频率/时钟输出（out_altvoltageZ）的相位（以弧度为单位）。如果输出对应单个通道，则该数字必须始终指定且唯一。

- **What**: /sys/bus/iio/devices/iio:deviceX/events

- **KernelVersion**: 2.6.35

- **Contact**: linux-iio@vger.kernel.org

- **Description**: 传递到用户空间的硬件生成事件的配置。**What：**

- `/sys/.../iio:deviceX/events/in_accel_x_thresh_rising_en`

- `/sys/.../iio:deviceX/events/in_accel_x_thresh_falling_en`

- `/sys/.../iio:deviceX/events/in_accel_y_thresh_rising_en`

- `/sys/.../iio:deviceX/events/in_accel_y_thresh_falling_en`

- `/sys/.../iio:deviceX/events/in_accel_z_thresh_rising_en`

- `/sys/.../iio:deviceX/events/in_accel_z_thresh_falling_en`

- `/sys/.../iio:deviceX/events/in_anglvel_x_thresh_rising_en`

- `/sys/.../iio:deviceX/events/in_anglvel_x_thresh_falling_en`

- `/sys/.../iio:deviceX/events/in_anglvel_y_thresh_rising_en`

- `/sys/.../iio:deviceX/events/in_anglvel_y_thresh_falling_en`

- `/sys/.../iio:deviceX/events/in_anglvel_z_thresh_rising_en`

- `/sys/.../iio:deviceX/events/in_anglvel_z_thresh_falling_en`

- `/sys/.../iio:deviceX/events/in_magn_x_thresh_rising_en`

- `/sys/.../iio:deviceX/events/in_magn_x_thresh_falling_en`

- `/sys/.../iio:deviceX/events/in_magn_y_thresh_rising_en`

- `/sys/.../iio:deviceX/events/in_magn_y_thresh_falling_en`

- `/sys/.../iio:deviceX/events/in_magn_z_thresh_rising_en`

- `/sys/.../iio:deviceX/events/in_magn_z_thresh_falling_en`

- `/sys/.../iio:deviceX/events/in_rot_from_north_magnetic_thresh_rising_en`

- `/sys/.../iio:deviceX/events/in_rot_from_north_magnetic_thresh_falling_en`

- `/sys/.../iio:deviceX/events/in_rot_from_north_true_thresh_rising_en`

- `/sys/.../iio:deviceX/events/in_rot_from_north_true_thresh_falling_en`

- `/sys/.../iio:deviceX/events/in_rot_from_north_magnetic_tilt_comp_thresh_rising_en`

- `/sys/.../iio:deviceX/events/in_rot_from_north_magnetic_tilt_comp_thresh_falling_en`

- `/sys/.../iio:deviceX/events/in_rot_from_north_true_tilt_comp_thresh_rising_en`

- `/sys/.../iio:deviceX/events/in_rot_from_north_true_tilt_comp_thresh_falling_en`

- `/sys/.../iio:deviceX/events/in_voltageY_supply_thresh_rising_en`

- `/sys/.../iio:deviceX/events/in_voltageY_supply_thresh_falling_en`

- `/sys/.../iio:deviceX/events/in_voltageY_thresh_rising_en`

- `/sys/.../iio:deviceX/events/in_voltageY_thresh_falling_en`

- `/sys/.../iio:deviceX/events/in_tempY_thresh_rising_en`

- `/sys/.../iio:deviceX/events/in_tempY_thresh_falling_en`

**KernelVersion：** 2.6.37

**Contact：** linux-iio@vger.kernel.org

**Description：**

事件在通道以指定的（_rising|_falling）方向通过阈值时生成。如果未指定方向，则设备将报告在任一方向通过单个阈值值的事件（例如，<type>[Y][_name]_<raw|input>_thresh_value），或者<type>[Y][_name]_<raw|input>_thresh_rising_value 和<type>[Y][_name]_<raw|input>_thresh_falling_value 可以取不同的值，但设备只能启用两个阈值或都不启用。

注意，驱动程序将假定最后请求的 p 个事件将被启用，其中 p 是它支持的数量（可能因请求的确切集合而异）。因此，如果您想确保设置了您认为已设置的内容，请在所有内容配置完成后检查这些属性的内容。驱动程序可能必须缓冲任何参数，以便在未来某个时间启用给定的事件类型时它们是一致的（而不是之前启用的任何事件的参数）。

**What：**

- `/sys/.../iio:deviceX/events/in_accel_x_roc_rising_en`

- `/sys/.../iio:deviceX/events/in_accel_x_roc_falling_en`

- `/sys/.../iio:deviceX/events/in_accel_y_roc_rising_en`

- `/sys/.../iio:deviceX/events/in_accel_y_roc_falling_en`

- `/sys/.../iio:deviceX/events/in_accel_z_roc_rising_en`

- `/sys/.../iio:deviceX/events/in_accel_z_roc_falling_en`

- `/sys/.../iio:deviceX/events/in_anglvel_x_roc_rising_en`

- `/sys/.../iio:deviceX/events/in_anglvel_x_roc_falling_en`

- `/sys/.../iio:deviceX/events/in_anglvel_y_roc_rising_en`

- `/sys/.../iio:deviceX/events/in_anglvel_y_roc_falling_en`

- `/sys/.../iio:deviceX/events/in_anglvel_z_roc_rising_en`

- `/sys/.../iio:deviceX/events/in_anglvel_z_roc_falling_en`

- `/sys/.../iio:deviceX/events/in_magn_x_roc_rising_en`

- `/sys/.../iio:deviceX/events/in_magn_x_roc_falling_en`

- `/sys/.../iio:deviceX/events/in_magn_y_roc_rising_en`

- `/sys/.../iio:deviceX/events/in_magn_y_roc_falling_en`

- `/sys/.../iio:deviceX/events/in_magn_z_roc_rising_en`

- `/sys/.../iio:deviceX/events/in_magn_z_roc_falling_en`

- `/sys/.../iio:deviceX/events/in_rot_from_north_magnetic_roc_rising_en`

- `/sys/.../iio:deviceX/events/in_rot_from_north_magnetic_roc_falling_en`

- `/sys/.../iio:deviceX/events/in_rot_from_north_true_roc_rising_en`

- `/sys/.../iio:deviceX/events/in_rot_from_north_true_roc_falling_en`

- `/sys/.../iio:deviceX/events/in_rot_from_north_magnetic_tilt_comp_roc_rising_en`

- `/sys/.../iio:deviceX/events/in_rot_from_north_magnetic_tilt_comp_roc_falling_en`

- `/sys/.../iio:deviceX/events/in_rot_from_north_true_tilt_comp_roc_rising_en`

- `/sys/.../iio:deviceX/events/in_rot_from_north_true_tilt_comp_roc_falling_en`

- `/sys/.../iio:deviceX/events/in_voltageY_supply_roc_rising_en`

- `/sys/.../iio:deviceX/events/in_voltageY_supply_roc_falling_en`

- `/sys/.../iio:deviceX/events/in_voltageY_roc_rising_en`

- `/sys/.../iio:deviceX/events/in_voltageY_roc_falling_en`

- `/sys/.../iio:deviceX/events/in_tempY_roc_rising_en`

- `/sys/.../iio:deviceX/events/in_tempY_roc_falling_en`

**KernelVersion：** 2.6.37

**Contact：** linux-iio@vger.kernel.org

**Description：**

事件在通道以指定的（_rising|_falling）方向通过变化率（一阶微分）阈值时生成。如果未指定方向，则设备将报告在任一方向通过单个阈值值的事件（例如，<type>[Y][_name]_<raw|input>_roc_value），或者<type>[Y][_name]_<raw|input>_roc_rising_value 和<type>[Y][_name]_<raw|input>_roc_falling_value 可以取不同的值，但设备只能启用两个变化率阈值或都不启用。

注意，驱动程序将假定最后请求的 p 个事件将被启用，其中 p 是它支持的数量（可能因请求的确切集合而异）。因此，如果您想确保设置了您认为已设置的内容，请在所有内容配置完成后检查这些属性的内容。驱动程序可能必须缓冲任何参数，以便在未来某个时间启用给定的事件类型时它们是一致的（而不是之前启用的任何事件的参数）。**What：** `/sys/.../events/in_accel_thresh_rising_value`

**What：** `/sys/.../events/in_accel_thresh_falling_value`

**What：** `/sys/.../events/in_accel_x_raw_thresh_rising_value`

**What：** `/sys/.../events/in_accel_x_raw_thresh_falling_value`

**What：** `/sys/.../events/in_accel_y_raw_thresh_rising_value`

**What：** `/sys/.../events/in_accel_y_raw_thresh_falling_value`

**What：** `/sys/.../events/in_accel_z_raw_thresh_rising_value`

**What：** `/sys/.../events/in_accel_z_raw_thresh_falling_value`

**What：** `/sys/.../events/in_anglvel_x_raw_thresh_rising_value`

**What：** `/sys/.../events/in_anglvel_x_raw_thresh_falling_value`

**What：** `/sys/.../events/in_anglvel_y_raw_thresh_rising_value`

**What：** `/sys/.../events/in_anglvel_y_raw_thresh_falling_value`

**What：** `/sys/.../events/in_anglvel_z_raw_thresh_rising_value`

**What：** `/sys/.../events/in_anglvel_z_raw_thresh_falling_value`

**What：** `/sys/.../events/in_magn_x_raw_thresh_rising_value`

**What：** `/sys/.../events/in_magn_x_raw_thresh_falling_value`

**What：** `/sys/.../events/in_magn_y_raw_thresh_rising_value`

**What：** `/sys/.../events/in_magn_y_raw_thresh_falling_value`

**What：** `/sys/.../events/in_magn_z_raw_thresh_rising_value`

**What：** `/sys/.../events/in_magn_z_raw_thresh_falling_value`

**What：** `/sys/.../events/in_rot_from_north_magnetic_raw_thresh_rising_value`

**What：** `/sys/.../events/in_rot_from_north_magnetic_raw_thresh_falling_value`

**What：** `/sys/.../events/in_rot_from_north_true_raw_thresh_rising_value`

**What：** `/sys/.../events/in_rot_from_north_true_raw_thresh_falling_value`

**What：** `/sys/.../events/in_rot_from_north_magnetic_tilt_comp_raw_thresh_rising_value`

**What：** `/sys/.../events/in_rot_from_north_magnetic_tilt_comp_raw_thresh_falling_value`

**What：** `/sys/.../events/in_rot_from_north_true_tilt_comp_raw_thresh_rising_value`

**What：** `/sys/.../events/in_rot_from_north_true_tilt_comp_raw_thresh_falling_value`

**What：** `/sys/.../events/in_voltageY_supply_raw_thresh_rising_value`

**What：** `/sys/.../events/in_voltageY_supply_raw_thresh_falling_value`

**What：** `/sys/.../events/in_voltageY_raw_thresh_rising_value`

**What：** `/sys/.../events/in_voltageY_raw_thresh_falling_value`

**What：** `/sys/.../events/in_tempY_raw_thresh_rising_value`

**What：** `/sys/.../events/in_tempY_raw_thresh_falling_value`

**What：** `/sys/.../events/in_illuminance0_thresh_falling_value`

**what：** `/sys/.../events/in_illuminance0_thresh_rising_value`

**what：** `/sys/.../events/in_proximity0_thresh_falling_value`

**what：** `/sys/.../events/in_proximity0_thresh_rising_value`

**KernelVersion：** 2.6.37

**Contact：** linux-iio@vger.kernel.org

**Description：** 

指定设备针对由 `<type>Y[_name]_thresh[_rising|falling]_en` 启用的事件进行比较的阈值值。如果针对两个方向存在单独的属性，但此属性未指定方向，则单个阈值值适用于两个方向。名称中的“raw”或“input”元素指示值是以原始设备单位还是以处理后单位（如 sysfs 直接通道读取属性上的“_raw”和“_input”）。

**What：** `/sys/.../events/in_accel_scale`

**What：** `/sys/.../events/in_accel_peak_scale`

**What：** `/sys/.../events/in_anglvel_scale`

**What：** `/sys/.../events/in_magn_scale`

**What：** `/sys/.../events/in_rot_from_north_magnetic_scale`

**What：** `/sys/.../events/in_rot_from_north_true_scale`

**What：** `/sys/.../events/in_voltage_scale`

**What：** `/sys/.../events/in_voltage_supply_scale`

**What：** `/sys/.../events/in_temp_scale`

**What：** `/sys/.../events/in_illuminance_scale`

**What：** `/sys/.../events/in_proximity_scale`

**KernelVersion：** 3.21

**Contact：** linux-iio@vger.kernel.org

**Description：** 

指定从标准单位到用于设置事件触发阈值的设备特定单位的转换因子。

**What：** `/sys/.../events/in_accel_x_thresh_rising_hysteresis`

**What：** `/sys/.../events/in_accel_x_thresh_falling_hysteresis`

**What：** `/sys/.../events/in_accel_x_thresh_either_hysteresis`

**What：** `/sys/.../events/in_accel_y_thresh_rising_hysteresis`

**What：** `/sys/.../events/in_accel_y_thresh_falling_hysteresis`

**What：** `/sys/.../events/in_accel_y_thresh_either_hysteresis`

**What：** `/sys/.../events/in_accel_z_thresh_rising_hysteresis`

**What：** `/sys/.../events/in_accel_z_thresh_falling_hysteresis`

**What：** `/sys/.../events/in_accel_z_thresh_either_hysteresis`

**What：** `/sys/.../events/in_anglvel_x_thresh_rising_hysteresis`

**What：** `/sys/.../events/in_anglvel_x_thresh_falling_hysteresis`

**What：** `/sys/.../events/in_anglvel_x_thresh_either_hysteresis`

**What：** `/sys/.../events/in_anglvel_y_thresh_rising_hysteresis`

**What：** `/sys/.../events/in_anglvel_y_thresh_falling_hysteresis`

**What：** `/sys/.../events/in_anglvel_y_thresh_either_hysteresis`

**What：** `/sys/.../events/in_anglvel_z_thresh_rising_hysteresis`

**What：** `/sys/.../events/in_anglvel_z_thresh_falling_hysteresis`

**What：** `/sys/.../events/in_anglvel_z_thresh_either_hysteresis`

**What：** `/sys/.../events/in_magn_x_thresh_rising_hysteresis`

**What：** `/sys/.../events/in_magn_x_thresh_falling_hysteresis`

**What：** `/sys/.../events/in_magn_x_thresh_either_hysteresis`

**What：** `/sys/.../events/in_magn_y_thresh_rising_hysteresis`

**What：** `/sys/.../events/in_magn_y_thresh_falling_hysteresis`

**What：** `/sys/.../events/in_magn_y_thresh_either_hysteresis`

**What：** `/sys/.../events/in_magn_z_thresh_rising_hysteresis`

**What：** `/sys/.../events/in_magn_z_thresh_falling_hysteresis`

**What：** `/sys/.../events/in_magn_z_thresh_either_hysteresis`

**What：** `/sys/.../events/in_rot_from_north_magnetic_thresh_rising_hysteresis`

**What：** `/sys/.../events/in_rot_from_north_magnetic_thresh_falling_hysteresis`

**What：** `/sys/.../events/in_rot_from_north_magnetic_thresh_either_hysteresis`

**What：** `/sys/.../events/in_rot_from_north_true_thresh_rising_hysteresis`

**What：** `/sys/.../events/in_rot_from_north_true_thresh_falling_hysteresis`

**What：** `/sys/.../events/in_rot_from_north_true_thresh_either_hysteresis`

**What：** `/sys/.../events/in_rot_from_north_magnetic_tilt_comp_thresh_rising_hysteresis`

**What：** `/sys/.../events/in_rot_from_north_magnetic_tilt_comp_thresh_falling_hysteresis`

**What：** `/sys/.../events/in_rot_from_north_magnetic_tilt_comp_thresh_either_hysteresis`

**What：** `/sys/.../events/in_rot_from_north_true_tilt_comp_thresh_rising_hysteresis`

**What：** `/sys/.../events/in_rot_from_north_true_tilt_comp_thresh_falling_hysteresis`

**What：** `/sys/.../events/in_rot_from_north_true_tilt_comp_thresh_either_hysteresis`

**What：** `/sys/.../events/in_voltageY_thresh_rising_hysteresis`

**What：** `/sys/.../events/in_voltageY_thresh_falling_hysteresis`

**What：** `/sys/.../events/in_voltageY_thresh_either_hysteresis`

**What：** `/sys/.../events/in_tempY_thresh_rising_hysteresis`

**What：** `/sys/.../events/in_tempY_thresh_falling_hysteresis`

**What：** `/sys/.../events/in_tempY_thresh_either_hysteresis`

**what：** `/sys/.../events/in_illuminance0_thresh_falling_hysteresis`

**what：** `/sys/.../events/in_illuminance0_thresh_rising_hysteresis`

**what：** `/sys/.../events/in_illuminance0_thresh_either_hysteresis`

**what：** `/sys/.../events/in_proximity0_thresh_falling_hysteresis`

**what：** `/sys/.../events/in_proximity0_thresh_rising_hysteresis`

**what：** `/sys/.../events/in_proximity0_thresh_either_hysteresis`

**KernelVersion：** 3.13

**Contact：** linux-iio@vger.kernel.org

**Description：** 

指定设备针对由 `<type>Y[_name]_thresh[_(rising|falling)]_hysteresis` 启用的事件进行比较的阈值滞后。如果针对两个方向存在单独的属性，但此属性未指定方向，则单个滞后值适用于两个方向。对于下降事件，滞后值将添加到此事件的`_value`属性中，以获取事件恢复正常时的上限阈值；对于上升事件，滞后值将从`_value`属性中减去。例如，如果`in_voltage0_raw_thresh_rising_value`设置为 1200，`in_voltage0_raw_thresh_rising_hysteresis`设置为 50，则一旦`in_voltage0_raw`超过 1200，事件将被激活，一旦值降至 1150 以下，事件将再次变为非活动状态。**设备事件相关路径：**

- /sys/.../events/in_accel_x_raw_roc_rising_value

- /sys/.../events/in_accel_x_raw_roc_falling_value

- /sys/.../events/in_accel_y_raw_roc_rising_value

- /sys/.../events/in_accel_y_raw_roc_falling_value

- /sys/.../events/in_accel_z_raw_roc_rising_value

- /sys/.../events/in_accel_z_raw_roc_falling_value

- /sys/.../events/in_anglvel_x_raw_roc_rising_value

- /sys/.../events/in_anglvel_x_raw_roc_falling_value

- /sys/.../events/in_anglvel_y_raw_roc_rising_value

- /sys/.../events/in_anglvel_y_raw_roc_falling_value

- /sys/.../events/in_anglvel_z_raw_roc_rising_value

- /sys/.../events/in_anglvel_z_raw_roc_falling_value

- /sys/.../events/in_magn_x_raw_roc_rising_value

- /sys/.../events/in_magn_x_raw_roc_falling_value

- /sys/.../events/in_magn_y_raw_roc_rising_value

- /sys/.../events/in_magn_y_raw_roc_falling_value

- /sys/.../events/in_magn_z_raw_roc_rising_value

- /sys/.../events/in_magn_z_raw_roc_falling_value

- /sys/.../events/in_rot_from_north_magnetic_raw_roc_rising_value

- /sys/.../events/in_rot_from_north_magnetic_raw_roc_falling_value

- /sys/.../events/in_rot_from_north_true_raw_roc_rising_value

- /sys/.../events/in_rot_from_north_true_raw_roc_falling_value

- /sys/.../events/in_rot_from_north_magnetic_tilt_comp_raw_roc_rising_value

- /sys/.../events/in_rot_from_north_magnetic_tilt_comp_raw_roc_falling_value

- /sys/.../events/in_rot_from_north_true_tilt_comp_raw_roc_rising_value

- /sys/.../events/in_rot_from_north_true_tilt_comp_raw_roc_falling_value

- /sys/.../events/in_voltageY_supply_raw_roc_rising_value

- /sys/.../events/in_voltageY_supply_raw_roc_falling_value

- /sys/.../events/in_voltageY_raw_roc_rising_value

- /sys/.../events/in_voltageY_raw_roc_falling_value

- /sys/.../events/in_tempY_raw_roc_rising_value

- /sys/.../events/in_tempY_raw_roc_falling_value

**内核版本：**

KernelVersion: 2.6.37

**联系邮箱：**

Contact: linux-iio@vger.kernel.org

**描述：**

指定设备针对由 `<type>[Y][_name]_roc[_rising|falling]_en` 启用的事件进行比较的变化率阈值的值。

如果两个方向存在单独的属性，但此属性未指定方向，则单个阈值适用于两个方向。

名称中的“raw”或“input”元素表示该值是在原始设备单位还是在处理后的单位（如 sysfs 直接通道读取属性中的 `_raw` 和 `_input` ）。 

**其他事件相关路径：**

- /sys/.../events/in_accel_x_thresh_rising_period

- /sys/.../events/in_accel_x_thresh_falling_period

- /sys/.../events/in_accel_x_roc_rising_period

- /sys/.../events/in_accel_x_roc_falling_period

- /sys/.../events/in_accel_y_thresh_rising_period

- /sys/.../events/in_accel_y_thresh_falling_period

- /sys/.../events/in_accel_y_roc_rising_period

- /sys/.../events/in_accel_y_roc_falling_period

- /sys/.../events/in_accel_z_thresh_rising_period

- /sys/.../events/in_accel_z_thresh_falling_period

- /sys/.../events/in_accel_z_roc_rising_period

- /sys/.../events/in_accel_z_roc_falling_period

- /sys/.../events/in_anglvel_x_thresh_rising_period

- /sys/.../events/in_anglvel_x_thresh_falling_period

- /sys/.../events/in_anglvel_x_roc_rising_period

- /sys/.../events/in_anglvel_x_roc_falling_period

- /sys/.../events/in_anglvel_y_thresh_rising_period

- /sys/.../events/in_anglvel_y_thresh_falling_period

- /sys/.../events/in_anglvel_y_roc_rising_period

- /sys/.../events/in_anglvel_y_roc_falling_period

- /sys/.../events/in_anglvel_z_thresh_rising_period

- /sys/.../events/in_anglvel_z_thresh_falling_period

- /sys/.../events/in_anglvel_z_roc_rising_period

- /sys/.../events/in_anglvel_z_roc_falling_period

- /sys/.../events/in_magn_x_thresh_rising_period

- /sys/.../events/in_magn_x_thresh_falling_period

- /sys/.../events/in_magn_x_roc_rising_period

- /sys/.../events/in_magn_x_roc_falling_period

- /sys/.../events/in_magn_y_thresh_rising_period

- /sys/.../events/in_magn_y_thresh_falling_period

- /sys/.../events/in_magn_y_roc_rising_period

- /sys/.../events/in_magn_y_roc_falling_period

- /sys/.../events/in_magn_z_thresh_rising_period

- /sys/.../events/in_magn_z_thresh_falling_period

- /sys/.../events/in_magn_z_roc_rising_period

- /sys/.../events/in_magn_z_roc_falling_period

- /sys/.../events/in_rot_from_north_magnetic_thresh_rising_period

- /sys/.../events/in_rot_from_north_magnetic_thresh_falling_period

- /sys/.../events/in_rot_from_north_magnetic_roc_rising_period

- /sys/.../events/in_rot_from_north_magnetic_roc_falling_period

- /sys/.../events/in_rot_from_north_true_thresh_rising_period

- /sys/.../events/in_rot_from_north_true_thresh_falling_period

- /sys/.../events/in_rot_from_north_true_roc_rising_period

- /sys/.../events/in_rot_from_north_true_roc_falling_period

- /sys/.../events/in_rot_from_north_magnetic_tilt_comp_thresh_rising_period

- /sys/.../events/in_rot_from_north_magnetic_tilt_comp_thresh_falling_period

- /sys/.../events/in_rot_from_north_magnetic_tilt_comp_roc_rising_period

- /sys/.../events/in_rot_from_north_magnetic_tilt_comp_roc_falling_period

- /sys/.../events/in_rot_from_north_true_tilt_comp_thresh_rising_period

- /sys/.../events/in_rot_from_north_true_tilt_comp_thresh_falling_period

- /sys/.../events/in_rot_from_north_true_tilt_comp_roc_rising_period

- /sys/.../events/in_rot_from_north_true_tilt_comp_roc_falling_period

- /sys/.../events/in_voltageY_supply_thresh_rising_period

- /sys/.../events/in_voltageY_supply_thresh_falling_period

- /sys/.../events/in_voltageY_supply_roc_rising_period

- /sys/.../events/in_voltageY_supply_roc_falling_period

- /sys/.../events/in_voltageY_thresh_rising_period

- /sys/.../events/in_voltageY_thresh_falling_period

- /sys/.../events/in_voltageY_roc_rising_period

- /sys/.../events/in_voltageY_roc_falling_period

- /sys/.../events/in_tempY_thresh_rising_period

- /sys/.../events/in_tempY_thresh_falling_period

- /sys/.../events/in_tempY_roc_rising_period

- /sys/.../events/in_tempY_roc_falling_period

- /sys/.../events/in_accel_x&y&z_mag_falling_period

- /sys/.../events/in_intensity0_thresh_period

- /sys/.../events/in_proximity0_thresh_period

- /sys/.../events/in_activity_still_thresh_rising_period

- /sys/.../events/in_activity_still_thresh_falling_period

- /sys/.../events/in_activity_walking_thresh_rising_period

- /sys/.../events/in_activity_walking_thresh_falling_period

- /sys/.../events/in_activity_jogging_thresh_rising_period

- /sys/.../events/in_activity_jogging_thresh_falling_period

- /sys/.../events/in_activity_running_thresh_rising_period

- /sys/.../events/in_activity_running_thresh_falling_period

**内核版本（另一个）：**

KernelVersion: 3.19

**联系邮箱（另一个）：**

Contact: linux-iio@vger.kernel.org

**描述（另一个）：**

启用或禁用活动事件。根据传感器进入或离开给定状态的方向，在特定时刻生成事件。

**设备相关信息：**

- `/sys/.../events/in_activity_still_thresh_rising_value`

- `/sys/.../events/in_activity_still_thresh_falling_value`

- `/sys/.../events/in_activity_walking_thresh_rising_value`

- `/sys/.../events/in_activity_walking_thresh_falling_value`

- `/sys/.../events/in_activity_jogging_thresh_rising_value`

- `/sys/.../events/in_activity_jogging_thresh_falling_value`

- `/sys/.../events/in_activity_running_thresh_rising_value`

- `/sys/.../events/in_activity_running_thresh_falling_value`

**内核版本：** 3.19

**联系人：** linux-iio@vger.kernel.org

**描述：**

用于决定何时生成事件的置信值（以百分比为单位）。例如，对于跑步：如果传感器报告的置信值大于`in_activity_running_thresh_rising_value`，则传感器进入跑步状态。相反，如果传感器报告的置信值低于`in_activity_running_thresh_falling_value`，则传感器离开跑步状态。

- `/sys/.../iio:deviceX/events/in_accel_mag_en`

- `/sys/.../iio:deviceX/events/in_accel_mag_rising_en`

- `/sys/.../iio:deviceX/events/in_accel_mag_falling_en`

- `/sys/.../iio:deviceX/events/in_accel_x_mag_en`

- `/sys/.../iio:deviceX/events/in_accel_x_mag_rising_en`

- `/sys/.../iio:deviceX/events/in_accel_x_mag_falling_en`

- `/sys/.../iio:deviceX/events/in_accel_y_mag_en`

- `/sys/.../iio:deviceX/events/in_accel_y_mag_rising_en`

- `/sys/.../iio:deviceX/events/in_accel_y_mag_falling_en`

- `/sys/.../iio:deviceX/events/in_accel_z_mag_en`

- `/sys/.../iio:deviceX/events/in_accel_z_mag_rising_en`

- `/sys/.../iio:deviceX/events/in_accel_z_mag_falling_en`

- `/sys/.../iio:deviceX/events/in_accel_x&y&z_mag_rising_en`

- `/sys/.../iio:deviceX/events/in_accel_x&y&z_mag_falling_en`

**内核版本：** 2.6.37

**联系人：** linux-iio@vger.kernel.org

**描述：**

类似于`in_accel_x_thresh[_rising|_falling]_en`，但这里是将通道的幅度与阈值进行比较，而不是其有符号值。

- `/sys/.../events/in_accel_raw_mag_value`

- `/sys/.../events/in_accel_x_raw_mag_rising_value`

- `/sys/.../events/in_accel_y_raw_mag_rising_value`

- `/sys/.../events/in_accel_z_raw_mag_rising_value`

**内核版本：** 2.6.37

**联系人：** linux-iio@vger.kernel.org

**描述：**

设备将与之进行比较的幅度值。如果未指定数字或方向，则适用于此类型的所有通道。

- `/sys/.../events/in_steps_change_en`

**内核版本：** 4.0

**联系人：** linux-iio@vger.kernel.org

**描述：**

当通道通过绝对值变化阈值时生成的事件。例如，对于步数：每次用户走 N 步时都会生成一个步数变化事件，其中 N 使用`in_steps_change_value`设置。

- `/sys/.../events/in_steps_change_value`

**内核版本：** 4.0

**联系人：** linux-iio@vger.kernel.org

**描述：**

指定设备为`in_steps_change_en`启用的事件所比较的变化阈值值。例如，对于步数：如果设置为 3，则每 3 步将生成一个步数变化事件。

- `/sys/bus/iio/devices/iio:deviceX/trigger/current_trigger`

**内核版本：** 2.6.35

**联系人：** linux-iio@vger.kernel.org

**描述：**

正在使用的触发源的名称，根据`/sys/class/iio/triggerY/name`中给出的字符串。

- `/sys/bus/iio/devices/iio:deviceX/buffer/length`

**内核版本：** 2.6.35

**联系人：** linux-iio@vger.kernel.org

**描述：**

缓冲区中包含的扫描次数。

- `/sys/bus/iio/devices/iio:deviceX/buffer/bytes_per_datum`

**内核版本：** 2.6.37

**联系人：** linux-iio@vger.kernel.org

**描述：**

每个扫描的字节数。由于对齐问题，扫描可能比扫描元素参数直接暗示的要大。

- `/sys/bus/iio/devices/iio:deviceX/buffer/enable`

**内核版本：** 2.6.35

**联系人：** linux-iio@vger.kernel.org

**描述：**

实际开始缓冲区捕获。如果是第一个设备且合适，将启动触发。

- `/sys/bus/iio/devices/iio:deviceX/scan_elements`

**内核版本：** 2.6.37

**联系人：** linux-iio@vger.kernel.org

**描述：**

包含将为缓冲区中的单个触发样本集捕获的元素的接口目录。

- `/sys/.../iio:deviceX/scan_elements/in_accel_x_en`

- `/sys/.../iio:deviceX/scan_elements/in_accel_y_en`

- `/sys/.../iio:deviceX/scan_elements/in_accel_z_en`

- `/sys/.../iio:deviceX/scan_elements/in_anglvel_x_en`

- `/sys/.../iio:deviceX/scan_elements/in_anglvel_y_en`

- `/sys/.../iio:deviceX/scan_elements/in_anglvel_z_en`

- `/sys/.../iio:deviceX/scan_elements/in_magn_x_en`

- `/sys/.../iio:deviceX/scan_elements/in_magn_y_en`

- `/sys/.../iio:deviceX/scan_elements/in_magn_z_en`

- `/sys/.../iio:deviceX/scan_elements/in_rot_from_north_magnetic_en`

- `/sys/.../iio:deviceX/scan_elements/in_rot_from_north_true_en`

- `/sys/.../iio:deviceX/scan_elements/in_rot_from_north_magnetic_tilt_comp_en`

- `/sys/.../iio:deviceX/scan_elements/in_rot_from_north_true_tilt_comp_en`

- `/sys/.../iio:deviceX/scan_elements/in_timestamp_en`

- `/sys/.../iio:deviceX/scan_elements/in_voltageY_supply_en`

- `/sys/.../iio:deviceX/scan_elements/in_voltageY_en`

- `/sys/.../iio:deviceX/scan_elements/in_voltageY-voltageZ_en`

- `/sys/.../iio:deviceX/scan_elements/in_incli_x_en`

- `/sys/.../iio:deviceX/scan_elements/in_incli_y_en`

- `/sys/.../iio:deviceX/scan_elements/in_pressureY_en`

- `/sys/.../iio:deviceX/scan_elements/in_pressure_en`

- `/sys/.../iio:deviceX/scan_elements/in_rot_quaternion_en`

- `/sys/.../iio:deviceX/scan_elements/in_proximity_en`

**内核版本：** 2.6.37

**联系人：** linux-iio@vger.kernel.org

**描述：**

触发数据捕获的扫描元素控制。

- `/sys/.../iio:deviceX/scan_elements/in_accel_type`

- `/sys/.../iio:deviceX/scan_elements/in_anglvel_type`

- `/sys/.../iio:deviceX/scan_elements/in_magn_type`

- `/sys/.../iio:deviceX/scan_elements/in_incli_type`

- `/sys/.../iio:deviceX/scan_elements/in_voltageY_type`

- `/sys/.../iio:deviceX/scan_elements/in_voltage_type`

- `/sys/.../iio:deviceX/scan_elements/in_voltageY_supply_type`

- `/sys/.../iio:deviceX/scan_elements/in_timestamp_type`

- `/sys/.../iio:deviceX/scan_elements/in_pressureY_type`

- `/sys/.../iio:deviceX/scan_elements/in_pressure_type`

- `/sys/.../iio:deviceX/scan_elements/in_rot_quaternion_type`

- `/sys/.../iio:deviceX/scan_elements/in_proximity_type`

**内核版本：** 2.6.37

**联系人：** linux-iio@vger.kernel.org

**描述：**

缓冲区中扫描元素数据存储的描述，以及从用户空间读取它的形式。形式为[be|le]:[s|u]bits/storagebits[>>shift]。be 或 le 指定大端或小端。s 或 u 指定是否为有符号（2 的补码）或无符号。bits 是数据的位数，storagebits 是它在缓冲区中占用的空间（经过填充后）。如果指定了 shift，则在屏蔽未使用的位之前需要应用该 shift。一些设备将其数据放在传输的元素中间，并在两侧有额外的信息。请注意，一些设备在未使用的位中会有额外的信息，因此要获得干净的值，必须使用 bits 值适当地屏蔽缓冲区输出值。storagebits 值也指定了数据对齐。因此，s48/64>>2 将是一个存储在 64 位位置的有符号 48 位整数，对齐到 64 位边界。要获得干净的值，向右 shift 2 并应用掩码以零化结果的高位 16 位。对于其他存储组合，此属性将适当扩展。

- `/sys/.../iio:deviceX/scan_elements/in_accel_type_available`

**内核版本：** 2.6.37

**联系人：** linux-iio@vger.kernel.org

**描述：**

如果类型参数可以取一组小的值，此属性将列出它们。**What 相关：**

- `/sys/.../iio:deviceX/scan_elements/in_voltageY_index`

- `/sys/.../iio:deviceX/scan_elements/in_voltageY_supply_index`

- `/sys/.../iio:deviceX/scan_elements/in_accel_x_index`

- `/sys/.../iio:deviceX/scan_elements/in_accel_y_index`

- `/sys/.../iio:deviceX/scan_elements/in_accel_z_index`

- `/sys/.../iio:deviceX/scan_elements/in_anglvel_x_index`

- `/sys/.../iio:deviceX/scan_elements/in_anglvel_y_index`

- `/sys/.../iio:deviceX/scan_elements/in_anglvel_z_index`

- `/sys/.../iio:deviceX/scan_elements/in_magn_x_index`

- `/sys/.../iio:deviceX/scan_elements/in_magn_y_index`

- `/sys/.../iio:deviceX/scan_elements/in_magn_z_index`

- `/sys/.../iio:deviceX/scan_elements/in_rot_from_north_magnetic_index`

- `/sys/.../iio:deviceX/scan_elements/in_rot_from_north_true_index`

- `/sys/.../iio:deviceX/scan_elements/in_rot_from_north_magnetic_tilt_comp_index`

- `/sys/.../iio:deviceX/scan_elements/in_rot_from_north_true_tilt_comp_index`

- `/sys/.../iio:deviceX/scan_elements/in_incli_x_index`

- `/sys/.../iio:deviceX/scan_elements/in_incli_y_index`

- `/sys/.../iio:deviceX/scan_elements/in_timestamp_index`

- `/sys/.../iio:deviceX/scan_elements/in_pressureY_index`

- `/sys/.../iio:deviceX/scan_elements/in_pressure_index`

- `/sys/.../iio:deviceX/scan_elements/in_rot_quaternion_index`

- `/sys/.../iio:deviceX/scan_elements/in_proximity_index`

**KernelVersion：** 2.6.37

**Contact：** linux-iio@vger.kernel.org

**Description：**

一个指定此扫描元素在缓冲区中位置的单个正整数。注意，这些与启用的内容无关，可能不连续。因此，对于用户空间要确定完整布局，必须与所有 _en 属性结合使用，以确定存在哪些通道，并使用相关的 _type 属性确定数据存储格式。

**What 相关：**

- `/sys/.../iio:deviceX/in_activity_still_input`

- `/sys/.../iio:deviceX/in_activity_walking_input`

- `/sys/.../iio:deviceX/in_activity_jogging_input`

- `/sys/.../iio:deviceX/in_activity_running_input`

**KernelVersion：** 3.19

**Contact：** linux-iio@vger.kernel.org

**Description：**

此属性用于以百分比为单位读取活动的置信度。

**What 相关：**

- `/sys/.../iio:deviceX/in_anglvel_z_quadrature_correction_raw`

**KernelVersion：** 2.6.38

**Contact：** linux-iio@vger.kernel.org

**Description：**

此属性用于读取设备在给定时间存在的正交误差量。

**What 相关：**

- `/sys/.../iio:deviceX/in_accelX_power_mode`

**KernelVersion：** 3.11

**Contact：** linux-iio@vger.kernel.org

**Description：**

指定芯片电源模式。低噪声：降低 ADC 的噪声水平，低功耗：启用低电流消耗。要获取可用输出电源模式的列表，请读取 in_accel_power_mode_available。

**What 相关：**

- `/sys/.../iio:deviceX/in_energy_input`

- `/sys/.../iio:deviceX/in_energy_raw`

**KernelVersion：** 4.0

**Contact：** linux-iio@vger.kernel.org

**Description：**

此属性用于读取设备报告的能量值（例如：人体活动传感器报告用户消耗的能量）。应用比例后，单位为焦耳。

**What 相关：**

- `/sys/.../iio:deviceX/in_distance_input`

- `/sys/.../iio:deviceX/in_distance_raw`

**KernelVersion：** 4.0

**Contact：** linux-iio@vger.kernel.org

**Description：**

此属性用于读取自上次重新启动激活以来用户走过的距离。应用比例后，单位为米。

**What 相关：**

- `/sys/bus/iio/devices/iio:deviceX/store_eeprom`

**KernelVersion：** 3.4.0

**Contact：** linux-iio@vger.kernel.org

**Description：**

写入 '1' 将当前设备配置存储到片上 EEPROM 中。上电或芯片复位后，设备将自动加载保存的配置。

**What 相关：**

- `/sys/.../iio:deviceX/in_proximity_raw`

- `/sys/.../iio:deviceX/in_proximity_input`

- `/sys/.../iio:deviceX/in_proximityY_raw`

**KernelVersion：** 3.4

**Contact：** linux-iio@vger.kernel.org

**Description：**

接近度测量，表明某个物体靠近传感器，通常通过观察发射的红外或超声波的反射率来实现。这些传感器通常是无单位的，因此无法转换为 SI 单位。接近度测量值越高，表示物体越近，反之亦然。

**What 相关：**

- `/sys/.../iio:deviceX/in_illuminance_input`

- `/sys/.../iio:deviceX/in_illuminance_raw`

- `/sys/.../iio:deviceX/in_illuminanceY_input`

- `/sys/.../iio:deviceX/in_illuminanceY_raw`

- `/sys/.../iio:deviceX/in_illuminanceY_mean_raw`

- `/sys/.../iio:deviceX/in_illuminance_ir_raw`

- `/sys/.../iio:deviceX/in_illuminance_clear_raw`

**KernelVersion：** 3.4

**Contact：** linux-iio@vger.kernel.org

**Description：**

照度测量，应用比例和偏移后，单位为勒克斯。

**What 相关：**

- `/sys/.../iio:deviceX/in_intensityY_raw`

- `/sys/.../iio:deviceX/in_intensityY_ir_raw`

- `/sys/.../iio:deviceX/in_intensityY_both_raw`

**KernelVersion：** 3.4

**Contact：** linux-iio@vger.kernel.org

**Description：**

无单位的光强度。修饰符 both 和 ir 分别表示测量包含可见光和红外光成分或仅包含红外光。

**What 相关：**

- `/sys/.../iio:deviceX/in_intensity_red_integration_time`

- `/sys/.../iio:deviceX/in_intensity_green_integration_time`

- `/sys/.../iio:deviceX/in_intensity_blue_integration_time`

- `/sys/.../iio:deviceX/in_intensity_clear_integration_time`

- `/sys/.../iio:deviceX/in_illuminance_integration_time`

**KernelVersion：** 3.12

**Contact：** linux-iio@vger.kernel.org

**Description：**

此属性用于获取/设置以秒为单位的积分时间。

**What 相关：**

- `/sys/.../iio:deviceX/in_velocity_sqrt(x^2+y^2+z^2)_integration_time`

**KernelVersion：** 4.0

**Contact：** linux-iio@vger.kernel.org

**Description：**

计算速度的秒数。

**What 相关：**

- `/sys/bus/iio/devices/iio:deviceX/in_rot_quaternion_raw`

**KernelVersion：** 3.15

**Contact：** linux-iio@vger.kernel.org

**Description：**

使用 x y z w 格式的四元数分量的原始值。这里，x、y 和 z 分量表示将发生旋转的轴，w 分量表示旋转量。

**What 相关：**

- `/sys/bus/iio/devices/iio:deviceX/in_rot_from_north_magnetic_tilt_comp_raw`

- `/sys/bus/iio/devices/iio:deviceX/in_rot_from_north_true_tilt_comp_raw`

- `/sys/bus/iio/devices/iio:deviceX/in_rot_from_north_magnetic_raw`

- `/sys/bus/iio/devices/iio:deviceX/in_rot_from_north_true_raw`

**KernelVersion：** 3.15

**Contact：** linux-iio@vger.kernel.org

**Description：**

使用或不使用倾斜传感器补偿从真/磁北测量的旋转的原始值。

**What 相关：**

- `/sys/.../iio:deviceX/in_currentX_raw`

**KernelVersion：** 3.18

**Contact：** linux-iio@vger.kernel.org

**Description：**

来自通道 X 的原始电流测量值。应用比例和偏移后，单位为毫安。如果不存在偏移或比例，则输出应视为以毫安为单位进行处理。

**What 相关：**

- `/sys/.../iio:deviceX/in_energy_en`

- `/sys/.../iio:deviceX/in_distance_en`

- `/sys/.../iio:deviceX/in_velocity_sqrt(x^2+y^2+z^2)_en`

- `/sys/.../iio:deviceX/in_steps_en`

**KernelVersion：** 3.19

**Contact：** linux-iio@vger.kernel.org

**Description：**

激活在固件/硬件中运行的设备功能。例如，对于步数：计步器在不使用时节省电量；激活后，它将在固件中计算用户采取的步数，并通过 in_steps_input 导出它们。

**What 相关：**

- `/sys/.../iio:deviceX/in_steps_input`

**KernelVersion：** 3.19

**Contact：** linux-iio@vger.kernel.org

**Description：**

此属性用于读取自上次重新启动激活以来用户采取的步数。

**What 相关：**

- `/sys/.../iio:deviceX/in_velocity_sqrt(x^2+y^2+z^2)_input`

- `/sys/.../iio:deviceX/in_velocity_sqrt(x^2+y^2+z^2)_raw`

**KernelVersion：** 3.19

**Contact：** linux-iio@vger.kernel.org

**Description：**

此属性用于读取用户的当前速度值（即速度向量的范数或大小）。应用比例后的单位是 m/s。

**waht**：/sys/.../iio:deviceX/in_steps_debounce_count

内核版本：4.0

联系人：linux-iio@vger.kernel.org

描述：指定在 in_steps_filter_debounce_time 内必须发生的步数，以使计步器决定消费者正在行走。

**waht**：/sys/.../iio:deviceX/in_steps_debounce_time

内核版本：4.0

联系人：linux-iio@vger.kernel.org

描述：指定计算步数的秒数，以便决定消费者是否在行走。

**waht**：/sys/bus/iio/devices/iio:deviceX/buffer/watermark

内核版本：4.2

联系人：linux-iio@vger.kernel.org

描述：一个正整数，指定要等待的扫描元素的最大数量。轮询将阻塞，直到达到水印。阻塞读取将等待，直到请求的读取量或低水印之间的最小值可用。非阻塞读取将从缓冲区中检索可用的样本，即使样本数量少于水印级别。这允许应用程序在轮询时设置超时并在超时过期后读取可用的样本，从而保证最大延迟。

**waht**：/sys/bus/iio/devices/iio:deviceX/buffer/hwfifo_enabled

内核版本：4.2

联系人：linux-iio@vger.kernel.org

描述：一个只读的布尔值，指示硬件 FIFO 当前是启用还是禁用。如果设备没有硬件 FIFO，则此条目不存在。当缓冲区启用且当前硬件 FIFO 水印级别设置且其他当前设备设置允许时，硬件 FIFO 启用。如果硬件 FIFO 启用且硬件 FIFO 的级别达到硬件 FIFO 水印级别，设备将其硬件 FIFO 刷新到设备缓冲区。在设备缓冲区中没有样本时对设备进行非阻塞读取也将强制刷新。当硬件 FIFO 启用时，无需使用触发器来使用缓冲区模式，因为水印设置保证了硬件 FIFO 被刷新到设备缓冲区。

**waht**：/sys/bus/iio/devices/iio:deviceX/buffer/hwfifo_watermark

内核版本：4.2

联系人：linux-iio@vger.kernel.org

描述：一个只读条目，包含一个指定硬件 FIFO 当前水印级别的整数。如果设备没有硬件 FIFO，则此条目不存在。硬件 FIFO 的水印级别由驱动程序根据用户在 buffer/watermark 中设置的值设置，但要考虑硬件限制（例如，大多数硬件缓冲区限制为 32-64 个样本，一些硬件缓冲区水印是固定的或有最低级别）。值为 0 表示硬件水印未设置。

**waht**：/sys/bus/iio/devices/iio:deviceX/buffer/hwfifo_watermark_min

内核版本：4.2

联系人：linux-iio@vger.kernel.org

描述：一个正整数，指定此设备硬件 FIFO 的最小水印级别。如果设备没有硬件 FIFO，则此条目不存在。如果用户将 buffer/watermark 设置为小于此值的值，则硬件水印将保持未设置。

**waht**：/sys/bus/iio/devices/iio:deviceX/buffer/hwfifo_watermark_max

内核版本：4.2

联系人：linux-iio@vger.kernel.org

描述：一个正整数，指定此设备硬件 FIFO 的最大水印级别。如果设备没有硬件 FIFO，则此条目不存在。如果用户将 buffer/watermark 设置为大于此值的值，则硬件水印将被限制在该值。

**waht**：/sys/bus/iio/devices/iio:deviceX/buffer/hwfifo_watermark_available

内核版本：4.2

联系人：linux-iio@vger.kernel.org

描述：一个正整数列表，指定硬件 FIFO 的可用水印级别。此条目是可选的，如果不存在，则意味着支持 hwfifo_watermark_min 和 hwfifo_watermark_max 之间的所有值。如果用户将 buffer/watermark 设置为大于 hwfifo_watermak_min 但不等于此列表中的任何值，则驱动程序将为硬件 FIFO 水印级别选择一个适当的值。