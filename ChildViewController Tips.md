### 0.目录

[TOC]

### 1.子视图控制器的 view 不一定是父视图控制器的 view,可以是子孙 view

`containerView`可以是控制器的 `self.view`,也可以是`self.view`的任意一个子孙视图

` containerView.addSubview(child.view)`

### 2.添加子视图控制器后，子视图控制器的生命周期会被如何调用

常见的生命周期函数有下面几个

`viewDidLoad`

`viewWillAppear`

`viewDidAppear`

`viewWillDisappear`

`viewDidDisappear`

#### 谁来触发生命周期方法

**情形 1 **只添加` containerView.addSubview(child.view)` 会触发 `viewDidLoad`和`viewWillAppear`和`viewDidAppear`

但是不会触发 `viewWillDisappear`和`viewDidDisappear`

**情形 2** 只添加 `addChild(childController: UIViewController)` 



### 3.如何自定义视图中内部添加一个子视图控制器

```swift
open override func willMove(toSuperview newSuperview: UIView?) {
  super.willMove(toSuperview: newSuperview)
  var next: UIResponder? = newSuperview
  while next != nil {
      if let vc = next as? UIViewController{
          vc.addChild(containerVC)
          break
      }
      next = next?.next
  }
}
```

