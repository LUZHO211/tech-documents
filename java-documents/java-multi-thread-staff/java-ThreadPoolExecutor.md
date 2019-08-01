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
|`corePoolSize`|线程池的核心线程数，一旦创建就会一直保留在线程池中（除非调用`allowCoreThreadTimeOut(true)`方法将`allowCoreThreadTimeOut`参数设置成`true`）|
|`maximumPoolSize`|线程池中允许存活的最大线程数|
|`keepAliveTime`|当创建的线程数量**超过了核心线程数**，允许线程池中处于空闲状态的**非核心线程**的存活时间（若设置了`allowCoreThreadTimeOut`参数为`true`，核心线程也会被回收）|
|`unit`|`keepAliveTime`参数的时间单位|
|`workQueue`|工作队列，用于存放将被执行的线程任务（`Runnable tasks`）：阻塞队列（`BlockingQueue`）|
|`threadFactory`|创建线程的工厂，可以用于标记区分不同线程池所创建出来的线程|
|`handler`|当线程池和工作队列的容量**均达到上限**，再向线程池提交任务时所触发的拒绝策略逻辑`handler`|

## 1 线程池大小
线程池有两个关于线程数量配置的参数：`corePoolSize`和`maximumPoolSize`：

- `corePoolSize` —— 设置线程池的核心线程数
- `maximumPoolSize` —— 设置线程池中允许存活的最大线程数

线程池创建后，默认情况下线程池中不会有任何线程，当有线程任务提交到线程池中，线程池才会创建线程来处理任务。

**但是如果显式调用了`prestartAllCoreThreads()`方法或者`prestartCoreThread()`方法，会立即创建核心线程。**

```java
ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(
                corePoolSize,
                maximumPoolSize,
                keepAliveTime,
                TimeUnit.SECONDS,
                workQueue,
                threadFactory,
                rejectedExecutionHandler);

// 调用prestartAllCoreThreads()来立即创建所有核心线程
threadPoolExecutor.prestartAllCoreThreads();
```

## 2 线程池阻塞队列


## 3 线程池拒绝策略

## 4 线程池内部处理逻辑


## 5 Executors
`Java`提供了`java.util.concurrent.Executors`这个工具类来帮助开发者快速创建定义好的四种线程池：`FixedThreadPool`、`SingleThreadPool`、`CachedThreadPool`以及`ScheduledThreadPool`。

- `Executors.newFixedThreadPool(int nThreads)`：创建一个固定数量线程的线程池，池中的线程数量**达到最大值后会始终保持不变**。
- `Executors.newSingleThreadExecutor()`：创建一个只包含单个线程的线程池，可以保证所有任务按提交的顺序被单一的一个线程串行地执行。
- `Executors.newCachedThreadPool()`：创建一个会根据任务按需地创建、回收线程的线程池。这种类型线程池适合执行数量多、耗时少的任务。
- `Executors.newScheduledThreadPool(int corePoolSize)`：创建一个具有定时功能的线程池，适用于执行定时任务。

**这些线程池本质上是对`ThreadPoolExecutor`进行封装，请往下看。**

#### newFixedThreadPool

```java
new ThreadPoolExecutor(nThreads, nThreads, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<Runnable>());
```

#### newSingleThreadExecutor

```java
new ThreadPoolExecutor(1, 1, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<Runnable>());
```

#### newCachedThreadPool

```java
new ThreadPoolExecutor(0, Integer.MAX_VALUE, 60L, TimeUnit.SECONDS, new SynchronousQueue<Runnable>());
```

### 提醒的方面
以上四种定义好的线程池在某些场景确实很方便我们的使用，但是我们需要了解它们的隐患之处：

- `FixedThreadPool`和`SingleThreadPool`所允许的工作队列最大容量为`Integer.MAX_VALUE`，这有可能会随着工作队列中的任务堆积而导致`OOM`；
- `CachedThreadPool`和`ScheduledThreadPool`所允许创建的线程数量为`Integer.MAX_VALUE`，这也有可能因为创建大量线程导致`OOM`或者线程切换开销巨大。

最好还是熟悉`ThreadPoolExecutor`的原理和用法，在`Java`提供的四种线程池不能满足需求，或者想把控整个线程池的情况下，可以自己使用`ThreadPoolExecutor`类来构建自定义的线程池。

