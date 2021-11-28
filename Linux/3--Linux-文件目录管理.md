[TOC]

## 目录跳转

### `pwd` 打印当前工作目录

pwd 即 Print Working Directory
```shell
$ pwd
/home/ubuntu
```

### `ls` 列出目录内容

| 选项 | 长选项 | 描述 |
| --- | --- | --- |
| -a | --all | 列出所有文件，甚至包括文件名以圆点开头的隐藏文件。 |
| -d | --directory | 列出目录本身。通常，如果指定了目录名，ls 命令会列出这个目录中的内容，而不是目录本身。 把这个选项与 -l 选项结合使用，可以看到所指定目录的详细信息，而不是目录中的内容。 |
| -F | --classify | 这个选项会在每个所列出的名字后面加上一个指示符。目录/、可执行文件* 、链接文件@。 |
| -h | --human-readable | 以人们可读的格式，而不是以字节数来显示文件的大小。 |
| -l |  | 同时列出目录文件的属性信息。 |
| -r | --reverse | 以相反的顺序来显示结果。通常，ls 命令的输出结果按照字母升序排列。 |
| -S |  | 命令输出结果按照文件大小来排序。 |
| -t |  | 按照修改时间来排序。 |

* 列出当前目录下包含的文件及子目录列表：
```shell
$ ls
go1.11.linux-amd64.tar.gz  nodejs-v8.10.0  openca-tools-1.3.1  openca-tools-1.3.1.tar.gz  p2p  paxos  Paxos_cloud  work
```

* 列出指定目录下的内容：
```shell
$ ls /home/ubuntu/work
bin  pkg  src
```

* 列出多个指定目录的内容：
```shell
$ ls ~ /home/ubuntu/work
/home/ubuntu:
go1.11.linux-amd64.tar.gz  nodejs-v8.10.0  openca-tools-1.3.1  openca-tools-1.3.1.tar.gz  p2p  paxos  Paxos_cloud  work

/home/ubuntu/work:
bin  pkg  src
```

* 列出目录本身&列出目录下的内容：
```shell
# 列出目录本身：
$ ls -ld ./JavaScript
drwxr-xr-x  6 huqilin  staff  192  3 18 23:40 ./JavaScript

# 列出目录文件的属性信息：
$ ls -l ./JavaScript
total 0
drwxr-xr-x  14 huqilin  staff  448  5  3 11:00 Notes
drwxr-xr-x   6 huqilin  staff  192  4 17 13:19 book
drwxr-xr-x   4 huqilin  staff  128  3 18 23:39 sourceCode
```

* 列出所有文件（包括隐藏文件），并以长模式输出：
```shell
$ ls -al
total 20
drwxrwxr-x  5 ubuntu ubuntu 4096 Sep 17 04:59 .
drwxr-xr-x 13 ubuntu ubuntu 4096 Dec 20 03:54 ..
drwxrwxr-x  2 ubuntu ubuntu 4096 Sep 17 04:59 bin
drwxrwxr-x  3 ubuntu ubuntu 4096 Sep 17 04:58 pkg
drwxrwxr-x  3 ubuntu ubuntu 4096 Sep 17 04:58 src
```

* 递归列出目录及子目录：
```
$ ls -lR
```

### `tree` 以树状图列出目录的内容
`tree` 程序并非系统内建，需要安装后使用：`sudo apt install tree`。
```shell
# 参数：-A 表示使用 ASNI 绘图字符显示树状图而非以 ASCII 字符组合
$ tree -A
.
├── 20180930
│   └── 21_061028
│       └── recording_sys.log
├── main.cpp
├── main.o
├── Makefile
├── recorder_local
└── release
    └── bin
        └── recorder
```


### `cd` 更改当前工作目录

| 命令 | 运行结果 |
| --- | --- |
| cd | 更改工作目录到主目录。 |
| cd ~ | 更改工作目录到主目录。 |
| cd ~user_name | 更改工作目录到用户名为 user_name 的主目录。 |

```shell
# 跳转到绝对路径
$ cd /usr/bin

# 跳转到相对路径
$ cd ../pathName

# 返回上层目录
$ cd ..  

# 跳转到用户主目录 /Users/andy
$ cd  
$ cd ~
```

#### 绝对路径&相对路径
* 绝对路径从根目录开始，直到它的目的地，而相对路径开始于工作目录。
* 在几乎所有的情况下，你可以省略 "./"。它是隐含地。
* 如果不指定一个文件的目录，那它的工作目录会被假定为当前工作目录。

| 相对路径 | 描述 |
| --- | --- |
| / | 根目录 |
| . | 工作目录 |
| .. | 工作目录的父目录 |
| - | 前一个工作目录 |
| ~ | 用户 Home 目录 |


#### 关于文件名的重要规则
1. 以 "." 字符开头的文件名是隐藏文件。这仅表示，`ls` 命令不能列出它们， 除非使用 `ls -a` 命令。
2. 文件名和命令名是大小写敏感的。
3. Linux 没有 “文件扩展名” 的概念，不像其它一些系统。
4. 虽然 Linux 支持长文件名，文件名可能包含空格，标点符号，但标点符号仅限 使用 “.”，“－”，下划线。


### `file` 确定文件类型
格式：`file filename`

* 打印出文件内容的简单描述，以确定文件类型：
```shell
$ file openca-tools-1.3.1
openca-tools-1.3.1: directory

$ file go1.11.linux-amd64.tar.gz
go1.11.linux-amd64.tar.gz: gzip compressed data
```

### `cat` 连接文件

* `cat` 命令连接文件并打印到标准输出设备上，`cat` 经常用来显示文件的内容。
* 当文件较大时，文本会在屏幕上迅速闪过（滚屏），用户无法上下翻滚查看页面内容。因此，这时候一般用 `more` 、`less` 分屏显示。

| 参数 | 含义                      |
| ---- | -------------------------- |
| -n   | 显示行号                  |
| -A   | 显示所有内容，包括特殊符号 |

```shell
# 查看 Ubuntu 系统版本号
$ cat /etc/issue
Ubuntu 16.04.5 LTS \n \l

# 查看 Ubuntu 版本详细信息
$ sudo lsb_release -a
Distributor ID:	Ubuntu
Description:	Ubuntu 16.04.5 LTS
Release:	16.04
Codename:	xenial
```

#### 相关阅读
[cat 命令的源码进化史](http://blog.jobbole.com/114591/)

### `more` 按页显示文件内容

相比于 `cat` 命令，`more` 以全屏幕的方式按页显示文本文件的内容。

| 按键         | 功能         |
| ------------ | ------------ |
| B 键 | 向上翻页     |
| space 空格键 | 向下翻页     |
| Enter 键     | 向下滚动一行 |
| H 键         | 帮助         |
| Q 键         | 退出         |

### `less` 浏览文件内容
`less` 命令是一个用来浏览文本文件的程序。
格式：`less filename`

* 查看定义了系统中全部用户身份的文件：
```shell
$ less /etc/passwd
```

less 程序常见命令：

| 命令 | 行为 |
| --- | --- |
| PageUp or b | 向上翻滚一页 |
| PageDown or space | 向下翻动一页 |
| ↑ or j | 向前移动一行 |
| ↓ or k | 向后移动一行 |
| G | 移动到最后一行 |
| 1G or g | 移动到开头一行 |
| /charaters | 向下查找指定的字符串，按n显示下一个 |
| ?charaters | 向上查找指定的字符串，按n显示下一个 |
| n | 向前查找下一个出现的字符串，这个字符串是之前所指定查找的 |
| h | 显示帮助屏幕 |
| q | 退出 less 程序 |

#### less is more
`less` 程序是早期 Unix 程序 `more` 的改进版。“less” 这个名字，对习语 “less is more” 开了个玩笑， 这个习语是现代主义建筑师和设计者的座右铭。
`less` 命令允许用户向前或向后浏览文件，而 `more` 命令只能向前浏览。


### `tac` 反向打印文件的内容到屏幕上



## 操作文件和目录
使用命令行而不是图形文件管理器操作文件和目录的优势：**命令行程序功能强大灵活。**

### 通配符
通配符的作用：用特殊字符帮助你快速指定一组文件名。

| 通配符 | 意义 |
| --- | --- |
| * | 匹配任意多个字符（包括零个或一个） |
| ? | 	匹配任意一个字符（不包括零个） |
| [characters] | 匹配任意一个属于字符集中的字符 |
| [!characters] | 	匹配任意一个不是字符集中的字符 |
| [[:class:]] | 匹配任意一个属于指定字符类中的字符 |


| 字符类 | 意义 |
| --- | --- |
| [:alnum:] | 匹配任意一个字母或数字 |
| [:alpha:] | 匹配任意一个字母 |
| [:digit:] | 匹配任意一个数字 |
| [:lower:] | 匹配任意一个小写字母 |
| [:upper:] | 匹配任意一个大写字母 |

借助通配符，为文件名构建非常复杂的选择标准成为可能。下面是一些类型匹配的范例:

| 示例 | 匹配对象 |
| --- | --- |
| g | 文件名以 “g” 开头的文件 |
| b*.txt | 以 "b" 开头，中间有零个或任意多个字符，并以 ".txt" 结尾的文件 |
| Data??? | 以 “Data” 开头，其后紧接着 3 个字符的文件 |
| [abc] | 文件名以 "a","b", 或 "c" 开头的文件 |
| BACKUP.[0-9][0-9][0-9] | 以 "BACKUP." 开头，并紧接着 3 个数字的文件 |
| [[:upper:]] | 以大写字母开头的文件 |
| [![:digit:]] | 不以数字开头的文件 |
| *[[:lower:]123] | 文件名以小写字母结尾，或以 “1”，“2”，或 “3” 结尾的文件  |


### `cp` 复制文件和目录
格式：`cp item1 item2`

| 常用参数 | 意义 |
| --- | --- |
| -a, --archive | 复制文件和目录，以及它们的属性，包括所有权和权限。 通常，复本具有用户所操作文件的默认属性。 |
| -i, --interactive | 在重写已存在文件之前，提示用户确认。如果这个选项不指定， cp 命令会默认重写文件。 |
| -r, --recursive | 递归地复制目录及目录中的内容。当复制目录时， 需要这个选项（或者 -a 选项）。 |
| -u, --update | 当把文件从一个目录复制到另一个目录时，仅复制目标目录中不存在的文件，或者是文件内容新于目标目录中已经存在的文件。 |
| -v, --verbose | 显示翔实的命令操作信息 |

cp 命令示例

| 命令 | 运行结果 |
| --- | --- |
| cp file1 file2 | 复制文件 file1 内容到文件 file2。如果 file2 已经存在，file2 的内容会被 file1 的内容重写。如果 file2 不存在，则会创建 file2。 |
| cp -i file1 file2 | 这条命令和上面的命令一样，除了如果文件 file2 存在的话，在文件 file2 被重写之前， 会提示用户确认信息。 |
| cp file1 file2 dir1 | 复制文件 file1 和文件 file2 到目录 dir1。目录 dir1 必须存在。 |
| cp dir1/* dir2 | 使用一个通配符，在目录 dir1 中的所有文件都被复制到目录 dir2 中。 dir2 必须已经存在。 |
| cp -r dir1 dir2 | 复制目录 dir1 中的内容到目录 dir2。如果目录 dir2 不存在， 创建目录 dir2，操作完成后，目录 dir2 中的内容和 dir1 中的一样。 如果目录 dir2 存在，则目录 dir1(和目录中的内容) 将会被复制到 dir2 中。 |

```shell
# 将文件 test.php 复制到 test 目录下
$ cp test.php test/		

# cp 命令在执行复制任务时会自动跳过目录
# -r，将 test 目录及其子文件 复制到 Desktop 目录下
$ cp -r test/ Desktop/		
```

### `mv` 移动/重命名文件和目录
`mv` 命令可以执行文件移动和文件重命名任务，这依赖于你怎样使用它。任何一种情况下，完成操作之后，原来的文件名不再存在。
格式：`mv item1 item2`


| 常用参数 | 意义 |
| --- | --- |
| -i --interactive | 在重写一个已经存在的文件之前，提示用户确认信息。 如果不指定这个选项，mv 命令会默认重写文件内容。 |
| -b | 自动在同名文件后加“~”，避免文件覆盖。 |
| -u --update | 当把文件从一个目录移动另一个目录时，只是移动不存在的文件， 或者文件内容新于目标目录相对应文件的内容。 |
| -v --verbose | 当操作 mv 命令时，显示翔实的操作信息。 |

示例：

| 命令 | 运行结果 |
| --- | --- |
| mv file1 file2 | 移动 file1 到 file2。如果 file2 存在，它的内容会被 file1 的内容重写。 如果 file2 不存在，则创建 file2。 每种情况下，file1 不再存在。 |
| mv -i file1 file2  | 除了如果 file2 存在的话，在 file2 被重写之前，用户会得到提示信息外，这个和上面的选项一样。 |
| mv file1 file2 dir1 | 移动 file1 和 file2 到目录 dir1 中。dir1 必须已经存在。 |
| mv dir1 dir2 | 如果目录 dir2 不存在，创建目录 dir2， 并且移动目录 dir1 的内容到目录 dir2 中，同时删除目录 dir1。 如果目录 dir2 存在，移动目录 dir1（及它的内容）到目录 dir2。 |

示例：
```shell
# 将 Downloads 目录下的密钥文件移至 .ssh 目录
$ mv ~/Downloads/MyKeyPair.pem ~/.ssh/MyKeyPair.pem  

# 将桌面上的密钥文件(key-pair.pem)移至 .ssh 目录，并修改所属主只读权限
$ mv key-pair.pem ~/.ssh/
$ chmod 400 ~/.ssh/key-pair.pem
```

### `mkdir` 创建一个或多个目录
格式：`mkdir [-mp] dirName`
| 常用参数 | 意义                |
| ---- | -------------------- |
| -p   | 递归创建目录         |
| -m   | 指定要创建目录的权限 |

```shell
mkdir directory...
```
注意表示法: 在描述一个命令时（如上所示），当有三个圆点跟在一个命令的参数后面， 这意味着那个参数可以重复.

### `rmdir` 删除一个目录

⚠️ 该命令只能删除**空目录**，实际很少用。

| 参数 | 含义               |
| ---- | ------------------ |
| -p   | 将上级目录一起删除 |

### `rm` 删除文件和目录
格式：`rm item`
"item" 代表一个或多个文件或目录。


| 常用参数 | 意义 |
| --- | --- |
| -i, --interactive | 在删除已存在的文件前，提示用户确认信息。如果不指定这个选项，rm 会默默地删除文件 |
| -r, --recursive | 递归地删除文件，这意味着，如果要删除一个目录，而此目录又包含子目录，那么子目录也会被删除。要删除一个目录，必须指定这个选项。 |
| -f, --force | 忽视不存在的文件，不显示提示信息。这选项颠覆了 "--interactive" 选项。 |
| -v, --verbose | 	在执行 rm 命令时，显示翔实的操作信息。 |


| 命令 | 运行结果 |
| --- | --- |
| rm file1  | 默默地删除文件  |
| rm -i file1 | 	除了在删除文件之前，提示用户确认信息之外，和上面的命令作用一样。  |
| rm -r file1 dir1 | 删除文件 file1, 目录 dir1，及 dir1 中的内容。 |
| rm -rf file1 dir1 | 同上，除了如果文件 file1，或目录 dir1 不存在的话，rm 仍会继续执行。|

```shell
# 强制删除指定的目录或文件
rm -rf dirOrFile

# 删除 default 链接文件
lrwxrwxrwx 1 root root 34 Jun  4 06:03 default -> /etc/nginx/sites-available/default
$ sudo rm -i default
rm: remove symbolic link 'default'? yes
```

### `touch`  新建空文件

**touch 命令**有两个功能：

1. 是用于把已存在文件的时间标签更新为系统当前的时间（默认方式），它们的数据将原封不动地保留下来；
2. 是用来创建新的空文件。

语法：`touch [选项] [参数]`

选项：

```
-a：或--time=atime或--time=access或--time=use  只更改存取时间；
-c：或--no-create  不建立任何文件；
-d：<时间日期> 使用指定的日期时间，而非现在的时间；
-f：此参数将忽略不予处理，仅负责解决BSD版本touch指令的兼容性问题；
-m：或--time=mtime或--time=modify  只更该变动时间；
-r：<参考文件或目录>  把指定文件或目录的日期时间，统统设成和参考文件或目录的日期时间相同；
-t：<日期时间>  使用指定的日期时间，而非现在的时间；
--help：在线帮助；
--version：显示版本信息。
```

### `ln` 创建硬链接和符号链接
* 创建硬链接：`ln file link`
* 创建符号链接：`ln -s item link`

```shell
# 符号链接（软链接），带 -s，可用于目录
$ ln -s TARGET LINK_NAME    ## 给目标文件 TARGET 取了一个别名 LINK_NAME
# arlingbc -> /etc/nginx/sites-available/arlingbc
$ sudo ln -s /etc/nginx/sites-available/arlingbc arlingbc 

# 硬链接，不带 -s，不可用于目录
$ ln passwd passwd-hd    ## 建立一个名为 passwd-hd 硬链接指向文本文件 passwd
```

#### 硬链接 & 符号链接（软链接）
1. 一个硬链接不能关联它所在文件系统之外的文件。
2. 硬链接是两个完全独立的文件，拥有相同的内容，对其中一个文件的改动会反映在另一个文件中。
3. 硬链接不能关联一个目录，而符号链接可以关联目录。
4. 硬链接是直接引用，而符号链接是通过名称进行引用。
5. 你可以符号链接理解为成 Windows 下的快捷方式。


## 使用命令

命令的四种形式？
1. 可执行程序。可以编译成二进制文件或者由脚本写出的程序。
2. 内建于 shell 自身的命令。bash 支持若干命令，内部叫做 shell 内部命令。如 `cd`。
3. shell 函数。小规模的 shell 脚本。
4. 命名别名。基于其他命令而自己定义的命令。

### `type` 显示命令的类型
格式：`type command`
```shell
$ type pwd
pwd is a shell builtin

$ type ls
ls is aliased to `ls --color=auto'

$ type mv
mv is /bin/mv
```

### `which` 显示一个可执行程序的位置

* `which` 命令用于确定所给定的执行程序的绝对路径。
* `which` 是在 PATH 变量指定的路径中，搜索某个系统命令的位置，并且返回第一个搜索结果。
* 使用 `which` 命令，就可以看到**某个系统命令是否存在**，以及执行的到底是哪一个位置的命令。 
* 这个命令只对可执行程序有效，不包括内部命令和命令别名，别名是真正的可执行程序的替代物。

| 参数 | 含义                                                         |
| ---- | ------------------------------------------------------------ |
| -n   | 指定文件名长度，指定的长度必须大于或等于所有文件中最长的文件名。 |
| -p   | 与-n参数相同，但此处的文件名长度包含了文件的路径。           |
| -w   | 指定输出时栏位的宽度。                                       |
| -V   | 显示版本信息。   

```shell
$ which ls
/bin/ls

$ which docker
/usr/bin/docker

$ which openssl
/usr/bin/openssl
```

> 💡💡💡
> 与查找相关的命令：`whereis`、`locate`、`find`。

### `help` 得到 shell 内部命令的帮助文档
格式：`help command`
```shell
$ help cd
cd: cd [-L|[-P [-e]] [-@]] [dir] 
# 出现在命令语法说明中的方括号，表示可选的项目。一个竖杠字符(|)表示互斥选项。
    Change the shell working directory.
    ...
```

### `--help` 显示用法信息
显示某个命令所支持的语法和选项说明。
格式：`command --help`

### `man` 显示命令手册页

格式：`man program`

* `man` 命令是 Linux 下的帮助指令，通过 `man` 指令可以查看 Linux 中的指令帮助、配置文件帮助和编程帮助等信息。
* 在大多数 Linux 系统中，`man` 使用 `less` 工具来显示参考手册
* `man` 所显示的参考手册，被分成几个章节,手册页的组织形式如下：

| 章节 | 内容 |
| --- | --- |
| 1 | 用户命令 |
| 2 | 程序接口内核系统调用 |
| 3 | C 库函数程序接口 |
| 4 | 特殊文件，比如说设备结点和驱动程序 |
| 5 | 文件格式 |
| 6 | 游戏娱乐，如屏幕保护程序 |
| 7 | 其他方面 |
| 8 | 系统管理员命令 |

查看特定章节语法：`man section search_term`

```shell
man 5 passwd
# 命令运行结果会显示文件 /etc/passwd 的文件格式说明手册。
```

### `apropos` 显示适当的命令
`apropos` 命令在一些特定的包含系统命令的简短描述的数据库文件里查找关键字，然后把结果送到标准输出。
格式：`apropos keyword`


### `info` 显示命令 info 条目
格式：`info command`

* GNU 项目提供了一个命令程序手册页的替代物，称为 `info`。
* info 页是超级链接形式的，和网页很相似。info 内容可通过 info 阅读器程序读取。
控制 info 阅读器的 info 命令：

| 命令 | 行为 |
| --- | --- |
| ? | 显示命令帮助 |
| PgUp or Backspace | 显示上一页 |
| PgDn or Space | 显示下一页 |
| n | next，下一个 - 显示下一个结点 |
| p | preview，上一个 - 显示上一个结点 |
| u | Up - 显示当前所显示结点的父结点，通常是个菜单 |
| Enter | 激活光标位置下的超级链接 |
| q | 退出 |

显示 GNU 项目 "coreutils" 包中的命令行程序信息：
```
$ info coreutils
```

### `whatis` 显示一个命令的简洁描述
whatis 程序显示匹配特定关键字的手册页的名字和一行命令说明。


### README 和其他程序文档
许多安装在你系统中的软件，都有自己的文档文件，这些文件位于 **/usr/share/doc** 目录下。 

### `alias` 用别名创建你自己的命令

> 「设置别名」可以理解为输入法中的「文本替换」功能。

格式：`alias name='string'`

在命令行中定义别名的问题：当你的 shell 会话结束时，它们会消失。

💡 Tips：可以使用“;”将多个命令放在同一行上：`command1; command2; command3;`


别名创建示例：
```shell
# 1⃣️ 使用 type 命令查看别名是否已存在
$ type foo
-bash: type: foo: not found

# 2⃣️ 创建自定义命令，定义别名
$ alias foo='cd /usr; ls -l; cd ~'

# 3⃣️ 执行自定义命令
$ foo
total 68
drwxr-xr-x   2 root root 36864 Jan 10 06:19 bin
drwxr-xr-x   2 root root  4096 Apr 12  2016 games
drwxr-xr-x  36 root root  4096 Sep 17 04:54 include
drwxr-xr-x  67 root root  4096 Nov  8 06:45 lib
drwxr-xr-x  11 root root  4096 Sep 17 04:33 local
drwxr-xr-x   2 root root  4096 Jan 10 06:19 sbin
drwxr-xr-x 128 root root  4096 Sep 17 09:48 share
drwxr-xr-x   3 root root  4096 Sep  7 02:27 soft
drwxr-xr-x  14 root root  4096 Dec 21 22:46 src

# 4⃣️ 使用 type 命令查看自己的别名
$ type foo
foo is aliased to `cd /usr; ls -l; cd ~'

# 5⃣️ 删除别名
$ unalias foo

# 6⃣️ 使用 type 命令查看别名是否存在
$ type foo
-bash: type: foo: not found
```
要查看所有定义在系统环境中的别名，使用不带参数的 alias 命令：
```
$ alias
alias alert='notify-send --urgency=low -i "$([ $? = 0 ] && echo terminal || echo error)" "$(history|tail -n1|sed -e '\''s/^\s*[0-9]\+\s*//;s/[;&|]\s*alert$//'\'')"'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l='ls -CF'
alias la='ls -A'
alias ll='ls -alF'
alias ls='ls --color=auto'
```
