# �ں��������� `i2c-sis630`

**֧�ֵ�������**��
  * Silicon Integrated Systems Corp (SiS)
    630 оƬ�飨���ݱ����� http://www.sfr-fresh.com/linux ��ȡ��
    730 оƬ��
    964 оƬ��
  * ���ܵ����� SiS оƬ�飿

**����**��Alexander Malysh <amalysh@web.de>
    Amaury Decrême <amaury.decreme@gmail.com> - SiS964 ֧��

**ģ�����**

* `force = [1|0]` ǿ������ SIS630��Σ�գ�
    ��������δ������оƬ�飬����������ڼ�����Ƿ�����������оƬ�飬����Σ�գ�
* `high_clock = [1|0]` ǿ�ƽ�������ʱ������Ϊ 56KHz��Ĭ��ֵ�������� BIOS ʹ�õ�ֵ����Σ�գ���Ӧ�û��һ�㣬�����ܻ�ʹĳЩϵͳ���ᣨ�����ҵıʼǱ����ԣ����������� SIS630/730 оƬ��

**����**

�˽� SMBus ����������֪���ھ�������������оƬ��������Ϲ�����

����������������ݣ�

```
00:00.0 Host bridge: Silicon Integrated Systems [SiS] 630 Host (rev 31)
00:01.0 ISA bridge: Silicon Integrated Systems [SiS] 85C503/5513
```

�����������ݣ�

```
00:00.0 Host bridge: Silicon Integrated Systems [SiS] 730 Host (rev 02)
00:01.0 ISA bridge: Silicon Integrated Systems [SiS] 85C503/5513
```

�����������ݣ�

```
00:00.0 Host bridge: Silicon Integrated Systems [SiS] 760/M760 Host (rev 02)
00:02.0 ISA bridge: Silicon Integrated Systems [SiS] SiS964 [MuTIOL Media IO]
							LPC Controller (rev 36)
```

������ `lspci` ����У���ô��������������������оƬ�顣

**��л**
---------

Philip Edelbrock <phil@netroedge.com>
 - ���� SiS730 ֧��
Mark M. Hoffman <mhoffman@lightlink.com>
 - �����޸�

��л�κα��������ڴ˴����ˣ� 