# Simtec 电子 EB2410ITX（BAST）

===================================

http://www.simtec.co.uk/products/EB2410ITX/

# 简介

------------

EB2410ITX 是基于 S3C2410 的开发板，具有各种外围设备和扩展连接器。该板也被简称为 Bast。

# 配置

-------------

要设置默认配置，请使用`make bast_defconfig`，它支持该板的常用功能。

# 支持

-------

官方支持信息可在 Simtec 电子网站的产品页面 http://www.simtec.co.uk/products/EB2410ITX/ 找到。

有用的链接：

- 资源页面 http://www.simtec.co.uk/products/EB2410ITX/resources.html

- 板卡常见问题解答 http://www.simtec.co.uk/products/EB2410ITX/faq.html

# MTD

---

NAND 和 NOR 支持已从 linux-mtd 项目合并。如有任何问题，请访问 http://www.linux-mtd.infradead.org/ 以获取更多信息或最新版本的 linux-mtd。

# IDE

---

板上的两个 IDE 端口均受支持，但不支持更改设备速度，应使用支持 PIO 模式 4 的驱动器。

# 维护者

-----------

该板由 Simtec 电子维护。

版权 2004 Ben Dooks，Simtec 电子