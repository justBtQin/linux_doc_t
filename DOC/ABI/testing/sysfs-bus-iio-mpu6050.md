**What：** `/sys/bus/iio/devices/iio:deviceX/in_gyro_matrix`

**What：** `/sys/bus/iio/devices/iio:deviceX/in_accel_matrix`

**What：** `/sys/bus/iio/devices/iio:deviceX/in_magn_matrix`

**KernelVersion：** 3.4.0

**Contact：** linux-iio@vger.kernel.org

**Description：**

这是运动传感器的安装矩阵。安装矩阵是一个 3x3 的酉矩阵。一个典型的安装矩阵看起来像

```
\[
\begin{bmatrix}
0 & 1 & 0 \\
1 & 0 & 0 \\
0 & 0 & -1
\end{bmatrix}
\]。
```

使用此信息，可以轻松确定传感器之间的相对位置以及它们相对于承载这些传感器的板的位置。单位矩阵

```
\[
\begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0 & 0 & 1
\end{bmatrix}
\]
```

意味着传感器芯片和设备彼此完全对齐。所有轴完全相同。