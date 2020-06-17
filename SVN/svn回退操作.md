# svn回退操作
假设我们有许多个版本，版本号分别是1-10
如果我们在7这里选择revert to this version，那么svn会回退到7版本状态，7之后的8,9,10的操作都会被消除
如果在7选择revert changes from this version，那么7版本的修改将会被消除，但8,9,10的操作会被保留
如果同时选择7,8 revert changes from this version那么7和8两个版本的所做的修改都会被消除
如果在7选择update item to revison:,本地更新到这个svn版本，作为只读模式版本无法提交所作的更改，一般作查看历史版本用，无其它用途！