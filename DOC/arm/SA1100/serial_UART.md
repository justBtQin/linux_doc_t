**SA1100 串口的主/次编号已被正式分配：**

```
> 日期：Sun, 24 Sep 2000 21:40:27 -0700
> 发件人：H. Peter Anvin <hpa@transmeta.com>
> 收件人：Nicolas Pitre <nico@CAM.ORG>
> 抄送：设备列表维护者 <device@lanana.org>
> 主题：关于设备的回复
> 
> 好的。请注意，设备编号 204 和 205 用于“低密度串行设备”，因此在这些主设备上你将有一系列次设备编号（tty 设备层处理得很好，所以你不必担心做任何特殊的事情。）
> 
> 所以你的分配如下：
> 
> 204 char        Low-density serial ports
>                   5 = /dev/ttySA0               SA1100 builtin serial port 0
>                   6 = /dev/ttySA1               SA1100 builtin serial port 1
>                   7 = /dev/ttySA2               SA1100 builtin serial port 2
> 
> 205 char        Low-density serial ports (alternate device)
>                   5 = /dev/cusa0                Callout device for ttySA0
>                   6 = /dev/cusa1                Callout device for ttySA1
>                   7 = /dev/cusa2                Callout device for ttySA2
```


你必须在你的基于 SA1100 的设备所使用的根文件系统的 /dev 中创建这些 inode：

```
`mknod ttySA0 c 204 5`
`mknod ttySA1 c 204 6`
`mknod ttySA2 c 204 7`
`mknod cusa0 c 205 5`
`mknod cusa1 c 205 6`
`mknod cusa2 c 205 7`
```

除了在上面创建适当的设备节点之外，你还必须确保你的用户空间应用程序使用正确的设备名称。经典的例子是 /etc/inittab 文件的内容，在其中你可能在 ttyS0 上启动一个 getty 进程。在这种情况下：

- 将 ttyS0 的出现替换为 ttySA0，ttyS1 替换为 ttySA1 等。

- 不要忘记在 /etc/securetty 中添加 'ttySA0'、'console' 或适当的 tty 名称，以便 root 也能被允许登录。