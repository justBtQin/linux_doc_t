<markdown_document>

# cpufreq-nforce2 驱动在 NVIDIA nForce2 平台上更改 FSB。

这在其他平台上效果更好，因为 CPU 的 FSB 可以独立于 PCI/AGP 时钟进行控制。

该模块有两个选项：

- `fid`：乘数 * 10（例如 8.5 = 85）

- `min_fsb`：最小 FSB

如果未设置，fid 是根据当前 CPU 速度和 FSB 计算得出的。min_fsb 默认在启动时为 FSB - 50 MHz。

重要事项：可用范围向下受限！ 此外，对于启动时为 200 MHz 的系统，最小可用 FSB 可能会有所不同，150 应该始终有效。

</markdown_document>