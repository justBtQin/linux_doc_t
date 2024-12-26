此目录尝试记录 Linux 内核与用户空间之间的应用二进制接口（ABI），以及这些接口的相对稳定性。由于 Linux 的不断变化的性质以及不同的成熟度级别，用户空间程序应以不同的方式使用这些接口。

我们有四个不同级别的 ABI 稳定性，如本位置的四个不同子目录所示。接口可能会根据以下描述的规则更改稳定性级别。

不同的稳定性级别如下：

`stable/`

此目录记录了开发人员定义为稳定的接口。用户空间程序可以自由使用这些接口，没有任何限制，并且将至少保证两年的向后兼容性。大多数接口（如系统调用）预计永远不会更改并且始终可用。

`testing/`

此目录记录了被认为是稳定的接口，因为该接口的主要开发已经完成。可以更改接口以添加新功能，但通过这样做不会破坏当前接口，除非在其中发现严重错误或安全问题。用户空间程序可以开始依赖这些接口，但必须意识到在这些接口移至标记为稳定之前可能会发生更改。强烈鼓励使用这些接口的程序将其名称添加到这些接口的描述中，以便内核开发人员在发生任何更改时可以轻松通知他们（有关如何执行此操作的详细信息，请参阅下面文件布局的描述）。

`obsolete/`

此目录记录了仍在内核中但标记为在以后某个时间点删除的接口。接口的描述将记录其过时的原因以及预计删除的时间。

`removed/`

此目录包含已从内核中删除的旧接口列表。

这些目录中的每个文件都将包含以下信息：

What：接口的简短描述

Date：创建日期

KernelVersion：此功能首次出现的内核版本

Contact：此接口的主要联系人（可能是邮件列表）

Description：接口的详细描述以及如何使用它。

Users：希望在接口更改时收到通知的此接口的所有用户。这对于“测试”阶段的接口非常重要，以便内核开发人员可以与用户空间开发人员合作，确保不会以不可接受的方式破坏事物。对于这些接口获得反馈也很重要，以确保它们以正确的方式工作并且不需要进一步更改。

接口在不同级别之间的移动方式：

稳定的接口可以移动到过时的接口，只要提供了适当的通知。

如果经过记录的时间已经过去，接口可以从过时状态中删除并从内核中删除。

在测试状态下的接口可以在开发人员认为完成时移动到稳定状态。如果不先经过过时状态，它们不能从内核树中删除。

由开发人员决定将其接口放置在他们希望其开始的类别中。

非 ABI 的值得注意的部分，在任何情况下都不应被视为稳定：

- Kconfig。用户空间不应依赖于 /proc/config.gz 中、通常安装到 /boot 的.config 副本中或任何内核构建过程调用中的任何特定 Kconfig 符号的存在或不存在。

- 内核内部符号。不要依赖于 System.map 文件或内核二进制文件本身中的任何内核符号的存在、不存在、位置或类型。请参阅 Documentation/stable_api_nonsense.txt。