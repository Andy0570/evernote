[TOC]

## vi
### 使用 vi 的三个原因
1. vi 很多系统都预装。
2. vi 是轻量级且执行快速的编辑器。
3. 我们不希望其他 Linux 和 Unix 用户把我们看作胆小鬼。

> 大多数 Linux 发行版不包含真正的 vi；而是自带一款高级替代版本，叫做 vim（它是 “vi improved” 的简写）由 Bram Moolenaar 开发。vim 相对于传统的 Unix vi 来说，取得了实质性进步。通常，vim 在 Linux 系统中是 “vi” 的符号链接（或别名）。

> 💡 小贴示
>  如果你在 vi 中 “迷失” 了，试着按下 Esc 键两次来找到路（回到普通模式）。

### 三种模式

![三种模式的相互切换](http://upload-images.jianshu.io/upload_images/2648731-774a06b32b76f51e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 命令模式
  ```shell
  dd、ndd：删除 n行
  yy、nyy：复制
  /name ?name 搜索匹配字符
  i：插入模式
  a：追加文本
  A：将光标移动到当前行尾，同时切换到 insert 模式。
  o：在下方插入新行
  u：撤销最后一次修改
  ```
  

* 输入模式
* 末行模式

  ```shell
  w：保存
  q: 退出Vi
  q!: 强制退出vi，但是不保存设置。
  wq: 保存设置并退出。
  /*** 查找
  ```





### 移动光标

| 命令 | 行为 |
| --- | --- |
| l or 右箭头 | 向右移动一个字符 |
| h or 左箭头 | 向左移动一个字符 |
| j or 下箭头 | 向下移动一行 |
| k or 上箭头 | 向上移动一行 |
| 0（零按键） | 移动到当前行的行首。 |
| ^ | 移动到当前行的第一个非空字符。 |
| $ | 移动到当前行的末尾。 |
| w | 移动到下一个单词或标点符号的开头。 |
| W | 移动到下一个单词的开头，忽略标点符号。 |
| b | 移动到上一个单词或标点符号的开头。 |
| B | 移动到上一个单词的开头，忽略标点符号。 |
| Ctrl+F or Page Down | 向下翻一页 |
| Ctrl+B or Page Up | 向上翻一页 |
| numberG | 移动到第 number 行。例如，1G 移动到文件的第一行。 |
| G | 移动到文件末尾。 |

> vi 中的许多命令都可以在前面加上一个数字，比方说上面提到的 "G" 命令。
> 在命令之前加上一个 数字，我们就可以指定命令执行的次数。
> 例如，命令 "5j" 导致 vi 向下移动 5 行。

### 打开一行

| 命令 | 行为 |
| --- | --- |
| o | 当前行的下方打开一行。 |
| O | 当前行的上方打开一行。 |

### 删除文本

| 删除命令 | 删除的文本 |
| --- | --- |
| x | 当前字符 |
| 3x | 当前字符及其后的两个字符。 |
| dd | 当前行。 |
| 5dd | 当前行及随后的四行文本。 |
| dW | 从光标位置开始到下一个单词的开头。 |
| d$ | 从光标位置开始到当前行的行尾。 |
| d0 | 从光标位置开始到当前行的行首。 |
| d^ | 从光标位置开始到文本行的第一个非空字符。 |
| dG | 从当前行到文件的末尾。 |
| d20G | 从当前行到文件的第 20 行。 |

### 剪切、复制和粘贴文本

d 命令不仅删除文本，它还 “剪切” 文本。每次我们使用 d 命令，删除的部分被复制到一个粘贴缓冲区中（看作剪切板）。过后我们执行小 p 命令把剪切板中的文本粘贴到光标位置之后， 或者是大 P 命令把文本粘贴到光标之前。

| 复制命令 | 复制的内容 |
| --- | --- |
| yy | 当前行。 |
| 5yy | 当前行及随后的四行文本。 |
| yW | 从当前光标位置到下一个单词的开头。 |
| y$ | 从当前光标位置到当前行的末尾。 |
| y0 | 从当前光标位置到行首。 |
| y^ | 从当前光标位置到文本行的第一个非空字符。 |
| yG | 从当前行到文件末尾。 |
| y20G | 从当前行到文件的第 20 行。 |

### 连接行
大写 J 把行与行之间连接起来。

### 查找和替换

#### 查找一行
f 命令查找一行，移动光标到下一个所指定的字符上。例如，命令 fa 会把光标定位到同一行中下一个出现的 "a" 字符上。在一行中执行了字符的查找命令之后，通过输入分号来重复这个查找。

#### 查找整个文件
/string 查找某个单词或短语，n 命令重复先前的查找。

### 全局查找和替换
vi 使用 ex 命令来执行查找和替代操作（vi 中叫做 “替换”）。

示例：把整个文件中的单词“line”更改为“Line”：

```shell
:%s/line/Line/g
```

| 条目 | 含义 |
| --- | --- |
| : | 冒号字符运行一个 ex 命令。 |
| % | 指定要操作的行数。% 是一个快捷方式，表示从第一行到最后一行。另外，操作范围也可以用 1,5 来代替（因为我们的文件只有 5 行文本），或者用 1,$ 来代替，意思是 “从第一行到文件的最后一行。” 如果省略了文本行的范围，那么操作只对当前行生效。 |
| s | 指定操作。在这种情况下是，替换（查找与替代）。 |
| /line/Line | 查找类型与替代文本。 |
| g | 这是 “全局” 的意思，意味着对文本行中所有匹配的字符串执行查找和替换操作。如果省略 g，则只替换每个文本行中第一个匹配的字符串。 |

示例，加“c”字符，指定一个需要用户确认的替换命令
```
# c：指定用户需要确认替换
:%s/line/Line/gc
replace with line (y/n/a/q/l/^E/^Y)?
```

| 替换确认按键选项 | 行为 |
| --- | --- |
| y | 执行替换操作 |
| n | 跳过这个匹配的实例 |
| a | 对这个及随后所有匹配的字符串执行替换操作。 |
| q or esc | 退出替换操作。 |
| l | 执行这次替换并退出。l 是 “last” 的简写。 |
| Ctrl+E, Ctrl+Y | 分别是向下滚动和向上滚动。用于查看建议替换的上下文。 |


#### vim 搜索高亮设置
依赖于系统中 vim 的特殊配置，搜索🔍匹配项将会被高亮显示。如若不是，试试这个命令模式：
```
:hlsearch
```


### 编辑多个文件
同时编辑多个文件：`vi file1 file2 file3...`

#### 文件之间转换

* 从这个文件转到下一个文件：`:n`
* 回到先前的文件：`:N`

查看正在编辑的文件列表：
```
:buffers
1 #     "foo.txt"                 line 1
2 %a    "ls-output.txt"           line 0
Press ENTER or type command to continue
```
然后可以使用缓冲区号码来转换文件：`:buffer 1`

### 从一个文件复制内容到另一个文件

使用缓冲区号码来在文件之间切换：`:buffer 1`


### 插入整个文件到另一个文件

:r 命令（是 "read" 的简称）把指定的文件插入到光标位置之前：
```
:r file.txt
```

### 保存
* 在命令模式下，输入 zz 保存并退出当前文件。
* 

```shell
:w 保存
:wq 保存并退出
:w file.txt 保存指定文件为副本（类似于 Save As...）
```

## vim 口令速查表
![image](http://upload-images.jianshu.io/upload_images/2648731-8371abd7d006d4cd.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image](http://upload-images.jianshu.io/upload_images/2648731-76a7477c182ffdc8.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image](http://upload-images.jianshu.io/upload_images/2648731-80079de06a7db1e1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image](http://upload-images.jianshu.io/upload_images/2648731-1ac102fc1b32ff2a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image](http://upload-images.jianshu.io/upload_images/2648731-55e0863ff37a62fc.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image](http://upload-images.jianshu.io/upload_images/2648731-d7aa2a944228e848.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image](http://upload-images.jianshu.io/upload_images/2648731-17ce4679edd89cb5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image](http://upload-images.jianshu.io/upload_images/2648731-d69592f51d9d57a9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image](http://upload-images.jianshu.io/upload_images/2648731-d376f15e8046d7fb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image](http://upload-images.jianshu.io/upload_images/2648731-c2b20dcdff0feeb9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
参考：[Micheal: VIM Cheat Sheet for Programmers](http://michael.peopleofhonoronly.com/vim/)

### 参考
* [Learning the vi Editor](https://en.wikibooks.org/wiki/Learning_the_vi_Editor)
* [程序员请收好，这里整理了一堆 Vim 口令速查表](https://m.ithome.com/html/391074.htm)
* [5 Best VIM Cheat Sheet](https://rumorscity.com/2014/08/16/5-best-vim-cheat-sheet/)
