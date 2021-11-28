[TOC]

## 一、文件压缩程序

数据压缩是一个删除冗余数据的过程。游程编码是最基本的数据压缩技术。

压缩算法：
1. 无损压缩：保留原始文件的所有数据。
2. 有损压缩：执行压缩操作时会删除数据，允许更大的压缩。
 
### `gzip` 文件压缩与解压

`gzip` 程序被用来压缩一个或多个**文件**(⚠️  `gzip` 不可以压缩目录)。当执行 `gzip` 命令时，原始文件的压缩版会替代原始文件。相对应的 `gunzip` 程序被用来把压缩文件复原为没有被压缩的版本。


|gzip 选项|	说明|
|--------|------|
|-c	|把输出写入到标准输出，并且保留原始文件。也有可能用 --stdout 和 --to-stdout 选项来指定。|
|-d	|解压缩。正如 gunzip 命令一样。也可以用 --decompress 或者 --uncompress 选项来指定.|
|-f	|强制压缩，即使原始文件的压缩文件已经存在了，也要执行。也可以用 --force 选项来指定。|
|-h	|显示用法信息。也可用 --help 选项来指定。|
|-l	|列出每个被压缩文件的压缩数据。也可用 --list 选项。|
|-r	|若命令的一个或多个参数是目录，则递归地压缩目录中的文件。也可用 --recursive 选项来指定。|
|-t	|测试压缩文件的完整性。也可用 --test 选项来指定。|
|-v	|显示压缩过程中的信息。也可用 --verbose 选项来指定。|
|-number	|设置压缩指数。number 是一个在 1（最快，最小压缩）到 9（最慢，最大压缩）之间的整数。 数值 1 和 9 也可以各自用 --fast 和 --best 选项来表示。默认值是整数 6。|

简单示例：
```
# 压缩
$ gzip linux_book_bak.tar        ## 压缩一个打包文件
$ gzip -l linux_book_bak.tar     ## 查看压缩效果
$ gzip -tv linux_book_bak.tar    ## 测试压缩文件完整性

# 解压缩
$ gunzip linux_book_bak.tar.gz     ## 解压缩文件
$ gzip -d linux_book_bak.tar.gz    ## 同上
```

完整示例：
```shell
# 创建一个文本文件，其内容包含一个目录的列表清单。
$ ls -l /etc > foo.txt

# 查看文件大小（11K）
$ ls -lh foo.*
-rw-r--r-- 1 root root 11K Jan 19 09:34 foo.txt

# 压缩文件，同时会删除源文件。
$ gzip foo.*

# -t 测试压缩文件的完整性
gzip -tv foo.*
foo.txt.gz:      OK

# 查看压缩文件大小（2.3k）
$ ls -lh foo*
-rw-r--r-- 1 root root 2.3K Jan 19 09:34 foo.txt.gz

# 不解压缩，只是浏览压缩文本文件的内容
$ gunzip -c foo.* | less

# 解压缩文件
$ gunzip foo.*
$ gzip -d foo* # 同上

# 查看解压缩文件大小（11K）
$ ls -lh foo.*
-rw-r--r-- 1 root root 11K Jan 19 09:34 foo.txt
```

> 相关命令：`zcat`、`zless`。


### `bzip2` 块排序文件压缩器
* `bzip2` 与 `gzip` 程序相似，但是使用了不同的压缩算法，舍弃了压缩速度，实现了更高的压缩级别。
* 由 `bzip2` 压缩的文件，用扩展名 .bz2 来表示。
* 伴随着 `bzip2` 程序，有 `bunzip2` 和 `bzcat` 程序来解压缩文件。
* bzip2 文件也带有 `bzip2recover` 程序，其会试图恢复受损的 .bz2 文件。

语法：`bzip2 [-dz] filename` 
* -d：解压缩；
* -z：压缩。


示例代码：
```shell
# 压缩文件
$ bzip2 foo.txt

# 查看压缩文件大小（2.1k）
$ ls -lh foo.*
-rw-r--r-- 1 root root 2.1K Jan 19 09:34 foo.txt.bz2

# 解压缩文件
$ bunzip2 foo.*

# 查看解压缩文件大小（11k）
$ ls -lh foo.*
-rw-r--r-- 1 root root 11K Jan 19 09:34 foo.txt
```

> 💡💡💡
> 【不要强迫性压缩】
> **不要压缩一个已经被压缩过的文件**。 如果你再次压缩已经压缩过的文件，实际上你会得到一个更大的文件。


## 二、 归档程序

**归档**就是收集许多文件，并把它们捆绑成一个大文件的过程。

打包&压缩：
* **打包（归档）**：将一大堆文件或目录变成一个总的文件；
* **压缩**：将一个大的文件通过一些压缩算法变成一个小文件。

| 文件名后缀 | 压缩工具                                                     |
| ---------- | ------------------------------------------------------------ |
| .gz        | gzip 压缩工具压缩的文件                                      |
| .bz2       | bzip2 压缩工具压缩的文件                                     |
| .tar       | tar 打包程序打包的文件 (tar 并没有压缩功能，只是把一个目录合并成一个文件) |
| .tar.gz    | 可以理解为先用 tar 打包，然后再 gzip 压缩                    |
| .tar.bz2   | 同上，先用 tar 打包，然后再 bzip2 压缩                       |

### `tar` 磁带打包工具

* 利用 `tar` （tape archive，磁带存档）命令，可以把一大堆的文件和目录全部打包成一个文件，这对于备份文件或将几个文件组合成为一个文件以便于网络传输是非常有用的。
* 我们经常看到扩展名为 .tar 或者 .tgz 的文件，它们各自表示 “普通” 的 tar 包和被 `gzip` 程序压缩过的 tar 包。
* 设置 `tar` 命令参数时，必须首先指定模式，然后才是其它的选项。

|tar 模式	|说明|
|--------|----|
|c	|为文件和／或目录列表创建归档文件。|
|x	|抽取归档文件。|
|r	|追加具体的路径到归档文件的末尾。|
|t	|列出归档文件的内容。|

* 默认情况下，路径名是相对的，而不是绝对路径。

语法：`tar [-zjxcvfpP] filename`

| 参数 | 含义                                                         |
| ---- | ------------------------------------------------------------ |
| -z   | 是否同时用 gzip 压缩                                         |
| -j   | 是否同时用 bzip2 压缩                                        |
| -x   | 解包或者解压缩                                               |
| -t   | 查看 tar 包里面的文件                                        |
| -c   | 创建归档文件                                                 |
| -v   | 可视化，显示命令的执行过程                                   |
| -f   | 后面跟文件名，压缩时跟 -f 文件名，指定归档文件名，解压时跟 -f 文件名，意思是解压filename。请注意，如果是多个参数组合的情况下带有 -f，请把 f 写到最后面。 |
| -p   | 使用原文件的属性，压缩前什么属性压缩后还什么属性。（不常用） |
| -P   | 可以使用绝对路径。（不常用）                                 |


简单示例：
```shell
# 打包文件
$ tar -cvf shell.tar shell/        ## 将 shell 目录连同其下的文件一同打包成文件 shell.tar
$ tar -czvf shell.tar.gz shell/    ## 打包并使用 gizp 压缩文件

# 解开文件
$ tar -xvf shell.tar
$ tar -xzvf shell.tar.gz          ## 先调用 gzip 解压缩，再解开文件。
```

完整示例：
```shell
# -p：递归创建目录
$ mkdir -p playground/dir-{00{1..9},0{10..99},100}
# 创建空文件夹
$ touch playground/dir-{00{1..9},0{10..99},100}/file-{A-Z}

# 打包文件
# ⚠️ 参数不需要开头的短横线
tar cf playground.tar playground

# 列出包文件内容
tar tf playground.tar
tar tvf playground.tar

# 抽取包文件内容到新目录
$ mkdir foo
$ cd foo
$ tar xf ../playground.tar
$ ls
playground

# 从归档文件(archive.tar)中抽取单个文件
$ tar xf archive.tar pathname
```

GNU 版本的 `tar` 命令（在 Linux 发行版中最常出现）通过 --wildcards 选项来支持通配符：
```shell
$ tar xf ../playground2.tar --wildcards 'home/me/playground/dir-*/file-A'
```

`tar` 命令经常结合 `find` 命令一起来制作归档文件。
```shell
# 查找某个特定的文件集合，归档到一个文件中：
#  - exec：唤醒带有追加模式（r）的 tar 命令。
$ find playground -name 'file-A' -exec tar rf playground.tar '{}' '+'
```

现在的 GUN 版本的 `tar` 命令 ，`gzip` 和 `bzip2` 压缩两者都直接支持，各自使用 z 和 j 选项。
```shell
# gzip 压缩：z
$ find playground -name 'file-A' | tar czf playground.tgz -T -

# bzip2 压缩：j
$ find playground -name 'file-A' | tar cjf playground.tbz -T -
```


### `zip` 打包和压缩文件

`zip` 程序既是压缩工具，也是一个打包工具。然而，在 Linux 中，`gzip` 是主要的压缩程序，而 `bzip2` 则位居第二。
格式：`zip options zipfile file...`
 
示例：
```shell
# 除非包含 -r 选项，要不然只有 playground 目录（没有任何它的内容）被存储。
$ zip -r playground.zip playground
```

使用 `unzip` 程序，来直接抽取一个 zip 文件的内容。
```shell
$ unzip ../playground.zip

# - l，只列出文件包中的内容而没有抽取文件。
$ unzip -l playground.zip playground/dir-87/file-Z

# 有选择地从一个 zip 文件包中抽取文件，只要给 unzip 命令指定文件名：
unzip ./playground.zip playground/dir-87/file-Z

```

对于 `zip` 命令（与 `tar` 命令相反）要注意一点，就是如果指定了一个已经存在的文件包，其被更新而不是被替代。这意味着会保留此文件包，但是会添加新文件，同时替换匹配的文件。

## 三、同步文件和目录
维护系统备份的常见策略是保持一个或多个目录与另一个本地系统（通常是某种可移动的存储设备）或者远端系统中的目录（或多个目录）同步。

### `rsync` 远程数据同步工具

* `rsync` 程序能同步本地与远端的目录，通过使用 `rsync` 远端更新协议，允许 `rsync` 快速地检测两个目录的差异，执行最小量的复制来达到目录间的同步（增量同步？）。
* `rsync` 程序的真正好处之一，是它可以被用来在网络间复制文件。
* 通过使用 `rsync` 服务器。`rsync` 可以被配置为一个守护进程，监听即将到来的同步请求。

示例：
```shell
# 同步 playground 目录和它在 foo 目录中相对应的副本
# - a 选项（递归和保护文件属性）
# - v 选项（冗余输出）
$ rsync -av playground foo
```

## 四、备份文件系统

### `dump` 备份文件系统

* `dump` 使用“备份级别”实现增量备份，即每次级别为 N 的备份会对从上次级别小于 N 的备份以来，修改过的文件执行备份。
* 使用 `dump` 进行增量备份时，只能在像磁带这样的字符设备上进行。如果一定要使用硬盘做备份，只能进行 0 级备份。

```shell
$ dump -0u -f /dev/nst0 /web    ## 执行从 /web 到 /dev/nst0 的 0级备份

# 参数含义
# -0: 执行 0 级备份，备份级别共有10个（0～9），0表示完整备份；
#
# -u: 1.指定 dump 更新 /etc/dumpdates 文件，该文件类似于一个备份日志文件；
#     2.该选项要求备份的必须是一个完整的文件系统；
#
# -f: 指定用于存放备份的设备；
#

$ rdump -0u -f backup:/dev/nst0 /web    ## 将备份转储到远程主机上
```

### `restore` 从灾难中恢复

```shell
# 1. 需要建立一个临时目录
$ mkdir /var/restore       ## 建立用于恢复文件的目录 /var/restore
$ cd /var/restore

# 2. 从备份设备中提取数据
# -i: 交互式地恢复单个文件和目录
# -f: 指定存放备份的设备
$ restore -i -f /dev/sdb3

# 3. 进入交互式命令行界面
# 用 add xxx 标记需要恢复的文件
# 最后用 extract 命令提取所有做过标记的文件和目录
# quit 退出 restore

# -r: 恢复整个文件系统
$ restore -r -f /dev/sdb3
```
