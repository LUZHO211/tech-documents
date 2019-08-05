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
`Java`使用阻塞队列（`BlockingQueue`）作为线程池的工作队列，可以直接应用在多线程并发的环境下缓存线程任务。

**如果阻塞队列（`BlockingQueue`）为空（`empty`），则尝试从队列中获取（读取）任务的线程会被阻塞；如果阻塞队列（`BlockingQueue`）满了（`full`），则尝试往队列中插入任务的线程会被阻塞。**

阻塞队列`java.util.concurrent.BlockingQueue`是一个接口，这个接口类中定义的四种类型的方法，分别会在无法完成写入、读取元素等队列操作的情况下作出不同的反应：

|操作类型|直接抛异常|返回特定值|一直阻塞|超时退出|
|:---|:---|:---|:---|:---|
|插入元素（`Insert`）|`add(e)`|`offer(e)`|`put(e)`|`offer(e, time, unit)`|
|删除元素（`Remove`）|`remove()`|`poll()`|`take()`|`poll(time, unit)`|
|检查（`Examine`）|`element()`|`peek()`|`not applicable`|`not applicable`|


阻塞队列（`BlockingQueue`）在`JDK`实现类中的常用几个如下说明：

|阻塞队列实现类|阻塞队列说明|
|:---|:---|
|`ArrayBlockingQueue`|一个基于数组结构的**有界**阻塞队列。|
|`LinkedBlockingQueue`|一个基于链表结构的**有界**阻塞队列。|
|`SynchronousQueue`|一个不存储任何元素的阻塞队列。一个线程的插入操作必须等待另一个线程来读取才能完成，才会允许下一个插入操作。`CachedThreadPool`用的就是这个队列，所以向线程池中提交任务就会新建一个或分配一个空闲的线程来处理任务，因为`SynchronousQueue`不会存储任何元素。|
|`PriorityBlockingQueue`|一个支持优先级排序的**无界**阻塞队列。|
|`DelayQueue`|一个使用优先级队列实现的**无界**阻塞队列。用于处理延迟任务。|

## 3 线程池拒绝策略
`ThreadPoolExecutor`内置了四种拒绝策略：

- `ThreadPoolExecutor.AbortPolicy`：取消策略，丢弃任务并抛出`RejectedExecutionException`，**默认的拒绝策略**。
- `ThreadPoolExecutor.DiscardPolicy`：丢弃策略，丢弃任务但是不会抛出任何异常。
- `ThreadPoolExecutor.DiscardOldestPolicy`：丢弃策略，丢弃队列头部的任务（`oldest unhandled request`）并重新尝试执行所提交的任务。
- `hreadPoolExecutor.CallerRunsPolicy`：调用者执行策略，将任务直接给提交该任务的线程来执行。

>以上四种拒绝策略是`ThreadPoolExecutor`内置的，对于被拒绝的任务处理比较简单。当然，我们也可以根据我们的需求场景来继承这些策略类或者直接实现`RejectedExecutionHandler`接口来达到我们更复杂的拒绝策略。

## 4 线程池内部处理逻辑（流程）
>**重点理解线程池的内部处理逻辑（流程）。**

- 当线程池中的线程数量**小于**核心线程数：新提交一个任务时，无论是否存在空闲的线程，线程池都将新建一个新的线程来执行新任务；
- 当线程池中的线程数量**等于**核心线程数（核心线程已满）：新提交的任务会被存储到工作队列中，等待空闲线程来执行，**而不会创建新线程**；
- 当工作队列已满，并且池中的线程数量**小于**最大线程数（`maximumPoolSize`）：如果继续提交新的任务，线程池会创建新线程来处理任务；
- 当工作队列已满，并且池中线程数量已达到最大值：继续提交新任务时，线程池会触发拒绝策略处理逻辑；
- 如果线程池中存在空闲的线程并且其空闲时间达到了`keepAliveTime`参数的限定值，线程池会回收这些空闲线程，但是线程池不会回收空闲的核心线程（若设置了`allowCoreThreadTimeOut`参数为`true`，核心线程也会被回收）。

## 5 execute & submit
向线程池中提交线程任务有两种方式：调用`execute`方法或者调用`submit`方法。

- **`execute`方法**：定义在`java.util.concurrent.Executor`接口中

```java
void execute(Runnable command);
```

- **`submit`方法**：定义在`java.util.concurrent.ExecutorService`接口中

```java
// submit方法定义有三个，参数和返回值不一样而已：这里只贴出其中一个方法的实现
public Future<?> submit(Runnable task) {
    if (task == null) throw new NullPointerException();
    RunnableFuture<Void> ftask = newTaskFor(task, null);
    execute(ftask);
    return ftask;
}
```

- `submit`方法只是将线程任务封装成一个`FutureTask`，最终还是调用`execute`方法来执行任务。
- `submit`方法会返回一个`Future`，通过调用`Future.get()`方法可以获取到任务的执行结果；`execute`方法返回`void`，无法获取任务执行结果。
- 若提交的线程任务里面抛出异常：`execute`会直接将异常抛出并退出线程；`submit`会**吞掉异常**，并在`Future.get()`的时候将异常抛出。


## 6 Executors
`Java`提供了`java.util.concurrent.Executors`这个工具类来帮助开发者快速创建定义好的四种线程池：`FixedThreadPool`、`SingleThreadPool`、`CachedThreadPool`以及`ScheduledThreadPool`。

- `Executors.newFixedThreadPool(int nThreads)`：创建一个固定数量线程的线程池，池中的线程数量**达到最大值后会始终保持不变**。
- `Executors.newSingleThreadExecutor()`：创建一个只包含单个线程的线程池，可以保证所有任务按提交的顺序被单一的一个线程串行地执行。
- `Executors.newCachedThreadPool()`：创建一个会根据任务按需地创建、回收线程的线程池。这种类型线程池适合执行数量多、耗时少的任务。
- `Executors.newScheduledThreadPool(int corePoolSize)`：创建一个具有定时功能的线程池，适用于执行定时任务。

>**这些线程池本质上是对`ThreadPoolExecutor`进行封装，请往下看。**

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

#### newScheduledThreadPool

```java
new ScheduledThreadPoolExecutor(corePoolSize);

public ScheduledThreadPoolExecutor(int corePoolSize) {
    super(corePoolSize, Integer.MAX_VALUE, 0, NANOSECONDS, new DelayedWorkQueue());
}
```

以上四种定义好的线程池在某些场景确实很方便我们的使用，但是我们需要了解它们的隐患之处：

- `FixedThreadPool`和`SingleThreadPool`所允许的工作队列最大容量为`Integer.MAX_VALUE`，这有可能会随着工作队列中的任务堆积而导致`OOM`；
- `CachedThreadPool`和`ScheduledThreadPool`所允许创建的线程数量为`Integer.MAX_VALUE`，这也有可能因为创建大量线程导致`OOM`或者线程切换开销巨大。

最好还是熟悉`ThreadPoolExecutor`的原理和用法，在`Java`提供的四种线程池不能满足需求，或者想把控整个线程池的情况下，可以自己使用`ThreadPoolExecutor`类来构建自定义的线程池。

