`/sys/module/acpi/parameters/`：

- `trace_method_name`：用户想要追踪的 AML 方法名称。

- `trace_debug_layer`：追踪方法时使用的临时`debug_layer`。如果为 0，则默认使用 0xffffffff。

- `trace_debug_level`：追踪方法时使用的临时`debug_level`。如果为 0，则默认使用 0x00ffffff。

- `trace_state`：追踪功能的状态。

    - “enabled”表示此功能已启用，并且每次执行 AML 方法时都会进行追踪。

    - “1”表示此功能已启用，并且仅在下次执行时追踪 AML 方法。

    - “disabled”表示此功能已禁用。用户可以通过“echo string > /sys/module/acpi/parameters/trace_state”来启用/禁用此调试追踪功能。“string”应为“enable”、“disable”或“1”之一。