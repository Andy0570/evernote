💡最后更新：20180930



 参考：

* [Linux入门教程](http://wiki.jikexueyuan.com/project/linux/)
* [Gitbook：鸟哥的 Linux 私房菜：基础学习篇 第四版](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/)
* [查询：Linux 命令大全](http://man.linuxde.net/)
* [网络常见的 9 个命令](http://blog.sina.com.cn/s/blog_672b409101012vpo.html)
* [13 款 Linux 运维比较实用的工具](https://blog.csdn.net/jb19900111/article/details/17756183)
* [20 个 Unix/Linux 命令技巧](https://blog.csdn.net/chenleixing/article/details/44569827)
* [Linux 命令总结](https://blog.csdn.net/qwe6112071/article/details/50806734)
* [Linux 思维导图整理](https://www.jianshu.com/p/59f759207862)
* [Linux 服务器程序规范](https://blog.csdn.net/dextrad_ihacker/article/details/51930998)
* [每天一个 linux 命令目录](https://www.cnblogs.com/peida/archive/2012/12/05/2803591.html)
* [Bash 脚本 15 分钟进阶教程](https://linux.cn/article-2921-1.html)
* [linux shell 逻辑运算符、逻辑表达式详解](https://www.cnblogs.com/chengmo/archive/2010/10/01/1839942.html)






# Linux 发行版

Linux 根据 Unix 演变而来。

Linux 隶属于 GNU（GNU is Not Unix，自由软件组织），遵循 GPL（GNU公共许可证）协议。

目前 Linux 主要发行版本有两大系列：Red Hat 和 Debain。

Linux 的发行版简而言之就是将 Linux 内核与应用软件做一个打包。较知名的发行版有：Ubuntu、RedHat、CentOS、Debain、Fedora、SuSE、OpenSUSE、TurboLinux、BluePoint、RedFlag、Xterm、SlackWare 等。

[Download Linux](https://www.linux.org/pages/download/)



## Linux 操作系统的安装

* [乌班图系统 Ubuntu 12.04 安装教程详细步骤 (图解)](http://www.jb51.net/os/84475.html)
* [Ubuntu 16.04 Server 版安装过程图文详解](https://www.linuxidc.com/Linux/2017-11/148341.htm)
* [Linux 磁盘分区和逻辑卷详解](https://www.linuxidc.com/Linux/2018-04/152069.htm)
* [Linux 系统安装时分区的选择（推荐）](https://www.cnblogs.com/gylei/archive/2011/12/04/2275987.html)



```shell
# 查看 Ubuntu 版本号
$ cat /etc/issue

# 查看 Ubuntu 版本详细信息
$ sudo lsb_release -a
```



### 图形模式与文字模式的切换

命令行窗口：Ctrl + Alt + F1 ~ F6

图形界面：Ctrl + Alt + F7



VMWare 虚拟机下：

命令行窗口： Alt + Space + F1~F6

图形界面 —> 命令窗口:Alt + Shift + Ctrl + F1~F6



### 快捷键

| 命令       | 含义                                |
| ---------- | ----------------------------------- |
| Ctrl + C   | 终止当前命令                        |
| Ctrl + D   | 退出当前终端，同样你也可以输入 exit |
| Ctrl + Z   | 暂停当前进程，fg 恢复               |
| Ctrl + L   | 清屏，使光标移动到第一行            |
| Tab        | 命令自动补全                        |
| man + 命令 | 查询帮助文档                        |
| shift+` .  | 退出无响应的SSH终端                 |



### Linux 系统目录结构

| 目录   | 内容                                     |
| ------ | ---------------------------------------- |
| /bin   | 构建最小系统所需要的命令（最常用的命令） |
| /boot  | 内核与启动文件                           |
| /dev   | 各种设备文件                             |
| /etc   | 系统软件的启动和配置文件                 |
| /home  | 用户的主目录                             |
| /lib   | C编译器的库                              |
| /media | 可移动介质的安装点                       |
| /opt   | 可选的应用软件包（很少使用）             |
| /proc  | 进程的映像                               |
| /root  | 超级用户root的主目录                     |
| /sbin  | 和系统操作有关的命令～                   |
| /tmp   | 临时文件存放点                           |
| /usr   | 非系统的程序和命令（应用程序目录）       |
| /var   | 系统专用的数据和配置文件                 |

* /etc，这个目录用来存放所有的系统管理所需要的配置文件和子目录。
* /bin, /sbin, /usr/bin, /usr/sbin: 这是系统预设的执行文件的放置目录。
* /bin, /usr/bin，给系统用户使用的指令。
* /sbin, /usr/sbin，给root使用的指令。
* /var/log，应用程序日志文件



### Linux 系统关机

| 命令       | 含义             |
| ---------- | ---------------- |
| who        | 查看当前在线用户 |
| netstat -a | 查看网络联机状态 |
| ps -aux    | 查看后台执行程序 |

正确的关机流程：

#### `sync` 将数据由内存同步到硬盘中

#### `shutdown` 关机指令

| shutdown 命令                                             | 含义                               |
| --------------------------------------------------------- | ---------------------------------- |
| shutdown -h 10 'This server will shutdown after 10 mins!' | 告诉登录用户，系统将在10分钟后关机 |
| shutdown -h now                                           | 系统立马关机，halt                 |
| shutdown -h +10                                           | 系统10分钟后关机                   |
| shutdown -h 20:25                                         | 系统会在今晚 20:25 关机            |
| shutdown -r now                                           | 系统立马重启                       |
| shutdown -r +10                                           | 系统10分钟后重启                   |

#### `reboot` 重启指令，等同于`shutdown -r now`

#### `halt` 关闭系统，等同于 `shutdown -h now` 和 `poweroff`



# Linux 文件与目录管理



#### `pwd` 查看当前路径

pwd = Print Working Directory



#### `cd` 跳转目录

cd = Change Directory

| 相对路径 | 含义             |
| -------- | ---------------- |
| /        | 根目录           |
| ./       | 当前目录         |
| ../      | 上一层目录       |
| -        | 前一个工作目录   |
| ~        | 当前用户的家目录 |



```shell
## 跳转到指定目录下 /Users/huqilin
$ cd /Users/huqilin  

## 返回上层目录
$ cd ..  

## 跳转到用户主目录 /Users/huqilin
$ cd  
$ cd ~
```



#### `mkdir` 创建一个或多个目录

语法：`mkdir [-mp] [目录名称]`

| 参数 | 含义                 |
| ---- | -------------------- |
| -p   | 递归创建目录         |
| -m   | 指定要创建目录的权限 |

```shell
$ mkdir document picture    # 新建两个子目录
```




#### `rmdir` 删除一个目录

💡该命令只能删除**空目录**，实际很少用。

| 参数 | 含义               |
| ---- | ------------------ |
| -p   | 将上级目录一起删除 |



#### `rm` 删除目录或文件


| 参数 | 含义                           |
| ---- | ------------------------------ |
| -r   | 可递归删除不为空的**目录**     |
| -f   | 强制删除文件，不管文件是否存在 |
| -i   | 交互式删除                     |

```shell
rm -rf xxx    ## 强制删除指定的目录或文件

# 删除 default 链接文件
lrwxrwxrwx 1 root root 34 Jun  4 06:03 default -> /etc/nginx/sites-available/default
$ sudo rm -i default
rm: remove symbolic link 'default'? yes
```





#### `cp` 复制文件和目录

语法：`cp [选项] [源文件] [目标文件]`

| 参数 | 含义                                                         |
| ---- | ------------------------------------------------------------ |
| -d   | 软连接，类似于创建快捷方式                                   |
| -r   | 将子目录连同其中的文件一起复制到另一个目录下                 |
| -i   | 交互式拷贝，如果遇到一个存在的文件，会提示是否覆盖           |
| -u   | 仅当目标文件存在时才生效， 如果源文件比目标文件新才会拷贝，否则不做任何动作。 |



```shell
$ cp test.php test/		# 将文件 test.php 复制到 test 目录下

# cp 命令在执行复制任务时会自动跳过目录
$ cp -r test/ Desktop/		# -r，将 test 目录及其子文件 复制到 Desktop 目录下
```



#### `mv` 移动目录和文件的同时对其重命名

> 💡 Windows 下的重命名，在 Linux 下可以用 `mv` 指令实现。

语法：`mv [选项] [源文件] [目标文件]`

| 参数 | 含义                                                         |
| ---- | ------------------------------------------------------------ |
| -i   | 如果遇到一个存在的文件，会提示是否覆盖                       |
| -b   | 自动在同名文件后加“~”，避免文件覆盖                          |
| -u   | 仅当目标文件存在时才生效， 如果源文件比目标文件新才会拷贝，否则不做任何动作。 |



```shell
## 将 Downloads 目录下的密钥文件移至 .ssh 目录
$ mv ~/Downloads/MyKeyPair.pem ~/.ssh/MyKeyPair.pem  

## 将桌面上的密钥文件(key-pair.pem)移至 .ssh 目录，并修改所属主只读权限
$ mv key-pair.pem ~/.ssh/
$ chmod 400 ~/.ssh/key-pair.pem
```



#### `alias` 设置指令的别名

可以理解为输入法中的**文本替换**功能。

语法：`alias [别名]=[指令名称]`

如：`alias rm = 'rm -i'`，当我们使用 `rm` 命令时，实际上执行的是 `rm -i`，而使用绝对路径则不会被 `alias`。



#### 环境变量 PATH

```shell
# 命令可以直接使用，而不需要指定命令所处的绝对路径，是因为 PATH 的作用
☁  Desktop [master] ⚡ echo $PATH
/Library/Frameworks/Python.framework/Versions/3.6/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Users/huqilin/.rvm/bin
```



#### `ls` 查看目录

列出当前目录下的所有文件和子目录。

| 参数                      | 含义                           |
| ------------------------- | ------------------------------ |
| -F                        | 目录/ 、可执行文件*、链接文件@ |
| -a                        | 列出全部档案，包括隐藏文件     |
| -l                        | 详细列出文件的属性信息         |
| --color=never/always/auto | 设置显示颜色                   |
| -d 目录                   | 列出目录本身                   |



查看目录的属性：`ls -ld [FILE...]`

```shell
$ ls -ld ./JavaScript
drwxr-xr-x  6 huqilin  staff  192  3 18 23:40 ./JavaScript

$ ls -l ./JavaScript
total 0
drwxr-xr-x  14 huqilin  staff  448  5  3 11:00 Notes
drwxr-xr-x   6 huqilin  staff  192  4 17 13:19 book
drwxr-xr-x   4 huqilin  staff  128  3 18 23:39 sourceCode

# 递归列出目录及子目录
$ ls -lR
.:
total 40
drwxr-xr-x 2 ubuntu ubuntu 4096 Aug 29 15:29 conf.d
-rw-r--r-- 1 ubuntu ubuntu 1007 Aug 28 13:32 fastcgi_params
-rw-r--r-- 1 ubuntu ubuntu 2837 Aug 28 13:32 koi-utf
-rw-r--r-- 1 ubuntu ubuntu 2223 Aug 28 13:32 koi-win
-rw-r--r-- 1 ubuntu ubuntu 5231 Aug 28 13:32 mime.types
lrwxrwxrwx 1 ubuntu ubuntu   22 Aug 28 13:32 modules -> /usr/lib/nginx/modules
-rw-r--r-- 1 ubuntu ubuntu  643 Aug 28 13:32 nginx.conf
-rw-r--r-- 1 ubuntu ubuntu  636 Aug 28 13:32 scgi_params
-rw-r--r-- 1 ubuntu ubuntu  664 Aug 28 13:32 uwsgi_params
-rw-r--r-- 1 ubuntu ubuntu 3610 Aug 28 13:32 win-utf

./conf.d:
total 4
-rw-r--r-- 1 ubuntu ubuntu 1093 Aug 28 13:32 default.conf
```



#### `tree` 以树状图列出目录的内容

```shell
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
        
#-A 使用 ASNI 绘图字符显示树状图而非以 ASCII 字符组合
```



#### `touch`  新建空文件

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





#### `echo` 打印

示例：`echo "123456" > bb/ee`，将123456 打印在 bb/ee 文件中。

| 输出重定向符号 | 含义                                 |
| -------------- | ------------------------------------ |
| >              | 如果文件中有内容则会删除文件中的内容 |
| \>>            | 在文件中追加内容                     |





#### `echo $LOGNAME` 查看当前登录用户名

类似命令：

```shell
who    ##查看当前在线用户
```





#### `cat` 查看普通文件

一次将所有内容全部显示在屏幕上。

| 参数 | 含义                       |
| ---- | -------------------------- |
| -n   | 显示行号                   |
| -A   | 显示所有内容，包括特殊符号 |



#### `tac` 反向打印文件的内容到屏幕上



#### `grep` 、`egrep`文件内容查找

在文件中查找指定内容。

语法：`grep [OPTIONS] PATTERN [FILE...]`

**grep** 通过“基础正则表达式”进行搜索。

```shell
## 查看 /dev 目录下以“sd”开头的文件，即显示所有硬盘文件。
## “|” 是管道标识符，将一条命令的输出连接到另一条命令的输入。
$ ls /dev/ | grep sd    
```



#### `more` 按页显示文件内容

相比于 **cat** 命令，**more** 可以一页一页显示内容。

| 按键        | 功能                              |
| ----------- | --------------------------------- |
| space 空格键 | 向下翻页                            |
| Enter 键   | 向下滚动一行                  |
| Q 键 | 退出 |



#### `less` 查看文件内容，可以上翻、下翻

参数：-M 可以显示更多文件信息。

| 按键         | 功能                              |
| ------------ | --------------------------------- |
| space 空格键 | 向下翻页                          |
| B 键         | 向上翻页                          |
| j            | 向下移动                          |
| k            | 向上移动                          |
| /word        | 当前行向下搜索word，按n显示下一个 |
| ?word        | 当前行向上搜索word，按n显示下一个 |



#### `head` 显示文件的前10行

示例：`head -5 test` 显示test文件前5行内容。



#### `tail` 显示文件最后10行

示例：`tail -5 test` 显示test文件最后5行内容。

-f 动态显示文件的最后10行。







# 文件的所属主以及所属组

所属主：即文件的拥有者。

所属组：即该文件所属主所在的一个组。

一个 Linux 目录或者文件，都会有一个所属主和所属组。Linux 文件属性不仅规定了所属主和所属组，还规定了所属主（user）、所属组 (group) 以及其他用户（others）对该文件的权限。

![image](http://upload-images.jianshu.io/upload_images/2648731-0118d859e1898f94.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1. 第一列共10位

第一位描述文件类型，Linux 中一共有7种文件类型：

| 符号 | 文件类型                                                     |
| ---- | ------------------------------------------------------------ |
| d    | 目录                                                         |
| -    | 普通文件                                                     |
| l    | 符号链接（linux file）、软链接，类似 Windows 系统中的快捷方式 |
| b    | 块设备文件，比如磁盘分区                                     |
| c    | 字符设备文件、串行端口设备，例如键盘、鼠标                   |
| s    | 本地域套接字文件（socket），用于进程间通信。                 |
| p    | 有名管道                                                     |


后边的 9 位，每三个为一组。均为 rwx 三个参数的组合。其中 r 代表可读，w 代表可写，x 代表可执行。

前三位为所属主（user）的权限；

中间三位为所属组（group）的权限；

最后三位为其他非本群组（others）的权限；

对于一个目录来讲，打开这个目录即为执行这个目录，所以任何一个目录必须要有 x 权限才能打开并查看该目录。

2. 第 2 列，表示为连接占用的节点（inode），若为目录时，通常与该目录下还有多少子目录有关系，关于连接（link）在以后章节详细介绍。
3. 第 3 列，表示该文件的所属主。
4. 第 4 列，表示该文件的所属组。
5. 第 5 列，表示该文件的大小。
6. 第 6 列、第 7 列和第 8 列为该文件的创建日期或者最近的修改日期，分别为月份日期以及时间。
7. 第 9 列，文件名。如果前面有一个. 则表示该文件为隐藏文件。



### 更改文件权限



#### 1. `chgrp` 更改所属组

语法：`chgrp [组名] [文件名]`

参数：-R  改变一个目录及其下所有文件（和子目录）的所有权设置。



#### 2. `chown` 更改文件所有权

语法：`chown [OPTION] [OWNER][:[GROUP]] FILE...`

参数：-R  改变一个目录及其下所有文件（和子目录）的所有权设置。

```shell
$ sudo chown users:root testfile    # 将文件的属主改为 users，属组改为 root。
$ sudo chown users testfile         # 只更改所属主
$ sudo chown :root testfile         # 只更改所属组，⚠️ 注意 :不能省略
```



#### 3. `chmod` 更改用户对文件的读写执行权限

* 语法一：`chmod [-R] xyz 文件名`，（xyz表示数字，777）

r=4，w=2，x=1，-=0，默认目录权限**755**（drwxr-xr-x）、默认文件权限**644**(-rw-r--r--)。

| 权限符 | 读取（r） | 写入（w） | 执行（x） |
| :----- | :-------: | :-------: | :-------: |
| 二进制 |     4     |     2     |     1     |

如果你创建了一个目录，而该目录不想让其他人看到内容，则只需设置成 rwxr----- (740) 即可。

```shell
### 修改私钥文件权限为只读属性
chmod 400 ~/.ssh/mykeypair.pem
```



* 语法二：用户组+/-权限 、 用户组=权限、用户组1=用户组2

| 用户组 | 文件属主 | 文件属组 | 其他人 | 所有人 |
| ------ | :------: | :------: | :----: | :----: |
| 标识符 |    u     |    g     |   o    |   a    |

```shell
# 用户组+/-权限 
$ chmod u+x days    # 增加属主对文件的执行权限
$ chmod a-x days    # 删除所有人对文件的执行权限

# 用户组=权限
$ chmod ug=rw,o=r days    # 赋予属主和属组的读取/写入权限，其他人只有读取权限

# 用户组1=用户组2
$ chmod o=u days   # 设置其他人的权限与属主权限完全相同
```



> 💡只有文件的属主和root用户才有修改文件的权限！



#### `unmask` 

#### `chattr`  修改文件的特殊属性

#### `lsattr` 列出文件/目录的特殊属性

语法：`lsattr -[aR] [文件/目录名]`

| 参数 | 含义                                           |
| ---- | ---------------------------------------------- |
| -a   | 类似与 ls 的 -a 选项，即连同隐藏文件一同列出； |
| -R   | 连同子目录的数据一同列出                       |



### 在 Linux 下搜索一个文件

#### `which` 查找可执行文件的绝对路径

which 是在 PATH 变量指定的路径中，搜索某个系统命令的位置，并且返回第一个搜索结果。

也就是说，使用 which 命令，就可以**看到某个系统命令是否存在**，以及执行的到底是哪一个位置的命令。 

which的使用方法如下：

`which lsmod`
 which 的命令参数说明如下：

| 参数 | 含义                                                         |
| ---- | ------------------------------------------------------------ |
| -n   | 指定文件名长度，指定的长度必须大于或等于所有文件中最长的文件名。 |
| -p   | 与-n参数相同，但此处的文件名长度包含了文件的路径。           |
| -w   | 指定输出时栏位的宽度。                                       |
| -V   | 显示版本信息。                                               |



#### `whereis` 通过预先生成的一个文件列表库去查找跟给出的文件名相关的文件。

语法：`whereis [-bmsu] [文件名称]`

| 参数 | 含义                               |
| ---- | ---------------------------------- |
| -b   | 只找 binary 文件                   |
| -m   | 只找在说明文件 manual 路径下的文件 |
| -s   | 只找 source 来源文件               |
| -u   | 没有说明档的文件                   |





#### `locate` 类似于 `whereis`

如果你的 linux 没有这个命令，请安装软件包 mlocate ，这个软件包在你的系统安装盘里，后缀名是 RPM。

如果你装的 CentOS 你可以使用这个命令来安装 `yum install –y mlocate`



#### ⭐️ `find` 文件的查找 

**find** 命令可以迅速在指定范围内查找到文件。

语法：`find [OPTION] [path...] [expression]`



##### `find [path] -name fileName` : 通过文件名查找

例如：

```shell
$ find /root -name test1
$ find /usr/bin -name zip -print    ##在 /usr/bin 目录中查找 zip 命令
$ find ~/ -name *.c -print          ##列出用户主目录下所有的 c 程序文件
```



##### `find [path] -type fileType` : 通过文件类型查找

type 文件类型包括：f, b, c, d, l, s 等。

```shell
find /root -type d
```



##### 通过指定时间查找

| 参数      | 含义                                                         |
| --------- | ------------------------------------------------------------ |
| -atime +n | Access time，访问或执行时间大于 n 天的文件                   |
| -ctime +n | Create time，写入、更改 inode 属性（例如更改所有者、权限或者连接）时间大于 n 天的文件 |
| -mtime +n | Modified time，写入时间大于 n 天的文件                       |

+n 表示大于n天，-n表示小于n天。



`ls -|` 命令可用来列出文件的 atime、ctime 和 mtime。

`ls -|c filename` :  列出文件的 ctime

`ls -|u filename` : 列出文件的 atime

`ls -| filename ` : 列出文件的 mtime  



### Linux 的文件系统

| 操作系统 | 文件系统格式          |
| -------- | --------------------- |
| Windows  | FAT、NTFS、           |
| Linux    | Ext2、Ext3、 reiserFS |

* Ext2 文件系统是非日志文件系统。
* Ext3 文件系统带有日志功能，可以跟踪记录文件系统的变化，并将变化内容写入日志。
* 在 Linux 系统中你可以挂载 Windows 的文件系统，linux 目前支持 MS-DOS，VFAT，FAT，BSD 等格式。
* 如果你使用的是 Redhat 或者 CentOS，那么你不要妄图挂载 NFS 格式的文件到 Linux 下，因为它不支持 NFS。
* Ext3 文件系统为 Redhat/CentOS 默认使用的文件系统。



### Linux 文件类型

1）正规文件（regular file）：一般类型的文件，当用 `ls –l` 查看某个目录时，第一个属性为”-“的文件就是正规文件，或者叫普通文件。正规文件又可分成纯文字文件（ascii）和二进制文件（binary）。纯文本文件可以通过 cat, more, less 等工具直接查看内容，而二进制文件并不能。例如我们用的命令 /bin/ls 这就是一个二进制文件。

2）目录（directory）：这个很容易理解，就是目录，跟 windows 下的文件夹一个意思，只不过在 Linux 中我们不叫文件夹，而是叫做目录。`ls –l` 查看第一个属性为”d”。

3）连接档（link）：`ls –l` 查看第一个属性为 “l”，类似 windows 下的快捷方式。这种文件在 Linux 中很常见，而且笔者在日常的系统运维工作中用的很多。

4）设备档（device）：与系统周边相关的一些档案，通常都集中在 /dev 这个目录之下！通常又分为两种：

**区块(block) 设备档**：就是一些储存数据，以提供系统存取的接口设备，简单的说就是硬盘啦！例如你的一号硬盘的代码是/dev/hda1 等等的档案啦！第一个属性为 “ b “；

**字符 (character) 设备档**：亦即是一些串行端口的接口设备，例如键盘、鼠标等等！第一个属性为 “ c “。



#### `ln` 创建软连接/硬连接

语法：`ln [-s] [源文件] [目的文件]`

-s: 建立软链接，不带该参数表示建立硬链接。

```shell
# 符号链接（软链接），带 -s，可用于目录
$ ln -s TARGET LINK_NAME    ##给目标文件 TARGET 取了一个别名 LINK_NAME
# arlingbc -> /etc/nginx/sites-available/arlingbc
$ sudo ln -s /etc/nginx/sites-available/arlingbc arlingbc 


# 硬链接，不带 -s，不可用于目录
$ ln passwd passwd-hd    ##建立一个名为passwd-hd硬链接指向文本文件passwd
```



硬链接 & 软链接的区别：

* 硬链接是直接引用，而符号链接是通过名称进行引用。
* 硬链接是两个完全独立的文件，拥有相同的内容，对其中一个文件的改动会反映在另一个文件中。
* 软链接可以理解为 Windows 中的快捷方式。



### 文件重定向

**重定向**就是将数据传到其他地方。具体地说：将应该出现到屏幕上的数据，传送到其他设备，列如，文件或打印机。

**I/O重定向**简单来说就是一个过程，这个过程捕捉一个文件，或者命令、程序、脚本，甚至脚本中的代码块（ code block）的输出，然后把捕捉到的输出，作为输入发送给另外一个文件、命令、程序或者脚本。

**文件标识符**是一个数字，不同数字代不同的含义，默认情况下，系统占用了3个，分别是0标准输入（ stdin）、1标准输出（ stdout）、2标准错误（ stderr），另外3~9是保留的标识符，以把这些标识符指定成标准输入、输出或者错误作为临时连接。

```shell
# 输入重定向符号 <，默认指向键盘
$ cat < days    ## 让程序从文件中获取输入

# 立即文档 <<
$ cat << EOF    ## 明确告诉Shell从键盘接受输入，并传递给程序

# 结合使用输入重定向与输出重定向
# 输出重定向符号 >，默认指向显示器
# > 会覆盖原有内容
# >> 会保留原有内容
$ cat << EOF > hello    ## 让cat命令以立即文档的方式获取输入，再把cat的输出重定向到hello文件
```





# Linux 系统用户以及用户组管理

#### `/etc/passwd`  存储用户信息

```
7个字段
root:x:0:0:root:/root:/bin/bash
登录名:口令占位符:用户ID号（UID）:默认组ID号（GID）:用户私人信息:用户主目录:登录 shell
```

| 用户组ID（UID） | 账号种类           |
| --------------- | ------------------ |
| 0               | 系统管理员         |
| 1～499          | 系统保留给服务使用 |
| 500～65535      | 普通用户           |



####  `/etc/shadow`  保存用户口令

```
9个字段
root:$1$..../:15092:0:99999:7:::
用户名:用户密码:上次更改密码时间:要过多少天才可以更改密码:密码多少天后到期:密码到期前的警告期限:账号失效期限:账号的生命周期:保留字
```



#### `/etc/group`文件，保存系统中所有组的名称，以及每个组中的成员列表

```
4个字段
root:x:0:
admin:x:115:lewis,rescuer
组名:组口令占位符:组 ID 号（GID）,成员列表，用逗号分开（不能加空格）
```





#### `groupadd `  新增用户组

格式：`groupadd [-g GID] [-o] [-r] [-f] groupname`

-g 选项可以自定义 gid，不加 -g 则按照系统默认的 gid 创建组，跟用户一样，gid 也是从 500 开始的。

| 参数   | 含义                            |
| ------ | ------------------------------- |
| -g GID | 指定组 ID 号                    |
| -o     | 允许组 ID 号，不必唯一          |
| -r     | 加入组 ID 号，低于 499 系统账号 |
| -f     | 加入已有的组时，发展程序退出    |



#### `groupdel` 删除用户组

格式：`groupdel groupname`



#### `useradd`  新增用户

格式：`useradd [-u UID] [-g GID] [-d HOME] [-M] [-s]`

| 参数 | 含义                                       |
| ---- | ------------------------------------------ |
| -m   | ⭐️自动为新用户建立主目录                    |
| -u   | 自定义 UID， 设定 ID 值时尽量要大于 500    |
| -g   | ⭐️指定用户所属的群组                        |
| -d   | 自定义用户的home目录                       |
| -M   | 不建立home目录                             |
| -s   | ⭐️指定用户登录后所使用的 shell，默认使用 sh |

```shell
$ sudo useradd -m alice    ##添加一个用户名为 alice 的用户，并自动建立主目录
$ sudo passwd alice        ##更改 alice 的登录密码cat
输入新的 Unix 口令:
重新输入新的 Unix 口令:
passwd：已成功更新密码

$ sudo useradd -s /bin/base mike    ##指定新用户的 shell 为 base
```

> 💡建议
>
> 在新建一个用户时，单独创建一个同名的用户组，然后把用户归入这个组中——这正是不带 -g 参数的 useradd 命令的默认行为。



#### `userdel` 删除用户

格式：`userdel [-r] username`

-r 选项的作用是删除用户时，连同用户的home目录一起删除。

> 💡 建议
>
> 将被删用户的主目录保留几周，然后再手动删除。



#### `passwd [username]` 创建/修改用户密码

```shell
$ sudo passwd root    ## 修改 root 密码
```



#### `su -` 切换到root用户

> ⚠️ Ubuntu Linux 的限制非常严格，必须使用 `sodu -s` 来获得 root 访问权。

语法：`su [-] username`

* 加”-“后会连同用户的环境变量一起切换过来。

```shell
$ sudo -s    #将自己提升为root用户 (💡💡💡Ubuntu Linux 系统💡💡💡)
$ su         #同上
$ su alice   #切换到alice账户

$ exit       #回到先前的用户状态
```



#### `sudo`

通过 sudo，我们能把某些超级权限有针对性地下放，并且不需要普通用户知道 root 密码，所以 sudo 相对于权限无限制性的 su 来说，还是比较安全的，基于此 sudo 也能被称为受限制的su；另外sudo是需要授权许可的，所以也被称为授权许可的 su。

需要 root 预先配置 **/etc/sudoers**：

```shell
yum install -y sudo    # 安装 visudo 命令
visudo                 # 使用 visudo 命令编辑 sudo 配置文件

# root 用户可以使用 sudo 在任何机器上（第一个ALL）以任何用户身份（第二个ALL）执行任何命令（第三个ALL）
root ALL=(ALL) ALL
%wheel ALL=(ALL) ALL    # 所属组权限设置，wheel 组的所有用户都拥有 sudo 权利
test ALL=(ALL) ALL      # 所属主权限设置，test 用户拥有 sudo 权利
```

[sudo 配置文件 / etc/sudoers 详解及实战用法](https://blog.csdn.net/field_yang/article/details/51547804)

[sudoers 的深入介绍与用户权限控制](https://segmentfault.com/a/1190000007394449)





#### `history` 查看用户历史操作记录

* 只能列出当前用户的操作记录；
* 默认存储 1000 条记录，可以指定让 history 列出最近几次输入的命令：

```shell
$ history 10    ##列出最近使用的10条命令
```

* 对于管理员而言，可以读取某一个用户主目录下的 **.bash_history** 文件。





# Linux 进程管理

* 进程表示正在运行的程序。
* 进程标识——PID，父进程标识——PPID。
* 群组标识——GID，创建者所属组的 ID 号。
* 用户可以设置进程的“**谦让度**”，**高谦让度意味着低优先级**。



### 进程监控

#### `ps` 静态监控

显示当前系统进程快照。

```shell
$ ps aux                ##显示当前系统上运行的所有进程信息
$ ps aux | grep init    ##在进程列表中搜索 init 进程

$ ps lax    ##可以显示进程父进程ID（PPID）和谦让度（NI）
```



#### `top` 动态监控

* 实时跟踪当前系统中进程的情况。
* 默认每 10s 更新一次。



#### `kill` 向进程发送信号

语法：`kill [-signal] pid`

默认情况下，kill 命令向进程发送 TERM 信号，表示请求终止某项操作。

```shell
$ kill -l    ## 显示所有信号及其编号
 1) SIGHUP	 2) SIGINT	 3) SIGQUIT	 4) SIGILL	 5) SIGTRAP
 6) SIGABRT	 7) SIGBUS	 8) SIGFPE	 9) SIGKILL	10) SIGUSR1
11) SIGSEGV	12) SIGUSR2	13) SIGPIPE	14) SIGALRM	15) SIGTERM
16) SIGSTKFLT	17) SIGCHLD	18) SIGCONT	19) SIGSTOP	20) SIGTSTP
21) SIGTTIN	22) SIGTTOU	23) SIGURG	24) SIGXCPU	25) SIGXFSZ
26) SIGVTALRM	27) SIGPROF	28) SIGWINCH	29) SIGIO	30) SIGPWR
31) SIGSYS	34) SIGRTMIN	35) SIGRTMIN+1	36) SIGRTMIN+2	37) SIGRTMIN+3
38) SIGRTMIN+4	39) SIGRTMIN+5	40) SIGRTMIN+6	41) SIGRTMIN+7	42) SIGRTMIN+8
43) SIGRTMIN+9	44) SIGRTMIN+10	45) SIGRTMIN+11	46) SIGRTMIN+12	47) SIGRTMIN+13
48) SIGRTMIN+14	49) SIGRTMIN+15	50) SIGRTMAX-14	51) SIGRTMAX-13	52) SIGRTMAX-12
53) SIGRTMAX-11	54) SIGRTMAX-10	55) SIGRTMAX-9	56) SIGRTMAX-8	57) SIGRTMAX-7
58) SIGRTMAX-6	59) SIGRTMAX-5	60) SIGRTMAX-4	61) SIGRTMAX-3	62) SIGRTMAX-2
63) SIGRTMAX-1	64) SIGRTMAX

## KILL 可以在内核级别“杀死”一个进程
$ sudo kill -KILL pid
$ sudo kill -SIGKILL pid
$ sudo kill -9 pid

## killall - 该命令可以直接使用进程的名字而不是进程标识号
$ killall -HUP inetd
```



#### `nice` 启动程序时设置其谦让度

* 高谦让度意味着低优先级。
* 谦让度的值应该在 -20～+19 之间浮动。
* 进程的属主可以提高进程的谦让度（降低优先级），但不能降低其谦让度（提高优先级）。
* root 用户可以任意设置进程的优先级。

```shell
$ nice -n 2 bc          ## 设置 bc 以谦让度增量 2 启动
$ sudo nice -n -3 bc    ## 使用 root 权限才能降低谦让度
$ nice bc               ## 不带 -n 参数的 nice 命令会将程序的谦让度增量设置为 10

$ nice                  ## 查看默认谦让度值
```



#### `renice` 进程运行时调整其谦让度

* `nice` 命令调整**相对谦让度**值，`renice` 命令调整**绝对谦让度**值。

```shell
$ ps lax               ## 获得进程的 PID
$ renice +12 -p pid    ## -p选项指定进程的 PID
```





# Linux 磁盘管理



* 硬盘分为 IDE 硬盘、SCSI 硬盘、SATA 硬盘。
  * IDE接口的设备被称为 hd，SCSI 和 SATA 接口的设备则被称为 sd。
  * SCSI 或者 SATA 接口设备中，不同硬盘的命名：sda、sdb、sdc ...
  * 一块硬盘上只能存在4个主分区：sda1、sda2、sda3、sda4。逻辑分区则从5开始标识。
  * **操作系统主体**可以安装在**主分区**，也可以安装在**逻辑分区**，但**引导程序**必须安装在**主分区**内。
* 操作系统通过文件系统对磁盘进行操作。
* 文件系统：一种对物理空间的组织方式，通常在格式化硬盘时创建。

![image](http://upload-images.jianshu.io/upload_images/2648731-1d6a77fe0bf17b48.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 交换分区（swap）：当实际内存不够用时，操作系统会从内存中取出一部分暂时不用的数据，放在交换分区中。一般来说，swap 分区容量应该要大于物理内存大小，但目前不能超过 2GB。



#### `df `、`du`  查看磁盘或者目录的容量

**df** 查看已挂载磁盘的总容量、使用容量、剩余容量等，可以不加任何参数，默认是按 k 为单位显示的。

该命令可以收集和整理当前已经挂载的全部文件系统的一些重要的统计数据

| 参数 | 含义                 |
| ---- | -------------------- |
| -i   | 使用 inodes 显示结果 |
| -h   | 系统自动调节单位     |
| -k   | 以 KB 为单位输出     |
| -m   | 以 MB 为单位输出     |




```shell
$ df  
文件系统        1K-块        已用    可用     已用%  挂载点
Filesystem     1K-blocks    Used Available Use% Mounted on
udev             8207696       0   8207696   0% /dev
tmpfs            1643140    8672   1634468   1% /run
/dev/xvda1       8065444 1879988   6169072  24% /
tmpfs            8215688       0   8215688   0% /dev/shm
tmpfs               5120       0      5120   0% /run/lock
tmpfs            8215688       0   8215688   0% /sys/fs/cgroup
tmpfs            1643140       0   1643140   0% /run/user/1000

$ df -t ext4    ## ⭐️⭐️⭐️ 显示所有已挂载的 ext4 文件系统
Filesystem     1K-blocks    Used Available Use% Mounted on
/dev/xvda1       8065444 1879988   6169072  24% /

$ df -h -t ext4
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvda1      7.7G  1.6G  6.1G  21% /
```



**du** **用来查看某个目录所占空间大小**

语法：`du [-abckmsh] [文件或者目录名]`

| 参数 | 含义                                                         |
| ---- | ------------------------------------------------------------ |
| -a   | 列出所有文件与目录容量。如果不加任何选项和参数只列出目录（包含子目录）大小。 |
| -b   | 列出的值以 bytes 为单位输出，默认是以 Kbytes                 |
| -c   | 最后求和                                                     |
| -k   | 以 KB 列出容量内容                                           |
| -m   | 以 MB 列出容量内容                                           |
| -s   | 列出总量，不列出每个目录容量。                               |
| -h   | 系统自动调节单位                                             |



```shell
$ du -sh ./                 ## 查看当前目录大小
$ du -sh dictionary_name    ## 查看某一指定目录大小
$ du -a                     ## 将每一个文件大小都列出
```



#### ⭐️⭐️⭐️ 磁盘的分区和格式化

#### `fdisk` Linux 下的硬盘分区工具

语法：`fdisk [-l] [设备名称]`

-l ：后边不跟设备名会直接列出系统中所有的磁盘设备以及分区表，加上设备名会列出该设备的分区表。

如果不加 -l 则进入另一个模式，在该模式下，可以对磁盘进行分区操作。

| 分区常用参数 | 缩写形式 | 含义                                  |
| ------------ | -------- | ------------------------------------- |
| print        | p        | 显示当前分区设置                      |
| new          | n        | 创建一个新分区                        |
| type         | t        | 设置分区类型                          |
| delete       | d        | 删除一个分区                          |
| write        | w        | 把分区信息写入硬盘                    |
| quit         | q        | 退出                                  |
| menu         | m        | 显示 fdisk 所有可用的命令及其简要介绍 |



说明：

* Linux 中最多只能创建 4 个主分区，一旦创建 4 个主分区后就不能增加任何分区了。
* swap 分区最大不能超过 2GB。swap 分区类型号：82。
* 最多只能创建一个扩展分区。扩展分区下的逻辑分区最多可以创建10个（hdb5-hdb15）。IDE 的硬盘（类似于 hda, hdb, hdc 等）。
* 只有在使用 **write** 命令之后，硬盘上的信息才会真正被改变。



参考

* [AWS: EC2 Linux 添加硬盘 2014](http://blog.51cto.com/beanxyz/1529931)
* [AWS: EC2 Linux 扩展硬盘 2014](http://blog.51cto.com/beanxyz/1532125)



#### `mkfs.ext2` / `mkfs.ext3` /`mke2fs` 格式化 Linux 硬盘分区



| 常用参数 | 含义                                                         |
| -------- | ------------------------------------------------------------ |
| -b       | 分区时设定每个数据区块占用空间大小，目前支持 1024, 2048 以及 4096 bytes 每个块 |
| -i       | 设定 inode 大小                                              |
| -N       | 设定 inode 数量，有时使用默认的 inode 数不够用，所以要自定设定 inode 数量 |
| -c       | 在格式化前先检测一下磁盘是否有问题，加上这个选项后会非常慢   |
| -L       | 预设该分区的标签 label                                       |
| -j       | 建立 ext3 格式的分区，如果使用 mkfs.ext3 就不用加这个选项了  |

示例：

```shell
mkfs.ext3 -b 4096 -i 4096 /dev/hdb2
mkfs.ext3 -L label2 /dev/hdb2    # 自定义分区label标签
```





#### `e2label`  查看或者修改分区的标签（label）

```shell
e2label /dev/hdb2    # 查看分区标签
e2lable /dev/hdb2 label1    # 修改分区标签
```



### `fsck` 检查硬盘有是否有坏道

语法：` fsck [-Aar] [分区]`

| 参数 | 含义                                                         |
| ---- | ------------------------------------------------------------ |
| -A   | 加该参数时，后不需要跟分区名作为参数。它会自动检查 /etc/fstab 文件下的所有分区（开机过程中就会执行一次该操作） |
| -a   | 自动修复检查到有问题的分区                                   |
| -r   | 当检查到有坏道的分区时会让用户决定是否修复                   |



### 挂载/卸载磁盘

#### `mount` 挂载文件系统

* 在挂载某个分区前需要先建立一个挂载点，这个挂载点是以**目录**的形式出现的。
* 一旦把某一个分区挂载到了这个挂载点（目录）下，那么再往这个目录写数据时，则都会写到该分区中。
* 注意：在挂载该分区前，挂载点（目录）下必须是个空目录。
* Linux 把所有的设备文件都放在 **/dev** 目录下（device）.
* mount 挂载好新硬盘后，还需要编辑 **/etc/fstab** 文件让系统重启时自动挂载设备。

```shell
$ sudo mkdir /mnt/cdrom            # 新建一个目录
$ sudo mount /dev/cdrom /mnt/cdrom # 挂载光盘至这个新建的目录
...
$ cd /                             # 卸载光盘前必须退出光盘所在的目录，即 /mnt/cdrom 目录
$ sudo umount /dev/cdrom           # 卸载光盘
```



#### `blkid`  显示磁盘分区的UUID



#### `umount` 卸载文件系统

```
$ sudo umount -r /dev/sda1    ## -r, 指导 umount 在无法卸载文件系统时，尝试以只读方式重新载入
```

> ⚠️  文件系统只有在没有被使用的情况下才可以被卸载。



#### quota管理磁盘配额

quota 这个模块主要分为 quota quotacheck quotaoff quotaon quotastats edquota setquota warnquota repquota 这几个命令



# 文档的压缩与打包



| 文件名后缀 | 压缩工具                                                     |
| ---------- | ------------------------------------------------------------ |
| .gz        | gzip 压缩工具压缩的文件                                      |
| .bz2       | bzip2 压缩工具压缩的文件                                     |
| .tar       | tar 打包程序打包的文件 (tar 并没有压缩功能，只是把一个目录合并成一个文件) |
| .tar.gz    | 可以理解为先用 tar 打包，然后再 gzip 压缩                    |
| .tar.bz2   | 同上，先用 tar 打包，然后再 bzip2 压缩                       |



 #### gzip 压缩工具

压缩工具，支持的格式有 .gz、.Z、-gz、.z、-z 和 z

语法：`gzip [-d#] filename`

| 参数 | 含义                                       |
| ---- | ------------------------------------------ |
| -d   | 解压缩                                     |
| -#   | 压缩等级，1 压缩最差，9 压缩最好，6 为默认 |
| -l   | 查看压缩效果                               |
| -t   | 测试压缩文件的完整性，正常则不输出         |



> ⚠️  **gzip 不可以压缩目录。**

```shell
#### 压缩
$ gzip linux_book_bak.tar        ## 压缩文件
$ gzip -l linux_book_bak.tar     ## 查看压缩效果
$ gzip -tv linux_book_bak.tar    ## 测试压缩文件完整性

#### 解压缩
$ gunzip linux_book_bak.tar.gz     ## 解压缩文件
$ gzip -d linux_book_bak.tar.gz    ## 同上
```



### bzip2

语法：`bzip2 [-dz] filename` 

-d ：解压缩

-z ：压缩



### tar 打包文件

该程序可以读取多个文件和目录，并将它们打包成一个文件。

语法：`tar [-zjxcvfpP] filename`

| 参数 | 含义                                                         |
| ---- | ------------------------------------------------------------ |
| -z   | 是否同时用 gzip 压缩                                         |
| -j   | 是否同时用 bzip2 压缩                                        |
| -x   | 解包或者解压缩                                               |
| -t   | 查看 tar 包里面的文件                                        |
| -c   | 创建归档文件                                                 |
| -v   | 可视化，显示命令的执行过程                                   |
| -f   | 后面跟文件名，压缩时跟 -f 文件名，指定归档文件名，解压时跟 -f 文件名，意思是解压filename。请注意，如果是多个参数组合的情况下带有 -f，请把 f 写到最后面。 |
| -p   | 使用原文件的属性，压缩前什么属性压缩后还什么属性。（不常用） |
| -P   | 可以使用绝对路径。（不常用）                                 |

```shell
#### 打包文件
$ tar -cvf shell.tar shell/        ## 将 shell 目录连同其下的文件一同打包成文件 shell.tar
$ tar -czvf shell.tar.gz shell/    ## 打包并使用 gizp 压缩文件

#### 解开文件
$ tar -xvf shell.tar
$ tar -xzvf shell.tar.gz          ## 先调用 gzip 解压缩，再解开文件。
```



### 备份文件系统：dump、restore

#### `dump` 备份文件系统

* dump 使用“备份级别”实现增量备份，即每次级别为 N 的备份会对从上次级别小于 N 的备份以来，修改过的文件执行备份。
* 使用 dump 进行增量备份时，只能在像磁带这样的字符设备上进行。如果一定要使用硬盘做备份，只能进行 0 级备份。



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



#### `restore` 从灾难中恢复

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



# Linux 软件包管理



### DEB 软件包管理机制

* DEB 软件包管工具主要用于 Debain 和 Ubuntu 版本。
* DEB 格式的软件包通常以 .deb 结尾。

```shell
$ dpkg --help    ## 帮助文档
$ sudo dpkg --install xxx    ## 安装软件包
$ sudo dpkg --remove xxx     ## 卸载软件包
```



### RPM 软件包管理机制

* RPM 是”Redhat Package Manager” 的缩写。该工具用于管理 .rpm 结尾的软件包。
* 高级软件包管理工具：APT（Ubuntu 系统）、yum（fedora，redhat 系统）。
* yum 是 Redhat 所特有的安装 RPM 程序包的工具。
* 使用 yum 工具就可以连同依赖的程序包一起安装。
* 在 CentOS 中你可以免费使用 yum。



1. 安装一个 rpm 包：

   ```shell
   rpm -ivh alacarte-0.10.0-1.fc6.noarch.rpm
   # -i: 安装
   # -v: 显示 rmp正在执行的工作
   # -h: 打印 # 显示安装进度
   ```

   

   | 参数    | 含义                                                         |
   | ------- | ------------------------------------------------------------ |
   | -i      | 安装                                                         |
   | -v      | 可视化                                                       |
   | -h      | 显示安装进度                                                 |
   | --force | 强制安装，即使覆盖属于其他包的文件也要安装                   |
   | —nodeps | 当要安装的 rpm 包依赖其他包时，即使其他包没有安装，也要安装这个包 |

2. 升级一个 rpm 包

   ```shell
   rpm -Uvh filename    ## 更新一个软件包
   ```

3. 卸载一个 rpm 包

   ```shell
   rmp -e filename
   
   $ sudo rpm -e -vv --test xorg-xll-devel  ## 模拟卸载
   #
   # 因为单纯的卸载可能存在软件包的相互依赖问题
   # --test: 模拟删除软件包的全过程，但并不是真的卸载
   # -VV:    要求 RPM 输出完整的调试信息
   #
   ```

4. 查询一个包是否安装

   ```
   rpm -q rpm 包名（这里的包名，是不带有平台信息以及后缀名的）
   ```

5. 得到一个 rpm 包的相关信息

   ```
   rpm -qi 包名（同样不需要加平台信息与后缀名）
   ```

6. 列出一个 rpm 包安装的文件

   ```
   rpm -ql 包名
   ```

7. 列出某一个文件属于哪个 rpm 包

   ```
   rpm -qf 文件的绝对路径
   ```



### APT 软件包管理工具

⭐️⭐️⭐️

高级软件包工具（Advanced Package Tool，APT）：**自动检测软件依赖问题、自动下载和安装所有文件、自动更新整个系统上所有的软件包**。

* `apt-get`: 执行和软件包安装有关的所有操作；
* `apt-cahce`: 查找软件包相关信息；
* **安装源**：所有 apt-get 用于下载软件的地址，目录：**/ect/apt/source.list**



从 Internet 更新当前系统软件包的信息:

```shell
$ sudo apt-get update       ## 检查当前 apt-get 缓存中的软件包信息(只检查，不更新)
$ sudo apt-get -y upgrade   ## 更新已安装的软件包
```

#### apt-get 常用命令

| 命令            | 描述                                       |
| :-------------- | ------------------------------------------ |
| apt-get install | 下载并安装软件包                           |
| apt-get upgrade | 下载并安装在本系统上已有的软件包的最新版本 |
| apt-get remove  | 卸载特定的软件包                           |
| apt-get source  | 下载特定的软件源代码                       |
| apt-get clean   | 删除所有已下载的包文件                     |
| apt-get -h      | 列出 apt-get 的完整用法                    |



#### 安装 node.js

* [Debian and Ubuntu based Linux distributions](https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions)
* [E: Unable to locate package npm](https://stackoverflow.com/questions/47371904/e-unable-to-locate-package-npm)

```shell
# 安装 node.js v8.11.2
$ curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
$ sudo apt-get install -y nodejs

# 要从 npm 编译和安装原生插件，您可能还需要安装构建工具
$ sudo apt-get install -y build-essential 

# 检查当前版本
$ nodejs -v      
v8.11.2
$ npm -v
5.6.0
# 更新 npm
$ npm i -g npm

$ sudo apt-get upgrade nodejs    ## 更新
$ sudo apt-get remove nodejs     ## 卸载
```

nodejs 安装在系统环境 `/usr/bin` 目录下，之后使用 `npm -g` 安装其他插件也会安装到 `/usr/lib/node_modules`(需要使用sudo权限)。



# 任务计划

#### 简单的计时 `at` 命令，一次性任务

优点：能进行后台执行。

```shell
# 在2017/12/30凌晨3:00 执行脚本（～/alarm）
# 日期格式：MM/DD/YY (月/日/年)
# -f: 接受文件路径作为参数
$ at 3:00 12/30/2017 -f ~/alarm    


$ atq       ## 查看当前已经设置的任务
$ atrm x    ## 删除任务，x为任务编号
```



#### 定时备份文件系统 `cron`

```shell
$ sudo /etc/init.d/cron restart    ## 重新启动 cron
```



#### `crontab` 控制计划任务的命令

* cron 执行命令的最短周期是1分钟。
* crontab 是 cron 的配置文件。
* **/etc/crontab** : 存放和系统维护有关的全局任务计划。
* **/etc/cron.d**: 应用软件自己的任务计划。
* **/var/spool/cron**: 普通用户的配置文件。
* 管理员配置文件：
  * **/etc/cron.allow**： 列出可以提交crontab的用户。
  * **/etc/cron.deny**：不能提交crontab的用户。





# 网络管理

IP地址的配置方式：

1. 动态主机配置协议（DHCP）；
2. 静态IP；



### 路由配置

```shell
$ netstat -r    ## 查看当前系统路由信息表
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
default         ip-172-31-0-1.u 0.0.0.0         UG        0 0          0 eth0 
# default 表示默认路由
172.31.0.0      *               255.255.240.0   U         0 0          0 eth0
# 最后一个字节为0表示该地址和本机同处一个网络
```



#### `route` 增加或者删除一条路由

```shell
$ sudo route add default gw 10.71.84.2    ## 增加一条默认路由

## 使主机能访问到某个网络
## 连接到网络地址为 10.62.74.0/24 的网络
## 本地网络中 IP 地址为 10.71.84.51 的主机作为网关
$ sudo route add -net 10.62.74.0/24 gw 10.71.84.51  

# 将所有发送到 10.62.74.4 的包转发到 10.71.84.51 的主机上
$ sudo route add -host 10.62.74.4 gw 10.71.84.51 

# 删除路由
$ sudo route del default    ## 删除默认路由
```



#### `ifconfig` 启动或禁用一个网络接口，同时设置 IP地址、子网掩码以及其它网络项

```shell
$ ifconfig    # 显示当前系统上所有网络接口的配置

# 将网络接口 eth0 的 IP 地址设置为：192.168.1.14，子网掩码设置为：255.255.255.0，
# up：启动网络接口
$ sudo ifconfig eth0 192.168.1.14 netmask 255.255.255.0 up

# down：关闭网络接口
$ sudo ifocnfig eth0 down
```



#### `ping` 检测网络是否畅通 - 通过 ping 确认延时和丢包情况

```shell
$ ping ip地址          # 需要按 Ctrl + C 退出
$ ping -c 10 ip地址    # 指定发送包的数量：10次
$ ping -I 2 ip地址     # 间隔 2 秒

ping localhost        # ping 本机（127.0.0.1）
```

#### `trace` - 通过 trace 确认对应的路由节点
```
traceroute api.mch.weixin.qq.com
```

#### `mtr` - 通过 mtr 确认对应的路由节点丢包情况
```
mtr -4 -i 1 api.mch.weixin.qq.com
```

#### `netstat` 检测端口

netstat 主要用于 Linux 查看自身网络状况

```shell
netstat -a     # 列出所有当前的连接
netstat -at    # 列出 TCP 协议的连接
netstat -au    # 列出 UDP 协议的连接
netstat -nr    # 列出路由表
netstat -r     # 查看当前系统路由信息表
$ sudo netstat -tulnp | grep 22    ## 查看 22 端口状态
```



#### Nethogs 流量监控工具

1. [Nethogs](http://man.linuxde.net/nethogs) 是一个终端下的**网络流量监控**工具，可以显示每个进程的带宽占用情况。

   ```shell
   $ sudo apt-get install nethogs    ## 安装 NetHogs 包
   $ sudo nethogs eth0               ## 监控 eth0 网卡
   ```

2. Ifstat 是一个网络接口监测工具，**监控 I/O 状态 和 CPU 状态**。

   ```shell
   $ sudo apt-get install ifstat    ## Ubuntu 下安装 ifstat
   $ ifstat -l                      ## 使用
   ```





#### hostname

[ubuntu 临时或永久修改 hostname 的方法](https://www.jb51.net/article/115258.htm)

```shell
$ sudo vim /etc/hostname
clumsiest

$ sudo vim /etc/hosts
127.0.0.1 localhost
127.0.1.1 clumsiest
52.14.183.223 www.arlingbc.com

# 查看当前主机名
$ uname -u
```







# 搭建网络服务



Linux 系统的启动过程：

![image](http://upload-images.jianshu.io/upload_images/2648731-0af6c6a27b5be4c9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



* 内核引导：基本输入输出系统 BIOS —> 主引导记录MBR —> 引导加载器 Grub/LILO。
* init 的配置文件是：**/etc/inittab**



### 服务器启动脚本

* 启动服务器应用程序的脚本位于 **/etc/init.d** 目录下，每个脚本各控制着一个特定的守护进程。
* Ubuntu 和 Debain 使用 **upstart** (非 init) 管理启动脚本。Ubuntu 和 Debain 默认没有 inittab 文件，而是使用 **/etc/event.d/rc-default** 来确定启动的默认运行级。



#### `start`、`stop` 启动、停止服务器守护进程

```shell
$ sudo /etc/init.d/sshd start    ## 启动 SSH 服务器守护进程
$ sudo /etc/init.d/sshd stop     ## 停止 SSH 服务器守护进程
$ sudo /etc/init.d/sshd restart  ## 重启 SSH 服务器守护进程
```



#### `inetd` 、 `xinetd` 切换服务器守护进程的运行方式

服务器守护进程的2种运行方式：

1. **standalone 模式**：随系统启动而启动，并持续在后台监听。
2. **inetd/xinetd 模式**：在需要的时候启动，完成任务后把监听任务交给 inetd/xinetd。



#### 配置 xinetd

* xinetd 守护进程依赖于 **/etc/xinetd.conf** 的配置，用户应该为每个服务单独开辟一个文件，存放在 **/etc/xinetd.d**目录下。查看 xinetd. conf可以看到这一点:







### FTP服务器——提供文件上传和下载服务



### NFS服务器——网络文件系统，在计算机间共享文件资源



### Samba服务器——在 Windows 和 Linux 系统之间共享文件



### Apach 服务器——在 Linux 系统中组建网站





# Linux 系统远程登录

#### `ssh` 安全的shell

ssh 服务默认开启在 22 号端口。

ssh 服务配置文件：`/etc/ssh/sshd_config	`

```shell
################################################
$ sudo /etc/init.d/sshd start    # 启动 SSH 服务器守护进程
$ sudo /etc/init.d/sshd stop     # 停止 SSH 服务器守护进程
$ sudo /etc/init.d/sshd restart  # 重启 SSH 服务器守护进程


################################################
# 登录语法：ssh -l login_name hostname
# 登录语法：ssh -l login_name -p port_number hostname

$ ssh -l liu 192.168.150.139
$ ssh login_name@192.168.0.2    # 同上

# -p：指定端口号
$ ssh -l liu -p 202 192.168.150.139    
$ ssh -p 9122 login_name@192.168.0.2   # 同上

$ exit ## 断开SSH连接
```
[iTerm2 中 ssh 保持连接不断开](http://bluebiu.com/blog/iterm2-ssh-session-idle.html)


Mac 访问虚拟机 [shuttle](http://fitztrev.github.io/shuttle/)，windows 使用 [Xshell](https://www.netsarang.com/products/xsh_overview.html)、[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/)。

* [mac 下的效率工具 shuttle 介绍](http://blog.qingtian16265.com/2016/04/07/introduce-shuttle/)
* [Shuttle 安装以及配置简介](https://www.bluelzy.com/articles/shuttle_brief_introduction.html)
* [Shuttle - Mac 快捷菜单神器](https://www.jianshu.com/p/1f9091641312)


使用密钥认证机制远程登录Linux：PUTTYGEN.exe 生成密钥对。





## 安全设置



### 杀毒软件 [ClamAV](http://www.clamav.net/documents/upgrading-clamav)

ClamAV 是 Linux 上最流行的防病毒软件。它包含完整的防病毒工具库，并且更新迅速。

设置通过代理服务器上网，配置文件：**/etc/clamav/freshclam.conf**，添加如下：

```
HTTPProxyServer 你的IP地址
HTTPProxyPort   端口号
```



#### clamav 使用

```shell
# 下载安装 clamav
$ sudo apt-get install clamav   
$ sudo apt-get install clamtk    # 下载安装 ClamAV 的图形化工具：应用程序|附件|ClamTK

# 移除 clamav
$ sudo apt-get remove clamav

# 更新 ClamAV
$ sudo freshclam

$ clamscan                                      ##扫描当前目录（不会深入子目录）
$ sudo clamscan -r /media/station/document/     ##-r: 递归扫描目录（深入到子目录）
$ clamscan sum.exe                              ##扫描文件
$ clamscan --tgz ask.tar.gz                     ##--tgz: 扫描.tar.gz文件
```



处理打包文件的 clamscan 选项：

| 选项    | 适用的文件类型 |
| ------- | -------------- |
| --unrar | .rar 文件      |
| --arj   | .arj 文件      |
| --unzoo | .zoo 文件      |
| --lha   | .lzh 文件      |
| --jar   | .jar 文件      |
| --deb   | .deb 安装包    |
| --tar   | .tar 文件      |
| --tgz   | .tar.gz 文件   |



ClamAV 并没有提供**清除病毒**的功能。clamscan 处理**被感染文件**的选项：

| 选项             | 描述                                  |
| ---------------- | ------------------------------------- |
| --remove         | 删除受感染的文件                      |
| --move=DIRECTORY | 把受感染的文件移动到目录 DIRECTORY 下 |
| --copy=DIRECTORY | 把受感染的文件复制到目录 DIRECTORY 下 |



### iptables 防火墙

* Linux 的防火墙工具是 **IP Tables**。
* Linux 防火墙是一种典型的包过滤防火墙。
* 防火墙行为的主要依据是数据包的目的地址、端口号、协议类型。
* filter 过滤器默认包含3个链：FORWARD、INPUT、OUTPUT。

| 链      | 链中定义的规则作用               |
| ------- | -------------------------------- |
| FORWARD | 需要转发到另一个网络接口的数据包 |
| INPUT   | 发送到本机的数据包               |
| OUTPUT  | 从本机发送出去的数据包           |



![image](http://upload-images.jianshu.io/upload_images/2648731-387922f62a505f2a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### iptables 基本配置

语法：`iptables-t filter -A INPUT -p cimp -j DROP`

| 常用选项 | 含义                               |
| -------- | ---------------------------------- |
| -F       | 清除链中所有的规则                 |
| -P       | 为链设置一条默认策略（或者说目标） |
| -A       | 为链增加一条规则说明               |
| -D       | 从链中删除一条规则                 |
| -L       | 查看当前表中的链和规则             |



#### 查看是否安装防火墙

```shell
$ whereis iptables
iptables: /sbin/iptables /usr/share/iptables /usr/share/man/man8/iptables.8.gz # 表示已安装

$ sudo apt-get install iptables ##如果默认没有安装，请执行此命令安装防火墙
```



#### 1. 清空表数据

```shell
$ sudo iptables -F           # 清空默认表（filter）中的数据
$ sudo iptables -F INPUT     # 清空某一条特定的链（FORWARD、INPUT、OUTPUT）
```



#### 2. 查看防火墙设置

```shell
$ sudo iptables -L                   # 查看当前防火墙设置
$ sudo iptables -L --line-numbers    # 查看链规则的编号


Chain INPUT (policy DROP)
target     prot opt source               destination

# 允许ping操作
ACCEPT     icmp --  anywhere             anywhere             icmp echo-request 
#允许22端口SSH
ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:ssh
#允许80端口
ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:http
#开启访问日志
LOG        all  --  anywhere             anywhere             LOG level warning 
#允许 ping localhost
ACCEPT     all  --  anywhere             anywhere 

Chain FORWARD (policy DROP)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```



#### 3. 设置默认策略

```shell
# 设置默认策略
#
# sudo iptables -P chain-name target
# chain-name(链名): FORWARD/INPUT/OUTPUT
# target(目标字段策略): ACCEPT/DROP/REJECT/LOG

$ sudo iptables -P INPUT DROP             # 将 INPUT 链的默认策略设置为 DROP
$ sudo iptables -P FORWARD DROP           # 将 FORWARD 链的默认策略设置为 DROP
```

> ⚠️⚠️⚠️ 
>
> 使用 SSH 连接到服务器时，请不要在未添加22端口链规则的情况下直接设置默认DROP策略。——来自血的教训。



#### 4. 添加链规则

```shell
# 添加链规则
# iptables -A chain-name -i interface -j target
# chain-name(链名): FORWARD/INPUT/OUTPUT
# -i interface: 指定该规则适用的网络接口 ：eth0/lo
# target(目标字段策略): ACCEPT/DROP/REJECT/LOG
# -p: 匹配的网络协议：tcp/udp/icmp/ALL

$ sudo iptables -A INPUT -i lo -p ALL -j ACCEPT    #允许所有通过 lo 本地回环接口的连接请求
$ sudo iptables -A INPUT -i eth0 -p icmp --icmp-type 8 -j ACCEPT    #允许 ping
$ sudo iptables -A INPUT -i eth0 -p tcp --dport 80 -j ACCEPT        #增加 80 端口访问许可
$ sudo iptables -A INPUT -i eth0 -p tcp --dport 22 -j ACCEPT        #增加 22 端口访问许可

# 将 SSH 服务设置为只对本地网络的用户开发
$ sudo iptables -A INPUT -i eth0 -s 10.62.74.0/24 -p tcp --dport 22 -j ACCEPT

# 将 222.24.21.195 发送到本机的 ICMP 封包丢弃(ping)
iptables-A INPUT -p icmp -s 222.24.21.195 -j DROP
```



#### 5. 添加LOG（日志记录）策略

记录哪些人正在试图访问服务器

```shell
$ sudo iptables -A INPUT -i eht0 -j LOG                            #添加LOG（日志记录）策略
$ sudo iptables -A INPUT -i eth0 -j LOG --log-level 4
```

* 默认情况下，防火墙记录的访问信息被保存在 **/var/log/messages** 中。
* 日志分析工具：swatch、logcheck。（来源：sourceforge.net）
* [How to Enable Logging in Iptables on Linux](https://tecadmin.net/enable-logging-in-iptables-on-linux/)
* [Linux 使用问题杂记 -- 解决无 / var/log/messages 问题](https://blog.csdn.net/C_Major/article/details/51321684)
* [Ubuntu 14.04 配置 iptables 防火墙](https://www.cnblogs.com/yhdsir/p/4920138.html)
* [iptables 日志分析](https://www.jianshu.com/p/33469ad74180)





#### 6. 删除链规则

语法：

```shell
# 1. 使用规则描述来匹配某条链规则
iptables -D chain rule-specification
iptables -D INPUT -i lo -p ALL -j ACCEPT    # 删除对 lo 环回接口的规则设置

# 2. 使用编号
iptables -d chain rulenum

$ sudo iptables -L --line-numbers    # 1.先查看链规则的编号
$ sudo iptables -D 11                # 2.删除编号为11的链规则
```



#### iptables 备份：`iptables-save`

**iptables-save** 命令用于批量导出 iptables 防火墙规则。

```shell
# 备份所有表的规则
$ iptables-save > /opt/iprules_all.txt
$ service iptables save    ##该命令默认保存到 /etc/sysconfig/iptables
```



#### iptables 恢复：`iptables-restore`

**iptables-restore** 命令用于批量导入 iptables 防火墙规则。

```shell
# 将备份到规则恢复到 iptables 中
$ iptables-restore < /opt/iprules_all.txt
$ service iptables start    ##该命令默认将 /etc/sysconfig/iptables 文件的内容加载到 iptbles 中
```





## 网络安全工具

### 端口扫描工具 NMap

* nmap 用于扫描一组主机的网络端口。
* 80端口用于提供 HTTP 服务、22端口接受 SSH 连接、21端口提供FTP服务。

```shell
# 安装 nmap
$ sudo apt install nmap

 ##扫描dbl.example.org
$ nmap -sT dbl.example.org    

##############################################################################
$ nmap -sT insecure.org        ##扫描insecure.org

Starting Nmap 7.01 ( https://nmap.org ) at 2018-06-01 08:57 UTC
Nmap scan report for insecure.org (45.33.49.119)
Host is up (0.071s latency).
Other addresses for insecure.org (not scanned): 2600:3c01::f03c:91ff:fe98:ff4e
rDNS record for 45.33.49.119: ack.nmap.org
Not shown: 993 filtered ports
PORT      STATE  SERVICE
22/tcp    open   ssh
25/tcp    open   smtp
70/tcp    closed gopher
80/tcp    open   http
113/tcp   closed ident
443/tcp   open   https
31337/tcp closed Elite

Nmap done: 1 IP address (1 host up) scanned in 5.47 seconds
##############################################################################

$ sudo nmap -O -sV dbl.example.org
# -O: 探测操作系统
# -SV: 探测端口上运行的软件

$ nmap -sT -Pn ipAddress             ## 强制扫描
$ nmap -sT -PN -p1-5000 ipAddress    ## 指定对主机的 1～5000 号端口进行扫描
```

### 漏洞扫描工具 Nessus

[Nessus](https://www.tenable.com/downloads/nessus)
