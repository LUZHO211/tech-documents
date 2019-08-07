# jmap

- 查找`Java`的`PID`：`jps -l`或者`ps -ef | grep java`

```bash
$ jps -l
1271 sun.tools.jps.Jps
1215 com.luzho211.dk.DeadLock
```

- 查看该`PID`进程下的**线程**所占用的系统资源情况：`top -Hp 1215`

```bash
$ top -Hp 1215
 PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND                                                                                   
 1224 root      20   0 2152132  22164  15184 S  0.3  2.2   0:00.02 java                                                                                      
 1215 root      20   0 2152132  22164  15184 S  0.0  2.2   0:00.00 java                                                                                      
 1216 root      20   0 2152132  22164  15184 S  0.0  2.2   0:00.04 java                                                                                      
 1217 root      20   0 2152132  22164  15184 S  0.0  2.2   0:00.00 java                                                                                      
 1218 root      20   0 2152132  22164  15184 S  0.0  2.2   0:00.00 java                                                                                      
 1219 root      20   0 2152132  22164  15184 S  0.0  2.2   0:00.00 java                                                                                      
 1220 root      20   0 2152132  22164  15184 S  0.0  2.2   0:00.00 java                                                                                      
 1221 root      20   0 2152132  22164  15184 S  0.0  2.2   0:00.00 java                                                                                      
 1222 root      20   0 2152132  22164  15184 S  0.0  2.2   0:00.00 java                                                                                      
 1223 root      20   0 2152132  22164  15184 S  0.0  2.2   0:00.00 java                                                                                      
 1225 root      20   0 2152132  22164  15184 S  0.0  2.2   0:00.00 java                                                                                      
 1226 root      20   0 2152132  22164  15184 S  0.0  2.2   0:00.00 java 
```

- `top`指令列出的是本地线程`ID`的十进制表示，转换成16进制：

```bash
printf '%x\n' 1224
4c8
```

- `jstack`查看`Java`进程中的线程栈信息：注意看`nid=0x4c8`的线程，就是上一步得到的本地线程`ID`

```bash
$ jstack 1215
"VM Thread" os_prio=0 tid=0x00007f570c06f000 nid=0x4c1 runnable 

"VM Periodic Task Thread" os_prio=0 tid=0x00007f570c0b8000 nid=0x4c8 waiting on condition 

JNI global references: 5


Found one Java-level deadlock:
=============================
"Thread-1":
  waiting to lock monitor 0x00007f56f00062c8 (object 0x00000000f065ab38, a java.lang.Object),
  which is held by "Thread-0"
"Thread-0":
  waiting to lock monitor 0x00007f56f0004e28 (object 0x00000000f065ab48, a java.lang.Object),
  which is held by "Thread-1"

```

- 生成`Java`堆文件：

```bash
$ jmap -dump:format=b,file=./heap.hprof 1215
```