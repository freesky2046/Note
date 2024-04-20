#### `contentOffset`如何理解

![图片](https://i.stack.imgur.com/oCKJr.png#pic_left)

这里有两个矩形

- 可视区域

- 全部区域

滚动是改变可见区域在整个区域中的起点位置。

所有的视图都有一个由view.bounds矩形表示的可见区域。UIScrollView类是一个具有独特属性的视图：它有一个比其边界更大的矩形，称为"content view"。因此，在滚动视图中：

可见区域是scrollView.bounds；
总区域被称为content view，其大小为scrollView.contentSize；

**contentOffset是scrollView.bounds.origin的另一个名称**

```swift
var contentOffset: CGPoint { 
    get { return bounds.origin }
    set {
        var bounds = self.bounds
        bounds.origin = newValue
        self.bounds = bounds
    }   
}
```

当我们以编程方式更改contentOffset时，我们也在改变bounds.origin，这会导致不同区域的content view被渲染出来。如果我们使用`setContentOffset(pt, animated: true)`来对此更改进行动画处理，scrollView会呈现出类似用户用手指拖动滚动的效果



#### `contentInset`如何理解

![contentInset](https://developer.apple.com/library/archive/documentation/WindowsViews/Conceptual/UIScrollView_pg/Art/contentSize_contentInset.jpg)

- 不占用 contentsize 的空间
- 会让 bounds.orign 为负数