[TOC]

## shell 环境

> 在 shell 会话中维护着大量的信息，这些信息称为 (shell) 环境。 存储在 shell 环境中的数据被程序用来确定配置属性。然而大多数程序用配置文件来存储程序设置，某些程序也会查找存储在 shell 环境中的数值来调整他们的行为。

shell 在环境中存储了两种基本类型的数据：
1. shell 变量和环境变量；
2. 可编程数据：命令别名和 shell 函数；

有两种 shell 会话类型：
1. 登录 shell 的会话；
2. 非登录 shell 的会话；

登录 shell 会读取一个或多个启动文件，登录 shell 会话的启动文件：

| 文件 | 内容 |
| --- | --- |
| /etc/profile | 应用于所有用户的全局配置脚本。 |
| ~/.bash_profile | 用户私人的启动文件。可以用来扩展或重写全局配置脚本中的设置。 |
| ~/.bash_login | 如果文件 ~/.bash_profile 没有找到，bash 会尝试读取这个脚本。 |
| ~/.profile | 如果文件 ~/.bash_profile 或文件 ~/.bash_login 都没有找到，bash 会试图读取这个文件。 这是基于 Debian 发行版的默认设置，比方说 Ubuntu。 |

非登录 shell 会话会读取以下启动文件：

| 文件 | 内容 |
| --- | --- |
| /etc/bash.bashrc | 应用于所有用户的全局配置文件。 |
| ~/.bashrc | 用户私有的启动文件，可以用来扩展或重写全局配置脚本中的设置。 |

### 修改 shell 环境
我们应该修改哪个文件？

按照通常的规则，添加目录到你的 PATH 变量或者是定义额外的环境变量，要把这些更改放置到 **.bash_profile** 文件中（或者其替代文件中，根据不同的发行版。例如，Ubuntu 使用 **.profile** 文件）。对于其它的更改，要放到 **.bashrc** 文件中。除非你是系统管理员，需要为系统中的所有用户修改默认设置，那么则限定你只能对自己主目录下的文件进行修改。当然，有可能会更改 **/etc** 目录中的文件，比如说 profile 文件，而且在许多情况下，修改这些文件也是明智的，但是现在，我们要安全起见。

### 使用文本编辑器
文本编辑器分为两种基本类型：
* 图形化的编辑器：gedit。
* 基于文本的编辑器：nano，vi、vim 和 emacs。

```shell
# 1. 编辑之前备份文件
$ cp .bashrc .bashrc.bak

# 使用 nano 编辑器打开文件
$ nano .bashrc

# 激活修改
$ source .bashrc
```

### `printenv` 打印部分或所有的环境变量
显示环境变量及其数值的列表：
```shell
# printenv 只是显示环境变量
$ printenv | less

# set 命令可以显示 shell 和环境变量
$ set | less
```

列出特定变量的数值：
```
$ printenv USER
root

$ printenv PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

#### 一些有趣的变量

环境变量：
| 变量 | 内容 |
| --- | --- |
| DISPLAY | 如果你正在运行图形界面环境，那么这个变量就是你显示器的名字。通常，它是 ":0"， 意思是由 X 产生的第一个显示器。 |
| EDITOR | 文本编辑器的名字。 |
| SHELL | shell 程序的名字。(SHELL=/bin/bash)|
| HOME | 用户主目录。(HOME=/root) |
| LANG | 定义了字符集以及语言编码方式。(LANG=en_US.UTF-8) |
| OLD_PWD | 先前的工作目录。 |
| PAGER | 页输出程序的名字。这经常设置为 / usr/bin/less。 |
| PATH | 由冒号分开的目录列表，当你输入可执行程序名后，会搜索这个目录列表。 |
| PS1 | Prompt String 1. 这个定义了你的 shell 提示符的内容。随后我们可以看到，这个变量内容可以全面地定制。 |
| PWD | 当前工作目录。 |
| TERM | 终端类型名。类似于 Unix 的系统支持许多终端协议；这个变量设置你的终端仿真器所用的协议。 |
| TZ | 指定你所在的时区。大多数类似于 Unix 的系统按照协调时间时 (UTC) 来维护计算机内部的时钟 ，然后应用一个由这个变量指定的偏差来显示本地时间。 |
| USER | USER |


### `set` 设置 shell 选项



### `export` 导出环境变量，让随后执行的程序知道

让 shell 的子进程可以使用 PATH 变量的内容：
```shell
export PATH
```


### `alias` 创建命令别名

```
# Change umask to make directory sharing easier
umask 0002
 # Ignore duplicates in command history and increase
 # history size to 1000 lines
export HISTCONTROL=ignoredups
export HISTSIZE=1000
 # Add some helpful aliases
alias l.='ls -d .* --color=auto'
alias ll='ls -l --color=auto'
```

