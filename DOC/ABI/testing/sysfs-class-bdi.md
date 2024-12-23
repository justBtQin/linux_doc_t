**What**：`/sys/class/bdi/<bdi>/`

**Date**：2008 年 1 月

**Contact**：Peter Zijlstra <a.p.zijlstra@chello.nl>

**Description**：

在 sysfs 中为 backing_dev_info 对象提供一个位置。这允许设置和检索各种特定于 BDI 的变量。

`<bdi>` 标识符可以是以下之一：

- `MAJOR:MINOR`：块设备的设备号，或者对于提供自己的 BDI 的非块文件系统（如 NFS 和 FUSE），为 `st_dev` 的值。

- `MAJOR:MINOR-fuseblk`：fuseblk 文件系统的 `st_dev` 值。

- `default`：默认的后备设备，用于没有提供自己的 BDI 的非块设备支持的文件系统。

`/sys/class/bdi/<bdi>/` 下的文件

- `read_ahead_kb`（可读可写）：预读窗口的大小，以千字节为单位

- `min_ratio`（可读可写）：在正常情况下，每个设备都被分配了与总回写缓存相关的一部分，这与它相对于其他设备的当前平均写出速度有关。`min_ratio` 参数允许为特定设备分配回写缓存的最小百分比。例如，这对于提供最低 QoS 很有用。

- `max_ratio`（可读可写）：允许限制特定设备使用不超过给定百分比的回写缓存。这在我们希望避免一个设备占用全部或大部分回写缓存的情况下很有用。例如，在容易卡住的 NFS 挂载或不能被信任公平使用的 FUSE 挂载的情况下。

- `stable_pages_required`（只读）：如果设置，后备设备要求组成写入请求的所有页面在写出完成之前都不能被更改。 