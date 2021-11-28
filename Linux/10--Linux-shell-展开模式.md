[TOC]

## shell 展开模式

> 大多数的参数展开会用在脚本中，而不是命令行中。

### `echo` 显示一行文本
* `echo` 是一个 shell 内部命令，它在标准输出中打印它的文本参数。

```shell
$ echo this is a test
this is a test
```

「字符展开」的概念：用户输入 ——> bash 字符展开 ——> 输出

```
$ ls
Agora_Recording                                   PaxosUdp_let    p2p    t-blockchain_screen
Agora_Recording_SDK_for_Linux_v2_2_2_FULL.tar.gz  nodejs-v8.10.0  paxos

$ echo *
Agora_Recording Agora_Recording_SDK_for_Linux_v2_2_2_FULL.tar.gz nodejs-v8.10.0 p2p paxos PaxosUdp_let t-blockchain_screen
```
这个示例中，shell 把 "* " 展开成了另外的东西（在这种情况下，就是在当前工作目录下的文件名字），在 `echo` 命令被执行前。当回车键被按下时，shell 在命令被执行前在命令行上自动展开任何符合条件的字符， 所以 `echo` 命令从不会发现 "* ", 只把它展开成结果。

### 路径名展开
通配符工作机制叫做路径名展开。
```shell
$ ls
Agora_Recording                                   PaxosUdp_let    p2p    t-blockchain_screen
Agora_Recording_SDK_for_Linux_v2_2_2_FULL.tar.gz  nodejs-v8.10.0  paxos

$ echo A*
Agora_Recording Agora_Recording_SDK_for_Linux_v2_2_2_FULL.tar.gz

$ echo *s
paxos

$ echo [[:upper:]]*
Agora_Recording Agora_Recording_SDK_for_Linux_v2_2_2_FULL.tar.gz PaxosUdp_let

$ echo /usr/*/share
/usr/local/share
```

### 波浪线展开
当它用在一个单词的开头时，它会展开成指定用户的主目录名，如果没有指定用户名，则是当前用户的主目录。
```shell
$ echo ~
/root

$ echo ~andy
/home/andy
```

### 算术表达式展开
算术表达式展开格式：`$((expression))`。
* shell 允许算术表达式通过展开来执行。这允许我们把 shell 提示当作计算器来使用。
* 算术表达式只支持整数（全部是数字，不带小数点），但是能执行很多不同的操作。

| 算术操作符 | 说明 |
| --- | --- |
| + | 加 |
| - | 减 |
| * | 乘 |
| / | 除（但是记住，因为展开只是支持整数除法，所以结果是整数。） |
| % | 取余，只是简单的意味着，“余数” |
| ** | 取幂 |

```shell
$ echo $((2+2))
4

$ echo $(((5**2) * 3))
75
```

### 花括号展开
从一个包含花括号的模式中创建多个文本字符串。

```shell
$ echo Front-{A,B,C}-Back
Front-A-Back Front-B-Back Front-C-Back

$ echo Num_{1..5}
Num_1 Num_2 Num_3 Num_4 Num_5

$ echo {Z..A}
Z Y X W V U T S R Q P O N M L K J I H G F E D C B A

$ echo a{A{1,2},B{3,4}}b
aA1b aA2b aB3b aB4b
```
示例：创建一系列的文件或目录列表。
```
$ mkdir Pics
$ cd Pics
$ mkdir {2007..2009}-{1..9} {2007..2009}-{10..12}
$ ls
2007-1   2007-12  2007-4  2007-7  2008-1   2008-12  2008-4  2008-7  2009-1   2009-12  2009-4  2009-7
2007-10  2007-2   2007-5  2007-8  2008-10  2008-2   2008-5  2008-8  2009-10  2009-2   2009-5  2009-8
2007-11  2007-3   2007-6  2007-9  2008-11  2008-3   2008-6  2008-9  2009-11  2009-3   2009-6  2009-9
```

### 参数展开
示例：一个叫做 "USER" 的变量包含你的用户名。
```shell
$ echo $USER
root

# 查看当前登录用户名
$ echo $LOGNAME
root

查看HOME目录
# echo $HOME
/root

# 查看当前在线用户
$ who
root     pts/3        Jan 14 09:49 (111.187.44.62)

# 查看系统语言设置
echo $LANG
en_US.UTF-8
```
示例：显示环境变量：
```shell
# 打印所有环境变量及其数值列表
$ printenv | less
```

#### 环境变量 PATH
命令可以直接使用，而不需要指定命令所处的绝对路径，是因为 PATH 的作用：
```shell
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

### 命令替换
命令替换允许我们把一个命令的输出作为一个展开模式来使用：
```shell
$ echo $(ls)
Agora_Recording Agora_Recording_SDK_for_Linux_v2_2_2_FULL.tar.gz PaxosUdp_let nodejs-v8.10.0 p2p paxos t-blockchain_screen

# 把 which cp 的执行结果作为一个参数传递给 ls 命令
$ ls -l $(which cp)
-rwxr-xr-x 1 root root 151024 Mar  3  2017 /bin/cp

# 管道线的输出结果成为 file 命令的参数列表
$ file $(ls /usr/bin/* | grep zip)
/usr/bin/gpg-zip:    POSIX shell script, ASCII text executable
/usr/bin/zipdetails: a /usr/bin/perl script, ASCII text executable
...
```

### 引用
shell 提供了一种叫做引用的机制，来有选择地禁止不需要的展开。
```shell
# shell 会从 echo 命令的参数列表中，删除多余的空格。
$ echo this is a    test
this is a test

# 参数展开把 $1 的值替换为一个空字符串，因为 1 是没有定义的变量。
$ echo The total is $100.00
The total is 00.00
```

### 双引号
把文本放在双引号中后，shell 使用的特殊字符，除了 $，\ (反斜杠），和 `（倒引号）之外， 则失去它们的特殊含义，被当作普通字符来看待。这意味着单词分割，路径名展开，波浪线展开，和花括号展开都被禁止，**然而参数展开，算术展开，和命令替换仍然有效。**

示例：使用双引号，可以处理包含空格的文件：
```shell
$ ls -l two words.txt
ls: cannot access 'two': No such file or directory
ls: cannot access 'words.txt': No such file or directory

# 使用双引号可以阻止「单词分割」生效
$ ls -l "two words.txt"

# 修复破损的文件
mv "two words.txt" two_words.txt
```

在双引号中，参数展开，算术表达式展开，和命令替换仍然有效：
```shell
$ echo "$USER $((2+2)) $(cal)"
root 4     January 2019
Su Mo Tu We Th Fr Sa
       1  2  3  4  5
 6  7  8  9 10 11 12
13 14 15 16 17 18 19
20 21 22 23 24 25 26
27 28 29 30 31
```

【单词分割机制】
在默认情况下，单词分割机制会在单词中寻找空格，制表符，和换行符，并把它们看作单词之间的界定符。
```shell
# 单词分割被禁止，内嵌的空格也不会被当作界定符，它们成为参数的一部分。
echo "this is a    test"
this is a    test
```

事实上，单词分割机制把换行符看作界定符，对命令替换产生了一个，虽然微妙，但有趣的影响。
```shell
# 1. 没有引用的命令替换导致命令行包含 38 个参数。
$ echo $(cal)
January 2019 Su Mo Tu We Th Fr Sa 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31

# 2. 命令行只有一个参数，参数中包括嵌入的空格和换行符。
$ echo "$(cal)"
    January 2019
Su Mo Tu We Th Fr Sa
       1  2  3  4  5
 6  7  8  9 10 11 12
13 14 15 16 17 18 19
20 21 22 23 24 25 26
27 28 29 30 31
```

### 单引号
如果需要禁止所有的展开，需要使用单引号。
```shell
# 1. 无引用
$ echo text ~/*.txt {a,b} $(echo foo) $((2+2)) $USER
text /root/*.txt a b foo 4 root

# 2. 双引号
$ echo "text ~/*.txt {a,b} $(echo foo) $((2+2)) $USER"
text ~/*.txt {a,b} foo 4 root

# 3. 单引号
$ echo 'text ~/*.txt {a,b} $(echo foo) $((2+2)) $USER'
text ~/*.txt {a,b} $(echo foo) $((2+2)) $USER
```

### 转义字符
有时候我们只想引用单个字符。我们可以在字符之前加上一个反斜杠，在这个上下文中叫做转义字符。
```shell
$ echo "The balance for user $USER is: \$5.00"
The balance for user root is: $5.00
```
使用转义字符来消除文件名中一个字符的特殊含义，是很普遍的。例如，在文件名中可能使用一些对于 shell 来说，有特殊含义的字符。这些字符包括 "$", "!", " " 等字符。在文件名中包含特殊字符，你可以这样做：
```shell
$ mv bad\&filename good_filename
```
为了允许反斜杠字符出现，输入 "\" 来转义。注意在单引号中，反斜杠失去它的特殊含义，它会被看作普通字符。

> 【反斜杠转义字符序列】
> 反斜杠除了作为转义字符外，反斜杠也是一种表示法的一部分，这种表示法代表某种特殊字符，叫做控制码。
> 
> | 转义序列 | 含义 |
> | --- | --- |
> | \a | 响铃（"警告"－导致计算机嘟嘟响 |
> | \b | 退格符 |
> | \n | 新的一行。在类似 Unix 系统中，产生换行。 |
> | \r | 回车符 |
> | \t | 制表符 |

echo 命令带上 "-e" 选项，能够解释转义序列。你可以把转义序列放在 $' '里面。 
```shell
# 10s 之后，系统提示“Time's up”并发出声音。
$ sleep 10; echo -e "Time's up\a"
Time's up

# 同上
$ sleep 10; echo -e "Time's up $'\a'"
```
