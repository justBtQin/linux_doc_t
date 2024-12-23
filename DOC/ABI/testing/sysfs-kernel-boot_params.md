**What**: `/sys/kernel/boot_params`

**Date**: December 2013

**Contact**: Dave Young <dyoung@redhat.com>

**Description**:  `/sys/kernel/boot_params`目录包含两个文件："data"和"version"以及一个子目录"setup_data"。它用于将 x86 平台的内核启动参数导出到用户空间，用于 kexec 和调试目的。如果在 boot_params 中没有 setup_data，则不会创建该子目录。"data"文件是 struct boot_params 的二进制表示。"version"文件是启动协议版本的字符串表示。"setup_data"子目录包含 boot_params 中的 setup_data 数据结构。setup_data 在内核中作为链表维护。在"setup_data"子目录中，每个链表节点有一个子目录，以链表节点的编号命名。链表节点子目录包含两个文件"type"和"data"。"type"文件是 setup_data 类型的字符串表示。"data"文件是 setup_data 有效负载的二进制表示。

整个 boot_params 目录结构如下：

`/sys/kernel/boot_params`

|__ data

|__ setup_data

|   |__ 0

|   |   |__ data

|   |   |__ type

|   |__ 1

|       |__ data

|       |__ type

|__ version

**Users**: Kexec