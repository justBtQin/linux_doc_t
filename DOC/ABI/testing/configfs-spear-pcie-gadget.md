**What**: /config/pcie-gadget

**Date**: 二月 2011

**KernelVersion**: 2.6.37

**Contact**: Pratyush Anand <pratyush.anand@st.com>

**Description**:

该接口用于将选定的双模式 PCIe 控制器配置为设备，然后对其各种寄存器进行编程以将其配置为特定的设备类型。此接口可用于显示 spear 的 PCIe 设备功能。仅在 configfs 挂载时节点才可见。要在 /config 目录中挂载 configfs，请使用：

`# mount -t configfs none /config/`

对于第 n 个 PCIe 设备控制器

/config/pcie-gadget.n/

    link...用于启用 ltssm 并读取其状态。int_type...用于配置和读取支持的中断类型

    no_of_msi...用于配置所需的 MSI 向量数量并读取授予的 MSI 数量。inta...写入 1 以断言 INTA，写入 0 以取消断言。send_msi...写入要发送的 MSI 向量。vendor_id...用于写入和读取供应商 ID（十六进制）

    device_id...用于写入和读取设备 ID（十六进制）

    bar0_size...用于写入和读取 bar0_size

    bar0_address...用于写入和读取十六进制的 bar0 映射区域。bar0_rw_offset...用于写入和读取 bar0 的偏移量，其中 bar0_data 将被写入或读取。bar0_data...用于在 bar0_rw_offset 处写入和读取数据。