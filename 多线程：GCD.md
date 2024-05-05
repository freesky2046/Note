## 一 队列类型

- 串行： **强制**多个任务一个一个执行，强制要求 
- 并行：可以**多个线程，从而多个任务可以同时执行。有多个任务同时执行的能力，**但是并不是一定会这样做

**内置的队列**

内置的串行队列且是主线性上执行的队列

```swift
let mainQueue = DispatchQueue.main
```

内置的并行队列

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
- 异步： **不强制** 不等待提交的任务完成返回后继续执行     有开启新线程的能力，但是**不一定开启**

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

1. 主线程 同步 主队列 三个同时满足的时候会死锁。
2. 非主线程上，同步提交到主队列，任务会在主线程上执行
3. 其他情况下都是在当前线程同步执行，不额外开线程。

正如刚开始说的，**强制的同步遇到了不强制的并行**，优先满足同步，不开启新线程，任务一个一个执行

### 2. 异步各种场景

**异步**有开启线程的能力，但是并不一定会开启线程。 分析一下面几种情况

- 主线程上异步提交到主队列 
- 非主线程上异步提交到主队列 
- 异步提交到自定义串行队列----->开启一个新线程
- 异步提交到自定义并行队列或预置并行队列----->开启多个线程

