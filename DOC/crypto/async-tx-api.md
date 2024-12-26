<markdown_document>

# Asynchronous Transfers/Transforms API

## 1 INTRODUCTION

异步传输/转换 API 提供了用于描述异步批量内存传输/转换链的方法，并支持事务间的依赖关系。它被实现为一个 dmaengine 客户端，用于平滑不同硬件卸载引擎实现的细节。编写为该 API 的代码可以针对异步操作进行优化，并且该 API 将操作链适配到可用的卸载资源。

## 2 GENEALOGY

该 API 最初设计用于使用英特尔 (R) Xscale 系列 I/O 处理器中的卸载引擎来卸载 md-raid5 驱动程序的内存复制和异或奇偶校验计算。它还基于为在网络堆栈中使用英特尔 (R) I/OAT 引擎卸载内存复制而开发的“dmaengine”层。由此产生了以下设计特点：

1/ 隐式同步路径：API 的用户不需要知道他们正在运行的平台是否具有卸载能力。当有引擎可用时，操作将被卸载，否则在软件中执行。

2/ 跨通道依赖链：API 允许提交依赖操作链，如 raid5 情况下的异或->复制->异或。API 自动处理从一个操作到另一个操作的转换意味着硬件通道切换的情况。

3/ dmaengine 扩展以支持多个客户端和除“memcpy”之外的操作类型

## 3 USAGE

### 3.1 General format of the API:

struct dma_async_tx_descriptor *

async_<operation>(<op specific parameters>, struct async_submit ctl *submit)

### 3.2 Supported operations:

memcpy - 源缓冲区和目标缓冲区之间的内存复制

memset - 用一个字节值填充目标缓冲区

xor - 对一系列源缓冲区进行异或运算，并将结果写入目标缓冲区

xor_val - 对一系列源缓冲区进行异或运算，并在结果为零时设置一个标志。实现尝试防止写入内存

pq - 从一系列源缓冲区生成 p+q（raid6 校验和）

pq_val - 验证 p 和/或 q 缓冲区与给定的源系列同步

datap - （raid6_datap_recov）从给定的源中恢复 raid6 数据块和 p 块

2data - （raid6_2data_recov）从给定的源中恢复 2 个 raid6 数据块

### 3.3 Descriptor management:

当操作已排队异步执行时，返回值非 NULL 并指向一个“描述符”。描述符是在卸载引擎驱动程序控制下可回收的资源，在操作完成后可重复使用。当应用程序需要提交一系列操作时，必须保证在提交依赖项之前不会自动回收描述符。这要求在允许卸载引擎驱动程序回收（或释放）描述符之前，所有描述符都由应用程序确认。可以通过以下方法之一确认描述符：

1/ 设置 ASYNC_TX_ACK 标志，如果不提交子操作

2/ 将未确认的描述符作为依赖项提交到另一个 async_tx 调用将隐式设置确认状态。

3/ 对描述符调用 async_tx_ack()。

### 3.4 When does the operation execute?

操作不会在 async_<operation>调用返回后立即发出。卸载引擎驱动程序会批处理操作，以通过减少管理通道所需的 mmio 周期数来提高性能。一旦达到驱动程序特定的阈值，驱动程序将自动发出挂起的操作。应用程序可以通过调用 async_tx_issue_pending_all()来强制此事件。由于应用程序不知道通道到操作的映射，因此此操作对所有通道都有效。

### 3.5 When does the operation complete?

应用程序有两种方法可以了解操作的完成情况。

1/ 调用 dma_wait_for_async_tx()。此调用使 CPU 在轮询操作完成时旋转。它处理依赖链和发出挂起的操作。

2/ 指定完成回调。如果卸载引擎驱动程序支持中断，则回调例程在 tasklet 上下文中运行；如果操作在软件中同步执行，则在应用程序上下文中调用。可以在调用 async_<operation>时设置回调，或者当应用程序需要提交未知长度的链时，可以使用 async_trigger_callback()例程在链的末尾设置完成中断/回调。

### 3.6 Constraints:

1/ 不允许在 IRQ 上下文中调用 async_<operation>。在不违反约束 #2 的情况下，允许其他上下文。

2/ 完成回调例程不能提交新的操作。这在同步情况下会导致递归，在异步情况下会导致获取两次自旋锁。

### 3.7 Example:

执行一个异或->复制->异或操作，其中每个操作都依赖于前一个操作的结果：

void callback(void *param)

{

	struct completion *cmp = param;

	complete(cmp);

}

void run_xor_copy_xor(struct page **xor_srcs,

		      int xor_src_cnt,

		      struct page *xor_dest,

		      size_t xor_len,

		      struct page *copy_src,

		      struct page *copy_dest,

		      size_t copy_len)

{

	struct dma_async_tx_descriptor *tx;

	addr_conv_t addr_conv[xor_src_cnt];

	struct async_submit_ctl submit;

	addr_conv_t addr_conv[NDISKS];

	struct completion cmp;

	init_async_submit(&submit, ASYNC_TX_XOR_DROP_DST, NULL, NULL, NULL,

			  addr_conv);

	tx = async_xor(xor_dest, xor_srcs, 0, xor_src_cnt, xor_len, &submit)

	submit->depend_tx = tx;

	tx = async_memcpy(copy_dest, copy_src, 0, 0, copy_len, &submit);

	init_completion(&cmp);

	init_async_submit(&submit, ASYNC_TX_XOR_DROP_DST | ASYNC_TX_ACK, tx,

			  callback, &cmp, addr_conv);

	tx = async_xor(xor_dest, xor_srcs, 0, xor_src_cnt, xor_len, &submit);

	async_tx_issue_pending_all();

	wait_for_completion(&cmp);

}

有关标志的更多信息，请参阅 include/linux/async_tx.h。有关更多实现示例，请参阅 drivers/md/raid5.c 中的 ops_run_*和 ops_complete_*例程。

## 4 DRIVER DEVELOPER NOTES

### 4.1 Conformance points:

dmaengine 驱动程序需要满足一些一致性点，以适应使用 async_tx API 的应用程序所做的假设：

1/ 期望完成回调在 tasklet 上下文中发生

2/ 从不在 IRQ 上下文中操作 dma_async_tx_descriptor 字段

3/ 在描述符清理路径中使用 async_tx_run_dependencies()来处理依赖操作的提交

### 4.2 "My application needs exclusive control of hardware channels"

主要在使用 DMA 引擎驱动程序支持设备到内存操作的情况下出现此要求。由于许多平台特定原因，执行这些操作的通道不能共享。对于这些情况，提供了 dma_request_channel()接口。

该接口是：

struct dma_chan *dma_request_channel(dma_cap_mask_t mask,

				     dma_filter_fn filter_fn,

				     void *filter_param);

其中 dma_filter_fn 定义为：

typedef bool (*dma_filter_fn)(struct dma_chan *chan, void *filter_param);

当可选的“filter_fn”参数设置为 NULL 时，dma_request_channel 仅返回满足能力掩码的第一个通道。否则，当掩码参数不足以指定必要的通道时，可以使用 filter_fn 例程来处理系统中可用的通道。对于系统中的每个空闲通道，都会调用一次 filter_fn 例程。一旦看到合适的通道，filter_fn 返回 DMA_ACK，这将标记该通道为 dma_request_channel 的返回值。通过此接口分配的通道专属于调用者，直到调用 dma_release_channel()。

在实现驱动程序和使用者时需要注意几点：

1/ 一旦通道被私有分配，即使在调用 dma_release_channel()之后，也不再被通用分配器考虑。

2/ 由于能力是在设备级别指定的，具有多个通道的 dma_device 将要么所有通道都是公共的，要么所有通道都是私有的。

## 5 SOURCE

include/linux/dmaengine.h：DMA 驱动程序和 API 用户的核心头文件

drivers/dma/dmaengine.c：卸载引擎通道管理例程

drivers/dma/：卸载引擎驱动程序的位置

include/linux/async_tx.h：async_tx API 的核心头文件

crypto/async_tx/async_tx.c：async_tx 到 dmaengine 的接口和通用代码

crypto/async_tx/async_memcpy.c：复制卸载

crypto/async_tx/async_xor.c：异或和异或零和卸载

</markdown_document>