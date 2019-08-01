# Java延迟队列——DelayQueue
## 1 DelayQueue简单介绍
对于分布式的延迟队列，我们可以使用`RabbitMQ`、`Redis`等实现；对于进程内的延迟队列，`Java`提供了比较方便使用的`DelayQueue`。

`DelayQueue`是`java.util.concurrent`并发包提供的一个类，它首先是一个阻塞队列（`BlockingQueue`），其内部是对优先级队列（`PriorityQueue`）的封装实现；可以根据消息的`TTL`时间的大小来进行优先排序，`DelayQueue`能保证`TTL`时间越小的消息就会越优先被消费。

可以说，`DelayQueue`是一个基于优先队列（`PriorityQueue`）实现的阻塞队列（`BlockingQueue`），队列中的消息的优先级是根据消息的`TTL`时间来决定的。

## 2 DelayQueue的使用

先说两个接口：`java.util.concurrent.Delayed`接口和`java.lang.Comparable`接口。

- 放入`DelayQueue`中的元素必须继承`Delayed`接口：

```java
public interface Delayed extends Comparable<Delayed> {

    /**
     * Delayed接口的实现类使用getDelay方法计算延迟任务的剩余延迟时间
     */
    long getDelay(TimeUnit unit);
    
}
```

- `Delayed`接口又继承了`java.lang.Comparable`接口：

```java
public interface Comparable<T> {
    
    /**
     * Delayed接口的实现类使用compareTo方法来实现队列中延迟任务的优先级排序
     */
    public int compareTo(T o);
    
}
```

**放进`DelayQueue`中的任务实体类必须实现`Delayed`接口，并覆写`getDelay`和`compareTo`方法，分别提供计算剩余时间和比较优先级排序的实现。**

### 2.1 延迟任务实体类

```java
/**
 * 延迟任务实体类
 * @author luz.ho211
 * @date 2018-07-04 10:57
 */
public class DelayMessage implements Delayed {

    private String message;   // 延迟任务中的任务数据
    private long ttl;         // 延迟任务到期时间（过期时间）

    /**
     * 构造函数
     * @param message 消息实体
     * @param ttl 延迟时间，单位毫秒
     */
    public DelayMessage(String message, long ttl) {
        setMessage(message);
        this.ttl = System.currentTimeMillis() + ttl;
    }

    @Override
    public long getDelay(TimeUnit unit) {
        // 计算该任务距离过期还剩多少时间
        long remaining = ttl - System.currentTimeMillis();
        // 一定要使用TimeUnit的convert方法转换时间单位
        return unit.convert(remaining, TimeUnit.MILLISECONDS);
    }

    @Override
    public int compareTo(Delayed o) {
        // 比较、排序：对任务的延时大小进行排序，将延时时间最小的任务放到队列头部
        return (int) (this.getDelay(TimeUnit.MILLISECONDS) - o.getDelay(TimeUnit.MILLISECONDS));
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

}
```

### 2.2 延迟队列的消费者代码

```java
/**
 * 延迟队列的消费者定义类
 * @author luz.ho211
 * @date 2018-07-04 11:16
 */
public class DelayQueueConsumer implements Runnable {

    private final static Logger logger = LoggerFactory.getLogger(DelayQueueConsumer.class);
    private final DelayQueue<DelayMessage> delayQueue;

    /**
     * 构造函数
     * @param delayQueue 延迟队列
     */
    public DelayQueueConsumer(DelayQueue<DelayMessage> delayQueue) {
        this.delayQueue = delayQueue;
    }

    @Override
    public void run() {
        while (true) {
            try {
                // 从延迟队列的头部获取已经过期的任务
                // 如果暂时没有过期任务或者队列为空，则take()方法会被阻塞，直到有过期的任务为止
                DelayMessage delayMessage = delayQueue.take();
                logger.info("Consumer received message: {}", delayMessage.getMessage());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

}
```

### 2.3 测试代码 & 测试结果

```java
public class Launcher {

    private final static Logger logger = LoggerFactory.getLogger(Launcher.class);

    public static void main(String[] args) throws Exception {
        // 创建延迟消息队列
        DelayQueue<DelayMessage> delayQueue = new DelayQueue<>();
        // 创建并启动延迟队列的消费者线程
        new Thread(new DelayQueueConsumer(delayQueue)).start();
        // 执行测试样例1
        // test1(delayQueue);
        // 执行测试样例2
        // test2(delayQueue);
        // 执行测试样例3
        test3(delayQueue);
    }

    /**
     * 测试用例1：生成5条TTL时间依次增大的延迟消息：1秒，2秒，3秒，4秒，5秒
     * @param delayQueue 延迟队列
     */
    private static void test1(DelayQueue<DelayMessage> delayQueue) {
        for (int i = 1; i <= 5; i++) {
            DelayMessage delayMessage = new DelayMessage(String.valueOf(i), i*1000L);
            logger.info("Producer publish message: {}", String.valueOf(i));
            delayQueue.offer(delayMessage);
        }
    }

    /**
     * 测试用例2：生成5条TTL时间依次减小的延迟任务：5秒，4秒，3秒，2秒，1秒
     * @param delayQueue 延迟队列
     */
    private static void test2(DelayQueue<DelayMessage> delayQueue) {
        for (int i = 5; i > 0; i--) {
            String message = String.valueOf(i);
            DelayMessage delayMessage = new DelayMessage(message, i*1000L);
            logger.info("Producer publish message: {}", message);
            delayQueue.offer(delayMessage);
        }
    }

    /**
     * 测试用例3：生成5个延迟时间随机的延迟任务
     * @param delayQueue 延迟队列
     */
    private static void test3(DelayQueue<DelayMessage> delayQueue) {
        Random random = new Random();
        for (int i = 0; i < 5; i++) {
            // 生成1~10的随机数：作为1秒-10秒的延迟时间
            int ttl = 1 + random.nextInt(10);
            String message = String.valueOf(ttl);
            DelayMessage delayMessage = new DelayMessage(message, ttl*1000L);
            logger.info("Producer publish message: {}", message);
            delayQueue.offer(delayMessage);
        }
    }

}


// test1()的测试结果
2018-07-04 16:30:20.769  Producer publish message: 1
2018-07-04 16:30:20.773  Producer publish message: 2
2018-07-04 16:30:20.774  Producer publish message: 3
2018-07-04 16:30:20.774  Producer publish message: 4
2018-07-04 16:30:20.774  Producer publish message: 5
2018-07-04 16:30:21.768  Consumer received message: 1
2018-07-04 16:30:22.773  Consumer received message: 2
2018-07-04 16:30:23.776  Consumer received message: 3
2018-07-04 16:30:24.776  Consumer received message: 4
2018-07-04 16:30:25.774  Consumer received message: 5

// test2()的测试结果
2018-07-04 16:31:24.395  Producer publish message: 5
2018-07-04 16:31:24.399  Producer publish message: 4
2018-07-04 16:31:24.401  Producer publish message: 3
2018-07-04 16:31:24.402  Producer publish message: 2
2018-07-04 16:31:24.402  Producer publish message: 1
2018-07-04 16:31:25.402  Consumer received message: 1
2018-07-04 16:31:26.405  Consumer received message: 2
2018-07-04 16:31:27.403  Consumer received message: 3
2018-07-04 16:31:28.399  Consumer received message: 4
2018-07-04 16:31:29.395  Consumer received message: 5

// test3()的测试结果
2018-07-04 14:19:35.347  Producer publish message: 2
2018-07-04 14:19:35.354  Producer publish message: 5
2018-07-04 14:19:35.355  Producer publish message: 4
2018-07-04 14:19:35.355  Producer publish message: 3
2018-07-04 14:19:35.355  Producer publish message: 9
2018-07-04 14:19:37.344  Consumer received message: 2
2018-07-04 14:19:38.356  Consumer received message: 3
2018-07-04 14:19:39.356  Consumer received message: 4
2018-07-04 14:19:40.355  Consumer received message: 5
2018-07-04 14:19:44.355  Consumer received message: 9
```

可以看出，`DelayQueue`可以严格按照延迟任务的`TTL`时间来进行优先级排序，`TTL`小的任务会优先出列。由于`DelayQueue`是进程内的延迟队列实现，所以实际应用中应该考虑程序挂掉的时候，消息丢失对实际生产的影响。



## 参考文章

- [精巧好用的DelayQueue](https://www.cnblogs.com/jobs/archive/2007/04/27/730255.html)
- [Guide to DelayQueue](http://www.baeldung.com/java-delay-queue)
- [延迟任务的实现总结](https://www.cnblogs.com/haoxinyue/p/6663720.html)
- [xmemcached 1.2.6.2紧急发布（升级到1.2.6.1的朋友注意）](http://www.blogjava.net/killme2008/archive/2010/10/22/335897.html)
- [Java延时队列DelayQueue的使用](https://my.oschina.net/lujianing/blog/705894)


