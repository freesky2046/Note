#### 方案选型

- 继承 `UIProgressView`
- 继承 `UISlider`
- 自定义`UIView` +` pan` 手势

`UIProgressView`没有手势，无法实现拖动，`UISlider`内部有手势可以实现拖动，但是只能在圆形按钮上进行拖动，需求希望在进度条的任意位置可以拖动。因此自定义` View` +` pan` 手势比较好的实现需求

#### 
