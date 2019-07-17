# Mac OS安装Python3

`Mac OS`系统自带的`Python`版本是`2.7.x`，由于`Python`的`2.x版本`与`3.x版本`不兼容，所以这里选择安装最新的`3.x版本`来学习。

安装`Python3`
```bash
$ brew install python3
```

安装完毕之后，`Python3`和`Python2`会并存于系统中。

- 使用`Python2`可以直接使用`python`命令：

```bash
$ python
Python 2.7.10 (default, Feb 22 2019, 21:55:15) 
[GCC 4.2.1 Compatible Apple LLVM 10.0.1 (clang-1001.0.37.14)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

- 使用`Python3`可以直接使用`python3`命令：

```bash
$ python3
Python 3.7.4 (default, Jul  9 2019, 18:13:23) 
[Clang 10.0.1 (clang-1001.0.46.4)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

使用`brew install python3`安装`Python3`时，`Homebrew`也会同时安装`Python`包管理工具`pip3`，可以使用`pip3`来安装`Python`依赖包：

```bash
$ pip3 install beautifulsoup4
Collecting beautifulsoup4
  Downloading https://files.pythonhosted.org/packages/1d/5d/3260694a59df0ec52f8b4883f5d23b130bc237602a1411fa670eae12351e/beautifulsoup4-4.7.1-py3-none-any.whl (94kB)
     |████████████████████████████████| 102kB 44kB/s 
Collecting soupsieve>=1.2 (from beautifulsoup4)
  Downloading https://files.pythonhosted.org/packages/35/e3/25079e8911085ab76a6f2facae0771078260c930216ab0b0c44dc5c9bf31/soupsieve-1.9.2-py2.py3-none-any.whl
Installing collected packages: soupsieve, beautifulsoup4
Successfully installed beautifulsoup4-4.7.1 soupsieve-1.9.2
```
