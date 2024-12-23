# 内核提供的用户帮助程序

============================

这些是内核提供的可从用户空间访问的用户代码段，位于内核内存中的固定地址。这用于为用户空间提供一些由于许多 ARM CPU 中未实现的原生特性和/或指令而需要内核帮助的操作。其想法是为了以最佳效率直接在用户模式下执行此代码，但由于它与内核对应部分关系密切，不能留给用户库。实际上，此代码甚至可能因可用的指令集或是否为 SMP 系统而在不同的 CPU 之间有所不同。换句话说，内核保留根据需要更改此代码而不发出警告的权利。仅此处记录的入口点及其结果是保证稳定的。

这与（但不排除）完全实现的 VDSO 不同，然而 VDSO 会阻止一些使用常量的汇编技巧，这些技巧允许高效地分支到这些代码段。并且由于这些代码段在返回用户代码之前仅使用几个周期，VDSO 间接远调用的开销将给这些极简操作增加可测量的开销。

用户空间应绕过这些帮助程序，并在针对具有必要原生支持的足够新的处理器进行优化时，内联实现这些功能（无论是在编译器直接生成的代码中，还是在库调用的实现中），但前提是由于使用类似的原生指令用于其他目的，生成的二进制文件已经与早期的 ARM 处理器不兼容。换句话说，不要仅仅为了不使用这些内核帮助程序而使二进制文件无法在早期处理器上运行，如果编译后的代码不会将新指令用于其他目的。

随着时间的推移，可能会添加新的帮助程序，因此较旧的内核可能缺少较新内核中存在的一些帮助程序。出于这个原因，程序在假设调用任何特定的帮助程序是安全的之前，必须检查`__kuser_helper_version`的值（见下文）。此检查最好仅在进程启动时执行一次，如果正在运行的进程的内核版本不提供所需的帮助程序，则应尽早中止执行。

`kuser_helper_version`

--------------------

位置：`0xffff0ffc`

引用声明：

  extern int32_t __kuser_helper_version;

定义：

  此字段包含正在运行的内核实现的帮助程序的数量。用户空间可以读取此值以确定特定帮助程序的可用性。

使用示例：

```
#define __kuser_helper_version (*(int32_t *)0xffff0ffc)
void check_kuser_version(void)
{
	if (__kuser_helper_version < 2) {
		fprintf(stderr, "can't do atomic operations, kernel too old\n");
		abort();
	}
}
```

注意：

  用户空间可以假设此字段的值在任何单个进程的生命周期内不会更改。这意味着此字段可以在库的初始化或程序的启动阶段读取一次。

# `kuser_get_tls`

-------------

位置：`0xffff0fe0`

引用原型：

  void * __kuser_get_tls(void);

输入：

  `lr` = 返回地址

输出：

  `r0` = TLS 值

被修改的寄存器：

  无

定义：

  获取之前通过`__ARM_NR_set_tls`系统调用设置的 TLS 值。

使用示例：

```
typedef void * (__kuser_get_tls_t)(void);
#define __kuser_get_tls (*(__kuser_get_tls_t *)0xffff0fe0)
void foo()
{
	void *tls = __kuser_get_tls();
	printf("TLS = %p\n", tls);
}
```

注意：

  - 仅在`__kuser_helper_version >= 1`（从内核版本 2.6.12 开始）时有效。

# `kuser_cmpxchg`

-------------

位置：`0xffff0fc0`

引用原型：

  int __kuser_cmpxchg(int32_t oldval, int32_t newval, volatile int32_t *ptr);

输入：

  `r0` = oldval

  `r1` = newval

  `r2` = ptr

  `lr` = 返回地址

输出：

  `r0` = 成功代码（零或非零）

  C 标志 = 如果`r0 == 0`则设置，否则清除

被修改的寄存器：

  `r3`、`ip`、`flags`

定义：

  仅当`*ptr`等于`oldval`时，才将`newval`原子性地存储在`*ptr`中。如果`*ptr`被更改，则返回零；如果没有发生交换，则返回非零。C 标志也会在`*ptr`被更改时设置，以便在调用代码中进行汇编优化。

使用示例：

```
typedef int (__kuser_cmpxchg_t)(int oldval, int newval, volatile int *ptr);
#define __kuser_cmpxchg (*(__kuser_cmpxchg_t *)0xffff0fc0)
int atomic_add(volatile int *ptr, int val)
{
	int old, new;
	do {
		old = *ptr;
		new = old + val;
	} while(__kuser_cmpxchg(old, new, ptr));
	return new;
}
```

注意：

  - 此例程已经包含了所需的内存屏障。

  - 仅在`__kuser_helper_version >= 2`（从内核版本 2.6.12 开始）时有效。

# `kuser_memory_barrier`

--------------------

位置：`0xffff0fa0`

引用原型：

  void __kuser_memory_barrier(void);

输入：

  `lr` = 返回地址

输出：

  无

被修改的寄存器：

  无

定义：

  应用任何所需的内存屏障，以保持与手动修改的数据和`__kuser_cmpxchg`使用的一致性。

使用示例：

```
typedef void (__kuser_dmb_t)(void);
#define __kuser_dmb (*(__kuser_dmb_t *)0xffff0fa0)
```

注意：

  - 仅在`__kuser_helper_version >= 3`（从内核版本 2.6.15 开始）时有效。

# `kuser_cmpxchg64`

---------------

位置：`0xffff0f60`

引用原型：

  int __kuser_cmpxchg64(const int64_t *oldval,

                        const int64_t *newval,

                        volatile int64_t *ptr);

输入：

  `r0` = 指向 oldval 的指针

  `r1` = 指向 newval 的指针

  `r2` = 指向目标值的指针

  `lr` = 返回地址

输出：

  `r0` = 成功代码（零或非零）

  C 标志 = 如果`r0 == 0`则设置，否则清除

被修改的寄存器：

  `r3`、`lr`、`flags`

定义：

  仅当`*ptr`等于指向`oldval`的 64 位值时，才将指向`newval`的 64 位值原子性地存储在`*ptr`中。如果`*ptr`被更改，则返回零；如果没有发生交换，则返回非零。C 标志也会在`*ptr`被更改时设置，以便在调用代码中进行汇编优化。

使用示例：

```
typedef int (__kuser_cmpxchg64_t)(const int64_t *oldval,
                                  const int64_t *newval,
                                  volatile int64_t *ptr);
#define __kuser_cmpxchg64 (*(__kuser_cmpxchg64_t *)0xffff0f60)
int64_t atomic_add64(volatile int64_t *ptr, int64_t val)
{
	int64_t old, new;
	do {
		old = *ptr;
		new = old + val;
	} while(__kuser_cmpxchg64(&old, &new, ptr));
	return new;
}
```

注意：

  - 此例程已经包含了所需的内存屏障。

  - 由于此序列的长度，它跨越 2 个传统的`kuser`“槽”，因此`0xffff0f80`未用作有效的入口点。

  - 仅在`__kuser_helper_version >= 5`（从内核版本 3.1 开始）时有效。