**/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/kone/roccatkone<minor>/actual_dpi**

- **Date**：March 2010

- **Contact**：Stefan Achatz <erazor_de@users.sourceforge.net>

- **Description**：可以通过按下一个按钮来切换鼠标的 DPI 设置。读取此文件时，将返回鼠标报告的实际 DPI 设置的原始数字。此数字必须进一步处理才能获得实际的 DPI 值。

    - **VALUE DPI**：

        - 1：800

        - 2：1200

        - 3：1600

        - 4：2000

        - 5：2400

        - 6：3200

    - 此文件为只读。

    - **Users**：http://roccat.sourceforge.net

**/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/kone/roccatkone<minor>/actual_profile**

- **Date**：March 2010

- **Contact**：Stefan Achatz <erazor_de@users.sourceforge.net>

- **Description**：读取此文件时，将返回实际配置文件的编号。此文件为只读。

- **Users**：http://roccat.sourceforge.net

**/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/kone/roccatkone<minor>/firmware_version**

- **Date**：March 2010

- **Contact**：Stefan Achatz <erazor_de@users.sourceforge.net>

- **Description**：读取此文件时，将返回鼠标报告的固件的原始整数版本号。使用整数值便于在其他程序中进一步使用。要获得实际版本号，需将小数点向左移动 2 位。例如，返回值 138 表示 1.38。此文件为只读。

- **Users**：http://roccat.sourceforge.net

**/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/kone/roccatkone<minor>/profile[1-5]**

- **Date**：March 2010

- **Contact**：Stefan Achatz <erazor_de@users.sourceforge.net>

- **Description**：鼠标可以存储 5 个配置文件，可以通过按下一个按钮进行切换。一个配置文件保存诸如按钮映射、灵敏度、5 个指示灯的颜色和灯光效果等信息。读取这些文件时，将返回相应的配置文件。返回的数据大小为 975 字节。写入此文件时，将相应的配置文件数据写回鼠标。数据必须为 975 字节长。鼠标将拒绝无效数据，而存储在配置文件中的配置文件编号不需要与存储的编号匹配。

- **Users**：http://roccat.sourceforge.net

**/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/kone/roccatkone<minor>/settings**

- **Date**：March 2010

- **Contact**：Stefan Achatz <erazor_de@users.sourceforge.net>

- **Description**：读取此文件时，将返回鼠标中存储的设置。数据大小为 36 字节，包含诸如启动配置文件、TCU 状态和校准数据等信息。写入此文件时，将设置写回鼠标。数据必须为 36 字节长。鼠标将拒绝无效数据。

- **Users**：http://roccat.sourceforge.net

**/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/kone/roccatkone<minor>/startup_profile**

- **Date**：March 2010

- **Contact**：Stefan Achatz <erazor_de@users.sourceforge.net>

- **Description**：此属性的整数值范围从 1 到 5。读取此属性时，将返回鼠标通电时激活的配置文件的编号。写入此文件时，将设置启动配置文件的编号，鼠标将立即激活此配置文件。

- **Users**：http://roccat.sourceforge.net

**/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/kone/roccatkone<minor>/tcu**

- **Date**：March 2010

- **Contact**：Stefan Achatz <erazor_de@users.sourceforge.net>

- **Description**：鼠标具有“跟踪控制单元”，可让用户校准激光功率以适应鼠标垫表面。读取此文件时，将返回 TCU 的当前状态，其中 0 表示关闭，1 表示打开。写入 0 将关闭 TCU。写入 1 将启动校准，大约需要 6 秒完成并激活 TCU。

- **Users**：http://roccat.sourceforge.net

**/sys/bus/usb/devices/<busnum>-<devnum>:<config num>.<interface num>/<hid-bus>:<vendor-id>:<product-id>.<num>/kone/roccatkone<minor>/weight**

- **Date**：March 2010

- **Contact**：Stefan Achatz <erazor_de@users.sourceforge.net>

- **Description**：鼠标可以配备 4 种提供的重量之一，范围从 5 克到 20 克，鼠标可以识别这些重量并可以读出其值。读取此文件时，将返回鼠标返回的原始值，以便在其他软件中进行进一步处理。

    - **VALUE WEIGHT**：

        - 0：无

        - 1：5g

        - 2：10g

        - 3：15g

        - 4：20g

    - 此文件为只读。

    - **Users**：http://roccat.sourceforge.net