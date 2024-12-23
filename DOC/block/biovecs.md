# 不可变的`biovecs`和`biovec`迭代器：

=======================================

Kent Overstreet <kmo@daterainc.com>

从 3.13 版本开始，在提交`bio`之后，`biovecs`不应再被修改。相反，我们有一个新的结构体`bvec_iter`，它表示`biovec`的一个范围 - 迭代器将在`bio`完成时被修改，而不是`biovec`。

更具体地说，以前需要部分完成`bio`的旧代码会更新`bi_sector`和`bi_size`，并将`bi_idx`推进到下一个`biovec`。如果它在`biovec`的中间部分结束，它会增加`bv_offset`并减少`bv_len`，减少的数量是在该`biovec`中完成的字节数。

在新的方案中，为了部分完成`bio`而必须进行突变的所有内容都被分离到结构体`bvec_iter`中：`bi_sector`、`bi_size`和`bi_idx`已被移动到那里；并且不是修改`bv_offset`和`bv_len`，结构体`bvec_iter`有`bi_bvec_done`，它表示在当前`bvec`中完成的字节数。

有一堆新的辅助宏用于隐藏可怕的细节 - 特别是呈现部分完成的`biovec`的幻觉，以便普通代码不必处理`bi_bvec_done`。

* 驱动程序代码不应再直接引用`biovecs`；我们现在有`bio_iovec()`和`bio_iovec_iter()`宏，它们返回文字`struct biovec`，从原始`biovec`构造，但考虑到`bi_bvec_done`和`bi_size`。

`bio_for_each_segment()`已更新为接受`bvec_iter`参数，而不是整数（对应于`bi_idx`）；对于很多代码，转换只需要将`bio_for_each_segment()`的参数类型更改为`bvec_iter`。

* 推进`bvec_iter`使用`bio_advance_iter()`；`bio_advance()`是`bio_advance_iter()`的包装器，它对`bio->bi_iter`进行操作，并在存在时推进`bio`完整性的迭代器。

有一个较低级别的推进函数 - `bvec_iter_advance()` - 它接受指向`biovec`的指针，而不是`bio`；这由`bio`完整性代码使用。

# 这一切给我们带来了什么？

=======================

拥有一个真正的迭代器，并使`biovecs`不可变，有许多优点：

* 以前，当你不是一次处理一个`bvec`时，遍历`bios`非常笨拙 - 例如，`fs/bio.c`中的`bio_copy_data()`，它将一个`bio`的内容复制到另一个`bio`中。因为`biovecs`不一定是相同的大小，旧代码很棘手且复杂 - 它必须同时遍历两个不同的`bios`，在每个`biovec`中保持`bi_idx`和偏移量。

新代码要简单得多 - 看看。这种模式在很多地方都会出现；很多驱动程序基本上在公开编写`bvec`迭代器，有了通用的实现，大大简化了很多代码。

* 以前，任何在`bio`完成后可能需要使用`biovec`的代码（也许是将数据复制到其他地方，或者如果有错误则将其重新提交到其他地方）都必须保存整个`bvec`数组 - 同样，这在很多地方都在进行。

* `biovecs`可以在多个`bios`之间共享 - `bvec`迭代器可以表示现有`biovec`的任意范围，既可以在`biovec`的中间开始，也可以在中间结束。这就是能够有效地分割任意`bios`的原因。请注意，这意味着我们仅使用`bi_size`来确定何时到达`bio`的末尾，而不是`bi_vcnt` - 并且`bio_iovec()`宏在构造`biovecs`时考虑了`bi_size`。

* 分割`bios`现在简单得多。旧的`bio_split()`甚至不适用于具有多个`bvec`的`bios`！现在，我们可以有效地分割任意大小的`bios` - 因为新的`bio`可以共享旧`bio`的`biovec`。

然而，必须注意确保在分割的`bio`仍在使用`biovec`时，`biovec`不会被释放，以防原始`bio`首先完成。在分割`bios`时使用`bio_chain()`有助于解决此问题。

* 提交部分完成的`bios`现在完全没问题 - 这在堆叠块驱动程序和各种代码（例如`md`和`bcache`）中偶尔会出现，为此有一些丑陋的解决方法。

以前，提交部分完成的`bio`对大多数设备来说都可以正常工作，但由于访问原始`bvec`数组是常态，并非所有驱动程序都会尊重`bi_idx`，这些驱动程序会出现故障。现在，由于所有驱动程序都必须通过`bvec`迭代器 - 并且已经经过审核以确保它们是 - 提交部分完成的`bios`是完全可以的。

# 其他影响：

===================

* 几乎所有对`bi_idx`的使用现在都是不正确的，并已被删除；相反，以前使用`bi_idx`的地方现在使用`bvec_iter`，可能将其传递给其中一个辅助宏。

即，不再使用`bio_iovec_idx()`（或`bio->bi_iovec[bio->bi_idx]`），而是使用`bio_iter_iovec()`，它接受一个`bvec_iter`并返回一个文字`struct bio_vec` - 从原始`biovec`动态构造，但考虑到`bi_bvec_done`（和`bi_size`）。

* 驱动程序代码不能信任或依赖`bi_vcnt` - 即，任何实际上不拥有`bio`的代码。原因有两个：首先，它实际上不再需要用于遍历`bio` - 我们只使用`bi_size`。其次，当克隆一个`bio`并重用（部分）原始`bio`的`biovec`时，为了计算新`bio`的`bi_vcnt`，我们必须遍历新`bio`中的所有`biovec` - 这很愚蠢，因为它不是必需的。

所以，不要再使用`bi_vcnt`了。