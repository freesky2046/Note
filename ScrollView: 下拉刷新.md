### 无感知上拉加载



> 很多时候我们上拉刷新需要提前加载新数据，这时候利用MJRefreshAutoFooter的属性triggerAutomaticallyRefreshPercent就可以实现，该属性triggerAutomaticallyRefreshPercent默认值为1，然后改成0的话划到底部就会自动刷新，改成-1的话，在快划到底部44px的时候就会自动刷新。

MJRefresh？使用MJRefreshAutoFooter，这个简单，我直接把基类的footer给替换掉就可以了

```swift
/// 设置尾部刷新控件,更新为无感知加载更多
let footer = MJRefreshAutoFooter()
footer.triggerAutomaticallyRefreshPercent = -1
tableView.mj_footer = footer

```

