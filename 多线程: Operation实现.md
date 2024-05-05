### 目录

- Operation 概述

- 子类化Operation

- 单独使用 Operation（不常用）

- Operation联合 OperationQueue 使用 实现多线程（常用）

  

### 一 Operation 概述

**Operatoin** 是一个抽象基类，系统预置了两个子类

**InvocationOperation**:启动一个由调用指定对象的选择器组成的操作,**在 swift 中这个 API 已经废弃了，因此不再使用**

**BlockOperation**:管理一个或多个块的**并发**执行的操作

当BlockOperation不能满足需求的时候，可以使用**子类化 的 Operation自定义 Operation**

### 二 子类化Operation



### 三 单独使用 Operation

