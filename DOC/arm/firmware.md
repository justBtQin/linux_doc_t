**接口用于注册和调用针对 ARM 的特定固件操作。**

由 Tomasz Figa <t.figa@samsung.com> 编写。

一些板卡运行在 TrustZone 安全世界中的安全固件，这改变了一些初始化方式。这就需要为这样的平台提供一个接口，以指定可用的固件操作并在需要时调用它们。

可以通过填充一个带有适当回调的`struct firmware_ops`结构，然后使用`register_firmware_ops()`函数进行注册来指定固件操作。

```c
void register_firmware_ops(const struct firmware_ops *ops)
```

`ops`指针必须非空。关于`struct firmware_ops`及其成员的更多信息可以在`arch/arm/include/asm/firmware.h`头文件中找到。

如果平台不需要固件操作，则无需设置任何内容，因为提供了一个默认的空操作集。

要调用一个固件操作，提供了一个辅助宏：

```c
#define call_firmware_op(op,...) \
    ((firmware_ops->op)? firmware_ops->op(__VA_ARGS__) : (-ENOSYS))
```

该宏检查是否提供了操作，如果提供则调用它，否则返回 -ENOSYS 以表示给定操作不可用（例如，允许回退到旧的操作）。

注册固件操作的示例：

```c
/* board file */

static int platformX_do_idle(void)
{
    /* tell platformX firmware to enter idle */
    return 0;
}

static int platformX_cpu_boot(int i)
{
    /* tell platformX firmware to boot CPU i */
    return 0;
}

static const struct firmware_ops platformX_firmware_ops = {
   .do_idle = exynos_do_idle,
   .cpu_boot = exynos_cpu_boot,
    /* other operations not available on platformX */
};

/* init_early callback of machine descriptor */
static void __init board_init_early(void)
{
    register_firmware_ops(&platformX_firmware_ops);
}
```

使用固件操作的示例：

```c
/* some platform code, e.g. SMP initialization */

__raw_writel(virt_to_phys(exynos4_secondary_startup),
    CPU1_BOOT_REG);

/* Call Exynos specific smc call */
if (call_firmware_op(cpu_boot, cpu) == -ENOSYS)
    cpu_boot_legacy(...); /* Try legacy way */

gic_raise_softirq(cpumask_of(cpu), 1);
```