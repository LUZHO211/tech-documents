## 1 Windows安装JDK
### 1.1 下载并安装JDK
- 到[Oracle官网](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下载适合自己系统的`JDK`版本，本文将使用`jdk-8u221-windows-x64.exe`这个版本来做演示。
- 双击`jdk-8u221-windows-x64.exe`进行安装，并记录安装的目录，这里安装目录为`D:\Program files\Java\jdk1.8.0_221`

### 1.2 配置Java环境变量


## 2 Ubuntu安装JDK
### 2.1 下载JDK并解压
- 到[Oracle官网](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下载适合自己系统的`JDK`版本，本文将使用`jdk-8u221-linux-x64.tar.gz`这个版本来做演示。
- 将下载的文件放到某个目录下，例如`/usr/local`
```bash
$ mv jdk-8u221-linux-x64.tar.gz /usr/local/
```

- `cd /usr/local/`进入`JDK`压缩文件所在目录并解压文件，将得到`jdk1.8.0_221`文件夹：
```bash
$ tar -zxvf jdk-8u221-linux-x64.tar.gz
```
**即，`JDK`的安装路径为：`/usr/local/jdk1.8.0_221`，后面环境变量`JAVA_HOME`就是设置成`JDK`的安装路径。**

### 2.2 配置Java环境变量
- 编辑`.bashrc`文件（`vim ~/.bashrc`），在文件的尾部追加以下内容：
```bash
export JAVA_HOME=/usr/local/jdk1.8.0_221
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

```bash
$ javac
Usage: javac <options> <source files>
where possible options include:
  -g                         Generate all debugging info
  -g:none                    Generate no debugging info
  -g:{lines,vars,source}     Generate only some debugging info
  -nowarn                    Generate no warnings
  -verbose                   Output messages about what the compiler is doing
  -deprecation               Output source locations where deprecated APIs are used
  -classpath <path>          Specify where to find user class files and annotation processors
  -cp <path>                 Specify where to find user class files and annotation processors

··· ···
```

- 出现以上信息说明成功安装`JDK`并且环境变量配置正确。否则需要检查环境变量的配置是否正确。
