**关于 /sys/class/infiniband_srp/srp-<hca>-<port_number>/add_target 的信息：**

- **日期**：2006 年 1 月 2 日

- **内核版本**：2.6.15

- **联系人**：linux-rdma@vger.kernel.org

- **描述**：用于使 ib_srp 连接到新目标的接口。可以通过向此 sysfs 属性写入逗号分隔的登录参数列表来请求 ib_srp 连接到新目标。支持的参数如下：

    - **id_ext**：一个 16 位十六进制数字，指定 16 字节 SRP 目标端口标识符中的 8 字节标识符扩展。SRP_LOGIN_REQ 请求中，ib_srp 将目标端口标识符发送到目标。

    - **ioc_guid**：一个 16 位十六进制数字，指定 16 字节目标端口标识符的 8 字节 I/O 控制器 GUID 部分。

    - **dgid**：一个 32 位十六进制数字，指定目标 GID。

    - **pkey**：一个 4 位十六进制数字，指定 InfiniBand 分区密钥。

    - **service_id**：一个 16 位十六进制数字，指定用于与 SRP 目标建立通信的 InfiniBand 服务 ID。如何查找 service_id 的值在 SRP 目标的文档中有规定。

    - **max_sect**：一个十进制数字，指定通过单个 SCSI 命令传输的最大 512 字节扇区数。

    - **max_cmd_per_lun**：一个十进制数字，指定单个 LUN 的最大未完成命令数。

    - **io_class**：一个十六进制数字，指定 SRP I/O 类。必须是 0xff00（版本 10）或 0x0100（版本 16a）。I/O 类定义了 SRP 启动器和目标端口标识符的格式。

    - **initiator_ext**：一个 16 位十六进制数字，指定 SRP 启动器端口标识符的标识符扩展部分。此数据在 SRP_LOGIN_REQ 请求中由启动器发送到目标。

    - **cmd_sg_entries**：一个在 1 到 255 范围内的数字，指定存储在 SRP_CMD 信息单元本身中的最大数据缓冲区描述符数。当 allow_ext_sg=0 时，参数 cmd_sg_entries 定义单个 SRP_CMD 的最大 S/G 列表长度，并且 S/G 列表折叠后 S/G 列表长度超过此限制的命令将失败。

    - **allow_ext_sg**：是否允许 ib_srp 在 SRP_CMD 中包含部分内存描述符列表，而不是整个列表。如果在 SRP_CMD 中包含了部分内存描述符列表，则剩余的内存描述符将通过额外的 RDMA 传输从启动器发送到目标。将 allow_ext_sg 设置为 1 可增加通过单个 SCSI 命令在启动器和目标之间传输的最大数据量。由于并非所有 SRP 目标实现都支持部分内存描述符列表，此选项的默认值为 0。

    - **sg_tablesize**：一个在 1 到 2048 范围内的数字，指定 SCSI 层允许传递给 ib_srp 的最大 S/G 列表长度。指定一个超过 cmd_sg_entries 的值仅在启用部分内存描述符列表支持（allow_ext_sg=1）时是安全的。

    - **comp_vector**：一个在 0 到 n-1 范围内的数字，指定第一个 RDMA 通道的 MSI-X 完成向量。某些 HCA 为每个 HCA 端口分配多个（n）MSI-X 向量。如果这些中断的 IRQ 关联掩码已配置为每个 MSI-X 中断由不同的 CPU 处理，则 comp_vector 参数可用于将 SRP 完成工作负载分散到多个 CPU 上。

    - **tl_retry_count**：一个在 2 到 7 范围内的数字，指定 IB RC 重试计数。

    - **queue_size**：启动器在每个 SCSI 主机上允许排队的最大命令数。此参数的默认值为 62。支持的最低值为 2。

**关于 /sys/class/infiniband_srp/srp-<hca>-<port_number>/ibdev 的信息：**

- **日期**：2006 年 1 月 2 日

- **内核版本**：2.6.15

- **联系人**：linux-rdma@vger.kernel.org

- **描述**：HCA 名称（<hca>）。

**关于 /sys/class/infiniband_srp/srp-<hca>-<port_number>/port 的信息：**

- **日期**：2006 年 1 月 2 日

- **内核版本**：2.6.15

- **联系人**：linux-rdma@vger.kernel.org

- **描述**：HCA 端口号（<port_number>）。

**关于 /sys/class/scsi_host/host<n>/allow_ext_sg 的信息：**

- **日期**：2011 年 5 月 19 日

- **内核版本**：2.6.39

- **联系人**：linux-rdma@vger.kernel.org

- **描述**：当与 SRP 目标通信时，ib_srp 是否允许在 SRP_CMD 中包含部分内存描述符列表。

**关于 /sys/class/scsi_host/host<n>/ch_count 的信息：**

- **日期**：2015 年 4 月 1 日

- **内核版本**：3.19

- **联系人**：linux-rdma@vger.kernel.org

- **描述**：用于与 SRP 目标通信的 RDMA 通道数。

**关于 /sys/class/scsi_host/host<n>/cmd_sg_entries 的信息：**

- **日期**：2011 年 5 月 19 日

- **内核版本**：2.6.39

- **联系人**：linux-rdma@vger.kernel.org

- **描述**：在单个 SRP_CMD 请求中可以发送到目标的最大数据缓冲区描述符数。

**关于 /sys/class/scsi_host/host<n>/comp_vector 的信息：**

- **日期**：2013 年 9 月 2 日

- **内核版本**：3.11

- **联系人**：linux-rdma@vger.kernel.org

- **描述**：用于第一个 RDMA 通道的完成向量。

**关于 /sys/class/scsi_host/host<n>/dgid 的信息：**

- **日期**：2006 年 6 月 17 日

- **内核版本**：2.6.17

- **联系人**：linux-rdma@vger.kernel.org

- **描述**：用于与 SRP 目标通信的 InfiniBand 目标 GID。如果发生端口重定向，则与 orig_dgid 不同。

**关于 /sys/class/scsi_host/host<n>/id_ext 的信息：**

- **日期**：2006 年 6 月 17 日

- **内核版本**：2.6.17

- **联系人**：linux-rdma@vger.kernel.org

- **描述**：16 字节目标端口标识符的 8 字节标识符扩展部分。

**关于 /sys/class/scsi_host/host<n>/ioc_guid 的信息：**

- **日期**：2006 年 6 月 17 日

- **内核版本**：2.6.17

- **联系人**：linux-rdma@vger.kernel.org

- **描述**：16 字节目标端口标识符的 8 字节 I/O 控制器 GUID 部分。

**关于 /sys/class/scsi_host/host<n>/local_ib_device 的信息：**

- **日期**：2006 年 11 月 29 日

- **内核版本**：2.6.19

- **联系人**：linux-rdma@vger.kernel.org

- **描述**：用于与 SRP 目标通信的 InfiniBand HCA 名称。

**关于 /sys/class/scsi_host/host<n>/local_ib_port 的信息：**

- **日期**：2006 年 11 月 29 日

- **内核版本**：2.6.19

- **联系人**：linux-rdma@vger.kernel.org

- **描述**：用于与 SRP 目标通信的 HCA 端口号。

**关于 /sys/class/scsi_host/host<n>/orig_dgid 的信息：**

- **日期**：2006 年 6 月 17 日

- **内核版本**：2.6.17

- **联系人**：linux-rdma@vger.kernel.org

- **描述**：在写入 add_target sysfs 属性的参数中指定的 InfiniBand 目标 GID。

**关于 /sys/class/scsi_host/host<n>/pkey 的信息：**

- **日期**：2006 年 6 月 17 日

- **内核版本**：2.6.17

- **联系人**：linux-rdma@vger.kernel.org

- **描述**：一个 16 位数字，表示用于与 SRP 目标通信的 InfiniBand 分区密钥。

**关于 /sys/class/scsi_host/host<n>/req_lim 的信息：**

- **日期**：2010 年 10 月 20 日

- **内核版本**：2.6.36

- **联系人**：linux-rdma@vger.kernel.org

- **描述**：ib_srp 在必须等待更多信用之前可以发送到目标的请求数。有关更多信息，请参阅 SRP 规范中的 SRP 信用算法。

**关于 /sys/class/scsi_host/host<n>/service_id 的信息：**

- **日期**：2006 年 6 月 17 日

- **内核版本**：2.6.17

- **联系人**：linux-rdma@vger.kernel.org

- **描述**：用于与 SRP 目标建立通信的 InfiniBand 服务 ID。

**关于 /sys/class/scsi_host/host<n>/sgid 的信息：**

- **日期**：2014 年 2 月 1 日

- **内核版本**：3.13

- **联系人**：linux-rdma@vger.kernel.org

- **描述**：用于与 SRP 目标通信的源端口的 InfiniBand GID。

**关于 /sys/class/scsi_host/host<n>/zero_req_lim 的信息：**

- **日期**：2006 年 9 月 20 日

- **内核版本**：2.6.18

- **联系人**：linux-rdma@vger.kernel.org

- **描述**：启动器在由于信用耗尽而向目标发送请求之前必须等待的次数。有关更多信息，请参阅 SRP 规范中的 SRP 信用算法。