# Kernel Connector.

Kernel connector - 基于新的 netlink 的用户空间<->内核空间易于使用的通信模块。

连接器驱动程序使得使用基于 netlink 的网络连接各种代理变得容易。必须注册一个回调函数和一个标识符。当驱动程序接收到带有适当标识符的特殊 netlink 消息时，将调用适当的回调函数。

从用户空间的角度来看，这非常简单：

`socket();`

`bind();`

`send();`

`recv();`

但是，如果内核空间想要使用这种连接的全部功能，驱动程序编写者必须创建特殊的套接字，必须了解`struct sk_buff`处理等。连接器驱动程序允许任何内核空间代理以一种明显更容易的方式使用基于 netlink 的网络进行进程间通信：

`int cn_add_callback(struct cb_id *id, char *name, void (*callback) (struct cn_msg *, struct netlink_skb_parms *));`

`void cn_netlink_send_multi(struct cn_msg *msg, u16 len, u32 portid, u32 __group, int gfp_mask);`

`void cn_netlink_send(struct cn_msg *msg, u32 portid, u32 __group, int gfp_mask);`

`struct cb_id`

{

`__u32` `idx;`

`__u32` `val;`

};

`idx`和`val`是唯一的标识符，必须在`connector.h`头文件中注册以供内核使用。`void (*callback) (void *)`是一个回调函数，当连接器核心接收到带有上述`idx.val`的消息时将被调用。该函数的参数必须解引用为`struct cn_msg *`。

`struct cn_msg`

{

`struct cb_id` `id;`

`__u32` `seq;`

`__u32` `ack;`

`__u32` `len;` /* 后续数据的长度 */

`__u8` `data[0];`

};

# Connector interfaces.

`int cn_add_callback(struct cb_id *id, char *name, void (*callback) (struct cn_msg *, struct netlink_skb_parms *));`

向连接器核心注册新的回调函数。

`struct cb_id *id` - 唯一的连接器用户标识符。必须在`connector.h`中注册以供合法的内核用户使用。

`char *name` - 连接器的回调符号名称。

`void (*callback) (struct cn..)` - 连接器的回调函数。`cn_msg`和发送者的凭证

`void cn_del_callback(struct cb_id *id);`

向连接器核心注销新的回调函数。

`struct cb_id *id` - 唯一的连接器用户标识符。

`int cn_netlink_send_multi(struct cn_msg *msg, u16 len, u32 portid, u32 __groups, int gfp_mask);`

`int cn_netlink_send(struct cn_msg *msg, u32 portid, u32 __groups, int gfp_mask);`

将消息发送到指定的组。可以在软中断上下文中安全地调用，但在内存压力较大时可能会静默失败。如果给定组没有监听器，则可能返回`ESRCH`。

`struct cn_msg *` - 消息头（带有附加数据）。

`u16 len` - 对于`_multi`，可以发送多个`cn_msg`消息

`u32 port` - 目标端口。如果非零，则将消息发送到给定的端口，该端口应设置为原始发送者。

`u32 __group` - 目标组。如果`port`和`__group`都为零，则将通过所有注册的连接器用户搜索适当的组，并将消息传递到与`msg`中相同`ID`的用户创建的组。如果`__group`不为零，则将消息传递到指定的组。

`int gfp_mask` - GFP 掩码。

注意：在注册新的回调用户时，连接器核心将为用户分配等于其`id.idx`的 netlink 组。

# Protocol description.

当前框架提供了一个具有固定头的传输层。使用这种头的推荐协议如下：

`msg->seq`和`msg->ack`用于确定消息的谱系。当有人发送消息时，他们使用本地唯一的序列和随机的确认号。序列号也可以复制到`nlmsghdr->nlmsg_seq`中。

每次发送消息时，序列号都会递增。

如果您期望收到对消息的回复，则接收消息中的序列号必须与原始消息中的相同，并且确认号必须是相同的+1。

如果我们收到一条消息，其序列号不等于我们期望的序列号，则它是一条新消息。如果我们收到一条消息，其序列号与我们期望的相同，但其确认号不等于原始消息中的序列号+1，则它是一条新消息。

显然，协议头包含上述`id`。

连接器允许以以下形式进行事件通知：内核驱动程序或用户空间进程可以要求连接器在选择的`id`打开或关闭（注册或注销其回调）时通知它。这是通过向连接器驱动程序发送特殊命令来完成的（它也使用`id={-1, -1}`进行注册）。

在`cn_test.c`模块中可以找到此用法的示例，该模块使用连接器请求通知并发送消息。

# Reliability.

Netlink 本身不是一个可靠的协议。这意味着由于内存压力或进程接收队列溢出，消息可能会丢失，因此调用者被警告必须做好准备。这就是为什么`struct cn_msg` [主要的连接器消息头]包含`u32 seq`和`u32 ack`字段的原因。

# Userspace usage.

2.6.14 有一个新的 netlink 套接字实现，默认情况下不允许人们向除 1 以外的 netlink 组发送数据。因此，如果您希望使用 netlink 套接字（例如使用连接器）并使用不同的组号，用户空间应用程序必须首先订阅该组。可以通过以下伪代码实现：

`s = socket(PF_NETLINK, SOCK_DGRAM, NETLINK_CONNECTOR);`

`l_local.nl_family = AF_NETLINK;`

`l_local.nl_groups = 12345;`

`l_local.nl_pid = 0;`

如果`bind(s, (struct sockaddr *)&l_local, sizeof(struct sockaddr_nl)) == -1) {`

`perror("bind");`

`close(s);`

`return -1;`

`}`

`{`

`int on = l_local.nl_groups;`

`setsockopt(s, 270, 1, &on, sizeof(on));`

`}`

其中上面的 270 是`SOL_NETLINK`，1 是`NETLINK_ADD_MEMBERSHIP`套接字选项。要取消多播订阅，应该使用定义为 0 的`NETLINK_DROP_MEMBERSHIP`参数调用上述套接字选项。

2.6.14 netlink 代码仅允许选择小于或等于在`netlink_kernel_create()`时使用的最大组号的组。对于连接器，它是`CN_NETLINK_USERS + 0xf`，因此如果您想使用组号 12345，必须将`CN_NETLINK_USERS`递增到该数字。额外的 0xf 个数字被分配给非内核用户使用。

由于此限制，组 0xffffffff 现在不起作用，因此不能使用添加/删除连接器的组通知，但据我所知，只有`cn_test.c`测试模块使用了它。

在 netlink 领域仍在进行一些工作，因此在 2.6.15 时间范围内可能会发生变化，如果发生这种情况，将为该内核更新文档。