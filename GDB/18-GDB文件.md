# 18 gdb 文件

gdb 需要知道要调试的程序的文件名，以便读取其符号表并启动您的程序。要调试先前运行的核心转储，您还必须告诉 gdb 核心转储文件的名称。

## 18.1 指定文件的命令

您可能需要指定可执行文件和核心转储文件的名称。通常的做法是在启动时使用 gdb 启动命令的参数（请参见第 2 章[进入和退出 gdb]，第 11 页）。

偶尔在 gdb 会话期间需要切换到不同的文件。或者您可能运行 gdb 但忘记指定要使用的文件。或者您通过 gdbserver 调试远程目标（请参见第 20.3 节[使用 gdbserver 程序]，第 320 页）。在这些情况下，指定新文件的 gdb 命令很有用。
```
file filename
	使用 `filename` 作为要调试的程序。它会读取其符号和纯内存的内容。当您使用 `run` 命令时，它也是要执行的程序。如果您没有指定目录并且在 gdb 工作目录中未找到该文件，gdb 会像 shell 查找要运行的程序一样使用环境变量 `PATH` 作为要搜索的目录列表。您可以使用 `path` 命令更改此变量的值，对 gdb 和您的程序都有效。
	`filename` 参数支持转义和引用，请参见第 3.4 节[作为命令参数的文件名]，第 27 页。
	您可以使用 `file` 命令将未链接的 `.o` 对象文件加载到 gdb 中。您将无法“运行”对象文件，但可以反汇编函数并检查变量。此外，如果底层 BFD 功能支持，您可以使用 `gdb -write` 技术使用此方法修补对象文件。请注意，在这种情况下，gdb 既不能解释也不能修改重定位，因此分支和一些初始化的变量似乎会去错误的地方。但此功能仍然不时很方便。

file
	没有参数的 `file` 命令会使 gdb 丢弃关于可执行文件和符号表的任何信息。

exec-file [ filename ]
	指定在 `filename` 中找到要运行的程序（但不是符号表）。如果需要，gdb 会搜索环境变量 `PATH` 来定位您的程序。省略 `filename` 意味着丢弃关于可执行文件的信息。
	`filename` 参数支持转义和引用，请参见第 3.4 节[作为命令参数的文件名]，第 27 页。

symbol-file [ filename [ -o offset ]]
	从文件 `filename` 读取符号表信息。必要时会搜索 `PATH`。使用 `file` 命令可以从同一文件获取符号表和要运行的程序。
	如果指定了可选的偏移量，它将添加到符号文件中每个节的起始地址。这在程序在运行时重定位时很有用，例如启用了 `kASLR` 的 Linux 内核。
	没有参数的 `symbol-file` 会清除 gdb 中关于您的程序符号表的信息。
	`symbol-file` 命令会导致 gdb 忘记一些断点和自动显示表达式的内容。这是因为它们可能包含指向内部数据记录符号和数据类型的指针，这些是 gdb 内部要丢弃的旧符号表数据的一部分。
	执行 `symbol-file` 后再次按 `RET` 它不会重复。
	`filename` 参数支持转义和引用，请参见第 3.4 节[作为命令参数的文件名]，第 27 页。
	当 gdb 为特定环境进行配置时，它会理解该环境生成的任何标准调试信息格式；您可以使用 gnu 编译器，或其他遵循本地约定的编译器。通常从 gnu 编译器获得的结果最好；例如，使用 `gcc` 您可以为优化后的代码生成调试信息。
	对于大多数类型的对象文件，除了使用 `COFF` 的旧 SVR3 系统外，`symbol-file` 命令通常不会立即完全读取符号表。相反，它会快速扫描符号表以查找存在哪些源文件和哪些符号。稍后根据需要一次读取一个源文件的详细信息。
	这种两阶段读取策略的目的是使 gdb 更快地启动。在大多数情况下，除了读取特定源文件的符号表详细信息时偶尔会暂停之外，它是不可见的。（如果需要，可以使用 `set verbose` 命令将这些暂停转换为消息。请参见第 22.9 节[可选的警告和消息]，第 381 页。）
	我们尚未为 `COFF` 实现两阶段策略。当符号表以 `COFF` 格式存储时，`symbol-file` 会立即完全读取符号表数据。请注意，“`stabs-in-COFF`”仍然执行两阶段策略，因为调试信息实际上是 `stabs` 格式。

symbol-file [ -readnow ] filename
file [ -readnow ] filename
	如果您希望确保 gdb 拥有完整的符号表，可以使用 `'-readnow'` 选项与任何加载符号表信息的命令一起覆盖 gdb 的两阶段读取策略。

symbol-file [ -readnever ] filename
file [ -readnever ] filename
	您可以使用 `'-readnever'` 选项指示 gdb 永远不读取 `filename` 中包含的符号信息。请参见[`–readnever`]，第 13 页。

core-file [filename]
core
	指定要用作“内存内容”的核心转储文件的位置。传统上，核心文件仅包含生成它们的进程的地址空间的某些部分；gdb 可以从可执行文件本身获取其他部分。
	没有参数的 `core-file` 表示不使用核心文件。
	请注意，当您的程序实际在 gdb 下运行时，核心文件将被忽略。因此，如果您一直在运行程序并且希望改为调试核心文件，则必须杀死运行该程序的子进程。为此，请使用 `kill` 命令（请参见第 4.8 节[杀死子进程]，第 42 页）。

add-symbol-file filename [ -readnow | -readnever ] [ -o offset ] [ textaddress ] [ -s section address... ]
	`add-symbol-file` 命令从文件 `filename` 读取附加的符号表信息。当 `filename` 已动态加载（通过其他方式）到正在运行的程序中时，您将使用此命令。
	`textaddress` 参数给出文件的文本节已加载的内存地址。您还可以使用任意数量的 `'-s section address'` 对指定其他节的基地址。如果省略一个节，gdb 将使用在 `filename` 中找到的默认地址。任何地址或 `textaddress` 都可以作为表达式给出。
	如果指定了可选的偏移量，它将添加到每个节的起始地址，除了那些已明确指定地址的节。
	文件 `filename` 的符号表将添加到最初使用 `symbol-file` 命令读取的符号表中。您可以多次使用 `add-symbol-file` 命令；新读取的符号数据将与旧数据一起保留。
	`filename` 参数支持转义和引用，请参见第 3.4 节[作为命令参数的文件名]，第 27 页。
	可以使用 `remove-symbol-file` 命令还原更改。
	虽然 `filename` 通常是共享库文件、可执行文件或已完全重定位以加载到进程中的其他对象文件，但只要满足以下条件，您也可以从可重定位的 `.o` 文件加载符号信息：
		• 文件的符号信息仅引用该文件中定义的链接器符号，而不是其他对象文件中定义的符号，
		• 文件的符号信息所引用的每个节实际上已按文件中的形式加载到下级中，
		• 您可以确定每个节加载的地址，并将其提供给 `add-symbol-file` 命令。
	一些嵌入式操作系统，如 Sun Chorus 和 VxWorks，可以将可重定位文件加载到已运行的程序中；此类系统通常使上述要求易于满足。但是，重要的是要认识到许多本机系统使用复杂的链接过程（例如 `.linkonce` 节分解和 C++ 构造函数表汇编），这使得要求难以满足。通常，不能假定使用 `add-symbol-file` 读取可重定位对象文件的符号信息将具有与以常规方式将可重定位对象文件链接到程序相同的效果。
	使用 `add-symbol-file` 后按 `RET` 它不会重复。

remove-symbol-file filename
remove-symbol-file -a address
	删除通过 `add-symbol-file` 命令添加的符号文件。要删除的文件可以通过其文件名或位于此符号文件在内存中的边界内的地址来标识。例如：
		(gdb) add-symbol-file /home/user/gdb/mylib.so 0x7ffff7ff9480
		add symbol table from file "/home/user/gdb/mylib.so" at
			.text_addr = 0x7ffff7ff9480
		(y or n) y
		Reading symbols from /home/user/gdb/mylib.so...
		(gdb) remove-symbol-file -a 0x7ffff7ff9480
		Remove symbol table from file "/home/user/gdb/mylib.so"? (y or n) y
		(gdb)
	
	地址可以是任何计算结果为地址的表达式。
	使用 `remove-symbol-file` 后按 `RET` 它不会重复。
	`filename` 参数支持转义和引用，请参见第 3.4 节[作为命令参数的文件名]，第 27 页。

add-symbol-file-from-memory address
	从直接映射到下级内存中的动态加载对象文件的给定地址加载符号。例如，Linux 内核将系统调用 DSO 映射到每个进程的地址空间；此 DSO 为某些系统调用提供特定于内核的代码。参数可以是任何计算结果为文件的共享对象文件头地址的表达式。
	要使此命令生效，您必须事先使用了 `symbol-file` 或 `exec-file` 命令。

section section addr
	`section` 命令将可执行文件中指定名称的节的基地址更改为 `addr`。如果可执行文件不包含节地址（例如在 `a.out` 格式中），或者文件本身指定的地址错误，可以使用此命令。每个节必须分别更改。下面描述的 `info files` 命令列出了所有节及其地址。

info files
info target
	`info files` 和 `info target` 是同义词；两者都会打印当前目标（请参见第 19 章[指定调试目标]，第 311 页），包括 gdb 当前正在使用的可执行文件和核心转储文件的名称，以及从中加载符号的文件。`help target` 命令列出所有可能的目标而不是当前的目标。

maint info sections [-all-objects] [filter-list]
	另一个可以为您提供有关程序节的额外信息的命令是 `maint info sections`。除了 `info files` 显示的节信息外，此命令还会显示可执行文件和核心转储文件中每个节的标志和文件偏移量。
	当传递 `'-all-objects'` 时，将打印所有已加载对象文件（包括共享库）的节。
	可选的 `filter-list` 是由空格分隔的过滤器关键字列表。匹配任何一个过滤器标准的节将被打印。有两种类型的过滤器：
		section-name
			显示名为 `section-name` 的任何节的信息。

		section-flag
			显示具有 `section-flag` 的任何节的信息。gdb 目前知道的节标志有：
				ALLOC                  节在加载时将在进程中分配空间。除包含调试信息的节外，所有节都设置。
				LOAD                   节将从文件加载到子进程内存中。为预初始化的代码和数据设置，为 `.bss` 节清除。
				RELOC                  节在加载前需要重定位。
				READONLY               子进程不能修改节。
				CODE                   节仅包含可执行代码。
				DATA                   节仅包含数据（无可执行代码）。
				ROM                    节将驻留在 ROM 中。
				CONSTRUCTOR            节包含构造函数/析构函数列表的数据。
				HAS_CONTENTS           节不为空。
				NEVER_LOAD             指示链接器不输出节。
				COFF_SHARED_LIBRARY    通知链接器该节包含 COFF 共享库信息。
				IS_COMMON              节包含公共符号。

maint info target-sections
	此命令打印 gdb 的内部节表。对于每个目标，gdb 维护一个表，其中包含来自所有当前映射对象的可分配节，以及有关节映射位置的信息。

set trust-readonly-sections on
	告诉 gdb 对象文件中的只读节确实是只读的（即其内容不会更改）。在这种情况下，gdb 可以从对象文件中获取这些节的值，而不是从目标程序中获取。对于某些目标（特别是嵌入式目标），这可以显著提高调试性能。
	默认值为 `off`。

set trust-readonly-sections off
	告诉 gdb 不信任只读节。这意味着节的内容在程序运行时可能会更改，因此在需要时必须从目标获取。

show trust-readonly-sections
	显示信任只读节的当前设置。
```
所有指定文件的命令都允许将绝对文件名和相对文件名作为参数。gdb 始终将文件名转换为绝对文件名并以这种方式记住它。

gdb 支持 gnu/Linux、MS-Windows、SunOS、Darwin/Mach-O、SVr4、IBM RS/6000 AIX、FDPIC（FR-V）和 DSBT（TIC6X）共享库。

在 MS-Windows 上，gdb 必须与 Expat 库链接以支持共享库。请参见[Expat]，第 748 页。

当您使用 `run` 命令或检查核心文件时，gdb 会自动从共享库加载符号定义。（在您发出 `run` 命令之前，除非您正在调试核心文件，否则 gdb 不理解对共享库中函数的引用）。

然而，有时您可能希望不自动从共享库加载符号定义，例如当它们特别大或数量很多时。

要控制共享库符号的自动加载，请使用以下命令：
```
set auto-solib-add mode
	如果 `mode` 为 `on`，当下级开始执行、您附加到独立启动的下级或动态链接器通知 gdb 已加载新库时，将自动从所有共享对象库加载符号。如果 `mode` 为 `off`，则必须使用 `sharedlibrary` 命令手动加载符号。默认值为 `on`。
	如果您的程序使用了大量带有调试信息且占用大量内存的共享库，可以通过阻止 gdb 自动从共享库加载符号来减少 gdb 的内存占用。为此，在运行下级之前输入 `set auto-solib-add off`，然后使用 `sharedlibrary regexp` 加载您确实需要其调试符号的每个库，其中 `regexp` 是匹配您希望加载其符号的库的正则表达式。

show auto-solib-add
	显示当前的自动加载模式。
```
要显式加载共享库符号，请使用 `sharedlibrary` 命令：
```
info share regex
info sharedlibrary regex
	打印当前加载的与 `regex` 匹配的共享库的名称。如果省略 `regex`，则打印所有已加载的共享库。

info dll regex
	这是 `info sharedlibrary` 的别名。

sharedlibrary regex
share regex
	为匹配 Unix 正则表达式的文件加载共享对象库符号。与自动加载的文件一样，它仅在核心文件或输入 `run` 后为您的程序所需的共享库加载符号。如果省略 `regex`，则加载您的程序所需的所有共享库。

nosharedlibrary
	卸载所有共享对象库符号。这会丢弃从所有共享库加载的所有符号。由用户显式请求加载的共享库的符号不会被丢弃。
```
有时，您可能希望 gdb 在任何共享库事件发生时停止并让您控制。最好的方法是使用 `catch load` 和 `catch unload`（请参见第 5.1.3 节[设置捕获点]，第 69 页）。

gdb 还支持 `set stop-on-solib-events` 命令来实现此目的。此命令由于历史原因存在。它不如设置捕获点有用，因为它不允许像捕获点那样设置条件或命令。
```
set stop-on-solib-events
	此命令控制当动态链接器通知 gdb 有关某些共享库事件时，gdb 是否应让您进行控制。最常见的感兴趣的事件是加载或卸载新的共享库。

show stop-on-solib-events
	显示当共享库事件发生时 gdb 是否停止并让您进行控制。
```
在许多交叉或远程调试配置中也支持共享库。gdb 需要能够访问目标的库；这可以通过在主机系统上提供库的副本，或者要求 gdb 自动从目标检索库来实现。如果提供了目标库的副本，它们需要与目标库相同，尽管目标上的副本可以被剥离，只要主机上的副本没有被剥离。

对于远程调试，您需要告诉 gdb 目标库的位置，以便它可以加载正确的副本——否则，它可能会尝试加载主机的库。gdb 有两个变量来指定目标库的搜索目录。
```
set sysroot path
	将 `path` 用作正在调试的程序的系统根目录。任何绝对共享库路径都将以 `path` 为前缀；许多运行时加载器在目标程序的内存中存储共享库的绝对路径。当远程启动进程，以及附加到已运行的进程（本地或远程）时，如果操作系统将其报告为绝对路径，则其可执行文件名将以 `path` 为前缀。如果您使用 `set sysroot` 来查找可执行文件和共享库，它们需要以与目标上相同的方式布局，例如在 `path` 下具有 `/bin`、`/lib` 和 `/usr/lib` 层次结构。
	如果 `path` 以序列 `target:` 开头，并且目标系统是远程的，则 gdb 将从远程系统检索目标二进制文件。这仅在使用支持远程 `get` 命令的远程目标时受支持（请参见第 20.2 节[将文件发送到远程系统]，第 320 页）。`path` 中初始 `target:` 之后的部分（如果存在）在远程文件系统上用作系统根前缀。如果 `path` 以序列 `remote:` 开头，则 `set sysroot` 将其转换为 `target:` 。如果您想要使用碰巧名为 `target:` 或 `remote:` 的目录指定本地系统根，则需要使用一些等效的名称变体，例如 `./target:` 。
	对于具有基于 MS-DOS 的文件系统的目标，例如 MS-Windows，gdb 尝试使用 `path` 为目标绝对文件名的几个变体添加前缀。但首先，在 Unix 主机上，gdb 将所有反斜杠目录分隔符转换为正斜杠，因为反斜杠在 Unix 上不是目录分隔符：
		c:\foo\bar.dll ⇒ c:/foo/bar.dll

	然后，gdb 尝试使用 `path` 为目标文件名添加前缀，并在主机文件系统中查找结果文件名：
		c:/foo/bar.dll ⇒ /path/to/sysroot/c:/foo/bar.dll

	如果未找到二进制文件，gdb 会尝试从驱动器规格中删除 `':'` 字符，既是为了方便，也是因为主机文件系统可能不支持带有冒号的文件名：
		c:/foo/bar.dll ⇒ /path/to/sysroot/c/foo/bar.dll

	这使得可以拥有一个系统根，它可以反映具有多个驱动器的目标。例如，您可能希望像这样设置目标系统共享库的本地副本（注意 `c` 与 `z`）：
		/path/to/sysroot/c/sys/bin/foo.dll
		/path/to/sysroot/c/sys/bin/bar.dll
		/path/to/sysroot/z/sys/bin/bar.dll

	并将系统根指向 `/path/to/sysroot`，以便 gdb 可以找到 `c:\sys\bin\foo.dll` 和 `z:\sys\bin\bar.dll` 的正确副本。
	如果仍然未找到二进制文件，gdb 会尝试从目标文件名中删除整个驱动器规格：
		c:/foo/bar.dll ⇒ /path/to/sysroot/foo/bar.dll

	最后一次查找使得如果您不想或不需要，就可以不关心驱动器名称。
	`set solib-absolute-prefix` 命令是 `set sysroot` 的别名。
	您可以使用配置时的 `--with-sysroot` 选项设置默认系统根。如果系统根在 gdb 配置的二进制前缀（使用 `--prefix` 或 `--exec-prefix` 设置）内，则如果安装的 gdb 移动到新位置，默认系统根将自动更新。

show sysroot
	显示当前可执行文件和共享库的前缀。

set solib-search-path path
	如果设置了此变量，`path` 是一个冒号分隔的目录列表，用于搜索共享库。在 `sysroot` 无法定位库，或者库的路径是相对而不是绝对时，使用 `'solib-search-path'` 。如果您想使用 `'solib-search-path'` 而不是 `'sysroot'`，请确保将 `'sysroot'` 设置为不存在的目录，以防止 gdb 找到主机的库。`'sysroot'` 是首选；将其设置为不存在的目录可能会干扰共享库符号的自动加载。

show solib-search-path
	显示当前共享库搜索路径。

set target-file-system-kind kind
	为目标报告的文件名设置假定的文件系统类型。
	目标系统报告的共享库文件名在 gdb 运行的系统上可能没有意义。例如，当从 Unix 主机远程调试具有基于 MS-DOS 文件系统语义的目标时，目标可能向 gdb 报告已加载共享库的文件名，例如 `c:\Windows\kernel32.dll` 。在 Unix 主机上，没有驱动器字母的概念，因此 `'c:\'` 前缀通常不被理解为指示绝对文件名，反斜杠也通常不被视为目录分隔符。在这种情况下，本机文件系统会将整个绝对文件名解释为没有目录组件的相对文件名。这将使得使用 `set sysroot` 无法将 gdb 指向主机上远程目标共享库的副本，并且使用 `set solib-search-path` 也不切实际。
	将 `target-file-system-kind` 设置为 `dos-based` 告诉 gdb 以类似于目标的方式解释此类文件名，并将它们映射到 gdb 本机文件系统语义上有效的文件名。`kind` 的值可以是 `"auto"` ，以及支持的文件系统类型之一。在这种情况下，gdb 会尝试根据当前目标的操作系统确定适当的文件系统变体（请参见第 22.7 节[配置当前 ABI]，第 375 页）。
	支持的文件系统设置有：
		unix        指示 gdb 假定目标文件系统是 Unix 类型。只有以正斜杠（`'/'`）开头的文件名被视为绝对文件名，并且目录分隔符也是正斜杠。
		dos-based   指示 gdb 假定目标文件系统是基于 DOS 的。以正斜杠或驱动器字母后跟冒号（例如，`'c:'`）开头的文件名被视为绝对文件名，并且斜杠（`'/'`）和反斜杠（`'\\'`）都被视为目录分隔符。
		auto        指示 gdb 使用与目标操作系统相关联的文件系统类型（请参见第 22.7 节[配置当前 ABI]，第 375 页）。这是默认值。
```
在处理用户提供的文件名时，gdb 经常需要将它们与程序的调试信息中记录的文件名进行比较。通常，gdb 仅将文件的基本名称作为字符串进行比较，即使对于非常大的程序，这也相当快（文件的基本名称是其名称的最后一部分，在剥离所有前面的目录之后）。这种比较中的快捷方式基于文件不能有多个基本名称的假设。这通常是正确的，但使用符号链接或类似文件系统设施对文件的引用违反了该假设。如果您的程序使用此类设施记录文件，或者如果您使用符号链接等向 gdb 提供文件名，则可以将 `basenames-may-differ` 设置为 `true` ，以指示 gdb 完全规范化它需要比较的每对文件名。这将使文件名比较准确，但代价是显著的减速。
```
set basenames-may-differ
	设置源文件是否可以有多个基本名称。

show basenames-may-differ
	显示源文件是否可以有多个基本名称。
```

## 18.2 文件缓存

为了加快文件加载速度并减少内存使用，gdb 会重用用于跟踪打开文件的 bfd 对象。请参见《二进制文件描述符库》中的“BFD”部分。以下命令允许查看和控制缓存行为。
```
maint info bfds
	此命令会打印 gdb 已知的每个 bfd 对象的信息。

maint set bfd-sharing
maint show bfd-sharing
	控制 bfd 对象是否可以共享。当共享启用时，gdb 会重用已打开的 bfd 对象，而不是重新打开相同的文件。关闭共享不会导致已共享的 bfd 对象不再共享，但之后打开的所有文件都会创建新的 bfd 对象。同样，重新启用共享也不会导致多个现有的 bfd 对象合并为一个共享的 bfd 对象。

set debug bfd-cache level
	开启 bfd 缓存的调试，将级别设置为 `level` 。

show debug bfd-cache
	显示 bfd 缓存的当前调试级别。
```

## 18.3 独立文件中的调试信息

gdb 允许将程序的调试信息放在与可执行文件本身分开的文件中，并且能够自动查找和加载调试信息。由于调试信息可能非常大——有时甚至比可执行代码本身还大——一些系统会将其可执行文件的调试信息分布在单独的文件中，用户仅在需要调试问题时才安装。

gdb 支持两种指定独立调试信息文件的方式：

- 可执行文件包含一个调试链接，指定了独立调试信息文件的名称。独立调试文件的名称通常是 `executable.debug`，其中 `executable` 是相应可执行文件的名称（不含前导目录，例如 `/usr/bin/ls` 对应的是 `ls.debug`）。此外，调试链接还指定了调试文件的 32 位循环冗余校验（CRC）校验和，gdb 用它来验证可执行文件和调试文件来自同一构建。

- 可执行文件包含一个构建 ID，这是一个在相应的调试信息文件中也存在的唯一比特字符串。（仅在某些操作系统上支持，使用 ELF 或 PE 文件格式的二进制文件和 gnu Binutils 时）。有关此功能的更多详细信息，请参见《GNU 链接器》中“命令行选项”部分中 `--build-id` 命令行选项的描述。调试信息文件的名称不是由构建 ID 明确指定的，但可以从构建 ID 计算得出，如下所示。

根据调试信息文件的指定方式，gdb 使用两种不同的方法查找调试文件：

- 对于“调试链接”方法，gdb 首先在可执行文件的目录中查找指定的文件，然后在该目录的 `.debug` 子目录中查找，最后在每个全局调试目录中，以可执行文件的绝对文件名的前导目录命名的子目录中查找。（在 MS-Windows/MS-DOS 上，可执行文件前导目录的驱动器字母会转换为一个字母的子目录，即 `d:/usr/bin/` 会转换为 `/d/usr/bin/` ，因为 Windows 文件系统不允许文件名中有冒号。）

- 对于“构建 ID”方法，gdb 在每个全局调试目录的 `.build-id` 子目录中查找名为 `nn/nnnnnnnn.debug` 的文件，其中 `nn` 是构建 ID 比特字符串的前两个十六进制字符，`nnnnnnnn` 是其余部分。（实际的构建 ID 字符串是 32 个或更多十六进制字符，而非 10 个。）gdb 可以使用构建 ID 自动查询 `debuginfod` 服务器以下载本地未找到的独立调试文件。更多信息请参见附录 K [Debuginfod]，第 889 页。

例如，假设您要求 gdb 调试 `/usr/bin/ls` ，它有一个指定文件 `ls.debug` 的调试链接，并且构建 ID 的十六进制值为 `abcdef1234` 。如果全局调试目录列表包括 `/usr/lib/debug` ，那么 gdb 将按以下顺序查找以下调试信息文件：

- `/usr/lib/debug/.build-id/ab/cdef1234.debug`

- `/usr/bin/ls.debug`

- `/usr/bin/.debug/ls.debug`

- `/usr/lib/debug/usr/bin/ls.debug`

如果仍未找到调试文件且 `debuginfod` 已启用（请参见附录 K [Debuginfod]，第 889 页），gdb 将尝试从 `debuginfod` 服务器下载该文件。

全局调试信息目录默认为 gdb 配置选项 `--with-separate-debug-dir` 设置的值，并通过 gdb 配置选项 `--additional-debug-dirs` 提供的以冒号分隔的目录列表进行扩充。在 gdb 运行期间，您还可以设置全局调试信息目录，并查看 gdb 当前正在使用的目录列表。
```
set debug-file-directory directories
	将 gdb 搜索独立调试信息文件的目录设置为 `directory` 。可以通过路径分隔符连接多个路径组件。

show debug-file-directory
	显示 gdb 搜索独立调试信息文件的目录。
```
调试链接是可执行文件的一个特殊节，名为 `.gnu_debuglink` 。该节必须包含：

- 一个文件名，删除任何前导目录组件，后跟一个零字节。

- 根据需要填充零到三个字节，以在节内达到下一个四字节边界。

- 一个四字节的 CRC 校验和，其字节序与可执行文件本身相同。通过下面给出的函数在调试信息文件的全部内容上计算校验和，将零作为 `crc` 参数传递。

任何可执行文件格式都可以携带调试链接，只要它能够包含一个名为 `.gnu_debuglink` 且具有上述内容的节。

构建 ID 是可执行文件（以及 gdb 可能考虑的其他 ELF 二进制文件）中的一个特殊节。这个节通常名为 `.note.gnu.build-id` ，但该名称不是强制的。它包含构建文件的唯一标识——在同一构建树的多次构建中，ID 保持不变。默认的 SHA1 算法为构建 ID 字符串的内容生成 160 位（40 个十六进制字符）。在带有符号的原始构建二进制文件、其剥离变体和独立的调试信息文件中，都存在具有相同值的相同节。

调试信息文件本身应该是一个普通的可执行文件，包含完整的链接器符号、节和调试信息。调试信息文件的节应该具有与原始文件相同的名称、地址和大小，但它们不需要包含任何数据——就像普通可执行文件中的 `.bss` 节一样。

gnu 二进制实用程序（Binutils）包中的 `objcopy` 实用程序可以使用以下命令生成独立的可执行文件/调试信息文件对：

```
objcopy --only-keep-debug foo foo.debug
strip -g foo
```

这些命令从可执行文件 `foo` 中删除调试信息并将其放入文件 `foo.debug` 中。您可以使用以下一种或两种方法来链接这两个文件：

- 对于调试链接方法，还需要以下额外的命令在 `foo` 中留下调试链接：

```
objcopy --add-gnu-debuglink=foo.debug foo
```

Ulrich Drepper 的 `elfutils` 包从版本 0.53 开始，其中的 `strip` 命令具有这样的功能，即 `strip foo -f foo.debug` 命令与上述两个 `objcopy` 命令和 `ln -s` 命令的功能相同。

- 构建 ID 可以使用 `ld --build-id` 或 `gcc` 的对应项 `gcc -Wl,--build-id` 嵌入到主可执行文件中。自版本 2.18 以来，gnu 二进制实用程序（Binutils）包中就存在构建 ID 支持以及调试文件分离的兼容性修复。

`.gnu_debuglink` 中使用的 CRC 是 IEEE 802.3 中定义的 CRC-32，使用的多项式为：

`x^32 + x^26 + x^23 + x^22 + x^16 + x^12 + x^11 + x^10 + x^8 + x^7 + x^5 + x^4 + x^2 + x + 1`

该函数是逐字节计算的，首先取每个字节的最低有效位。使用初始模式 `0xffffffff` ，以确保前导零影响 CRC，并且最终结果取反以确保尾随零也影响 CRC。

注意：这与处理远程串行协议 `qCRC` 数据包（请参见[qCRC 数据包]，第 792 页）中使用的 CRC 多项式相同。然而，在远程串行协议的情况下，CRC 是首先计算最高有效位，并且结果不取反，因此尾随零对 CRC 值没有影响。

为了完成描述，我们在下面展示生成 `.gnu_debuglink` 中使用的 CRC 的函数的代码。将最初提供的 `crc` 参数取反意味着向此函数传递零的初始调用将使用 `0xffffffff` 开始计算 CRC。
```
unsigned long
gnu_debuglink_crc32 (unsigned long crc,
					 unsigned char *buf, size_t len)
{
	static const unsigned long crc32_table[256] =
	{
		0x00000000, 0x77073096, 0xee0e612c, 0x990951ba, 0x076dc419,
		0x706af48f, 0xe963a535, 0x9e6495a3, 0x0edb8832, 0x79dcb8a4,
		0xe0d5e91e, 0x97d2d988, 0x09b64c2b, 0x7eb17cbd, 0xe7b82d07,
		0x90bf1d91, 0x1db71064, 0x6ab020f2, 0xf3b97148, 0x84be41de,
		0x1adad47d, 0x6ddde4eb, 0xf4d4b551, 0x83d385c7, 0x136c9856,
		0x646ba8c0, 0xfd62f97a, 0x8a65c9ec, 0x14015c4f, 0x63066cd9,
		0xfa0f3d63, 0x8d080df5, 0x3b6e20c8, 0x4c69105e, 0xd56041e4,
		0xa2677172, 0x3c03e4d1, 0x4b04d447, 0xd20d85fd, 0xa50ab56b,
		0x35b5a8fa, 0x42b2986c, 0xdbbbc9d6, 0xacbcf940, 0x32d86ce3,
		0x45df5c75, 0xdcd60dcf, 0xabd13d59, 0x26d930ac, 0x51de003a,
		0xc8d75180, 0xbfd06116, 0x21b4f4b5, 0x56b3c423, 0xcfba9599,
		0xb8bda50f, 0x2802b89e, 0x5f058808, 0xc60cd9b2, 0xb10be924,
		0x2f6f7c87, 0x58684c11, 0xc1611dab, 0xb6662d3d, 0x76dc4190,
		0x01db7106, 0x98d220bc, 0xefd5102a, 0x71b18589, 0x06b6b51f,
		0x9fbfe4a5, 0xe8b8d433, 0x7807c9a2, 0x0f00f934, 0x9609a88e,
		0xe10e9818, 0x7f6a0dbb, 0x086d3d2d, 0x91646c97, 0xe6635c01,
		0x6b6b51f4, 0x1c6c6162, 0x856530d8, 0xf262004e, 0x6c0695ed,
		0x1b01a57b, 0x8208f4c1, 0xf50fc457, 0x65b0d9c6, 0x12b7e950,
		0x8bbeb8ea, 0xfcb9887c, 0x62dd1ddf, 0x15da2d49, 0x8cd37cf3,
		0xfbd44c65, 0x4db26158, 0x3ab551ce, 0xa3bc0074, 0xd4bb30e2,
		0x4adfa541, 0x3dd895d7, 0xa4d1c46d, 0xd3d6f4fb, 0x4369e96a,
		0x346ed9fc, 0xad678846, 0xda60b8d0, 0x44042d73, 0x33031de5,
		0xaa0a4c5f, 0xdd0d7cc9, 0x5005713c, 0x270241aa, 0xbe0b1010,
		0xc90c2086, 0x5768b525, 0x206f85b3, 0xb966d409, 0xce61e49f,
		0x5edef90e, 0x29d9c998, 0xb0d09822, 0xc7d7a8b4, 0x59b33d17,
		0x2eb40d81, 0xb7bd5c3b, 0xc0ba6cad, 0xedb88320, 0x9abfb3b6,
		0x03b6e20c, 0x74b1d29a, 0xead54739, 0x9dd277af, 0x04db2615,
		0x73dc1683, 0xe3630b12, 0x94643b84, 0x0d6d6a3e, 0x7a6a5aa8,
		0xe40ecf0b, 0x9309ff9d, 0x0a00ae27, 0x7d079eb1, 0xf00f9344,
		0x8708a3d2, 0x1e01f268, 0x6906c2fe, 0xf762575d, 0x806567cb,
		0x196c3671, 0x6e6b06e7, 0xfed41b76, 0x89d32be0, 0x10da7a5a,
		0x67dd4acc, 0xf9b9df6f, 0x8ebeeff9, 0x17b7be43, 0x60b08ed5,
		0xd6d6a3e8, 0xa1d1937e, 0x38d8c2c4, 0x4fdff252, 0xd1bb67f1,
		0xa6bc5767, 0x3fb506dd, 0x48b2364b, 0xd80d2bda, 0xaf0a1b4c,
		0x36034af6, 0x41047a60, 0xdf60efc3, 0xa867df55, 0x316e8eef,
		0x4669be79, 0xcb61b38c, 0xbc66831a, 0x256fd2a0, 0x5268e236,
		0xcc0c7795, 0xbb0b4703, 0x220216b9, 0x5505262f, 0xc5ba3bbe,
		0xb2bd0b28, 0x2bb45a92, 0x5cb36a04, 0xc2d7ffa7, 0xb5d0cf31,
		0x2cd99e8b, 0x5bdeae1d, 0x9b64c2b0, 0xec63f226, 0x756aa39c,
		0x026d930a, 0x9c0906a9, 0xeb0e363f, 0x72076785, 0x05005713,
		0x95bf4a82, 0xe2b87a14, 0x7bb12bae, 0x0cb61b38, 0x92d28e9b,
		0xe5d5be0d, 0x7cdcefb7, 0x0bdbdf21, 0x86d3d2d4, 0xf1d4e242,
		0x68ddb3f8, 0x1fda836e, 0x81be16cd, 0xf6b9265b, 0x6fb077e1,
		0x18b74777, 0x88085ae6, 0xff0f6a70, 0x66063bca, 0x11010b5c,
		0x8f659eff, 0xf862ae69, 0x616bffd3, 0x166ccf45, 0xa00ae278,
		0xd70dd2ee, 0x4e048354, 0x3903b3c2, 0xa7672661, 0xd06016f7,
		0x4969474d, 0x3e6e77db, 0xaed16a4a, 0xd9d65adc, 0x40df0b66,
		0x37d83bf0, 0xa9bcae53, 0xdebb9ec5, 0x47b2cf7f, 0x30b5ffe9,
		0xbdbdf21c, 0xcabac28a, 0x53b39330, 0x24b4a3a6, 0xbad03605,
		0xcdd70693, 0x54de5729, 0x23d967bf, 0xb3667a2e, 0xc4614ab8,
		0x5d681b02, 0x2a6f2b94, 0xb40bbe37, 0xc30c8ea1, 0x5a05df1b,
		0x2d02ef8d
	};
	unsigned char *end;
	
	crc = ~crc & 0xffffffff;
	for (end = buf + len; buf < end; ++buf)
		crc = crc32_table[(crc ^ *buf) & 0xff] ^ (crc >> 8);
	return ~crc & 0xffffffff;
}
```
此计算不适用于“构建 ID”方法。

## 18.4 特殊节中的调试信息

一些系统提供预先构建的可执行文件和库，其中具有特殊的 `'.gnu_debugdata'` 节。此功能称为 MiniDebugInfo。此节包含一个 LZMA 压缩对象，用于为回溯提供额外的符号。

此节的目的是为简单的回溯提供额外的最小调试信息。它并非旨在替代完整的独立调试信息（请参见第 18.3 节[独立调试文件]，第 300 页）。下面的示例展示了其预期用途；然而，gdb 目前并未对该节中可能包含的调试信息类型施加限制。

gdb 支持此扩展。如果该节存在，并且在未找到其他调试信息源且 gdb 配置了 LZMA 支持的情况下，将使用它。

可以使用 `objcopy` 和其他标准实用程序轻松创建此节：

```
	# 从主二进制文件中提取动态符号，无需在常规符号表中也包含这些。
	nm -D binary --format=posix --defined-only \
		| awk ’{ print $1 }’ | sort > dynsyms

	# 从调试信息中提取所有文本（即函数）符号。
	# （请注意，实际上我们也接受 "D" 符号，以支持像 PowerPC64 这样使用函数描述符的平台。）
	nm binary --format=posix --defined-only \
		| awk ’{ if ($2 == "T" || $2 == "t" || $2 == "D") print $1 }’ \
		| sort > funcsyms

	# 保留所有不在动态符号表中的函数符号。
	comm -13 dynsyms funcsyms > keep_symbols

	# 将完整调试信息分离到调试二进制文件中。
	objcopy --only-keep-debug binary debug

	# 复制完整的调试信息，仅保留最小的符号集并删除一些不必要的节。
	objcopy -S --remove-section .gdb_index --remove-section .comment \
		--keep-symbols=keep_symbols debug mini_debuginfo

	# 从原始二进制文件中删除完整的调试信息。
	strip --strip-all -R .comment binary

	# 将压缩数据注入原始二进制文件的 `.gnu_debugdata` 节中。
	xz mini_debuginfo
	objcopy --add-section .gnu_debugdata=mini_debuginfo.xz binary
```

## 18.5 索引文件加速 gdb

当 gdb 找到符号文件时，它会扫描文件中的符号以构建内部符号表。这使得大多数 gdb 操作能够快速运行——但早期会有延迟。对于大型程序，此延迟可能会很长，因此 gdb 提供了一种构建索引的方法，以加快启动速度。

为了方便起见，gdb 附带了一个程序 `gdb-add-index`，可用于向符号文件添加索引。它将符号文件作为唯一的参数：

```
$ gdb-add-index symfile
```

请参见[gdb-add-index]，第 899 页。

也可以手动完成此工作。以下是 `gdb-add-index` 在幕后所做的事情。

索引存储为符号文件中的一个节。gdb 可以将索引写入文件，然后可以使用 `objcopy` 将其放入符号文件中。

要创建索引文件，请使用 `save gdb-index` 命令：
```
save gdb-index [-dwarf-5] directory
	为 gdb 当前已知的所有符号文件创建索引文件。对于每个已知的符号文件，此命令默认创建一个文件 `symbol-file.gdb-index`。如果使用 `-dwarf-5` 选项调用此命令，则会生成 2 个文件：`symbol-file.debug_names` 和 `symbol-file.debug_str`。这些文件将在给定的目录中创建。
	`directory` 参数支持转义和引用，请参见第 3.4 节[作为命令参数的文件名]，第 27 页。
```
创建索引文件后，可以使用 `objcopy` 将其合并到符号文件（此处名为 `symfile`）中，例如：

```
$ objcopy --add-section .gdb_index=symfile.gdb-index \
	--set-section-flags .gdb_index=readonly symfile symfile
```

或者对于 `-dwarf-5` ：

```
$ objcopy --dump-section .debug_str=symfile.debug_str.new symfile
$ cat symfile.debug_str >>symfile.debug_str.new
$ objcopy --add-section .debug_names=symfile.gdb-index \
	--set-section-flags .debug_names=readonly \
	--update-section .debug_str=symfile.debug_str.new symfile symfile
```

gdb 通常会忽略已弃用的较旧版本的 `.gdb_index` 节。通常它们被弃用是因为缺少新功能或存在性能问题。要告诉 gdb 无论如何都使用已弃用的索引节，请指定 `set use-deprecated-index-sections on`。默认值为 `off`。这可以加快启动速度，但可能会导致某些功能丢失。请参见附录 J [索引节格式]，第 885 页。

警告：将 `use-deprecated-index-sections` 设置为 `on` 必须在 gdb 读取文件之前完成。以下操作将不起作用：

```
$ gdb -ex "set use-deprecated-index-sections on" <program>
```

相反，您必须例如这样做：

```
$ gdb -iex "set use-deprecated-index-sections on" <program>
```

索引仅在使用 DWARF 调试信息时有效，而不适用于 `stabs` 。

### 18.5.1 自动符号索引缓存

gdb 可以自动在磁盘上的缓存中保存此索引的副本，并在将来加载相同二进制文件时从那里检索。可以使用 `set index-cache enabled on` 启用此功能。以下命令可用于调整索引缓存的行为。
```
set index-cache enabled on
set index-cache enabled off
	启用或禁用符号索引缓存的使用。

set index-cache directory directory
show index-cache directory
	设置/显示索引文件将保存的目录。
	此目录的默认值取决于主机平台。在大多数系统上，如果定义了 `XDG_CACHE_HOME` 环境变量，则索引缓存在该变量指向的目录的 `gdb` 子目录中，否则在您的主目录的 `.cache/gdb` 子目录中。但是，在某些系统上，默认值可能根据本地约定而有所不同。
	索引缓存使用的磁盘空间没有限制。完全可以安全地删除该目录的内容以释放磁盘空间。

show index-cache stats
	打印自 gdb 启动以来的缓存命中和未命中的数量。
```
### 18.5.2 使用 GNU gold 构建索引

GNU gold 链接器可以在链接时通过向 `gold` 传递 `--gdb-index` 标志将索引写入生成的 ELF 文件中。

如果您打算构建程序并立即在 gdb 下运行它，这特别有用。您可能会发现，链接器在内存中拥有相关信息时写入索引的总体速度更快，而不是使用 `gdb-add-index` 从磁盘重新加载数据，或者在根本没有索引的情况下调试程序，特别是对于大型程序。

## 18.6 `.debug_names` 的扩展

DWARF 规范文档中提到了一个可选的索引节 `'.debug_names'` 。gdb 既可以读取也可以创建此节。但是，为了与 gdb 配合使用，需要进行一些扩展。

gdb 使用扩充字符串 `'GDB2'` 。早期版本使用字符串 `'GDB'` ，但这些版本的索引不再受支持。

gdb 不使用指定的哈希表。因此，由于此哈希表是可选的，gdb 也不会写入它。

gdb 还生成并使用一些额外的索引属性：
```
DW_IDX_GNU_internal
	其值为 `0x2000` 。这是一个标志，设置时表示相关条目具有静态链接。

DW_IDX_GNU_main
	其值为 `0x2002` 。这是一个标志，设置时表示相关条目是程序的 `main` 函数。

DW_IDX_GNU_language
	其值为 `0x2003` 。它是 `DW_LANG_` 常量，表示相关条目的语言。

DW_IDX_GNU_linkage_name
	其值为 `0x2004` 。这是一个标志，设置时表示相关条目是一个链接名称，而不是源名称。
```
## 18.7 读取符号文件时的错误

在读取符号文件时，gdb 偶尔会遇到问题，例如不识别的符号类型或编译器输出中的已知错误。默认情况下，gdb 不会通知您此类问题，因为它们相对常见，并且主要是调试编译器的人员感兴趣。如果您有兴趣查看有关构造不良的符号表的信息，可以要求 gdb 针对每种此类问题仅打印一条消息，无论问题出现多少次；或者可以要求 gdb 打印更多消息，以查看问题出现的次数，使用 `set complaints` 命令（请参见第 22.9 节[可选的警告和消息]，第 381 页）。

当前打印的消息及其含义包括：
```
inner block not inside outer block in symbol
	符号信息显示了符号范围的开始和结束位置（例如在函数或语句块的开始）。此错误表示内部范围块未完全包含在其外部范围块中。
	gdb 通过将内部块视为与外部块具有相同的范围来解决此问题。在错误消息中，如果外部块不是函数，则符号可能显示为“（不知道）”。

block at address out of order
	符号范围块的符号信息应按地址递增的顺序出现。此错误表示并非如此。
	gdb 不会解决此问题，并且在查找其正在读取的源文件中的符号时会遇到麻烦。（您通常可以通过指定 `set verbose on` 来确定受影响的源文件。请参见第 22.9 节[可选的警告和消息]，第 381 页。）

bad block start address patched
	符号范围块的符号信息的起始地址小于前一个源行的地址。已知在 SunOS 4.1.1（及更早版本）的 C 编译器中会发生这种情况。
	gdb 通过将符号范围块视为从前一个源行开始来解决此问题。

bad string table offset in symbol n
	符号编号 `n` 包含指向字符串表的指针，该指针大于字符串表的大小。
	gdb 通过将该符号视为具有名称 `foo` 来解决此问题，这可能会导致如果许多符号最终具有此名称时出现其他问题。

unknown symbol type 0xnn
	符号信息包含 gdb 尚不知道如何读取的新数据类型。`0xnn` 是以十六进制表示的无法理解的信息的符号类型。
	gdb 通过忽略此符号信息来解决错误。这通常允许您调试程序，尽管某些符号不可访问。如果您遇到此类问题并想调试它，可以使用 gdb 自身进行调试，在 `complain` 上设置断点，然后转到函数 `read_dbx_symtab` 并检查 `*bufp` 以查看符号。

stub type has NULL name
	gdb 找不到结构体或类的完整定义。

const/volatile indicator missing (ok if using g++ v1.x), got...
	C++ 成员函数的符号信息缺少编译器最近版本应输出的某些信息。

info mismatch between compiler and debugger
	gdb 无法解析编译器输出的类型规范。
```

## 18.8 GDB 数据文件

gdb 有时会读取辅助数据文件。这些文件保存在一个称为数据目录的目录中。

您可以设置数据目录的名称，并查看 gdb 当前正在使用的名称。
```
set data-directory directory
	将 gdb 搜索辅助数据文件的目录设置为 `directory` 。

show data-directory
	显示 gdb 搜索辅助数据文件的目录。
```
您可以使用配置时的 `--with-gdb-datadir` 选项设置默认数据目录。如果数据目录在 gdb 配置的二进制前缀内（使用 `--prefix` 或 `--exec-prefix` 设置），则如果已安装的 gdb 移动到新位置，默认数据目录将自动更新。

数据目录也可以使用 `--data-directory` 命令行选项进行指定。请参见第 2.1.2 节[模式选项]，第 13 页。
