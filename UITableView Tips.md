

### 目录

[TOC]

### 滑动停止的判断

滑动停止下最常用的方法是

```swift

```

有一个系统的问题，当滑动过程中突然进入后台，或者离开 tableview 所在的页面，滑动停止的回调**不会**执行。

### `cell` 内部触发网络请求的正确姿势

以点赞为例,`cell` 会复用，点击点赞按钮后回调给 `vc`，然后`vc` 或 `vm`发送网络请求的时候发送完毕后，要刷新`list` 中的`model`和 `cell` ，由于网络请求是一个异步操作,可以发送请求的时候将 model 和 cell 在闭包中捕获，这样的好处是 

1. 始终能够找到对应的 `model`，比通过` indexPath` 的方式找到` model` 靠谱。也就是说 `model` 位置变了，也不妨碍找到正确的 `model`.
2. `cell` 角色会变化,只要比较一下 `cell.model`和 `model`是不是同一个，若是同一个，证明 `cell`已经被复用，当前被`cell`就不该刷新

#### 偷懒方式：

网络请求直接在 cell 中请求，请求完成后刷新 `cell.model`,会同步修改 `datalist`中的 `model`,注意也要保证 `cell.model`和 `model`是同一个

### `MJFooter` 在上滑刷新中的细节

1. 初始化开始要隐藏` isHidden = true`
2. 回调后，有数据显示，无数据隐藏 `isHidden = dataList.isEmpty`
3. 回调后，有更多数据 调用 `endRefreshing` ,无更多数据调用`endRerfresingWithNoData`
4. 是否需要标记位，标记正在运行中？ 不需要