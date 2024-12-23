**What**: 安全/ EVM

**Date**: 2011 年 3 月

**Contact**: Mimi Zohar <zohar@us.ibm.com>

**Description**:

EVM 保护文件的安全扩展属性（xattrs）免受完整性攻击。初始方法在扩展属性上维护一个 HMAC-sha1 值，将该值存储为扩展属性“security.evm”。

EVM 依赖内核密钥保留系统为其提供用于 HMAC-sha1 操作的受信任/加密密钥。该密钥使用 keyctl 加载到根的密钥环上。在 EVM 接收到密钥已成功加载到密钥环的通知（echo 1 > <securityfs>/evm）之前，EVM 无法创建或验证“security.evm”xattr，但返回 INTEGRITY_UNKNOWN。应尽早加载密钥并向 EVM 发出信号。通常在 initramfs 中完成，initramfs 已作为可信引导的一部分进行了测量。有关创建和加载现有受信任/加密密钥的更多信息，请参考：Documentation/keys-trusted-encrypted.txt。（一个加载受信任/加密密钥并启用 EVM 的示例 dracut 补丁可从 http://linux-ima.sourceforge.net/#EVM 获得。）