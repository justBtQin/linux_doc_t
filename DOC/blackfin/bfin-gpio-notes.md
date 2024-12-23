```
/*
 * 文件：Documentation/blackfin/bfin-gpio-notes.txt
 * 基于：
 * 作者：
 *
 * 创建：$Id: bfin-gpio-note.txt 2008-11-24 16:42 grafyang $
 * 描述：此文件包含在开发/使用 bfin-gpio 中的注意事项。
 *
 * 修订：
 *
 * 修改：
 *               版权 2004-2008 Analog Devices Inc.
 * 错误：在 http://blackfin.uclinux.org/ 上提交错误
 *
 */
```

# 1. Blackfin GPIO 介绍

Blackfin 上有许多 GPIO 引脚。这些引脚中的大多数被复用为多种功能。它们可以配置为外设，或仅作为 GPIO，配置为输入并启用中断，或输出。

有关详细信息，请参阅“arch/blackfin/kernel/bfin_gpio.c”或相关的 HRM。

# 2. 避免资源冲突

遵循以下功能组来避免资源冲突：

- 将引脚用作外设，

int peripheral_request(unsigned short per, const char *label);

int peripheral_request_list(const unsigned short per[], const char *label);

void peripheral_free(unsigned short per);

void peripheral_free_list(const unsigned short per[]);

- 将引脚用作 GPIO，

int bfin_gpio_request(unsigned gpio, const char *label);

void bfin_gpio_free(unsigned gpio);

- 将引脚用作 GPIO 中断，

int bfin_gpio_irq_request(unsigned gpio, const char *label);

void bfin_gpio_irq_free(unsigned gpio);

请求函数将记录特定引脚的功能状态，释放函数将清除其功能状态。一旦引脚被请求，在之前的调用者释放它之前，不能再次请求，否则内核将转储堆栈，并且请求函数将失败。这些函数被其他函数包装，大多数用户无需关心。

# 3. 但有一些例外情况

- 内核允许相同的 GPIO 既被请求为 GPIO 又被请求为 GPIO 中断。
	
一些驱动程序，如 gpio-keys，需要这种行为。内核仅输出警告消息，例如，

bfin-gpio: GPIO 24 is already reserved by gpio-keys: BTN0, and you are

configuring it as IRQ!
	
注意：考虑这种情况，如果有两个驱动程序需要相同的 GPIO，一个将其用作 GPIO，另一个将其用作 GPIO 中断。这将真正导致资源冲突。因此，如果有任何异常的驱动程序行为，请检查 bfin-gpio 警告消息。

- 内核允许相同的 GPIO 由同一个驱动程序两次请求。
