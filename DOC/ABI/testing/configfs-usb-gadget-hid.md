**What**: `/config/usb-gadget/gadget/functions/hid.name`

**Date**: 2014 年 11 月

**KernelVersion**: 3.19

**Description**:

    其属性如下：

    - `protocol` - 要使用的 HID 协议

    - `report_desc` - 与 HID 报告描述符相对应的 blob，除了通过 `/dev/hidg<N>` 传递的数据

    - `report_length` - HID 报告长度

    - `subclass` - 要使用的 HID 设备子类