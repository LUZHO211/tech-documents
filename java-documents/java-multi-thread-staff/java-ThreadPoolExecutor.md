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

|参数名称|参数说明|
|:---|:---|
|`corePoolSize`|线程池的核心线程数|
|`maximumPoolSize`|线程池的最大线程数|
|`keepAliveTime`|当线程池中线程数量大于核心线程数时，非核心线程的空闲存活时间|
|`unit`|`keepAliveTime`参数的时间单位|
|`workQueue`|线程任务缓存的工作队列|
|`threadFactory`|创建线程的线程工厂|
|`handler`|当线程池线程数量和工作队列均满，再提交任务时所触发的拒绝策略处理器|
