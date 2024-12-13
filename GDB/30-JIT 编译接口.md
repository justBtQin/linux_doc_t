# 30 JIT 编译接口

本章记录了 gdb 的即时（JIT）编译接口。JIT 编译器是一个在运行时生成原生可执行代码并执行它的程序或库，通常是为了在保持平台独立性的同时实现良好的性能。

使用 JIT 编译的程序通常难以调试，因为它们的部分代码是在运行时生成的，而不是从对象文件加载的，而 gdb 通常在对象文件中查找程序的符号和调试信息。为了调试使用 JIT 编译的程序，gdb 有一个接口，允许程序在运行时向 gdb 注册内存中的符号文件。

如果您使用 gdb 调试使用此接口的程序，那么只要您没有剥离二进制文件，它就应该透明地工作。如果您正在开发 JIT 编译器，那么该接口在本章的其余部分有记录。此时，此接口的唯一已知客户端是 LLVM JIT。

广义上讲，JIT 接口反映了动态加载器接口。JIT 编译器通过将数据写入全局变量并在知名符号处调用函数来与 gdb 通信。当 gdb 附加时，它从全局变量读取符号文件的链接列表以查找现有代码，并在该函数中放置一个断点，以便它可以了解额外的代码。

## 30.1 JIT 声明

这些是 C 程序实现接口应包含的相关结构体声明：

```c
typedef enum
{
    JIT_NOACTION = 0,
    JIT_REGISTER_FN,
    JIT_UNREGISTER_FN
} jit_actions_t;

struct jit_code_entry
{
    struct jit_code_entry *next_entry;
    struct jit_code_entry *prev_entry;
    const char *symfile_addr;
    uint64_t symfile_size;
};

struct jit_descriptor
{
    uint32_t version;
    /* This type should be jit_actions_t, but we use uint32_t
    to be explicit about the bitwidth. */
    uint32_t action_flag;
    struct jit_code_entry *relevant_entry;
    struct jit_code_entry *first_entry;
};
/* GDB 在这个函数中放置断点。 */
void __attribute__((noinline)) __jit_debug_register_code() { };

/* 确保静态指定版本，因为调试器可能在我们设置之前检查版本。 */
struct jit_descriptor __jit_debug_descriptor = { 1, 0, 0, 0 };
```

如果 JIT 是多线程的，那么 JIT 正确同步对这个全局数据的任何修改是很重要的，这可以通过在对这些结构的修改周围放置一个全局互斥锁轻松实现。

## 30.2 注册代码

要在 gdb 中注册代码，JIT 应遵循以下协议：

```
•   在内存中生成一个带有符号和其他所需调试信息的对象文件。
    该文件必须包括节的虚拟地址。
•   为该文件创建一个代码条目，提供符号文件的起始位置和大小。
•   将其添加到 JIT 描述符的链表中。
•   将描述符的相关条目字段指向该条目。
•   将 action_flag 设置为 JIT_REGISTER 并调用 __jit_debug_register_code。
```

当 gdb 附加并且断点触发时，gdb 使用相关的_entry 指针，这样它就不必遍历列表寻找新代码。然而，链表仍然必须被维护，以允许 gdb 附加到正在运行的进程并仍然找到符号文件。

## 30.3 注销代码

如果代码被释放，那么 JIT 应该使用以下协议：

```
• 从链表中删除与该代码对应的代码条目。
• 将描述符的 relevant_entry 字段指向该代码条目。
• 将 action_flag 设置为 JIT_UNREGISTER 并调用 __jit_debug_register_code。
```

如果 JIT 在未注销的情况下释放或重新编译代码，那么 gdb 和 JIT 将泄漏用于相关符号文件的内存。

## 30.4 自定义调试信息

对于即时（JIT）编译器，以平台原生文件格式（如 ELF 或 COFF）生成调试信息可能有些过分；特别是如果所有调试信息的用途只是显示有意义的回溯。这个问题可以通过让 JIT 编写者决定一种调试信息格式，并提供一个解析由 JIT 编译器生成的调试信息的读取器来解决。本节简要概述编写这样一个解析器的方法。更具体的细节可以在源文件 gdb/jit-reader.in 中找到，该文件也作为头文件安装在 includedir/gdb/jit-reader.h 中，以便于包含。

读取器被实现为一个共享对象（因此在运行时不允许加载共享对象的平台上，此功能不可用）。提供了两个 gdb 命令，jit-reader-load 和 jit-reader-unload，用于从预配置的目录加载和卸载读取器。一旦加载，共享对象就用于解析 JIT 编译器发出的调试信息。

### 30.4.1 使用 JIT 调试信息读取器

可以使用`jit-reader-load`和`jit-reader-unload`命令加载和卸载读取器。

```
jit-reader-load reader
	加载名为`reader`的 JIT 读取器，它是一个作为绝对或相对文件名指定的共享对象。在后一种情况下，`gdb`将尝试从预配置的目录中加载读取器，在 UNIX 系统上通常是`libdir/gdb/`（这里`libdir`是系统库目录，通常是`/usr/local/lib`）。
	一次只能有一个读取器处于活动状态；当一个读取器已经加载时尝试加载第二个读取器将导致`gdb`报告错误。可以通过首先使用`jit-reader-unload`卸载当前的读取器，然后调用`jit-reader-load`来加载新的 JIT 读取器。

jit-reader-unload
	卸载当前加载的 JIT 读取器。
```

### 30.4.2 编写 JIT 调试信息读取器

如前所述，读取器本质上是一个符合特定 ABI 的共享对象。这个 ABI 在 jit-reader.h 中进行了描述。

jit-reader.h 定义了编写读取器所需的结构、宏和函数。

它与 gdb 一起安装在 includedir/gdb 中，其中 includedir 是系统包含目录。

读取器需要在 GPL 兼容许可证下发布。可以通过在源文件中放置宏 GDB_DECLARE_GPL_COMPATIBLE_READER 来声明读取器在这样的许可证下发布。

读取器的入口点是符号 gdb_init_reader，它预期是一个具有以下原型的函数：

```c
extern struct gdb_reader_funcs *gdb_init_reader (void);
```

struct gdb_reader_funcs 包含一组指向回调函数的指针。执行这些函数以读取 JIT 编译器生成的调试信息（读取）、展开堆栈帧（展开）和创建规范帧 ID（获取帧 ID）。它还有一个在读取器被卸载时调用的回调（销毁）。该结构如下所示：

```c
struct gdb_reader_funcs
{
	/* Must be set to GDB_READER_INTERFACE_VERSION. */
	int reader_version;
	/* For use by the reader. */
	void *priv_data;
	gdb_read_debug_info *read;
	gdb_unwind_frame *unwind;
	gdb_get_frame_id *get_frame_id;
	gdb_destroy_reader *destroy;
};
```

回调函数由 gdb 提供另一组回调函数来完成它们的工作。对于读取，这些回调函数在 struct gdb_symbol_callbacks 中传递，对于展开和获取帧 ID，在 struct gdb_unwind_callbacks 中传递。struct gdb_symbol_callbacks 具有创建新对象文件和这些对象文件内部的新符号表的回调。struct gdb_unwind_callbacks 具有从当前帧读取寄存器和将前一帧中的寄存器值写出的回调。两者都有一个回调（target_read）来从目标的地址空间读取字节。