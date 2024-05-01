[TOC]

## 分类

动画大致分为两种

- UIView 动画
- 核心动画

前者是后者的高级封装

## UIView动画

，`UIView.animate`的一簇api 即可

注意可以设置**循环**，**反向**

缺点是：1.在进入后台 2.视图所在的控制器 disappear 后动画自动停止

优点：方便多个视图（或子视图）同时执行动画。动画组只能保证同一个 `layer` 同时执行动画，不能实现多个 `layer`同时执行动画

## 核心动画

### 核心动画相比 UIView 动画的优势

1.可以保证在**进入后台**或所在的视图控制器执行`disapper`后不自动停止，只需要

```swift
animation.isRemovedOnCompletion =  false
```

2.可以给动画加一个 `key` 这样方便判断是否已经存在,防止重复添加,还可以根据 key 去移除

 `redView.layer.add(animation, forKey: "animKey")`

3.平移动画中对于 layer 没有 `frame.origin.x`或`frame.origin.y`,但是有`position`.

对于中点 y，平移，指定这样的 keyPath 即可。

` let animation = CABasicAnimation(keyPath: "position.y")`

### 核心动画的分类

<img align="left" src="https://tbfungeek.github.io/2019/08/08/iOS-%E5%8A%A8%E7%94%BB%E6%80%BB%E7%BB%93/00001.png" alt="img" style="zoom:25%" />

常用的其实下面三种

- `CABasicAnimation`
- `CAKeyframeAnimation`
- `CAAnimationGroup`

### 简单动画`CABasicAnimation`

简单动画可以指定动画的起点和终点。这个基本上实现效果和UIView动画一样,但是关注多段顺序执行就比较麻烦

#### 简单的例子

`position`代表中心点，没有 `origin` 这个 `keypath`.所有的 `keypath`见参考[Core Animation(核心动画) 中KeyPath的取值](https://www.jianshu.com/p/71c880498d7a)

```swift
 let animation = CABasicAnimation(keyPath: "position.y")
 animation.fromValue = 88 + 22
 animation.toValue = 288 + 22
 animation.duration = 2.0
 animation.repeatCount = 1
 animation.timingFunction = CAMediaTimingFunction(name: .easeOut)
 redView.layer.add(animation, forKey: "animKey")
```

#### **平移**

- 两个方向平移:`position`

- 水平方向平移:`position.x`

- 垂直方向平移:`position.y`

#### 放缩

- 两个个方向放缩：`transform.scale`
- 水平方向放缩：`transform.scale.x`
- 垂直方向平移:  `transform.scale.y`

#### 卷轴

卷轴动画要用视图的另外一个属性`maskView`,只要为这个 maskview 设定非透明背景色，无论什么颜色，都可以将被`maskview`盖住的部分隐藏

通过对 `maskview`的 `layer` 做动画就可以实现卷轴动画

### 关键帧动画`CAKeyframeAnimation`

要进行**多段动画顺序执行**,譬如从上到下，然后从左到右顺序执行。简单动画只能起点+终点

### 动画组CAAnimationGroup

多段动画可以解决同一个` layer` 多个动画同时执行的问题

⚠️ 动画组支持的是同一个 `layer`下的几个动画的同时进行，不支持多个` layer` 同时执行

### 核心动画结束后闪回原来位置怎么解决

```
// 协议
anim.delegate = self;

#pragma mark - 核心动画的代理方法
// 核心动画开始
- (void)animationDidStart:(CAAnimation *)anim {

    NSLog(@"核心动画开始");

}

// 核心动画结束
- (void)animationDidStop:(CAAnimation *)anim finished:(BOOL)flag {

//    NSLog(@"核心动画结束!");
    // 在核心动画结束后,将控件的真实位置挪过来!
    _btn.center = CGPointMake(150, 500);

    // 将layer中的动画移除掉,保证不会闪了!
    [_btn.layer removeAllAnimations];
}
```

### 参考

[Core Animation(核心动画) 中KeyPath的取值](https://www.jianshu.com/p/71c880498d7a)















