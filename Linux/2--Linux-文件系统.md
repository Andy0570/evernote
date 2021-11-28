[TOC]

## Linux 发行版
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


## Linux 文件系统格式
| 操作系统 | 文件系统格式          |
| -------- | --------------------- |
| Windows  | FAT、NTFS           |
| Linux    | Ext2、Ext3、 reiserFS |

![image](http://upload-images.jianshu.io/upload_images/2648731-e297e61a474a3296.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* Ext2 文件系统是非日志文件系统。
* Ext3 文件系统带有日志功能，可以跟踪记录文件系统的变化，并将变化内容写入日志。
* 在 Linux 系统中你可以挂载 Windows 的文件系统，linux 目前支持 MS-DOS，VFAT，FAT，BSD 等格式。
* 如果你使用的是 Redhat 或者 CentOS，那么你不要妄图挂载 NFS 格式的文件到 Linux 下，因为它不支持 NFS。
* Ext3 文件系统为 Redhat/CentOS 默认使用的文件系统。

## Linux 文件类型

1. **普通文件（regular file）**：一般类型的文件，当用 `ls –l` 查看某个目录时，第一个属性为”-“的文件就是正规文件，或者叫普通文件。正规文件又可分成纯文字文件（ascii）和二进制文件（binary）。纯文本文件可以通过 `cat`, `more`, `less` 等工具直接查看内容，而二进制文件并不能。例如我们用的命令 /bin/ls 这就是一个二进制文件。
2. **目录（directory）**：这个很容易理解，就是目录，跟 windows 下的文件夹一个意思，只不过在 Linux 中我们不叫文件夹，而是叫做目录。`ls –l` 查看第一个属性为”d”。
3. **连接档（link）**：`ls –l` 查看第一个属性为 “l”，类似 windows 下的快捷方式。这种文件在 Linux 中很常见。
4. **设备档（device）**：与系统周边相关的一些档案，通常都集中在 /dev 这个目录之下！通常又分为两种：
   * **区块(block) 设备档**：就是一些储存数据，以提供系统存取的接口设备，简单的说就是硬盘啦！例如你的一号硬盘的代码是/dev/hda1 等等的档案啦！第一个属性为 “ b “；
   * **字符 (character) 设备档**：亦即是一些串行端口的接口设备，例如键盘、鼠标等等！第一个属性为 “ c “。

## Linux 系统中的目录

* Linux 以分层目录结构来组织所有文件。
* 在 Linux 中，任何东西都是一个文件。

| 目录 | 描述 |
| --- | --- |
| / | 根目录，万物起源。 |
| /bin | 包含系统启动和运行所必须的二进制程序。|
| /boot | 包含 Linux 内核，最初的 RMA 磁盘映像（系统启动时，由驱动程序所需），和启动加载程序。 有趣的文件：/boot/grub/grub.conf or menu.lst，被用来配置启动加载程序。/boot/vmlinuz，Linux 内核。|
| /dev | 这是一个包含设备结点的特殊目录。“一切都是文件”，也适用于设备。 在这个目录里，内核维护着它支持的设备。|
| /etc | 这个目录包含所有系统层面的配置文件。它也包含一系列的 shell 脚本，在系统启动时，这些脚本会运行每个系统服务。这个目录中的任何文件应该是可读的文本文件。有意思的文件：虽然 /etc 目录中的任何文件都有趣，但这里只列出了一些我一直喜欢的文件：/etc/crontab， 定义自动运行的任务。/etc/fstab，包含存储设备的列表，以及与他们相关的挂载点。/etc/passwd，包含用户帐号列表。|
| /home | 在通常的配置环境下，系统会在 /home 下，给每个用户分配一个目录。普通只能在他们自己的目录下创建文件。这个限制保护系统免受错误的用户活动破坏。 |
| /lib | 包含核心系统程序所需的库文件。这些文件与 Windows 中的动态链接库相似。|
| /lost+found | 每个使用 Linux 文件系统的格式化分区或设备，例如 ext3 文件系统， 都会有这个目录。当部分恢复一个损坏的文件系统时，会用到这个目录。除非文件系统 真正的损坏了，那么这个目录会是个空目录 |
| /media | 在现在的 Linux 系统中，/media 目录会包含可移除媒体设备的挂载点， 例如 USB 驱动器，CD-ROMs 等等。这些设备连接到计算机之后，会自动地挂载到这个目录结点下。|
| /mnt | 在早些的 Linux 系统中，/mnt 目录包含可移除设备的挂载点。|
| /opt | 这个 /opt 目录被用来安装 “可选的” 软件。这个主要用来存储可能安装在系统中的商业软件产品。|
| /proc | 这个 /proc 目录很特殊。从存储在硬盘上的文件的意义上说，它不是真正的文件系统。 反而，它是一个由 Linux 内核维护的虚拟文件系统。它所包含的文件是内核的窥视孔。这些文件是可读的， 它们会告诉你内核是怎样监管计算机的。|
| /root | root 帐户的主目录。|
| /sbin | 	这个目录包含 “系统” 二进制文件。它们是完成重大系统任务的程序，通常为超级用户保留。 |
| /tmp | 这个 /tmp 目录，是用来存储由各种程序创建的临时文件的地方。一些配置，导致系统每次 重新启动时，都会清空这个目录。|
| /usr | 在 Linux 系统中，/usr 目录可能是最大的一个。它包含普通用户所需要的所有程序和文件。 |
| /usr/bin | /usr/bin 目录包含系统安装的可执行程序。通常，这个目录会包含许多程序。|
| /usr/lib	 | 包含由 /usr/bin 目录中的程序所用的共享库。|
| /usr/local | 这个 /usr/local 目录，是非系统发行版自带，却打算让系统使用的程序的安装目录。 通常，由源码编译的程序会安装在 /usr/local/bin 目录下。新安装的 Linux 系统中，会存在这个目录， 但却是空目录，直到系统管理员放些东西到它里面。|
| /usr/sbin | 包含许多系统管理程序。|
|/usr/share| /usr/share 目录包含许多由 /usr/bin 目录中的程序使用的共享数据。 其中包括像默认的配置文件，图标，桌面背景，音频文件等等。|
| /usr/share/doc | 大多数安装在系统中的软件包会包含一些文档。在 /usr/share/doc 目录下， 我们可以找到按照软件包分类的文档。|
| /var | 除了 /tmp 和 /home 目录之外，相对来说，目前我们看到的目录是静态的，这是说， 它们的内容不会改变。/var 目录是可能需要改动的文件存储的地方。各种数据库，假脱机文件， 用户邮件等等，都驻扎在这里。|
| /var/log | 这个 /var/log 目录包含日志文件，各种系统活动的记录。这些文件非常重要，并且 应该时时监测它们。其中最重要的一个文件是 /var/log/messages。注意，为了系统安全，在一些系统中， 你必须是超级用户才能查看这些日志文件。|

需要关注的几个目录：
* /etc，这个目录用来存放所有的系统管理所需要的配置文件和子目录。
* /bin, /sbin, /usr/bin, /usr/sbin: 这是系统预设的执行文件的放置目录。
* /bin, /usr/bin，给系统用户使用的指令。
* /sbin, /usr/sbin，给 root 用户使用的指令。
* /var/log，应用程序日志文件目录。


## Linux 系统的启动过程

![image](http://upload-images.jianshu.io/upload_images/2648731-accd38538c549c79.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


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
xinetd 守护进程依赖于 **/etc/xinetd.conf** 的配置，用户应该为每个服务单独开辟一个文件，存放在 **/etc/xinetd.d**目录下。查看 xinetd. conf可以看到这一点。
