<markdown_document>

# ASYMMETRIC / PUBLIC-KEY CRYPTOGRAPHY KEY TYPE

Contents:

- **Overview**

- **Key identification**

- **Accessing asymmetric keys**

    - **Signature verification**

- **Asymmetric key subtypes**

- **Instantiation data parsers**

========

Overview

========

“不对称”密钥类型旨在作为公钥密码学中使用的密钥的容器，而不对密码学的形式或机制或密钥的形式施加任何特定限制。

不对称密钥被赋予一个子类型，该子类型定义了与密钥相关联的数据类型，并提供了描述和销毁它的操作。然而，并没有要求实际将密钥数据存储在密钥中。

可以定义一个完全在内核中的密钥保留和操作子类型，但也可以提供对密码硬件（如 TPM）的访问，该硬件可用于保留相关密钥并使用该密钥执行操作。在这种情况下，不对称密钥将只是 TPM 驱动程序的一个接口。

还提供了数据解析器的概念。数据解析器负责从传递给实例化函数的数据块中提取信息。第一个识别该数据块的解析器将有权设置密钥的子类型并定义可以对该密钥执行的操作。

数据解析器可以将数据块解释为包含表示密钥的位，或者将其解释为对系统中其他地方保存的密钥（例如 TPM）的引用。

==================

Key identification

==================

如果添加一个名称为空的密钥，实例化数据解析器将有机会预先解析密钥，并根据密钥的内容确定密钥的描述。

然后可以通过完全匹配或部分匹配来引用该密钥。密钥类型也可以使用其他标准来引用密钥。

不对称密钥类型的匹配函数可以执行比仅将描述与标准字符串进行直接比较更广泛的比较：

(1) 如果标准字符串的形式为“id:<十六进制数字>”，则匹配函数将检查密钥的指纹，以查看“id:”之后给出的十六进制数字是否与尾部匹配。例如：

`keyctl search @s asymmetric id:5acc2142`

将与指纹为：

`1A00 2040 7601 7889 DE11  882C 3823 04AD 5ACC 2142`的密钥匹配。

(2) 如果标准字符串的形式为“<子类型>:<十六进制数字>”，则匹配将像(1)中那样匹配 ID，但添加了限制，即仅匹配指定子类型（例如 tpm）的密钥。例如：

`keyctl search @s asymmetric tpm:5acc2142`

在 /proc/keys 中，将显示密钥指纹的最后 8 个十六进制数字以及子类型：

`1a39e171 I-----     1 perm 3f010000     0     0 asymmetri modsign.0: DSA 5acc2142 []`

=========================

Accessing asymmetric keys

=========================

要在内核中访问不对称密钥，需要包含以下内容：

`#include <crypto/public_key.h>`

这将提供处理不对称/公钥的函数。在其中定义了三个枚举来表示公钥密码学算法：

`enum pkey_algo`

以及这些算法使用的摘要算法：

`enum pkey_hash_algo`

和密钥标识符表示：

`enum pkey_id_type`

请注意，需要密钥类型表示类型，因为来自不同标准的密钥标识符不一定兼容。例如，PGP 通过对密钥数据加上一些 PGP 特定的元数据进行哈希来生成密钥标识符，而 X.509 具有任意的证书标识符。

可以对密钥执行的操作如下：

(1) 签名验证。

使用相同的密钥数据可以执行其他操作（如加密），但目前不支持，而其他操作（例如解密和签名生成）需要额外的密钥数据。

SIGNATURE VERIFICATION

----------------------

提供了一个操作来执行密码签名验证，使用不对称密钥提供或提供对公钥的访问。

`int verify_signature(const struct key *key,

const struct public_key_signature *sig);`

调用者必须已经从某个源获取了密钥，然后可以使用它来检查签名。调用者必须解析签名并将相关位传输到指向 sig 的结构中。

`struct public_key_signature {

u8 *digest;

u8 digest_size;

enum pkey_hash_algo pkey_hash_algo : 8;

u8 nr_mpi;

union {

MPI mpi[2];

... };

};`

必须在 sig->pkey_hash_algo 中记录使用的算法，并且构成实际签名的所有 MPI 都必须存储在 sig->mpi[] 中，并将 MPI 的数量放置在 sig->nr_mpi 中。

此外，数据必须由调用者进行摘要，并且生成的哈希必须指向 sig->digest，哈希的大小必须放置在 sig->digest_size 中。

如果签名匹配，函数将返回 0；如果签名不匹配，将返回 -EKEYREJECTED。

该函数还可能返回 -ENOTSUPP（如果指定了不支持的公钥算法或公钥/哈希算法组合，或者密钥不支持该操作）；-EBADMSG 或 -ERANGE（如果某些参数有奇怪的数据）；或 -ENOMEM（如果无法进行分配）。如果密钥参数类型错误或未完全设置，将返回 -EINVAL。

=======================

Asymmetric key subtypes

=======================

不对称密钥有一个子类型，该子类型定义了可以对该密钥执行的操作集，并确定将什么数据作为密钥有效负载附加。有效负载格式完全由子类型决定。

子类型由密钥数据解析器选择，并且解析器必须初始化其所需的数据。不对称密钥保留对子类型模块的引用。

子类型定义结构可以在以下位置找到：

`#include <keys/asymmetric-subtype.h>`

如下所示：

`struct asymmetric_key_subtype {

struct module *owner;

const char *name;

void (*describe)(const struct key *key, struct seq_file *m);

void (*destroy)(void *payload);

int (*verify_signature)(const struct key *key,

const struct public_key_signature *sig);

};`

不对称密钥通过其 type_data[0]成员指向此结构。

所有者和名称字段应设置为所属模块和子类型的名称。目前，名称仅用于打印语句。

子类型定义了以下一些操作：

(1) describe()。

必需。这允许子类型在 /proc/keys 中针对密钥显示一些内容。例如，可以显示公钥算法类型的名称。密钥类型将在其后显示密钥标识字符串的尾部。

(2) destroy()。

必需。这应该释放与密钥相关联的内存。不对称密钥将负责释放指纹并释放对子类型模块的引用。

(3) verify_signature()。

可选。这些是密钥使用操作的入口点。目前仅定义了一个。如果未设置，调用者将获得 -ENOTSUPP。子类型可以执行任何它喜欢的操作来实现一个操作，包括卸载到硬件。

==========================

Instantiation data parsers

==========================

不对称密钥类型通常不想存储或处理保存密钥数据的原始数据块。每次使用它时都必须解析它并进行错误检查。此外，数据块的内容可能有各种可以对其执行的检查（例如自签名、有效期），并且可能包含有关密钥的有用数据（标识符、功能）。

此外，数据块可能表示指向包含密钥的硬件的指针，而不是密钥本身。

可以为其实现解析器的数据块格式示例包括：

- OpenPGP 数据包流 [RFC 4880]。

- X.509 ASN.1 流。

- 指向 TPM 密钥的指针。

- 指向 UEFI 密钥的指针。

在密钥实例化期间，将尝试列表中的每个解析器，直到一个解析器不返回 -EBADMSG。

解析器定义结构可以在以下位置找到：

`#include <keys/asymmetric-parser.h>`

如下所示：

`struct asymmetric_key_parser {

struct module *owner;

const char *name;

int (*parse)(struct key_preparsed_payload *prep);

};`

所有者和名称字段应设置为所属模块和解析器的名称。

目前，解析器仅定义了一个操作，并且是必需的：

(1) parse()。

在密钥创建和更新路径中用于预先解析密钥。特别是，在分配密钥之前的密钥创建期间调用它，因此，如果调用者拒绝这样做，它可以提供密钥的描述。

调用者传递一个指向以下结构的指针，除了 data、datalen 和 quotalen 之外，所有字段都已清除（请参阅 Documentation/security/keys.txt）。

`struct key_preparsed_payload {

char *description;

void *type_data[2];

void *payload;

const void *data;

size_t datalen;

size_t quotalen;

};`

实例化数据位于指向 data 的数据块中，大小为 datalen。parse()函数根本不允许更改这两个值，并且除非它们识别数据块格式并且不会返回 -EBADMSG 以表明不是它们的，否则不应更改任何其他值。

如果解析器对数据块满意，它应该为密钥提出一个描述并将其附加到 ->description，->type_data[0] 应设置为指向要使用的子类型，->payload 应设置为指向该子类型的初始化数据，->type_data[1] 应指向一个十六进制指纹，并且 quotalen 应更新以指示此密钥应占用的配额。

清理时，将 kfree() 释放 ->type_data[1] 和 ->description 所附加的数据，并将 ->payload 所附加的数据传递给子类型的 ->destroy()方法进行处理。将放置对子类型的引用，该子类型由 ->type_data[0] 指向。

如果未识别数据格式，应返回 -EBADMSG。如果识别但由于某些原因无法设置密钥，应返回其他负错误代码。成功时，应返回 0。

可以对密钥的指纹字符串进行部分匹配。对于 RSA 和 DSA 等公钥算法，这可能是密钥指纹的可打印十六进制版本。

提供了以下函数来注册和注销解析器：

`int register_asymmetric_key_parser(struct asymmetric_key_parser *parser);

void unregister_asymmetric_key_parser(struct asymmetric_key_parser *subtype);`

解析器不得具有相同的名称。名称仅在调试消息中用于显示。

</markdown_document>