# wc命令

`wc`命令用于统计文件中的**字数（`word`）**、**字符数（`character`）**、**字节数（`byte`）** 以及 **行数（`newline`）** 等信息。

- 使用语法

```bash
$ wc [OPTION]... [FILE]...
```

|OPTION|OPTION说明|
|:---|:---|
|`-w`或`--words`|统计文件中的**字数（`word counts`）**|
|`-m`或`--chars`|统计文件中的**字符数（`character counts`）**|
|`-c`或`--bytes`|统计文件中的**字节数（`byte counts`）**|
|`-l`或`--lines`|统计文件中的**行数（`newline counts`）**|
|`-L`或`--max-line-length`|统计文件中长度最长的那一行的宽度|

## wc使用例子

假设有文件`foo.txt`内容如下：

```bash
line1: user login ok.
line2: user login ok.
line3: user login ok.
line4: user login ok.
line5: user login ok.
line6: user logout.
line7: user logout.
line8: user logout.
line9: user logout.
line10: user logout.
```

- 不实用任何`OPTION`将会依次输出文件的行数、字数以及字节数：

```bash
$ wc foo.txt
10  35 211 foo.txt
```

上面的输出信息为：文件一共有`10`行，`35`个单词以及文件大小为`211`字节。

- 如果只需要输出行数，就加上`-l`参数：

```bash
$ wc -l foo.txt
10 foo.txt
```

其他参数使用方式同上。

实际应用中，我们可能需要从某个日志文件（例如`login.log`文件）中筛选出某些特定日志，比如登录接口的日志`/login`；查看这个接口的调用量：

```bash
$ cat login.log | grep "/login" | wc -l
```

使用管道的方式先过滤出登录接口的数据，然后再通过`wc -l`来计算一共有多少行，就能知道该接口的调用量。
