**What**: `/config/usb-gadget/gadget/functions/SourceSink.name`

**Date**: 2013 年 11 月

**KernelVersion**: 3.13

**Description**:

    其属性如下：

    - `pattern` - 0（全零）、1（mod63）、2（无）

    - `isoc_interval` - 1 到 16

    - `isoc_maxpacket` - 0 到 1023（fs）、0 到 1024（hs/ss）

    - `isoc_mult` - 0 到 2（仅 hs/ss）

    - `isoc_maxburst` - 0 到 15（仅 ss）

    - `buflen` - 缓冲区长度