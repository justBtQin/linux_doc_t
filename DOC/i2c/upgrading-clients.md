以下是为您转换成的 Markdown 格式：

# 升级 I2C 驱动程序到新的 2.6 驱动模型

**作者**：Ben Dooks <ben-linux@fluff.org>

**介绍**

本指南概述了如何将现有的 Linux 2.6 客户端驱动程序从旧的绑定方法更改为新的绑定方法。

## 示例旧风格驱动程序

```c
struct example_state {
    struct i2c_client  client;
   ....
};

static struct i2c_driver example_driver;

static unsigned short ignore[] = { I2C_CLIENT_END };
static unsigned short normal_addr[] = { OUR_ADDR, I2C_CLIENT_END };

I2C_CLIENT_INSMOD;

static int example_attach(struct i2c_adapter *adap, int addr, int kind)
{
    struct example_state *state;
    struct device *dev = &adap->dev;  /* 用于 dev_ 报告 */
    int ret;

    state = kzalloc(sizeof(struct example_state), GFP_KERNEL);
    if (state == NULL) {
        dev_err(dev, "failed to create our state\n");
        return -ENOMEM;
    }

    example->client.addr    = addr;
    example->client.flags   = 0;
    example->client.adapter = adap;

    i2c_set_clientdata(&state->i2c_client, state);
    strlcpy(client->i2c_client.name, "example", I2C_NAME_SIZE);

    ret = i2c_attach_client(&state->i2c_client);
    if (ret < 0) {
        dev_err(dev, "failed to attach client\n");
        kfree(state);
        return ret;
    }

    dev = &state->i2c_client.dev;

    /* 其余的初始化在此处 */

    dev_info(dev, "example client created\n");

    return 0;
}

static int example_detach(struct i2c_client *client)
{
    struct example_state *state = i2c_get_clientdata(client);

    i2c_detach_client(client);
    kfree(state);
    return 0;
}

static int example_attach_adapter(struct i2c_adapter *adap)
{
    return i2c_probe(adap, &addr_data, example_attach);
}

static struct i2c_driver example_driver = {
   .driver    = {
       .owner    = THIS_MODULE,
       .name    = "example",
       .pm      = &example_pm_ops,
    },
   .attach_adapter = example_attach_adapter,
   .detach_client  = example_detach,
};
```

## 更新客户端

新的绑定模型将根据注册总线的代码提供的支持设备及其相关地址列表进行检查。这意味着可以删除驱动程序的 `.attach_adapter` 和 `.detach_client` 方法，以及 `addr_data`，如下所示：

```c
- static struct i2c_driver example_driver;

- static unsigned short ignore[] = { I2C_CLIENT_END };
- static unsigned short normal_addr[] = { OUR_ADDR, I2C_CLIENT_END };

- I2C_CLIENT_INSMOD;

- static int example_attach_adapter(struct i2c_adapter *adap)
- {
-    return i2c_probe(adap, &addr_data, example_attach);
- }

 static struct i2c_driver example_driver = {
-   .attach_adapter = example_attach_adapter,
-   .detach_client  = example_detach,
 }

添加 `probe` 和 `remove` 方法到 `i2c_driver`，如下所示：

```c
 static struct i2c_driver example_driver = {
+   .probe      = example_probe,
+   .remove     = example_remove,
 }

将 `example_attach` 方法更改为接受新的参数，其中包括它将使用的 `i2c_client`：

```c
- static int example_attach(struct i2c_adapter *adap, int addr, int kind)
+ static int example_probe(struct i2c_client *client,
+                          const struct i2c_device_id *id)

将 `example_attach` 重命名为 `example_probe`，以与 `i2c_driver` 条目名称对齐。现在，探测例程的其余部分需要更改，因为 `i2c_client` 已经设置好可供使用。

```c
必要的客户端字段在调用探测函数之前已经设置好了，因此可以删除以下客户端设置：

```c
-    example->client.addr    = addr;
-    example->client.flags   = 0;
-    example->client.adapter = adap;
-
-    strlcpy(client->i2c_client.name, "example", I2C_NAME_SIZE);

`i2c_set_clientdata` 现在变为：

```c
-    i2c_set_clientdata(&state->client, state);
+    i2c_set_clientdata(client, state);

不再需要调用 `i2c_attach_client`，如果探测例程成功退出，驱动程序将由内核自动附加。更改探测例程如下：

```c
-    ret = i2c_attach_client(&state->i2c_client);
-    if (ret < 0) {
-        dev_err(dev, "failed to attach client\n");
-        kfree(state);
-        return ret;
-    }

删除 `struct example_state` 中 `struct i2c_client` 的存储，因为在我们的 `example_probe` 中提供了 `i2c_client`。相反，我们存储一个指向它的指针，以备需要时使用。

```c
struct example_state {
-    struct i2c_client  client;
+    struct i2c_client  *client;

新的 `i2c` 客户端如下：

```c
-    struct device *dev = &adap->dev;  /* 用于 dev_ 报告 */
+    struct device *dev = &i2c_client->dev;  /* 用于 dev_ 报告 */

并且删除客户端附加后的更改，因为驱动程序不再需要向内核注册新的客户端结构：

```c
-    dev = &state->i2c_client.dev;

在探测例程中，确保新的状态存储了客户端：

```c
static int example_probe(struct i2c_client *i2c_client,
                         const struct i2c_device_id *id)
{
    struct example_state *state;
    struct device *dev = &i2c_client->dev;

    state = kzalloc(sizeof(struct example_state), GFP_KERNEL);
    if (state == NULL) {
        dev_err(dev, "failed to create our state\n");
        return -ENOMEM;
    }

+    state->client = i2c_client;

更新 `detach` 方法，将名称更改为 `_remove` 并删除 `i2c_detach_client` 调用。您还可以删除 `ret` 变量，因为它对于任何核心函数都不再需要。

```c
- static int example_detach(struct i2c_client *client)
+ static int example_remove(struct i2c_client *client)
{
    struct example_state *state = i2c_get_clientdata(client);

-    i2c_detach_client(client);

最后，确保我们为 `i2c-core` 和其他实用程序拥有正确的 `ID` 表：

```c
+ struct i2c_device_id example_idtable[] = {
+       { "example", 0 },
+       { }
+};
+
+MODULE_DEVICE_TABLE(i2c, example_idtable);

static struct i2c_driver example_driver = {
   .driver    = {
       .owner    = THIS_MODULE,
       .name    = "example",
    },
+   .id_table    = example_ids,

我们的驱动程序现在应该如下所示：

```c
struct example_state {
    struct i2c_client  *client;
   ....
};

static int example_probe(struct i2c_client *client,
                         const struct i2c_device_id *id)
{
    struct example_state *state;
    struct device *dev = &client->dev;

    state = kzalloc(sizeof(struct example_state), GFP_KERNEL);
    if (state == NULL) {
        dev_err(dev, "failed to create our state\n");
        return -ENOMEM;
    }

    state->client = client;
    i2c_set_clientdata(client, state);

    /* 其余的初始化在此处 */

    dev_info(dev, "example client created\n");

    return 0;
}

static int example_remove(struct i2c_client *client)
{
    struct example_state *state = i2c_get_clientdata(client);

    kfree(state);
    return 0;
}

static struct i2c_device_id example_idtable[] = {
    { "example", 0 },
    { }
};

MODULE_DEVICE_TABLE(i2c, example_idtable);

static struct i2c_driver example_driver = {
   .driver    = {
       .owner    = THIS_MODULE,
       .name    = "example",
       .pm      = &example_pm_ops,
    },
   .id_table    = example_idtable,
   .probe      = example_probe,
   .remove     = example_remove,
};