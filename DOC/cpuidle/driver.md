<markdown_document>

在内核中支持多个 CPU 空闲级别

cpuidle 驱动程序

cpuidle 驱动程序接入 cpuidle 基础设施，并处理与 CPU 空闲状态相关的架构/平台依赖部分。驱动程序提供平台空闲状态检测能力，并且还具有支持实际进入/退出 CPU 空闲状态的机制。

cpuidle 驱动程序为每个 CPU 设备初始化 cpuidle_device 结构，并使用 cpuidle_register_device 向 cpuidle 注册。

如果所有空闲状态都相同，则可以使用包装函数 cpuidle_register。

它还可以通过使用 cpuidle_pause_and_lock、cpuidle_disable_device 和 cpuidle_enable_device、cpuidle_resume_and_unlock 来支持动态变化（如电池<->交流电源）。

接口：

    extern int cpuidle_register(struct cpuidle_driver *drv,
    
    const struct cpumask *const coupled_cpus);
    
    extern int cpuidle_unregister(struct cpuidle_driver *drv);
    
    extern int cpuidle_register_driver(struct cpuidle_driver *drv);
    
    extern void cpuidle_unregister_driver(struct cpuidle_driver *drv);
    
    extern int cpuidle_register_device(struct cpuidle_device *dev);
    
    extern void cpuidle_unregister_device(struct cpuidle_device *dev);
    
    extern void cpuidle_pause_and_lock(void);
    
    extern void cpuidle_resume_and_unlock(void);
    
    extern int cpuidle_enable_device(struct cpuidle_device *dev);
    
    extern void cpuidle_disable_device(struct cpuidle_device *dev);

</markdown_document>