一个 DT 变更集是一种方法，它允许以这样的方式在活动树中应用更改：要么完整的更改集将被应用，要么都不应用。如果在应用变更集的过程中中途发生错误，那么树将回滚到之前的状态。变更集在应用后也可以被删除。

当应用变更集时，所有的更改都会在发送 `OF_RECONFIG` 通知器之前一次性应用到树中。这是为了让接收方在收到通知器时看到树的完整且一致的状态。

变更集的顺序如下：

1. `of_changeset_init()` - 初始化一个变更集

2. 一系列 DT 树更改调用，如 `of_changeset_attach_node()`、`of_changeset_detach_node()`、`of_changeset_add_property()`、`of_changeset_remove_property`、`of_changeset_update_property()` 来准备一组更改。此时不会对活动树进行更改。所有的更改操作都记录在 `of_changeset` 的“entries”列表中。

3. `mutex_lock(of_mutex)` - 开始一个变更集；全局的 `of_mutex` 确保一次只能有一个编辑者。

4. `of_changeset_apply()` - 将更改应用到树中。要么整个变更集被应用，要么如果有错误，树将恢复到之前的状态。

5. `mutex_unlock(of_mutex)` - 所有操作完成，释放互斥锁

如果需要删除一个已成功应用的变更集，可以按照以下顺序进行：

1. `mutex_lock(of_mutex)`

2. `of_changeset_revert()`

3. `mutex_unlock(of_mutex)`