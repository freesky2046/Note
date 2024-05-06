### 内容

- Operation 概述

- 子类化Operation

- 单独使用 Operation（**不常用，意义不大**）

- Operation联合 OperationQueue 使用 实现多线程（**常用**）

- Operation的优先级

- Operation的依赖

  

### 一 Operation 概述

**Operatoin** 是一个抽象基类，系统预置了**两个子类**

**InvocationOperation**:启动一个由调用指定对象的选择器组成的操作,**在 swift 中这个 API 已经废弃了，因此不再使用**

**BlockOperation**:管理一个或多个块的**并发**执行的操作

当BlockOperation不能满足需求的时候，可以使用**子类化 的 Operation自定义 Operation**

### 二 子类化Operation

两种方式

- 同步方式
- 异步方式

**同步方式**很简单，重写 main 方法即可

```swift
class SyncOperation: Operation {

    override func main() {
        if isCancelled {
            return
        }
        for i in 0..<1000 {
            print("\(i * 2)")
        }
        print(" 执行完毕")
    }
}
```

**异步方法**

必须实现的方法有下面四个

- Start方法
- isExecuting
- isFinished
- isConcurrent

可选的方法只有一个

- main 方法

**Start**:(必选)所有并发操作必须覆盖此方法，并用自己的**自定义实现替换默认行为**。要手动执行操作，可以调用它的 `start` 方法。因此，此方法的实现是操作的起点，是设置执行任务的线程或其他执行环境的地方。你的实现在**任何时候都不能调用 `super`** 。

开始正式执行方法前：

1. 要判断是否已经取消了 如果已经取消了 ，就要马上标记为 isFinished为 true。然后不执行任何操作
2. 若没有取消，就要执行任务了。标记isExecuting为 true.
3. **执行任务，可以提交到 main 上执行 这一步并不是必须的。**
4. 任务完成后，isExecuting标记为 false,isFinished标记为true.

**isExecuting&isFinished**:标记状态 的时候 需要1.对应 存储变量来记录状态 2.需要手动 kvo 来通知外部状态变更。 3.要使用递归锁保证线程安全

**isConcurrent**:返回 true 即可

```swift

class AsyncOperation: Operation {
    
    var _isExecuting: Bool = false
    var _isFinished: Bool = false
    var url:String?
    var lock: NSRecursiveLock = NSRecursiveLock()
    var image: UIImage?
    
    override var isExecuting: Bool {
        get {
            lock.lock()
            let result = _isExecuting
            lock.unlock()
            return result
        }
        set {
            lock.lock()
            willChangeValue(forKey: "isExecuting")
            _isExecuting = newValue
            didChangeValue(forKey: "isExecuting")
            lock.unlock()
        }
    }
    
    override var isFinished: Bool {
        get {
            lock.lock()
            let result = _isFinished
            lock.unlock()
            return result
        }
        set {
            lock.lock()
            willChangeValue(forKey: "isFinished")
            _isFinished = newValue
            willChangeValue(forKey: "isFinished")
            lock.unlock()
        }
    }
    
    override init() {
        super.init()
    }
    
    override func start() {
        if isCancelled == true {
            markCompletion()
            return
        }
        
        isExecuting = true
        isFinished = false
        guard let data = try? Data(contentsOf: URL(string: url ?? "")!) else {
            markCompletion()

            return
        }
        self.image = UIImage(data: data)
        markCompletion()
    }
    
    func markCompletion() {
        isExecuting = false
        isFinished = true
        completionBlock?()
    }
}

```



### 三 单独使用 Operation

单独使用Operation，创建 和配置operation 后，调用它的 start 方法即可

对于**BlockOperation**分为两种情况。

-  初始化只添加一个操作: **同步**，不开新线程

  ```swift
      func soloUseBlockOperation() {
          print("使用前\(Thread.current)")
          let blockOperation = BlockOperation {
              self.mockTimeOperation()
              print("单独使用 blockOperation\(Thread.current)")
          }
          blockOperation.start()
          print("使用后")
      }
      
      /// 一个耗时的操作
      func mockTimeOperation() {
          var j = 0
          for i in 0..<1000000 {
              j += i * 2
          }
      }
  ```

  

-   初始化后添加多个操作：**同步**（阻塞当前任务）。**但会有多个线程**，其中一个线程是当前线程。

  ```swift
    func useBlockAddOperation() {
        print("使用前\(Thread.current)")
        let blockOperation = BlockOperation {
            self.mockTimeOperation()
            print("单独使用 blockOperation\(Thread.current)")
        }
        blockOperation.addExecutionBlock {
            self.mockTimeOperation()
            print("追加使用 blockOperation1\(Thread.current)")
        }
        blockOperation.addExecutionBlock {
            self.mockTimeOperation()
            print("追加使用 blockOperation2\(Thread.current)")
        }
        blockOperation.start()
        print("使用后")
    }
  
  使用前<_NSMainThread: 0x600001700140>{number = 1, name = main}
  追加使用 blockOperation1<NSThread: 0x600001748bc0>{number = 4, name = (null)}
  追加使用 blockOperation2<NSThread: 0x60000174a700>{number = 6, name = (null)}
  单独使用 blockOperation<_NSMainThread: 0x600001700140>{number = 1, name = main}
  使用后
  ```



### 参考

[https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/OperationObjects/OperationObjects.html#//apple_ref/doc/uid/TP40008091-CH101-SW8](https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/OperationObjects/OperationObjects.html#//apple_ref/doc/uid/TP40008091-CH101-SW8)
