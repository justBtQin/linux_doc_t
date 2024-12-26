<markdown_document>

# Device Whitelist Controller

## 1. 描述：

实现一个 cgroup 来跟踪和强制对设备文件的打开和 mknod 限制。一个设备 cgroup 将一个设备访问白名单与每个 cgroup 相关联。一个白名单条目有 4 个字段。'type'是(a 表示所有)、c(字符)或 b(块)。'all'表示它适用于所有类型以及所有主设备号和次设备号。主设备号和次设备号可以是一个整数或*表示所有。访问是 r(读)、w(写)和 m(mknod)的组合。

根设备 cgroup 以对'all'的 rwm 开始。子设备 cgroup 获得父设备的一个副本。管理员然后可以从白名单中删除设备或添加新条目。子 cgroup 永远不会收到其父设备拒绝的设备访问。

## 2. 用户界面

使用 devices.allow 添加一个条目，使用 devices.deny 删除一个条目。例如：

echo 'c 1:3 mr' > /sys/fs/cgroup/1/devices.allow

允许 cgroup 1 读取和 mknod通常称为/dev/null 的设备。执行：

echo a > /sys/fs/cgroup/1/devices.deny

将删除默认的'a *:* rwm'条目。执行：

echo a > /sys/fs/cgroup/1/devices.allow

将向白名单中添加'a *:* rwm'条目。

## 3. 安全性

任何任务都可以在 cgroup 之间移动自己。这显然是不够的，但当人们在这方面有一些经验时，我们可以决定充分限制移动的最佳方式。我们可能只需要要求 CAP_SYS_ADMIN，它至少是与 CAP_MKNOD 不同的一位。我们可能只希望拒绝移动到不是当前 cgroup 后代的 cgroup。或者我们可能希望使用 CAP_MAC_ADMIN，因为我们实际上是在尝试锁定根。

修改白名单或移动另一个任务到新 cgroup 需要 CAP_SYS_ADMIN。(同样，我们可能希望改变这一点)。

一个 cgroup 不能被授予比其父 cgroup 更多的权限。

## 4. 层次结构

设备 cgroup 通过确保 cgroup 永远不会有比其父 cgroup 更多的访问权限来维护层次结构。每次向 cgroup 的 devices.deny 文件写入一个条目时，其所有子 cgroup 将从其白名单中删除该条目，并重新评估所有本地设置的白名单条目。如果本地设置的白名单条目之一将提供比 cgroup 的父 cgroup 更多的访问权限，它将从白名单中删除。

示例：

      A

     / \

        B

    组        行为        异常

    A            允许        "b 8:* rwm"，"c 116:1 rw"

    B            拒绝        "c 1:3 rwm"，"c 116:2 rwm"，"b 3:* rwm"

如果在组 A 中拒绝一个设备：

	# echo "c 116:* r" > A/devices.deny

它将向下传播，并在重新验证 B 的条目后，白名单条目"c 116:2 rwm"将被删除：

    组        白名单条目                        被拒绝的设备

    A            所有                                "b 8:* rwm"，"c 116:* rw"

    B            "c 1:3 rwm"，"b 3:* rwm"             所有其他

在父级的异常更改且本地异常不再被允许的情况下，它们将被删除。

注意，新的白名单条目不会被传播：

      A

     / \

        B

    组        白名单条目                        被拒绝的设备

    A            "c 1:3 rwm"，"c 1:5 r"               所有其他

    B            "c 1:3 rwm"，"c 1:5 r"               所有其他

当添加"c *:3 rwm"时：

	# echo "c *:3 rwm" >A/devices.allow

结果：

    组        白名单条目                        被拒绝的设备

    A            "c *:3 rwm"，"c 1:5 r"               所有其他

    B            "c 1:3 rwm"，"c 1:5 r"               所有其他

但现在可以向 B 添加新条目：

	# echo "c 2:3 rwm" >B/devices.allow

	# echo "c 50:3 r" >B/devices.allow

甚至

	# echo "c *:3 rwm" >B/devices.allow

一旦设备 cgroup 有子 cgroup，就不可能通过向 devices.allow 或 devices.deny 写入'a'来允许或拒绝所有。

### 4.1 层次结构（内部实现）

设备 cgroup 在内部使用行为（ALLOW，DENY）和异常列表来实现。内部状态使用相同的用户界面进行控制，以保持与以前的仅白名单实现的兼容性。将减少对设备访问的异常的删除或添加将在层次结构中向下传播。对于每个传播的异常，将根据当前父级的访问规则重新评估有效规则。

</markdown_document>