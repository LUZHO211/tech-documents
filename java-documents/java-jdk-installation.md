# Linux下安装JDK

## Ubuntu安装JDK

### 1 下载JDK并解压
到[Oracle官网](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下载JDK：`jdk-8u221-linux-x64.tar.gz`

将下载的文件放到某个目录下，例如`/usr/local`
```bash
$ mv jdk-8u221-linux-x64.tar.gz /usr/local/
```

解压文件，将得到`jdk1.8.0_221`文件夹：
```bash
$ tar -zxvf jdk-8u221-linux-x64.tar.gz
```

**即，`JDK`的安装路径为：`/usr/local/jdk1.8.0_221`，后面环境变量`JAVA_HOME`就是设置成`JDK`的安装路径。**

### 2 配置Java环境变量
- 编辑`.bashrc`文件（`vim ~/.bashrc`），在文件的尾部追加以下内容：
```bash
export JAVA_HOME=/usr/local/jdk1.8.0_221
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$JAVA_HOME/bin:$PATH
```

- 编辑完毕之后，执行命令：`source ~/.bashrc`来使`.bashrc`文件立即生效。
```bash
$ source ~/.bashrc
```

- 最后，检验一下`JDK`是否安装以及环境变量是否配置正确：
```bash
$ java -version
java version "1.8.0_221"
Java(TM) SE Runtime Environment (build 1.8.0_221-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.221-b11, mixed mode)
```

- 出现以上信息说明成功安装`JDK`并且环境变量配置正确。否则需要检查环境变量的配置是否正确。
