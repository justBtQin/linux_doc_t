To decode a hex IOCTL code:

Most architectures use this generic format, but check `include/ARCH/ioctl.h` for specifics, e.g. powerpc uses 3 bits to encode read/write and 13 bits for size.

| Bits | Meaning |
|------|---------|
| 31-30 | 00 - no parameters: uses `_IO` macro<br>10 - read: `_IOR`<br>01 - write: `_IOW`<br>11 - read/write: `_IOWR` |
| 29-16 | Size of arguments |
| 15-8  | ASCII character supposedly unique to each driver |
| 7-0   | Function # |

So for example 0x82187201 is a read with arg length of 0x218, character 'r' function 1. Grepping the source reveals this is:

```
#define VFAT_IOCTL_READDIR_BOTH         _IOR('r', 1, struct dirent [2])
```