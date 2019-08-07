# jstack：Java线程栈分析工具
jstack是JVM自带的一种线程堆栈跟踪工具。可以使用jstack打印JVM进程中的线程堆栈的快照信息。

```bash
Usage:
    jstack [-l] <pid>
        (to connect to running process)
    jstack -F [-m] [-l] <pid>
        (to connect to a hung process)
    jstack [-m] [-l] <executable> <core>
        (to connect to a core file)
    jstack [-m] [-l] [server_id@]<remote server IP or hostname>
        (to connect to a remote debug server)

Options:
    -F  to force a thread dump. Use when jstack <pid> does not respond (process is hung)
    -m  to print both java and native frames (mixed mode)
    -l  long listing. Prints additional information about locks
    -h or -help to print this help message
```

- 使用`jps`或者`ps -ef | grep java`找出`Java`的`PID`

```bash
$ jps -l
3659 org.jetbrains.jps.cmdline.Launcher
3691 sun.tools.jps.Jps
3660 com.luzho211.zk.ZookeeperClient
```

- 使用`jstack -l`打印JVM的线程栈信息：

```bash
$ jstack -l 3660
2019-08-06 17:21:49
Full thread dump Java HotSpot(TM) 64-Bit Server VM (25.211-b12 mixed mode):

"main-SendThread" #11 daemon prio=5 os_prio=31 tid=0x00007fc8611a3000 nid=0x4b07 runnable [0x00007000034e4000]
   java.lang.Thread.State: RUNNABLE
	at sun.nio.ch.KQueueArrayWrapper.kevent0(Native Method)
	at sun.nio.ch.KQueueArrayWrapper.poll(KQueueArrayWrapper.java:198)
	at sun.nio.ch.KQueueSelectorImpl.doSelect(KQueueSelectorImpl.java:117)
	at sun.nio.ch.SelectorImpl.lockAndDoSelect(SelectorImpl.java:86)
	- locked <0x00000007960eae50> (a sun.nio.ch.Util$3)
	- locked <0x00000007960ea5b8> (a java.util.Collections$UnmodifiableSet)
	- locked <0x00000007960e4e48> (a sun.nio.ch.KQueueSelectorImpl)
	at sun.nio.ch.SelectorImpl.select(SelectorImpl.java:97)
	at org.apache.zookeeper.ClientCnxnSocketNIO.doTransport(ClientCnxnSocketNIO.java:349)
	at org.apache.zookeeper.ClientCnxn$SendThread.run(ClientCnxn.java:1141)

   Locked ownable synchronizers:
	- None
```

- 或者将线程栈快照信息保存到文件中，便于分析：

```bash
$ jstack -l 3660 > stack-3660.
```
