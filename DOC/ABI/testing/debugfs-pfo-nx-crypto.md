**What**：/sys/kernel/debug/nx-crypto/*

**Date**：March 2012

**KernelVersion**：3.4

**Contact**：Kent Yoder <key@linux.vnet.ibm.com>

**Description**：

这些 debugfs 接口由 nx-crypto 驱动程序构建，内置于 arch/powerpc/crypto/nx 中。

# Error Detection

```
error：
	一个 u32 提供了自驱动程序加载以来的错误总数。这里仅计算从 hcall（H_COP_OP）返回的错误。

last_error：
	H_COP_OP hcall 的最近非零返回码。此处不记录 -EBUSY（hcall 将重试直到 -EBUSY 消失）。

last_error_pid：
	从 hcall 收到最近错误的进程的进程 ID。
```

# Device Use
```

aes_bytes：
	驱动程序支持的任何模式下使用 AES 加密的总字节数。

aes_ops：
	提交给硬件的 AES 操作总数。

sha256_bytes：
	硬件使用 SHA-256 哈希的总字节数。

sha256_ops：
	提交给硬件的 SHA-256 操作总数。

sha512_bytes：
	硬件使用 SHA-512 哈希的总字节数。

sha512_ops：
	提交给硬件的 SHA-512 操作总数。
```