**n_gsm.c GSM 0710 终端复用器使用指南**
===================================================

此线路规则实现了 GSM 07.10 复用协议，详细内容见以下 3GPP 文档：
http://www.3gpp.org/ftp/Specs/archive/07_series/07.10/0710-720.zip

本文档提供了一些关于如何将此驱动程序与连接到物理串行端口的 GPRS 和 3G 调制解调器一起使用的提示。

**如何使用**
-------------
1. 通过其串行端口将调制解调器初始化为 0710 复用模式（通常使用 `AT+CMUX=` 命令）。根据所使用的调制解调器，您可以向此命令传递或多或少的参数。
2. 通过使用 `TIOCSETD` `ioctl` 将串行线路切换为使用 `n_gsm` 线路规则。
3. 使用 `GSMIOC_GETCONF` / `GSMIOC_SETCONF` `ioctl` 配置复用器。

初始化程序的主要部分：
（一个好的起点是 `util-linux-ng/sys-utils/ldattach.c`）
```c
#include <linux/gsmmux.h>
#define N_GSM0710    21    /* GSM 0710 Mux */
#define DEFAULT_SPEED B115200
#define SERIAL_PORT   /dev/ttyS0

int ldisc = N_GSM0710;
struct gsm_config c;
struct termios configuration;

/* 打开连接到调制解调器的串行端口 */
fd = open(SERIAL_PORT, O_RDWR | O_NOCTTY | O_NDELAY);

/* 配置串行端口：速度、流控制等... */

/* 发送 `AT+CMUX=0\r` 命令将调制解调器切换到 CMUX 模式，并检查是否成功（应返回 `OK`） */
write(fd, "AT+CMUX=0\r", 10);

/* 经验表明，一些调制解调器在能够响应第一个 MUX 数据包之前需要一些时间，因此在某些情况下可能需要在此处延迟 */
sleep(3);

/* 使用 `n_gsm` 线路规则 */
ioctl(fd, TIOCSETD, &ldisc);

/* 获取 `n_gsm` 配置 */
ioctl(fd, GSMIOC_GETCONF, &c);
/* 我们是发起方，需要编码 0（基本） */
c.initiator = 1;
c.encapsulation = 0;
/* 我们的调制解调器默认最大大小为 127 字节 */
c.mru = 127;
c.mtu = 127;
/* 设置新的配置 */
ioctl(fd, GSMIOC_SETCONF, &c);

/* 并永远等待以保持线路规则启用 */
daemon(0,0);
pause();
```
4. 创建对应于“虚拟”串行端口的设备（请注意，每个调制解调器都有其配置，并且某些数据链路控制（DLC）具有专用功能，例如 GPS），从次设备号 1 开始（DLC0 保留用于复用器的管理）

```
MAJOR=`cat /proc/devices |grep gsmtty | awk '{print $1}`
for i in `seq 1 4`; do
    mknod /dev/ttygsm$i c $MAJOR $i
done
```
5. 将这些设备用作普通串行端口。例如，可以：
 - 使用 `gnokii` 在 `ttygsm1` 上发送/接收短信
 - 使用 `ppp` 在 `ttygsm2` 上建立数据链路

6. 在关闭物理端口之前，首先关闭所有虚拟端口。

**附加文档**
------------------------
有关协议以及工业调制解调器如何支持它的更多实际细节，可以在以下文档中找到：
http://www.telit.com/module/infopool/download.php?id=616
http://www.u-blox.com/images/downloads/Product_Docs/LEON-G100-G200-MuxImplementation_ApplicationNote_%28GSM%20G1-CS-10002%29.pdf
http://www.sierrawireless.com/Support/Downloads/AirPrime/WMP_Series/~/media/Support_Downloads/AirPrime/Application_notes/CMUX_Feature_Application_Note-Rev004.ashx
http://wm.sim.com/sim/News/photo/2010721161442.pdf

11-03-08 - Eric Bénard - <eric@eukrea.com> 