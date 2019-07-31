## 1 Windows系统安装JDK
- **本文使用`Windows 10`系统版本来做演示。**

### 1.1 下载并安装JDK
- 到[Oracle官网](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下载适合自己系统的`JDK`版本，本文将使用`jdk-8u221-windows-x64.exe`这个版本来做演示。
- 双击`jdk-8u221-windows-x64.exe`进行安装，并记录安装的目录，这里安装目录为`D:\Program files\Java\jdk1.8.0_221`

### 1.2 配置Java环境变量


## 2 Linux系统安装JDK
- **本文使用`Ubuntu 16.04.6 LTS`系统版本来做演示。**

### 2.1 下载并解压（安装）JDK
- 到[Oracle官网](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下载适合自己系统的`JDK`版本，本文将使用`jdk-8u221-linux-x64.tar.gz`这个版本来做演示。
- 将下载的文件放到某个目录下，例如`/usr/local`

```bash
$ mv jdk-8u221-linux-x64.tar.gz /usr/local/
```

- `cd /usr/local/`进入`JDK`压缩文件所在目录并解压文件（**解压完毕即安装成功**），将得到`jdk1.8.0_221`文件夹：

```bash
$ tar -zxvf jdk-8u221-linux-x64.tar.gz
```

**即，`JDK`的安装路径为：`/usr/local/jdk1.8.0_221`，后面环境变量`JAVA_HOME`就是设置成`JDK`的安装路径。**

### 2.2 配置Java环境变量
- 在用户主目录下编辑`.bashrc`文件（`vim ~/.bashrc`），在文件尾部追加以下内容：

```bash
export JAVA_HOME=/usr/local/jdk1.8.0_221
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$JAVA_HOME/bin:$PATH
```

- 编辑完毕之后，执行命令：`source ~/.bashrc`来使`.bashrc`文件立即生效。
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

**出现以上信息说明成功安装`JDK`并且环境变量配置正确。否则需要检查环境变量的配置是否正确。**

## 3 Mac OS系统安装JDK
- **本文使用`macOS Mojave 10.14.6`系统版本来做演示。**

### 3.1 下载并安装JDK
- 到[Oracle官网](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下载适合自己系统的`JDK`版本，本文将使用`jdk-8u221-macosx-x64.dmg`这个版本来做演示。
- 下载完毕之后，双击`jdk-8u221-macosx-x64.dmg`文件，并按照引导一路点击下去即可完成安装。

### 3.2 配置Java环境变量
- 打开`Terminal`，执行`/usr/libexec/java_home -V`指令来查看`JDK`的安装路径：

```bash
$ /usr/libexec/java_home -V
Matching Java Virtual Machines (1):
    1.8.0_221, x86_64:	"Java SE 8"	/Library/Java/JavaVirtualMachines/jdk1.8.0_221.jdk/Contents/Home

/Library/Java/JavaVirtualMachines/jdk1.8.0_221.jdk/Contents/Home
```

**`/Library/Java/JavaVirtualMachines/jdk1.8.0_221.jdk/Contents/Home`就是`JDK`的安装目录，后面配置`JAVA_HOME`环境变量会使用到。**

- 在用户主目录下编辑`.bash_profile`文件（`vim ~/.bash_profile`），在文件中加入以下内容：

```bash
JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_221.jdk/Contents/Home
CLASSPATH=.:$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib/dt.jar
PATH=$JAVA_HOME/bin:$PATH:.

export JAVA_HOME
export CLASSPATH
export PATH
```

- 编辑完毕之后，执行命令：`source ~/.bash_profile`来使`.bash_profile`文件立即生效。
- 最后，检验一下`JDK`是否安装以及环境变量是否配置正确：

```bash
$ java -verion
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

**出现以上信息说明成功安装`JDK`并且环境变量配置正确。否则需要检查环境变量的配置是否正确。**