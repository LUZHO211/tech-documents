# jps —— Java进程状态工具
如果需要在`Linux`上查看`Java`进程的相关信息，通常使用`ps -ef | grep java`来查看；但是`JDK`也有相关的工具来查看`Java`进程信息：`jps`。

jps（`Java Virtual Machine Process Status Tool`）是`JDK`自带的一个工具，用于查看`Java`进程的状态。例如列出系统中的所有`Java`进程、`Java`进程所用的`JVM`参数等等，可以协助我们进行`JVM`调优。

`jps -help`一下，查看`jps`的用法：
```bash
$ jps -help
usage: jps [-help]
       jps [-q] [-mlvV] [<hostid>]

Definitions:
    <hostid>:      <hostname>[:<port>]
```

关于`jps`命令更详细的手册说明，使用`man jps`查看：
```bash
$ man jps
jps(1)                                                         Monitoring Tools                                                         jps(1)

NAME
       jps - Lists the instrumented Java Virtual Machines (JVMs) on the target system. This command is experimental and unsupported.

SYNOPSIS

··· ···

```

## jps使用方法

- `jps -q`
 
`jps -q`只会列出`Java`进程的`PID`
```bash
$ jps -q
13412
20808
```

- `jps -m`

`jps -m`可以列出传递给`main`方法的参数（如果是嵌入式`JVM`则输出为`null`）
```bash
$ jps -m
91552 RemoteMavenServer
91936 OomTest
92062 Jps -m
```

- `jps -l`

`jps -l`可以列出`Java`进程的`main`函数或者`JAR`包的完整包路径

```bash
$ jps -l
91552 org.jetbrains.idea.maven.server.RemoteMavenServer
91936 com/luzho211/oom/OomTest
92063 sun.tools.jps.Jps
```

- `jps -v`

`jps -v`可以列出`Java`进程所使用的`JVM`参数

```bash
$ jps -v
91936 OomTest -Xms20m -Xmx20m -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=./com/luzho211/oom/oom.hprof
92104 Jps -Denv.class.path=/Library/Java/JavaVirtualMachines/jdk1.8.0_211.jdk/Contents/Home/lib/tools.jar:/Library/Java/JavaVirtualMachines/jdk1.8.0_211.jdk/Contents/Home/lib/dt.jar:. -Dapplication.home=/Library/Java/JavaVirtualMachines/jdk1.8.0_211.jdk/Contents/Home -Xms8m
```


