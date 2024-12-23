**/sys/class/tpm/tpmX/device/**

- **What**: 描述了一个特定 TPM 实例下的设备目录。

- **Date**: 2005 年 4 月

- **KernelVersion**: 2.6.12

- **Contact**: tpmdd-devel@lists.sf.net

- **Description**: 特定 TPM 实例下的`/device/`目录公开了该 TPM 芯片的属性。

**/sys/class/tpm/tpmX/device/active**

- **What**: 表示 TPM 芯片是否正在接受命令的属性。

- **Date**: 2006 年 4 月

- **KernelVersion**: 2.6.17

- **Contact**: tpmdd-devel@lists.sf.net

- **Description**: 若 TPM 芯片正在接受命令，"active"属性将打印'1'。非活动的 TPM 芯片仍包含活动芯片的所有状态（存储根密钥、NVRAM 等），并且对操作系统可见，但仅接受一组受限的命令。有关可用命令的更多信息，请参阅 TPM 主要规范第 2 部分、结构部分 17。

**/sys/class/tpm/tpmX/device/cancel**

- **What**: 用于取消当前挂起的 TPM 命令的属性。

- **Date**: 2005 年 6 月

- **KernelVersion**: 2.6.13

- **Contact**: tpmdd-devel@lists.sf.net

- **Description**: "cancel"属性允许取消当前挂起的 TPM 命令。向`cancel`写入任何值都将调用 TPM 供应商特定的取消操作。

**/sys/class/tpm/tpmX/device/caps**

- **What**: 包含 TPM 制造商和版本信息的属性。

- **Date**: 2005 年 4 月

- **KernelVersion**: 2.6.12

- **Contact**: tpmdd-devel@lists.sf.net

- **Description**: "caps"属性包含 TPM 制造商和版本信息。示例输出：

  - Manufacturer: 0x53544d20

  - TCG version: 1.2

  - Firmware version: 8.16

  - Manufacturer 是 TPM 中 4 字节制造商信息空间的十六进制转储。TCG version 显示芯片支持的 TCG TPM 规范级别。Firmware version 是芯片的版本，由制造商指定。

**/sys/class/tpm/tpmX/device/durations**

- **What**: 用于显示等待短、中、长 TPM 命令的 3 个供应商特定值的属性。

- **Date**: 2011 年 3 月

- **KernelVersion**: 3.1

- **Contact**: tpmdd-devel@lists.sf.net

- **Description**: "durations"属性显示用于等待短、中、长 TPM 命令的 3 个供应商特定值。所有 TPM 命令在执行时间上分为短、中、长，以便驱动程序在开始轮询结果之前不必等待不必要的时间。示例输出：

  - 3015000 4508000 180995000 [original]

  - 这里以微秒显示短、中、长持续时间。"[original]"表示值是从芯片查询时未修改显示的。如果芯片以毫秒而不是微秒报告持续时间并且需要缩放到以微秒显示，则持续时间将被修改，此时将显示"[adjusted]"代替"[original]"。

**/sys/class/tpm/tpmX/device/enabled**

- **What**: 表示 TPM 芯片是否已启用的属性。

- **Date**: 2006 年 4 月

- **KernelVersion**: 2.6.17

- **Contact**: tpmdd-devel@lists.sf.net

- **Description**: 若 TPM 芯片已启用，"enabled"属性将打印'1'，这意味着它对操作系统可见。此属性在某些禁用 TPM 的操作后可能可见但显示'0'。

**/sys/class/tpm/tpmX/device/owned**

- **What**: 表示 TPM_TakeOwnership 命令是否已在芯片中成功执行的属性。

- **Date**: 2006 年 4 月

- **KernelVersion**: 2.6.17

- **Contact**: tpmdd-devel@lists.sf.net

- **Description**: 若 TPM_TakeOwnership 命令已成功执行，"owned"属性将产生'1'。'0'表示尚未获取所有权。

**/sys/class/tpm/tpmX/device/pcrs**

- **What**: 用于转储 TPM 中当前所有平台配置寄存器值的属性。

- **Date**: 2005 年 4 月

- **KernelVersion**: 2.6.12

- **Contact**: tpmdd-devel@lists.sf.net

- **Description**: "pcrs"属性将转储 TPM 中当前所有平台配置寄存器的值。请注意，由于这些值可能在不断变化，输出仅在特定时间点有效。示例输出：

  - PCR-00: 3A 3F 78 0F 11 A4 B4 99 69 FC AA 80 CD 6E 39 57 C3 3B 22 75

  - PCR-01: 3A 3F 78 0F 11 A4 B4 99 69 FC AA 80 CD 6E 39 57 C3 3B 22 75

  - PCR-02: 3A 3F 78 0F 11 A4 B4 99 69 FC AA 80 CD 6E 39 57 C3 3B 22 75

  - PCR-03: 3A 3F 78 0F 11 A4 B4 99 69 FC AA 80 CD 6E 39 57 C3 3B 22 75

  - PCR-04: 3A 3F 78 0F 11 A4 B4 99 69 FC AA 80 CD 6E 39 57 C3 3B 22 75

  -...

  - PCR 的数量和表示 PCR 值所需的十六进制字节数将根据 TPM 芯片版本而变化。对于 TPM 1.1 和 1.2 芯片，PCR 表示 SHA-1 哈希，长度为 20 字节。使用"caps"属性确定 TPM 版本。

**/sys/class/tpm/tpmX/device/pubek**

- **What**: 用于返回 TPM 的公共背书密钥的属性（如果可能）。

- **Date**: 2005 年 4 月

- **KernelVersion**: 2.6.12

- **Contact**: tpmdd-devel@lists.sf.net

- **Description**: "pubek"属性将返回 TPM 的公共背书密钥（如果可能）。如果 TPM 已建立所有权并且是 1.2 版本，则在没有所有者授权的情况下不可用。由于 TPM 驱动程序不存储任何秘密，它无法授权自己获取 pubek，使其无法访问。公共背书密钥在 TPM 制造时生成，并在芯片的整个生命周期中存在。示例输出：

  - Algorithm: 00 00 00 01

  - Encscheme: 00 03

  - Sigscheme: 00 01

  - Parameters: 00 00 08 00 00 00 00 02 00 00 00 00

  - Modulus length: 256

  - Modulus:

    - B4 76 41 82 C9 20 2C 10 18 40 BC 8B E5 44 4C 6C

    - 3A B2 92 0C A4 9B 2A 83 EB 5C 12 85 04 48 A0 B6

    - 1E E4 81 84 CE B2 F2 45 1C F0 85 99 61 02 4D EB

    - 86 C4 F7 F3 29 60 52 93 6B B2 E5 AB 8B A9 09 E3

    - D7 0E 7D CA 41 BF 43 07 65 86 3C 8C 13 7A D0 8B

    - 82 5E 96 0B F8 1F 5F 34 06 DA A2 52 C1 A9 D5 26

    - 0F F4 04 4B D9 3F 2D F2 AC 2F 74 64 1F 8B CD 3E

    - 1E 30 38 6C 70 63 69 AB E2 50 DF 49 05 2E E1 8D

    - 6F 78 44 DA 57 43 69 EE 76 6C 38 8A E9 8E A3 F0

    - A7 1F 3C A8 D0 12 15 3E CA 0E BD FA 24 CD 33 C6

    - 47 AE A4 18 83 8E 22 39 75 93 86 E6 FD 66 48 B6

    - 10 AD 94 14 65 F9 6A 17 78 BD 16 53 84 30 BF 70

    - E0 DC 65 FD 3C C6 B0 1E BF B9 C1 B5 6C EF B1 3A

    - F8 28 05 83 62 26 11 DC B4 6B 5A 97 FF 32 26 B6

    - F7 02 71 CF 15 AE 16 DD D1 C1 8E A8 CF 9B 50 7B

    - C3 91 FF 44 1E CF 7C 39 FE 17 77 21 20 BD CE 9B

  - Possible values:

    - Algorithm: TPM_ALG_RSA (1)

    - Encscheme: TPM_ES_RSAESPKCSv15 (2)

            TPM_ES_RSAESOAEP_SHA1_MGF1 (3)

    - Sigscheme: TPM_SS_NONE (1)

    - Parameters, a byte string of 3 u32 values:

      - Key Length (bits): 00 00 08 00 (2048)

      - Num primes: 00 00 00 02 (2)

      - Exponent Size: 00 00 00 00 (0 表示默认指数)

    - Modulus Length: 256 (bytes)

    - Modulus: 256 字节的背书密钥模数

**/sys/class/tpm/tpmX/device/temp_deactivated**

- **What**: 表示芯片是否已被临时停用的属性（通常直到下一个电源周期）。

- **Date**: 2006 年 4 月

- **KernelVersion**: 2.6.17

- **Contact**: tpmdd-devel@lists.sf.net

- **Description**: 若芯片已被临时停用，"temp_deactivated"属性将返回'1'。热启动（重启）是否会将 TPM 芯片从临时停用状态中清除是平台特定的。

**/sys/class/tpm/tpmX/device/timeouts**

- **What**: 用于显示 TPM 接口规范超时的 4 个供应商特定值的属性。

- **Date**: 2011 年 3 月

- **KernelVersion**: 3.1

- **Contact**: tpmdd-devel@lists.sf.net

- **Description**: "timeouts"属性显示 TPM 接口规范超时的 4 个供应商特定值。这些超时的使用由芯片符合的 TPM 接口规范定义。示例输出：

  - 750000 750000 750000 750000 [original]

  - 四个超时值以微秒显示，后面带有"[original]"或"[adjusted]"，取决于驱动程序是否将值从毫秒缩放到微秒进行报告。