### 目录

- Operation 分类

- 子类化Operation

- 独立使用Operation

- OperationQueue概述

- Operation和OperationQueue双剑合璧

- NSOperation依赖

- NSOperation优先级

  

### NSOperation 分类

NSOperatoin 是一个抽象基类，系统预置了两个子类

**InvocationOperation**:启动一个由调用指定对象的选择器组成的操作,**在 swift 中这个 API 已经废弃了，因此不再使用**

**BlockOperation**:管理一个或多个块的**并发**执行的操作

当BlockOperation不能满足需求的时候，可以使用**子类化 的 Operation自定义 Operation**

### 自定义Operation



### 独立使用 Operation

#### BlockOperation

独立使用，初始化执行一个操作



在没有使用 NSOperationQueue、在主线程中单独使用 NSBlockOperation 执行一个操作的情况下，操作是在当前线程执行的，并没有开启新线程。
