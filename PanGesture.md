## PanGesture 目录

[TOC]

## pan作为连续手势识别器的状态转换管理



<img src="https://docs-assets.developer.apple.com/published/7c21d852b9/86fa3739-c97b-44cc-b51d-0215697660b7.png" style="zoom:50%;" />

默认状态下手势处于

手势状态变化可以分为几个个阶段

- 当触碰事件和手势匹配的时候，手势状态从`possible`转为`begin`
- 当触碰事件和手势不匹配的时候，手势从 `possible`转为 `failed`
- 拖动过程中，手势状态始终是`changed`
  - 若正常情况下手离开屏幕，代表手势识别完成从`changed`转变为`recognized`(即`end`)，表示手势正常完成
  - 若异常情况下如 突然切换页面，突然进入后台，手势会从 `changed`转变为 `canceled`,表示手势异常结束



## Pan手势回调方法细节

手势的回调方法和按钮类似 添加一个 `target-action`绑定方法即可

```swift
 let panGesture = UIPanGestureRecognizer(target: self, action: #selector(panGestureAction(_:)))
```

手势的触发的回调方法有若干次回调。起点总是`begin`,终点是`end`或`canceled`,`failed`和`possible`不会出现在回调方法中

拖动手指，第一次回调，手势转为`begin`状态的时候，即开始识别了手势

继续拖动手指，滑动过程中，多次回调，每次回调的状态都是`changed`。

正常用户抬起手，有一个结束的回调，状态`end`

拖动过程中，进入后台或者有系统消息或者异常终止，会有异常结束的回调，即`cancelled`

```swift
@objc
func panGestureAction(_ pan: UIPanGestureRecognizer) {
    switch pan.state {
    case .began:
        print("began")
    case .changed:
        print("changed")
    case .ended:
        print("end")
    case .cancelled:
        print("cancelled")
    case .possible, .failed:
        break
    @unknown default:
        break
    }
}
```

## Pan手势的一些重要方法

在手势触发过程中，有时候会关注手势触碰在哪里。可以使用下面的方法

```swift
pan.location(in: pan.view)
```

传入视图，代表以<u>**该视图为坐标系下**</u>触碰点的坐标，手势有一个属性可以找到当前附着的视图是什么，这个方法是**所有手势**都有的方法。

对于 **pan 手势**还有下面几个方法：



## Pan 手势常用场景

### 拖动控件

### 播放进度条

见 [ProgressView](ProgressView.md)



## 参考

> https://juejin.cn/post/6905914367171100680
>
> https://www.jianshu.com/p/77139b374313
>
> https://stackoverflow.com/questions/4825199/gesture-recognizer-and-button-actions