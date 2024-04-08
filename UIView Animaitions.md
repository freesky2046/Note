[TOC]



## 分类

动画大致分为两种

- UIView 动画
- 核心动画

前者是后者的高级封装

## UIView动画

没什么特别的东西，`UIView.animate`的一簇api 即可

注意可以设置**循环**，**反向**

缺点是：1.在进入后台 2.视图所在的控制器 disappear 后动画自动停止

## 核心动画

1.可以保证在进入后台或所在的视图控制器执行`disapper`后不自动停止，只需要

```swift
animation.isRemovedOnCompletion =  false
```

2.可以给动画加一个 `key` 这样方便判断是否已经存在,防止重复添加,还可以根据 key 去移除

 `redView.layer.add(animation, forKey: "animKey")`

3.平移动画中对于 layer 没有 `frame.origin.x`或`frame.origin.y`,但是有`position`.

对于中点 y，平移，指定这样的 keyPath 即可。

` let animation = CABasicAnimation(keyPath: "position.y")`

