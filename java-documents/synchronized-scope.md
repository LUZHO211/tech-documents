# Java synchronized关键字的作用域

- `synchronized`关键字用于对共享资源进行同步（加锁），而且是一个**悲观锁**；一旦某个线程获取到了资源的控制权，该线程将会独占该资源直至用完释放。
- `synchronized`关键字可以用来修饰**方法（`method`）**和**代码块（`code block`）**，给资源加锁，控制并发访问。
- `synchronized`关键字的作用域有两种：**类级别**和**实例级别**

### 1 类级别的synchronized
类级别作用域的`synchronized`加锁，范围较大，会影响其他线程对对象中对其他`synchronized`方法的访问。

例如下面的一个`SynchronizedScopeTest1.java`对象：
- `method1`和`method2`为同步的`static`方法（`synchronized`修饰）；`method3`为普通类方法（`static`但是没有`synchronized`修饰）。
- 启动三个线程`thread-1`、`thread-2`和`thread-3`分别去调用`method1`、`method2`和`method3`

```java

/**
 * 类级别的synchronized加锁
 * @author luz.ho211
 * @date 2019-07-31 14:38
 */
public class SynchronizedScopeTest1 {

    private final static Logger logger = LoggerFactory.getLogger(SynchronizedScopeTest1.class);

    public synchronized static boolean method1(String threadName) {
        logger.info("{} now in method1 ...", threadName);
        sleep(5000L);
        return true;
    }

    public synchronized static boolean method2(String threadName) {
        logger.info("{} now in method2 ...", threadName);
        sleep(5000L);
        return true;
    }
    
    public static boolean method3(String threadName) {
        logger.info("{} now in method3 ...", threadName);
        sleep(5000L);
        return true;
    }

    public static void main(String[] args) {
        new Thread(() -> {
            SynchronizedScopeTest1.method1("thread-1");
        }).start();

        new Thread(() -> {
            SynchronizedScopeTest1.method2("thread-2");
        }).start();

        new Thread(() -> {
            SynchronizedScopeTest1.method3("thread-3");
        }).start();
    }

}
```

**运行日志结果如下：**

```bash
15:24:48.728 INFO com.luzho211.sync.SynchronizedScopeTest1 - thread-1 now in method1 ...
15:24:48.736 INFO com.luzho211.sync.SynchronizedScopeTest1 - thread-3 now in method3 ...
15:24:53.738 INFO com.luzho211.sync.SynchronizedScopeTest1 - thread-2 now in method2 ...
```

**类级别加锁：`thread-1`和`thread-3`分别同时可以进入`method1`和`method3`，说明一个对象中有多个`synchronized`方法，只要某个线程获取了某个`synchronized`方法的访问控制权，其他线程就不能同时访问该对象中的其他任何`synchronized`方法，但是不影响其他线程该对象中的非`synchronized`方法的访问。**

**很容易理解：`static`方法是类方法，如果类方法加了锁，那自然就是锁住整个类。所以这个类如果被某线程获取到了锁，其他线程就无法继续获取类的锁。**

- **代码块**中使用`synchronized`也能达到类级别加锁的效果：

```java
public void method(String threadName) {
    synchronized (SynchronizedScopeTest1.class) {
       logger.info("{} now in method3 ...", threadName);
       sleep(5000L);
    }
}
```

### 2 实例级别的synchronized

实例别作用域的`synchronized`加锁，只会影响其他线程对某个实例的其他`synchronized`方法的访问，新创建一个实例，是不受影响的。

```java
/**
 * 实例级别的synchronized加锁
 * @author luz.ho211
 * @date 2019-07-31 14:38
 */
public class SynchronizedScopeTest2 {

    private final static Logger logger = LoggerFactory.getLogger(SynchronizedScopeTest2.class);

    public synchronized boolean method1(String threadName) {
        logger.info("{} now in method1 ...", threadName);
        sleep(5000L);
        return true;
    }

    public synchronized boolean method2(String threadName) {
        logger.info("{} now in method2 ...", threadName);
        sleep(5000L);
        return true;
    }

    public static void main(String[] args) {
        SynchronizedScopeTest2 instance1 = new SynchronizedScopeTest2();
        SynchronizedScopeTest2 instance2 = new SynchronizedScopeTest2();
        new Thread(() -> {
            instance1.method1("thread-1");
        }).start();

        // 同一实例：访问同一个synchronized方法
        new Thread(() -> {
            instance1.method1("thread-2");
        }).start();

        // 同一实例：访问不同的synchronized方法
        new Thread(() -> {
            instance1.method2("thread-3");
        }).start();

        // 不同实例：可以不受instance1的锁的限制
        new Thread(() -> {
            instance2.method1("thread-4");
        }).start();
    }

}
```

**运行日志结果如下：**

```bash
16:17:21.783 INFO com.luzho211.sync.SynchronizedScopeTest2 - thread-1 now in method1 ...
16:17:21.783 INFO com.luzho211.sync.SynchronizedScopeTest2 - thread-4 now in method1 ...
16:17:26.795 INFO com.luzho211.sync.SynchronizedScopeTest2 - thread-3 now in method2 ...
16:17:31.800 INFO com.luzho211.sync.SynchronizedScopeTest2 - thread-2 now in method1 ...
```

