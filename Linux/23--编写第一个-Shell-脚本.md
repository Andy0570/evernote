[TOC]


### 什么是 Shell 脚本

一个 shell 脚本就是一个包含一系列命令的文件。shell 读取这个文件，然后执行文件中的所有命令。


### 怎样编写一个 Shell 脚本

创建和运行 Shell 脚本的步骤：
1. 使用文本编辑器编写脚本。 Shell 脚本就是普通的文本文件。
2. 使脚本文件可执行。设置脚本文件的权限来允许其可执行。
3. 把脚本放置到 shell 能够找到的地方。当没有指定可执行文件明确的路径名时，shell 会自动地搜索某些目录，来查找此可执行文件。为了最大程度的方便，我们会把脚本放到这些目录当中。



### 脚本文件格式

1. 编辑脚本文件：hello_world
```bash
#!/bin/bash
# This is our first script.
echo 'Hello World!'
```
* 第一行的 #! 字符序列是一种特殊的结构叫做 shebang。 这个 shebang 被用来告诉操作系统将执行此脚本所用的解释器的名字。每个 shell 脚本都应该把这一文本行作为它的第一行。


2. 修改文件权限：

```bash
$ ls -l hello_world
-rw-r--r-- 1  me    me      63  2009-03-07 10:10 hello_world
$ chmod 755 hello_world
$ ls -l hello_world
-rwxr-xr-x 1  me    me      63  2009-03-07 10:10 hello_world
```

> 对于脚本文件，有两个常见的权限设置：
> * 权限为 755 的脚本，则每个人都能执行，
> * 权限为 700 的脚本，只有文件所有者能够执行。


### 脚本文件位置

为了运行脚本，必须指定脚本文件明确的路径。
```bash
$ ./hello_world
```

如果没有给出可执行程序的明确路径名，那么系统每次都会搜索一系列的目录，来查找此可执行程序。
```bash
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

修改 .bashrc 文件中包含下面一行文本：
```bash
export PATH=~/bin:"$PATH"
```
使修改后的 .bashrc 文件立即生效：
```bash
$ . .bashrc

# 同上
$ source .bashrc
```
> 注意：在 Ubuntu 系统中，如果存在 ~/bin 目录，当执行用户的 .bashrc 文件时， Ubuntu 会自动地添加这个 ~/bin 目录到 PATH 变量中。所以在 Ubuntu 系统中，如果我们创建 了这个 ~/bin 目录，随后退出，然后再登录，一切会正常运行。

然后把你的脚本文件放在：/home/YourName/bin 目录下。

#### 脚本文件应该放在哪里

* 【**个人**】：~/bin 目录是存放为个人所用脚本的好地方。
* 【**所有人**】：如果我们编写了一个脚本，系统中的每个用户都可以使用它，那么这个脚本的传统位置是 /usr/local/bin。
* 【**系统管理员**】：系统管理员使用的脚本经常放到 /usr/local/sbin 目录下。
* 【**本地软件**】：大多数情况下，本地支持的软件，不管是脚本还是编译过的程序，都应该放到 /usr/local 目录下， 而不是在 /bin 或 /usr/bin 目录下。这些目录都是由 Linux 文件系统层次结构标准指定，只包含由 Linux 发行商 所提供和维护的文件。


### 更多的格式技巧

严肃认真地写脚本的目标——方便维护。
使脚本容易阅读和理解是一种方便维护的方法。

#### 1. 长选项名称
```bash
# 1.在命令行中输入选项时，为了减少输入，短选项更受欢迎。
$ ls -ad

# 2.在脚本文件中，长选项能提供可读性。
$ ls --all --directory
```

#### 2. 缩进和行继续符

当使用长命令时，通过把命令在几个文本行中展开，可以提高命令的可读性。 
使用行继续符（反斜杠 - 回车符序列）和缩进，可以让一个复杂命令的逻辑性更清楚地描述给读者。

❎ 不适合的示例：
```bash
$ find playground \( -type f -not -perm 0600 -exec
chmod 0600 ‘{}’ ‘;’ \) -or \( -type d -not -perm 0711 -exec chmod
0711 ‘{}’ ‘;’ \)
```

✅ 推荐的示例：
```bash
find playground \
    \( \
        -type f \
        -not -perm 0600 \
        -exec chmod 0600 ‘{}’ ‘;’ \
    \) \
    -or \
    \( \
        -type d \
        -not -perm 0711 \
        -exec chmod 0711 ‘{}’ ‘;’ \
    \)
```

#### 3. 为书写脚本文件配置 vim 编辑器

* 打开语法高亮：
`:syntax on` 或者 `:set hlsearch`。

* 设置一个 tab 字符所占据的列数。默认是 8 列。把这个值设置为 4（一种常见做法）， 从而让长文本行更容易适应屏幕。停止缩进，输入 Ctrl+D。
`:set tabstop=4`

* 打开 "auto indent" 功能。这导致 vim 能对新的文本行缩进与刚输入的文本行相同的列数。
`:set autoindent`

通过把这些命令（没有开头的冒号字符）添加到你的 ~/.vimrc 文件中，这些改动会永久生效。

