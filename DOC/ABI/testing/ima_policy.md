**What**：`security/ima/policy`

**Date**：2008 年 5 月

**Contact**：Mimi Zohar <zohar@us.ibm.com>

**Description**：

可信计算组（TCG）运行时完整性测量架构（IMA）维护了此系统运行时加载的可执行文件和其他敏感系统文件的哈希值列表。在运行时，可以基于 LSM 特定数据来约束策略。通过打开`ima/policy`文件，一次写入一条规则，然后关闭文件，将策略加载到`securityfs`文件中。在`ima/policy`文件关闭后，新策略生效。

IMA 评估（如果已配置）使用这些文件测量值进行本地测量评估。

规则格式：`动作 [条件...]`

动作：`measure` | `dont_measure` | ` appraise` | ` dont_appraise` | ` audit`

条件：`base` | ` lsm [选项]`

`base`：`[[func=] [mask=] [fsmagic=] [fsuuid=] [uid=] [euid=] [fowner=]]`

`lsm`：`[[subj_user=] [subj_role=] [subj_type=] [obj_user=] [obj_role=] [obj_type=]]`

选项：`[[appraise_type=]] [permit_directio]`

`base`：`func:= [BPRM_CHECK][MMAP_CHECK][FILE_CHECK][MODULE_CHECK] [FIRMWARE_CHECK]`

`mask:= [[^]MAY_READ] [[^]MAY_WRITE] [[^]MAY_APPEND] [[^]MAY_EXEC]`

`fsmagic:=` 十六进制值

`fsuuid:=` 文件系统 UUID（例如 8bcbe394-4f13-4144-be8e-5aa9ea2ce2f6）

`uid:=` 十进制值

`euid:=` 十进制值

`fowner:=` 十进制值

`lsm`：是 LSM 特定的

选项：`appraise_type:= [imasig]`

**默认策略**：

```
# PROC_SUPER_MAGIC
dont_measure fsmagic=0x9fa0
dont_appraise fsmagic=0x9fa0

# SYSFS_MAGIC
dont_measure fsmagic=0x62656572
dont_appraise fsmagic=0x62656572

# DEBUGFS_MAGIC
dont_measure fsmagic=0x64626720
dont_appraise fsmagic=0x64626720

# TMPFS_MAGIC
dont_measure fsmagic=0x01021994
dont_appraise fsmagic=0x01021994

# RAMFS_MAGIC
dont_appraise fsmagic=0x858458f6

# DEVPTS_SUPER_MAGIC
dont_measure fsmagic=0x1cd1
dont_appraise fsmagic=0x1cd1

# BINFMTFS_MAGIC
dont_measure fsmagic=0x42494e4d
dont_appraise fsmagic=0x42494e4d

# SECURITYFS_MAGIC
dont_measure fsmagic=0x73636673
dont_appraise fsmagic=0x73636673

# SELINUX_MAGIC
dont_measure fsmagic=0xf97cff8c
dont_appraise fsmagic=0xf97cff8c

# CGROUP_SUPER_MAGIC
dont_measure fsmagic=0x27e0eb
dont_appraise fsmagic=0x27e0eb

# NSFS_MAGIC
dont_measure fsmagic=0x6e736673
dont_appraise fsmagic=0x6e736673

measure func=BPRM_CHECK
measure func=FILE_MMAP mask=MAY_EXEC
measure func=FILE_CHECK mask=MAY_READ uid=0
measure func=MODULE_CHECK
measure func=FIRMWARE_CHECK
appraise fowner=0
```

默认策略测量`bprm_check`中的所有可执行文件、`file_mmap`中所有以可执行方式映射的文件，以及`do_filp_open`中由根用户打开以供读取的所有文件。默认评估策略评估所有由根用户拥有的文件。

**LSM 特定定义的示例**：

SELinux：

```
dont_measure obj_type=var_log_t
 dont_appraise obj_type=var_log_t
 dont_measure obj_type=auditd_log_t
 dont_appraise obj_type=auditd_log_t
 measure subj_user=system_u func=FILE_CHECK mask=MAY_READ
 measure subj_role=system_r func=FILE_CHECK mask=MAY_READ
```

Smack：

```
measure subj_user=_ func=FILE_CHECK mask=MAY_READ
```