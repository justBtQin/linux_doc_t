<markdown_document>

# Scatterlist Cryptographic API

## INTRODUCTION

Scatterlist Crypto API 以页面向量（散射列表）作为参数，并直接在页面上工作。在某些情况下（例如 ECB 模式密码），这将允许页面原地加密而无需复制。

此设计的最初目标之一是易于支持 IPsec，以便可以对分页 skb 进行处理而无需线性化。

## DETAILS

最低级别是算法，它们动态注册到 API 中。

“变换”是用户实例化的对象，它维护状态，处理所有实现逻辑（例如操作散射列表）并为底层算法提供抽象。然而，在用户级别，它们非常简单。

概念上，API 分层如下：

  [变换 API]（用户界面）

  [变换操作]（按类型的逻辑胶水，例如 cipher.c、compress.c）

  [算法 API]（用于注册算法）

其思想是使用户界面和算法注册 API 非常简单，同时对两者隐藏核心逻辑。许多现有 API（如 Cryptoapi 和 Nettle）的良好思想已为此进行了改编。

API 目前支持五种主要类型的变换：AEAD（带关联数据的认证加密）、块密码、密码、压缩器和哈希。

请注意，“块密码”有点用词不当。实际上，它旨在支持所有密码，包括流密码。块密码和密码之间的区别在于，后者仅对一个块进行操作，而前者可以对任意数量的数据进行操作，但要符合块大小要求（即非流密码只能处理块的倍数）。

通过异步接口对硬件加密设备的支持正在开发中。

以下是如何使用该 API 的示例：

```c
#include <linux/crypto.h>
#include <linux/err.h>
#include <linux/scatterlist.h>

struct scatterlist sg[2];
char result[128];
struct crypto_hash *tfm;
struct hash_desc desc;

tfm = crypto_alloc_hash("md5", 0, CRYPTO_ALG_ASYNC);
if (IS_ERR(tfm))
    fail();

/*...设置散射列表...*/

desc.tfm = tfm;
desc.flags = 0;

if (crypto_hash_digest(&desc, sg, 2, result))
    fail();

crypto_free_hash(tfm);
```

许多实际示例可在回归测试模块（tcrypt.c）中找到。

## DEVELOPER NOTES

变换只能在用户上下文中分配，密码方法只能从软中断和用户上下文中调用。对于具有 setkey 方法的变换，也应仅从用户上下文中调用。

在使用 API 进行密码处理时，如果每个散射列表包含的数据是密码块大小的倍数（通常为 8 字节），则性能最佳。这可以防止在非对齐的页面片段边界上进行任何复制。

## ADDING NEW ALGORITHMS

提交新算法以供包含时，一个强制性要求是至少包含来自已知来源（最好是标准）的一些测试向量。

优先转换现有的知名代码，因为它更有可能经过审查和广泛测试。如果提交来自 LGPL 源的代码，请考虑将许可证更改为 GPL（见 LGPL 的第 3 节）。

提交的算法还必须总体上无专利（例如，IDEA 在 2011 年左右之前不会包含在主线中），并基于公认的标准和/或经过适当的同行评审。

还要检查是否有任何 RFC 与特定算法的使用有关，以及一般的应用说明，如 RFC2451（“ESP CBC-Mode 密码算法”）。

最好避免使用大量宏，而是使用内联函数，因为 gcc 在内联方面做得很好，而过度使用宏可能会在某些平台上导致编译问题。

还可以查看下面列出的网站上的 TODO 列表，以查看人们可能已经在做什么。

## BUGS

发送错误报告至：

linux-crypto@vger.kernel.org

抄送：Herbert Xu <herbert@gondor.apana.org.au>，

    David S. Miller <davem@redhat.com>

## FURTHER INFORMATION

有关更多补丁和各种更新，包括当前的 TODO 列表，请参阅：

http://gondor.apana.org.au/~herbert/crypto/

## AUTHORS

James Morris

David S. Miller

Herbert Xu

## CREDITS

以下人员在 API 的开发过程中提供了宝贵的反馈：

  Alexey Kuznetzov

  Rusty Russell

  Herbert Valerio Riedel

  Jeff Garzik

  Michael Richardson

  Andrew Morton

  Ingo Oeser

  Christoph Hellwig

此 API 的部分内容源自以下项目：

  Kerneli Cryptoapi（http://www.kerneli.org/）

    Alexander Kjeldaas

    Herbert Valerio Riedel

    Kyle McMartin

    Jean-Luc Cooke

    David Bryson

    Clemens Fruhwirth

    Tobias Ringstrom

    Harald Welte

以及：

  Nettle（http://www.lysator.liu.se/~nisse/nettle/）

    Niels Möller

密码算法的原始开发者：

  Dana L. How（DES）

  Andrew Tridgell 和 Steve French（MD4）

  Colin Plumb（MD5）

  Steve Reid（SHA1）

  Jean-Luc Cooke（SHA256、SHA384、SHA512）

  Kazunori Miyazawa / USAGI（HMAC）

  Matthew Skala（Twofish）

  Dag Arne Osvik（Serpent）

  Brian Gladman（AES）

  Kartikey Mahendra Bhatt（CAST6）

  Jon Oberheide（ARC4）

  Jouni Malinen（Michael MIC）

  NTT（Nippon Telegraph and Telephone Corporation）（Camellia）

SHA1 算法贡献者：

  Jean-Francois Dive

DES 算法贡献者：

  Raimar Falke

  Gisle Sælensminde

  Niels Möller

Blowfish 算法贡献者：

  Herbert Valerio Riedel

  Kyle McMartin

Twofish 算法贡献者：

  Werner Koch

  Marc Mutz

SHA256/384/512 算法贡献者：

  Andrew McDonald

  Kyle McMartin

  Herbert Valerio Riedel

AES 算法贡献者：

  Alexander Kjeldaas

  Herbert Valerio Riedel

  Kyle McMartin

  Adam J. Richter

  Fruhwirth Clemens（i586）

  Linus Torvalds（i586）

CAST5 算法贡献者：

  Kartikey Mahendra Bhatt（原始开发者未知，FSF 版权）。

TEA/XTEA 算法贡献者：

  Aaron Grothe

  Michael Ringe

Khazad 算法贡献者：

  Aaron Grothe

Whirlpool 算法贡献者：

  Aaron Grothe

  Jean-Luc Cooke

Anubis 算法贡献者：

  Aaron Grothe

Tiger 算法贡献者：

  Aaron Grothe

VIA PadLock 贡献者：

  Michal Ludvig

Camellia 算法贡献者：

  NTT（Nippon Telegraph and Telephone Corporation）（Camellia）

通用散射遍历代码由 Adam J. Richter <adam@yggdrasil.com>提供。

请将任何信用更新或更正发送至：

Herbert Xu <herbert@gondor.apana.org.au>

```