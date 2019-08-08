# Linux如何设置默认的文本编辑器

## 1 方法一：直接定义系统变量
设置系统环境变量`EDITOR`，将变量的值指向钟意的编辑器路径：

```bash
export EDITOR=/usr/bin/vim
```

**例如在`Ubuntu`发行版下：**

- 编辑`~/.bashrc`文件，在文件中追加`export EDITOR=/usr/bin/vim`来定义并`export`环境变量；
- `source ~/.bashrc`使变量立即生效，这样每次登录之后都会生效。

## 2 方法二：`update-alternatives --config editor`

运行指令`update-alternatives --config editor`来设置系统默认的编辑器：

```bash
$ update-alternatives --config editor
```

然后会出现各种编辑器的列表，让你选择。选一个你钟意:sparkling_heart:的编辑器即可：

```bash
There are 4 choices for the alternative editor (providing /usr/bin/editor).

  Selection    Path                Priority   Status
------------------------------------------------------------
  0            /bin/nano            40        auto mode
  1            /bin/ed             -100       manual mode
  2            /bin/nano            40        manual mode
  3            /usr/bin/vim.basic   30        manual mode
* 4            /usr/bin/vim.tiny    10        manual mode

Press <enter> to keep the current choice[*], or type selection number: 4
```

如果这个命令设置完成后还是没有改过来，那么再运行下面的命令：

```bash
$ select-editor
```

同样的，会出现各种编辑器的列表，让你选择。选一个你钟意:sparkling_heart:的编辑器即可：

```bash
Select an editor.  To change later, run 'select-editor'.
  1. /bin/ed
  2. /bin/nano        <---- easiest
  3. /usr/bin/vim.basic
  4. /usr/bin/vim.tiny

Choose 1-4 [2]: 4
```