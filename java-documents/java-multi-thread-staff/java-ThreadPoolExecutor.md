# Java线程池的使用——ThreadPoolExecutor

```java
// 创建线程池的核心构造函数
public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler) {
        if (corePoolSize < 0 ||
            maximumPoolSize <= 0 ||
            maximumPoolSize < corePoolSize ||
            keepAliveTime < 0)
            throw new IllegalArgumentException();
        if (workQueue == null || threadFactory == null || handler == null)
            throw new NullPointerException();
        this.acc = System.getSecurityManager() == null ?
                null :
                AccessController.getContext();
        this.corePoolSize = corePoolSize;
        this.maximumPoolSize = maximumPoolSize;
        this.workQueue = workQueue;
        this.keepAliveTime = unit.toNanos(keepAliveTime);
        this.threadFactory = threadFactory;
        this.handler = handler;
    }
```

- `ThreadPoolExecutor`核心构造函数的各个参数说明如下表：

|参数名称|参数说明|
|:---|:---|
|`corePoolSize`|线程池的核心线程数，一旦创建就会一直保留在线程池中（除非将调用了`allowCoreThreadTimeOut(true)`将`allowCoreThreadTimeOut`参数设置成`true`）|
|`maximumPoolSize`|线程池中允许存活的最大线程数|
|`keepAliveTime`|当创建的线程数量**超过了核心线程数**，允许线程池中处于空闲状态的**非核心线程**的最大存活时间（若设置了`allowCoreThreadTimeOut`参数为`true`，核心线程也会被回收）|
|`unit`|`keepAliveTime`参数的时间单位|
|`workQueue`|工作队列，用于存放将被执行的线程任务（`Runnable tasks`）：阻塞队列（`BlockingQueue`）|
|`threadFactory`|创建线程的工厂，可以用于标记区分不同线程池所创建出来的线程|
|`handler`|当线程池和工作队列的容量**均达到上限**，再向线程池提交任务时所触发的拒绝策略逻辑`handler`|
