## Java的四种线程池
### 1 前言
在应用程序中恰当地使用**多线程技术**可以充分发挥多核`CPU`的优势，极大地提升应用处理业务的效率。

应该**始终使用线程池**来创建、管理线程，而不是在程序中显式地使用`new Thread(Runnable)`的方式来创建线程，**因为使用线程池可以对线程进行统一管理：创建、销毁线程开销较大，线程池可以对线程池中的线程进行复用，无需反复创建和销毁线程。**

### 2 Java四种线程池介绍
`Java`提供了`java.util.concurrent.Executors`工具类来帮助我们快速创建各种定义好的线程池：`FixedThreadPool`、`SingleThreadPool`、`CachedThreadPool`以及`ScheduledThreadPool`这四种线程池（这些线程池本质上是对`ThreadPoolExecutor`进行封装）。

- `Executors.newFixedThreadPool(int nThreads)`：创建一个固定数量线程的线程池，池中的线程数量会始终保持不变。
- `Executors.newSingleThreadExecutor()`：创建一个只包含单个线程的线程池，可以保证所有任务按始终被单一的一个线程串行地执行。
- `Executors.newCachedThreadPool()`：创建一个会根据任务按需地创建、回收线程的线程池。这种类型线程池适合执行数量多、耗时少的任务。
- `Executors.newScheduledThreadPool(int corePoolSize)`：创建一个具有定时功能的线程池，适用于执行定时任务。

### 3 提醒的方面
以上四种定义好的线程池在某些场景确实很方便我们的使用，但是我们需要了解它们的隐患之处：

- `FixedThreadPool`和`SingleThreadPool`所允许的工作队列最大容量为`Integer.MAX_VALUE`，这有可能会随着工作队列中的任务堆积而导致`OOM`；
- `CachedThreadPool`和`ScheduledThreadPool`所允许创建的线程数量为`Integer.MAX_VALUE`，这也有可能因为创建大量线程导致`OOM`或者线程切换开销巨大。

### 4 最后
最好还是熟悉`ThreadPoolExecutor`的原理和用法，在`Java`提供的四种线程池不能满足需求，或者想把控整个线程池的情况下，可以自己使用`ThreadPoolExecutor`类来构建自定义的线程池。