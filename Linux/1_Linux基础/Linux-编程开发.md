> 💡最后更新：20180815



# 编辑器

* Vim、Emacs：功能强大的编辑工具、支持扩展设置。
* gedit：Linux 下的纯文本编辑器。



## Vim 编辑器

* Vim 的设计哲学：让程序员能够在主键盘区域完成所有工作。

* Vim 是 Vi 的增强版本，Vi 工作在其他大部分 Unix 系统中。

* Vim 的三种模式：底行模式、编辑模式、命令模式。

  1. 底行模式/行命令模式：输入命令；
  2. 编辑模式/插入模式：输入字符；
  3. 命令模式：执行除了输入字符外的所有操作，包括保存、搜索、移动光标等。

  ![image](http://upload-images.jianshu.io/upload_images/2648731-6ea4cb2ba78403ec.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* Vim 命令严格区分大小写。

* 移动光标：【H—左】、【J—下】、【K—上】、【L—右】。
* $：移动到行尾。
* g：移动到文件尾。
* o：在下方插入一行，并自动进入插入模式。
* x：删除光标所在位置的字符。
* dd：删除行
* `:wq` 保存并退出。
* `:q!` 放弃修改并强制退出。(⚠️ 不是`!q`)



#### 搜索字符串：`/string`

* n 跳转下一个匹配字符、“/”表示向前查找、“?”表示向后查找。

* 搜索大小写敏感设置：

  ```
  :set ignorecase    # 关闭大小写敏感搜索
  :set noignorecase  # 开启大小写敏感搜索
  ```

  

#### 替换字符串

```shell
# 完整语法：
:[range]s/pattern/string/[c,e,g,i]    # 将 pattern 所代表的字符串替换为 string

# [range]
# [1,4] 从第一行到第四行
# [1,$] 从第一行到最后一行
# [%] 全文搜索

# 可选字符
# c:每次替换前询问
# e:不显示错误信息
# g:替换一行中的所有匹配项
# i:不区分大小写

:%s/^M$//g    # 清除文本文件中的“^M”字符，一般用于处理来自 Windows 的源码文件。
```



#### 针对程序员的配置

* 打开语法高亮: `:syntax on`，已默认开启。
* 打开自动缩进:`:set autoindent`
* 设置Tab自动缩进值:`:set shiftwidth=4`



## GNU 编译器套件

* GNU 编译器套件包括 C、C++、Objective-C、Fortran、Java、Ada 和 Go 语言的前端，也包括这些语言的库（如 libstdc++、libgci 等）。
* GCC 和 G++ 都是 GNU 的一个编译器套件。
* 生成一个可执行文件：1. 编译；2. 链接。



C 编译的四个阶段：

1. 预处理阶段；
2. 编译、优化阶段；
3. 汇编阶段；
4. 链接阶段；



### Shell 编程

Hello World 示例：

1. 编写脚本文件：hello

```shell
#! /bin/bash 
#Display a line

echo "Hello World!"
```
* "#！" 指定脚本的运行环境。


2. 执行 Shell 脚本

```shell
$ chmod +x hello    # 为脚本加上可执行权限

$ ./hello           # 执行脚本
$ bash hello        # 同上
Hello World!
```



