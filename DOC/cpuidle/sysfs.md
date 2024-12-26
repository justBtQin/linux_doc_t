<markdown_document>

支持内核中的多个 CPU 空闲级别

cpuidle sysfs

系统全局的 cpuidle 相关信息和可调参数位于 /sys/devices/system/cpu/cpuidle 下。

此目录中的当前接口具有自解释的名称：

* current_driver

* current_governor_ro

使用 cpuidle_sysfs_switch 引导选项（用于开发者测试）时，将显示以下对象，而不是上述两个对象：* current_driver

* available_governors

* current_governor 在这种情况下，用户可以通过写入 current_governor 在运行时切换调节器。

每个逻辑 CPU 的特定 cpuidle 信息位于 /sys/devices/system/cpu/cpuX/cpuidle 下，其中 X 是每个在线的 cpu。

--------------------------------------------------------------------------------

```
# ls -lR /sys/devices/system/cpu/cpu0/cpuidle/
/sys/devices/system/cpu/cpu0/cpuidle/：
总计 0
drwxr-xr-x 2 root root 0 Feb  8 10:42 state0
drwxr-xr-x 2 root root 0 Feb  8 10:42 state1
drwxr-xr-x 2 root root 0 Feb  8 10:42 state2
drwxr-xr-x 2 root root 0 Feb  8 10:42 state3

/sys/devices/system/cpu/cpu0/cpuidle/state0：
总计 0
-r--r--r-- 1 root root 4096 Feb  8 10:42 desc
-rw-r--r-- 1 root root 4096 Feb  8 10:42 disable
-r--r--r-- 1 root root 4096 Feb  8 10:42 latency
-r--r--r-- 1 root root 4096 Feb  8 10:42 name
-r--r--r-- 1 root root 4096 Feb  8 10:42 power
-r--r--r-- 1 root root 4096 Feb  8 10:42 time
-r--r--r-- 1 root root 4096 Feb  8 10:42 usage

/sys/devices/system/cpu/cpu0/cpuidle/state1：
总计 0
-r--r--r-- 1 root root 4096 Feb  8 10:42 desc
-rw-r--r-- 1 root root 4096 Feb  8 10:42 disable
-r--r--r-- 1 root root 4096 Feb  8 10:42 latency
-r--r--r-- 1 root root 4096 Feb  8 10:42 name
-r--r--r-- 1 root root 4096 Feb  8 10:42 power
-r--r--r-- 1 root root 4096 Feb  8 10:42 time
-r--r--r-- 1 root root 4096 Feb  8 10:42 usage

/sys/devices/system/cpu/cpu0/cpuidle/state2：
总计 0
-r--r--r-- 1 root root 4096 Feb  8 10:42 desc
-rw-r--r-- 1 root root 4096 Feb  8 10:42 disable
-r--r--r-- 1 root root 4096 Feb  8 10:42 latency
-r--r--r-- 1 root root 4096 Feb  8 10:42 name
-r--r--r-- 1 root root 4096 Feb  8 10:42 power
-r--r--r-- 1 root root 4096 Feb  8 10:42 time
-r--r--r-- 1 root root 4096 Feb  8 10:42 usage

/sys/devices/system/cpu/cpu0/cpuidle/state3：
总计 0
-r--r--r-- 1 root root 4096 Feb  8 10:42 desc
-rw-r--r-- 1 root root 4096 Feb  8 10:42 disable
-r--r--r-- 1 root root 4096 Feb  8 10:42 latency
-r--r--r-- 1 root root 4096 Feb  8 10:42 name
-r--r--r-- 1 root root 4096 Feb  8 10:42 power
-r--r--r-- 1 root root 4096 Feb  8 10:42 time
-r--r--r-- 1 root root 4096 Feb  8 10:42 usage
```

--------------------------------------------------------------------------------

* desc：关于空闲状态的简短描述（字符串）

* disable：禁用此空闲状态的选项（布尔值）-> 见下面的注意事项

* latency：退出此空闲状态的延迟（以微秒为单位）

* name：空闲状态的名称（字符串）

* power：处于此空闲状态时消耗的功率（以毫瓦为单位）

* time：在此空闲状态下花费的总时间（以微秒为单位）

* usage：进入此状态的次数（计数）

注意：

disable 变量的行为和效果取决于特定调节器的实现。例如，在阶梯调节器中，它不是一致的，即如果禁用一个较浅的状态，那么所有更深的状态也将被禁用，但 disable 变量不会反映这一点。同样，如果启用一个较深的状态，但较浅的状态仍然被禁用，那么这将没有效果。

</markdown_document>