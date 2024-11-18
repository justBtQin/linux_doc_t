# Ioctl Numbers
19 October 1999
Michael Elizabeth Chastain
<mec@shout.net>

如果您要向内核添加新的 ioctl，您应该使用 `<linux/ioctl.h>` 中定义的 `_IO` 宏：

 - `_IO`  没有参数的 ioctl
 - `_IOW`  具有写入参数（从用户空间复制）的 ioctl
 - `_IOR`  具有读取参数（向用户空间复制）的 ioctl
 - `_IOWR`  同时具有写入和读取参数的 ioctl

“写入”和“读取”是从用户的角度来看的，就像系统调用“write”和“read”一样。例如，SET_FOO ioctl 会是 `_IOW`，尽管内核实际上会从用户空间读取数据；GET_FOO ioctl 会是 `_IOR`，尽管内核实际上会向用户空间写入数据。

对于 `_IO`、`_IOW`、`_IOR` 或 `_IOWR` 的第一个参数，是来自下面表格的识别字母或数字。由于驱动程序数量众多，许多驱动程序与其他驱动程序共享部分字母。

如果您正在为新设备编写驱动程序并且需要一个字母，请选择一个未使用的、有足够扩展空间的块：32 到 256 个 ioctl 命令。您可以通过修补此文件并将补丁提交给 Linus Torvalds 来注册该块。或者您可以给我发电子邮件至 `<mec@shout.net>`，我会为您注册一个。

`_IO`、`_IOW`、`_IOR` 或 `_IOWR` 的第二个参数是一个序列号，用于区分不同的 ioctl。`_IOW`、`_IOR` 或 `_IOWR` 的第三个参数是进入内核或从内核传出的数据类型（例如 'int' 或 'struct foo'）。注意！不要将 `sizeof(arg)` 用作第三个参数，因为这会导致您的 ioctl 认为它传递的参数类型为 `size_t`。

某些设备使用其主编号作为标识符；只要它是唯一的，这是可以的。有些设备不规范，不遵循任何约定。

遵循此约定是有好处的，原因如下：

(1) 保持 ioctl 在全局范围内的唯一性有助于错误检查：如果程序在错误的设备上调用 ioctl，它将得到一个错误，而不是一些意外的行为。

(2) `strace` 构建过程会自动查找使用 `_IO`、`_IOW`、`_IOR` 或 `_IOWR` 定义的 ioctl 编号。

(3) 当编号唯一时，`strace` 可以将编号解码回有用的名称。

(4) 当使用此约定定义 ioctl 编号时，查找 ioctl 的人可以更轻松地通过 `grep` 来查找它们。

(5) 遵循此约定时，驱动程序代码可以使用通用代码在用户空间和内核空间之间复制参数。

此表列出了 Linux/x86 中从用户空间可见的 ioctl。它包含了截至 2.6.31 版本的大多数驱动程序，但我知道我遗漏了一些。没有尝试列出非 X86 架构或来自 `drivers/staging/` 的 ioctl。

Code  Seq#(hex)	Include File		Comments
========================================================
	0x00	00-1F	linux/fs.h		conflict!
	0x00	00-1F	scsi/scsi_ioctl.h	conflict!
	0x00	00-1F	linux/fb.h		conflict!
	0x00	00-1F	linux/wavefront.h	conflict!
	0x02	all	linux/fd.h
	0x03	all	linux/hdreg.h
	0x04	D2-DC	linux/umsdos_fs.h	Dead since 2.6.11, but don't reuse these.
	0x06	all	linux/lp.h
	0x09	all	linux/raid/md_u.h
	0x10	00-0F	drivers/char/s390/vmcp.h
	0x10	10-1F	arch/s390/include/uapi/sclp_ctl.h
	0x10	20-2F	arch/s390/include/uapi/asm/hypfs.h
	0x12	all	linux/fs.h
			linux/blkpg.h
	0x1b	all	InfiniBand Subsystem	<http://infiniband.sourceforge.net/>
	0x20	all	drivers/cdrom/cm206.h
	0x22	all	scsi/sg.h
	'#'	00-3F	IEEE 1394 Subsystem	Block for the entire subsystem
	'$'	00-0F	linux/perf_counter.h, linux/perf_event.h
	'&'	00-07	drivers/firewire/nosy-user.h
	'1'	00-1F	<linux/timepps.h>	PPS kit from Ulrich Windl
						<ftp://ftp.de.kernel.org/pub/linux/daemons/ntp/PPS/>
	'2'	01-04	linux/i2o.h
	'3'	00-0F	drivers/s390/char/raw3270.h	conflict!
	'3'	00-1F	linux/suspend_ioctls.h	conflict!
			and kernel/power/user.c
	'8'	all				SNP8023 advanced NIC card
						<mailto:mcr@solidum.com>
	';'	64-7F	linux/vfio.h
	'@'	00-0F	linux/radeonfb.h	conflict!
	'@'	00-0F	drivers/video/aty/aty128fb.c	conflict!
	'A'	00-1F	linux/apm_bios.h	conflict!
	'A'	00-0F	linux/agpgart.h		conflict!
			and drivers/char/agp/compat_ioctl.h
	'A'	00-7F	sound/asound.h		conflict!
	'B'	00-1F	linux/cciss_ioctl.h	conflict!
	'B'	00-0F	include/linux/pmu.h	conflict!
	'B'	C0-FF				advanced bbus
						<mailto:maassen@uni-freiburg.de>
	'C'	all	linux/soundcard.h	conflict!
	'C'	01-2F	linux/capi.h		conflict!
	'C'	F0-FF	drivers/net/wan/cosa.h	conflict!
	'D'	all	arch/s390/include/asm/dasd.h
	'D'	40-5F	drivers/scsi/dpt/dtpi_ioctl.h
	'D'	05	drivers/scsi/pmcraid.h
	'E'	all	linux/input.h		conflict!
	'E'	00-0F	xen/evtchn.h		conflict!
	'F'	all	linux/fb.h		conflict!
	'F'	01-02	drivers/scsi/pmcraid.h	conflict!
	'F'	20	drivers/video/fsl-diu-fb.h	conflict!
	'F'	20	drivers/video/intelfb/intelfb.h	conflict!
	'F'	20	linux/ivtvfb.h		conflict!
	'F'	20	linux/matroxfb.h	conflict!
	'F'	20	drivers/video/aty/atyfb_base.c	conflict!
	'F'	00-0F	video/da8xx-fb.h	conflict!
	'F'	80-8F	linux/arcfb.h		conflict!
	'F'	DD	video/sstfb.h		conflict!
	'G'	00-3F	drivers/misc/sgi-gru/grulib.h	conflict!
	'G'	00-0F	linux/gigaset_dev.h	conflict!
	'H'	00-7F	linux/hiddev.h		conflict!
	'H'	00-0F	linux/hidraw.h		conflict!
	'H'	01	linux/mei.h		conflict!
	'H'	00-0F	sound/asound.h		conflict!
	'H'	20-40	sound/asound_fm.h	conflict!
	'H'	80-8F	sound/sfnt_info.h	conflict!
	'H'	10-8F	sound/emu10k1.h		conflict!
	'H'	10-1F	sound/sb16_csp.h	conflict!
	'H'	10-1F	sound/hda_hwdep.h	conflict!
	'H'	40-4F	sound/hdspm.h		conflict!
	'H'	40-4F	sound/hdsp.h		conflict!
	'H'	90	sound/usb/usx2y/usb_stream.h
	'H'	A0	uapi/linux/usb/cdc-wdm.h
	'H'	C0-F0	net/bluetooth/hci.h	conflict!
	'H'	C0-DF	net/bluetooth/hidp/hidp.h	conflict!
	'H'	C0-DF	net/bluetooth/cmtp/cmtp.h	conflict!
	'H'	C0-DF	net/bluetooth/bnep/bnep.h	conflict!
	'H'	F1	linux/hid-roccat.h	<mailto:erazor_de@users.sourceforge.net>
	'H'	F8-FA	sound/firewire.h
	'I'	all	linux/isdn.h		conflict!
	'I'	00-0F	drivers/isdn/divert/isdn_divert.h	conflict!
	'I'	40-4F	linux/mISDNif.h		conflict!
	'J'	00-1F	drivers/scsi/gdth_ioctl.h
	'K'	all	linux/kd.h
	'L'	00-1F	linux/loop.h		conflict!
	'L'	10-1F	drivers/scsi/mpt2sas/mpt2sas_ctl.h	conflict!
	'L'	E0-FF	linux/ppdd.h		encrypted disk device driver
						<http://linux01.gwdg.de/~alatham/ppdd.html>
	'M'	all	linux/soundcard.h	conflict!
	'M'	01-16	mtd/mtd-abi.h		conflict!
			and drivers/mtd/mtdchar.c
	'M'	01-03	drivers/scsi/megaraid/megaraid_sas.h
	'M'	00-0F	drivers/video/fsl-diu-fb.h	conflict!
	'N'	00-1F	drivers/usb/scanner.h
	'N'	40-7F	drivers/block/nvme.c
	'O'     00-06   mtd/ubi-user.h		UBI
	'P'	all	linux/soundcard.h	conflict!
	'P'	60-6F	sound/sscape_ioctl.h	conflict!
	'P'	00-0F	drivers/usb/class/usblp.c	conflict!
	'Q'	all	linux/soundcard.h
	'R'	00-1F	linux/random.h		conflict!
	'R'	01	linux/rfkill.h		conflict!
	'R'	C0-DF	net/bluetooth/rfcomm.h
	'S'	all	linux/cdrom.h		conflict!
	'S'	80-81	scsi/scsi_ioctl.h	conflict!
	'S'	82-FF	scsi/scsi.h		conflict!
	'S'	00-7F	sound/asequencer.h	conflict!
	'T'	all	linux/soundcard.h	conflict!
	'T'	00-AF	sound/asound.h		conflict!
	'T'	all	arch/x86/include/asm/ioctls.h	conflict!
	'T'	C0-DF	linux/if_tun.h		conflict!
	'U'	all	sound/asound.h		conflict!
	'U'	00-CF	linux/uinput.h		conflict!
	'U'	00-EF	linux/usbdevice_fs.h
	'U'	C0-CF	drivers/bluetooth/hci_uart.h
	'V'	all	linux/vt.h		conflict!
	'V'	all	linux/videodev2.h	conflict!
	'V'	C0	linux/ivtvfb.h		conflict!
	'V'	C0	linux/ivtv.h		conflict!
	'V'	C0	media/davinci/vpfe_capture.h	conflict!
	'V'	C0	media/si4713.h		conflict!
	'W'	00-1F	linux/watchdog.h	conflict!
	'W'	00-1F	linux/wanrouter.h	conflict!		(pre 3.9)
	'W'	00-3F	sound/asound.h		conflict!
	'X'	all	fs/xfs/xfs_fs.h		conflict!
			and fs/xfs/linux-2.6/xfs_ioctl32.h
			and include/linux/falloc.h
			and linux/fs.h
	'X'	all	fs/ocfs2/ocfs_fs.h	conflict!
	'X'	01	linux/pktcdvd.h		conflict!
	'Y'	all	linux/cyclades.h
	'Z'	14-15	drivers/message/fusion/mptctl.h
	'['	00-07	linux/usb/tmc.h		USB Test and Measurement Devices
						<mailto:gregkh@linuxfoundation.org>
	'a'	all	linux/atm*.h, linux/sonet.h	ATM on linux
						<http://lrcwww.epfl.ch/>
	'a'	00-0F	drivers/crypto/qat/qat_common/adf_cfg_common.h	conflict! qat driver
	'b'	00-FF				conflict! bit3 vme host bridge
						<mailto:natalia@nikhefk.nikhef.nl>
	'c'	all	linux/cm4000_cs.h	conflict!
	'c'	00-7F	linux/comstats.h	conflict!
	'c'	00-7F	linux/coda.h		conflict!
	'c'	00-1F	linux/chio.h		conflict!
	'c'	80-9F	arch/s390/include/asm/chsc.h	conflict!
	'c'	A0-AF   arch/x86/include/asm/msr.h	conflict!
	'd'	00-FF	linux/char/drm/drm/h	conflict!
	'd'	02-40	pcmcia/ds.h		conflict!
	'd'	F0-FF	linux/digi1.h
	'e'	all	linux/digi1.h		conflict!
	'e'	00-1F	drivers/net/irda/irtty-sir.h	conflict!
	'f'	00-1F	linux/ext2_fs.h		conflict!
	'f'	00-1F	linux/ext3_fs.h		conflict!
	'f'	00-0F	fs/jfs/jfs_dinode.h	conflict!
	'f'	00-0F	fs/ext4/ext4.h		conflict!
	'f'	00-0F	linux/fs.h		conflict!
	'f'	00-0F	fs/ocfs2/ocfs2_fs.h	conflict!
	'g'	00-0F	linux/usb/gadgetfs.h
	'g'	20-2F	linux/usb/g_printer.h
	'h'	00-7F				conflict! Charon filesystem
						<mailto:zapman@interlan.net>
	'h'	00-1F	linux/hpet.h		conflict!
	'h'	80-8F	fs/hfsplus/ioctl.c
	'i'	00-3F	linux/i2o-dev.h		conflict!
	'i'	0B-1F	linux/ipmi.h		conflict!
	'i'	80-8F	linux/i8k.h
	'j'	00-3F	linux/joystick.h
	'k'	00-0F	linux/spi/spidev.h	conflict!
	'k'	00-05	video/kyro.h		conflict!
	'k'	10-17	linux/hsi/hsi_char.h	HSI character device
	'l'	00-3F	linux/tcfs_fs.h		transparent cryptographic file system
						<http://web.archive.org/web/*/http://mikonos.dia.unisa.it/tcfs>
	'l'	40-7F	linux/udf_fs_i.h	in development:
						<http://sourceforge.net/projects/linux-udf/>
	'm'	00-09	linux/mmtimer.h		conflict!
	'm'	all	linux/mtio.h		conflict!
	'm'	all	linux/soundcard.h	conflict!
	'm'	all	linux/synclink.h	conflict!
	'm'	00-19	drivers/message/fusion/mptctl.h	conflict!
	'm'	00	drivers/scsi/megaraid/megaraid_ioctl.h	conflict!
	'm'	00-1F	net/irda/irmod.h	conflict!
	'n'	00-7F	linux/ncp_fs.h and fs/ncpfs/ioctl.c
	'n'	80-8F	linux/nilfs2_fs.h	NILFS2
	'n'	E0-FF	linux/matroxfb.h	matroxfb
	'o'	00-1F	fs/ocfs2/ocfs2_fs.h	OCFS2
	'o'     00-03   mtd/ubi-user.h		conflict! (OCFS2 and UBI overlaps)
	'o'     40-41   mtd/ubi-user.h		UBI
	'o'     01-A1   linux/dvb/*.h		DVB
	'p'	00-0F	linux/phantom.h		conflict! (OpenHaptics needs this)
	'p'	00-1F	linux/rtc.h		conflict!
	'p'	00-3F	linux/mc146818rtc.h	conflict!
	'p'	40-7F	linux/nvram.h
	'p'	80-9F	linux/ppdev.h		user-space parport
						<mailto:tim@cyberelk.net>
	'p'	A1-A5	linux/pps.h		LinuxPPS
						<mailto:giometti@linux.it>
	'q'	00-1F	linux/serio.h
	'q'	80-FF	linux/telephony.h	Internet PhoneJACK, Internet LineJACK
			linux/ixjuser.h		<http://web.archive.org/web/*/http://www.quicknet.net>
	'r'	00-1F	linux/msdos_fs.h and fs/fat/dir.c
	's'	all	linux/cdk.h
	't'	00-7F	linux/ppp-ioctl.h
	't'	80-8F	linux/isdn_ppp.h
	't'	90	linux/toshiba.h
	'u'	00-1F	linux/smb_fs.h		gone
	'u'	20-3F	linux/uvcvideo.h	USB video class host driver
	'v'	00-1F	linux/ext2_fs.h		conflict!
	'v'	00-1F	linux/fs.h		conflict!
	'v'	00-0F	linux/sonypi.h		conflict!
	'v'	C0-FF	linux/meye.h		conflict!
	'w'	all				CERN SCI driver
	'y'	00-1F				packet based user level communications
						<mailto:zapman@interlan.net>
	'z'	00-3F				CAN bus card	conflict!
						<mailto:hdstich@connectu.ulm.circular.de>
	'z'	40-7F				CAN bus card	conflict!
						<mailto:oe@port.de>
	'z'	10-4F	drivers/s390/crypto/zcrypt_api.h	conflict!
	'|'	00-7F	linux/media.h
	0x80	00-1F	linux/fb.h
	0x89	00-06	arch/x86/include/asm/sockios.h
	0x89	0B-DF	linux/sockios.h
	0x89	E0-EF	linux/sockios.h		SIOCPROTOPRIVATE range
	0x89	E0-EF	linux/dn.h		PROTOPRIVATE range
	0x89	F0-FF	linux/sockios.h		SIOCDEVPRIVATE range
	0x8B	all	linux/wireless.h
	0x8C	00-3F				WiNRADiO driver
						<http://www.winradio.com.au/>
	0x90	00	drivers/cdrom/sbpcd.h
	0x92	00-0F	drivers/usb/mon/mon_bin.c
	0x93	60-7F	linux/auto_fs.h
	0x94	all	fs/btrfs/ioctl.h
	0x97	00-7F	fs/ceph/ioctl.h		Ceph file system
	0x99	00-0F				537-Addinboard driver
						<mailto:buk@buks.ipn.de>
	0xA0	all	linux/sdp/sdp.h		Industrial Device Project
						<mailto:kenji@bitgate.com>
	0xA2	00-0F	arch/tile/include/asm/hardwall.h
	0xA3	80-8F	Port ACL		in development:
						<mailto:tlewis@mindspring.com>
	0xA3	90-9F	linux/dtlk.h
	0xAB	00-1F	linux/nbd.h
	0xAC	00-1F	linux/raw.h
	0xAD	00	Netfilter device	in development:
						<mailto:rusty@rustcorp.com.au>
	0xAE	all	linux/kvm.h		Kernel-based Virtual Machine
						<mailto:kvm@vger.kernel.org>
	0xAF	00-1F	linux/fsl_hypervisor.h	Freescale hypervisor
	0xB0	all	RATIO devices		in development:
						<mailto:vgo@ratio.de>
	0xB1	00-1F	PPPoX			<mailto:mostrows@styx.uwaterloo.ca>
	0xB3	00	linux/mmc/ioctl.h
	0xC0	00-0F	linux/usb/iowarrior.h
	0xCA	00-0F	uapi/misc/cxl.h
	0xCB	00-1F	CBM serial IEC bus	in development:
						<mailto:michael.klein@puffin.lb.shuttle.de>
	0xCD	01	linux/reiserfs_fs.h
	0xCF	02	fs/cifs/ioctl.c
	0xDB	00-0F	drivers/char/mwave/mwavepub.h
	0xDD	00-3F	ZFCP device driver	see drivers/s390/scsi/
						<mailto:aherrman@de.ibm.com>
	0xEC	00-01	drivers/platform/chrome/cros_ec_dev.h	ChromeOS EC driver
	0xF3	00-3F	drivers/usb/misc/sisusbvga/sisusb.h	sisfb (in development)
						<mailto:thomas@winischhofer.net>
	0xF4	00-1F	video/mbxfb.h		mbxfb
						<mailto:raph@8d.com>
	0xF6	all	LTTng			Linux Trace Toolkit Next Generation
						<mailto:mathieu.desnoyers@efficios.com>
	0xFD	all	linux/dm-ioctl.h
