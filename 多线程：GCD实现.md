## 一 队列类型

按照队列上的任务是否依次执行分为串行和并行

- 串行： **强制**多个任务一个一个执行。

- 并行：可以**多个线程，从而多个任务可以同时执行。有多个任务同时执行的能力，**但是并**不强制**

  

**内置的队列**

预置的串行队列，提交到这个队列的上的任务必须会在主线程上执行。因此叫它为**主队列**

```swift
let mainQueue = DispatchQueue.main
```

预置的全局并行队列，所谓全局是指app 无论在哪里访问这个队列都是同一个队列

```
DispatchQueue.global(qos: .userInteractive) 它用于执行与用户交互相关的任务，例如响应用户界面的操作、动画或其他需要即时响应的任务
DispatchQueue.global(qos: .userInitiated) 高优先级的全局队列 用户主动发起的任务
DispatchQueue.global(qos: .default) 默认优先级 大部分一般性任务，没有特别的优先级需求
DispatchQueue.global(qos: .utility) 执行一些不需要立即完成的任务，例如后台加载数据、文件操作
DispatchQueue.global(qos: .background) 不需要用户立即感知，例如网络请求、数据同步

```

**自定义的队列**

自定义串行队列

```swift
let serialQueue = DispatchQueue(label: "com.example.serialQueue")
```

自定义并行队列

```swift
let concurrentQueue = DispatchQueue(label: "com.example.concurrentQueue", attributes: .concurrent)
```

## 二 **同步 VS  异步**

- 同步：**强制** 等待任务完成返回后继续向下执行  **一定不会开启新线程，在当前线程上执行**

- 异步：不等待提交的任务完成返回后继续执行     有开启新线程的能力，但是**不一定开启**

  - 什么时候不开启：异步提交到主队列的时候，不会开新线程。（因为主线程早就存在了啊）

  

### 1. 同步各种场景

**同步**是不具有开启新线程的能力的，一定是在当前线程上执行(除了主队列必须在主线程上执行)。因此**无论是提交到什么队列，都不会开新的线程**

- 主线程上同步提交到主队列------**死锁**

  ```swift
  func mainThreadmainQueueTask() {
      let mainQueue = DispatchQueue.main
      print("start: \(Thread.current)")
      mainQueue.sync {
          print("主线程上同步提一个任务到主队列\(Thread.current)")
      }
      print("end")
  }
  ```

  

-  非主线程上同步提交到主队列,不死锁,**同步执行主队列上的任务,在主线程上执行**.

  ```swift
  func subThreadmainQueueTask() {
          let queue = DispatchQueue.global(qos: .userInteractive)
          queue.async {
              self.mainThreadmainQueueTask()
          }
      }
  ```

  

- 同步到自定义串行队列 -----> **同步**执行。在**提交任务所在的线程上**执行。

  ```swift
  override func viewDidLoad() {
      super.viewDidLoad()
      view.backgroundColor = .white
  
      for i in 0..<2 {
          // 任务会在提交任务所在的线程上执行：这里就是主线程
          syncSerialQueue()
      }
  
  
      let queue = DispatchQueue.global(qos: .default)
      queue.async {
          for i in 0..<2 {
              // 任务会在提交任务所在的线程上执行：这里是系统为这个默认并行队列分配一个线程上执行
              self.syncSerialQueue()
          }
      }
  
  }
  
  
  
  func syncSerialQueue() {
      let serialQueue = DispatchQueue(label: "com.example.serialQueue")
      print("提交前\(Thread.current)")
      serialQueue.sync {
          print("同步提任务1到自定义串行队列")
          print("\(Thread.current)")
      }
      print("提交后")
  
  }
  ```

  

- 同步到并行队列---->同步执行，**在提交任务所在的线程上执行，不开线程**

  

**总结**

1. 主线程 同步 主队列 三个同时满足的时候会死锁;非主线程上，同步提交到主队列，任务会在主线程上执行
2. 其他情况下都是在当前线程同步执行，不额外开线程。

正如刚开始说的，**强制的同步遇到了不强制的并行队列**，优先满足同步，不开启新线程，任务一个一个执行

### 2. 异步各种场景

**异步**有开启线程的能力，但是并不一定会开启线程。 分析一下面几种情况

- 主线程上异步提交到主队列 --->异步提交到主队列的任务**不会立即执行**，**任务将被添加到主队列的末尾**。因为主队列可能正在执行其他任务。**任务的执行时间取决于主队列中已有任务的数量和执行时间**。

  ```swift
      override func viewDidLoad() {
          super.viewDidLoad()
          view.backgroundColor = .white
          print("提交前")
          asyncMainQueue()
          print("提交后")
      }
      
      
      
      func asyncMainQueue() {
          let queue = DispatchQueue.main
          queue.async {
              print("异步提交任务到主队列")
              print("\(Thread.current)")
          }
      }
  
  // log
  // 提交前
  // 提交后
  // 异步提交任务到主队列
  // <_NSMainThread: 0x60000170c080>{number = 1, name = main}
  ```

  

- 非主线程上异步提交到主队列 --->

  ```swift
      func asyncMainQueueOnNotMainThread() {
          let defualtQueue = DispatchQueue.global(qos: .default)
          defualtQueue.async {
              print("提交前\(Thread.current)")
              self.asyncMainQueue()
              print("提交后\(Thread.current)")
          }
      }
  
  //提交前<NSThread: 0x60000174bd00>{number = 6, name = (null)}
  // 提交后<NSThread: 0x60000174bd00>{number = 6, name = (null)}
  //异步提交任务到主队列
  // <_NSMainThread: 0x600001700080>{number = 1, name = main}
  ```

  

- 异步提交到自定义串行队列----->开启一个新线程

  ```swift
      func asyncSerialQueue() {
          let queue = DispatchQueue(label: "com.example.serialQueue")
          print("提交前 1")
          queue.async {
              print("异步提交串行队列")
              print("\(Thread.current)")
          }
          print("提交后 1")
  
          
          print("提交前  2")
          queue.async {
              print("异步提交串行队列")
              print("\(Thread.current)")
          }
          print("提交后  2")
  
          
          let queue2 = DispatchQueue.global(qos: .default)
          queue2.async {
              print("提交前 3")
              queue.async {
                  print("异步提交串行队列")
                  print("\(Thread.current)")
              }
              print("提交后 3")
          }
      }
  
  ```

  

- 异步提交到自定义并行队列或预置并行队列----->开启多个线程

```swift
   func asyncConcurrent() {
        let queue = DispatchQueue(label: "com.example.concurrent", attributes: .concurrent)
        for _ in 0..<10 {
            queue.async {
                var j = 0
                for i in 0..<1000 {
                    j += 1
                }
                print("异步提交到并行队列\(Thread.current)")
            }
        }
    }

异步提交到并行队列<NSThread: 0x6000017372c0>{number = 6, name = (null)}
异步提交到并行队列<NSThread: 0x60000174c240>{number = 4, name = (null)}
异步提交到并行队列<NSThread: 0x600001746800>{number = 8, name = (null)}
异步提交到并行队列<NSThread: 0x600001749280>{number = 5, name = (null)}
异步提交到并行队列<NSThread: 0x600001737bc0>{number = 3, name = (null)}
异步提交到并行队列<NSThread: 0x600001707740>{number = 9, name = (null)}
异步提交到并行队列<NSThread: 0x600001752800>{number = 7, name = (null)}
异步提交到并行队列<NSThread: 0x60000175ed00>{number = 10, name = (null)}
异步提交到并行队列<NSThread: 0x60000173f9c0>{number = 11, name = (null)}
异步提交到并行队列<NSThread: 0x600001771500>{number = 12, name = (null)}
```



## 3.最终总结

只有异步提交到自定义串行队列（非主队列）或者异步提交到并行队列才可以开启多线程



1. 对于同步，是**不可能开启新线程**的

​	2.  对于异步

- ​	主队列

  - 主线程+主队列是**不会开启新线程**的（主队列上的任务一定是是主线程）:任务不会立即执行，放在主队列的末尾
  - 非主线程上+主队列上**也不会开新线程**（主队列上的任务一定是是主线程）：任务不会立即执行,最终在主线程上执行

- 串行队列 开启一个新线程

- 并行队列 开启多个新线程

  

   