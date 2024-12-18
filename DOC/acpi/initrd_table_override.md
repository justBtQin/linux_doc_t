# 通过 initrd 覆盖 ACPI 表

=================================

1) 介绍（这是关于什么的）

2) 这是用于什么的

3) 它是如何工作的

4) 参考资料（在哪里获取用户空间工具）

## 1) 这是关于什么的

---------------------

如果 ACPI_INITRD_TABLE_OVERRIDE 编译选项为真，则可以用一个经过检测和修改的 ACPI 表覆盖 BIOS 提供的几乎任何 ACPI 表。

要获取可覆盖的 ACPI 表的完整列表，请查看 drivers/acpi/osl.c 中的 char *table_sigs[MAX_ACPI_SIGNATURE]; 定义。

iasl（英特尔的 ACPI 编译器和解码器）知道的所有 ACPI 表都应该是可覆盖的，除了：

    - ACPI_SIG_RSDP（具有 6 字节的签名）

    - ACPI_SIG_FACS（没有普通的 ACPI 表头）

这两者也可以实现。

## 2) 这是用于什么的

-------------------

请记住，这是一个调试选项。ACPI 表不应该为生产使用而被覆盖。如果 BIOS ACPI 表被覆盖，内核将被标记为 TAINT_OVERRIDDEN_ACPI_TABLE 标志。如果您发现一个严重到在 Linux 内核中不接受变通方法的错误，请向您的平台/BIOS 供应商抱怨。

然而，它可以并且应该在任何内核中启用，因为：

    - 没有经过检测的 initrd 不会有功能上的改变。

    - 它提供了一个强大的功能，可以轻松地调试和测试 ACPI BIOS 表与 Linux 内核的兼容性。

## 3) 它是如何工作的

-------------------
```
# Extract the machine's ACPI tables:
cd /tmp
acpidump >acpidump
acpixtract -a acpidump
# Disassemble, modify and recompile them:
iasl -d *.dat
# For example add this statement into a _PRT (PCI Routing Table) function
# of the DSDT:
Store("HELLO WORLD", debug)
iasl -sa dsdt.dsl
# Add the raw ACPI tables to an uncompressed cpio archive.
# They must be put into a /kernel/firmware/acpi directory inside the
# cpio archive.
# The uncompressed cpio archive must be the first.
# Other, typically compressed cpio archives, must be
# concatenated on top of the uncompressed one.
mkdir -p kernel/firmware/acpi
cp dsdt.aml kernel/firmware/acpi
# A maximum of: #define ACPI_OVERRIDE_TABLES 10
# tables are  currently allowed (see osl.c):
iasl -sa facp.dsl
iasl -sa ssdt1.dsl
cp facp.aml kernel/firmware/acpi
cp ssdt1.aml kernel/firmware/acpi
# Create the uncompressed cpio archive and concatenate the original initrd
# on top:
find kernel | cpio -H newc --create > /boot/instrumented_initrd
cat /boot/initrd >>/boot/instrumented_initrd
# reboot with increased acpi debug level, e.g. boot params:
acpi.debug_level=0x2 acpi.debug_layer=0xFFFFFFFF
# and check your syslog:
[    1.268089] ACPI: PCI Interrupt Routing Table [\_SB_.PCI0._PRT]
[    1.272091] [ACPI Debug]  String [0x0B] "HELLO WORLD"
```

iasl 能够反汇编和重新编译相当多不同的，也包括静态的 ACPI 表。

## 4) 在哪里获取用户空间工具

------------------------------------

iasl 和 acpixtract 是英特尔 ACPICA 项目的一部分：

http://acpica.org/

并且应该由发行版打包（例如在 SUSE 上的 acpica 包中）。

acpidump 可以在 Len Browns 的 pmtools 中找到：

ftp://kernel.org/pub/linux/kernel/people/lenb/acpi/utils/pmtools/acpidump

这个工具也是 SUSE 上 acpica 包的一部分。或者，在最新的内核中，可以通过 sysfs 检索使用的 ACPI 表：

/sys/firmware/acpi/tables