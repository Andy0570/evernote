[TOC]

### OpenSSH SSH 客户端（远程登录程序）

* 大多数 Linux 发行版自带一个提供 SSH 功能的软件包，叫做 OpenSSH，来自于 BSD 项目。
* OpenSSH 软件包也包含两个程序：1. scp；2. sftp。
* ssh 服务配置文件：`/etc/ssh/sshd_config`


SSH 由两部分组成：
* SSH 服务器运行在远端主机上运行，在端口号 22 上监听将要到来的连接。
* SSH 客户端用在本地系统中，用来和远端服务器通信。

> SSH 通道
当你通过 SSH 协议与远端主机建立连接的时候，就在本地与远端系统之间创建了一条加密通道。通常，这条通道被用来把在本地系统中输入的命令安全地传输到远端系统，同样地，再把执行结果安全地发送回来。除了这个基本功能之外，SSH 协议允许大多数网络流量类型通过这条加密通道来被传送，在本地与远端系统之间创建某种 VPN（虚拟专用网络）。

```bash
# Ubuntu 安装 ssh
$ apt-get install openssh-server

################################################
$ sudo service ssh status
$ sudo /etc/init.d/sshd start    # 启动 SSH 服务器守护进程
$ sudo /etc/init.d/sshd stop     # 停止 SSH 服务器守护进程
$ sudo /etc/init.d/sshd restart  # 重启 SSH 服务器守护进程

################################################

# 查看 ssh 进程运行状态
$ sudo ps -e | grep ssh

# 登录语法：ssh -l login_name hostname
# 登录语法：ssh -l login_name -p port_number hostname
$ ssh -l liu 192.168.150.139
$ ssh login_name@192.168.0.2    # 同上

# -p：指定端口号
$ ssh -l liu -p 202 192.168.150.139    
$ ssh -p 9122 login_name@192.168.0.2   # 同上

# 公私钥登录
ssh -i ~/.ssh/MyKeyPair.pem username@ip_address
ssh -i /path/kp-123 默认用户名@弹性云服务器
ssh -i /path/kp-123 linux@弹性公网 IP

# 断开SSH连接
$ exit 
```

推荐的 SSH 便捷连接工具：Mac 访问虚拟机 [shuttle](http://fitztrev.github.io/shuttle/)，windows 使用 [Xshell](https://www.netsarang.com/products/xsh_overview.html)、[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/)。


### 参考
* [iTerm2 中 ssh 保持连接不断开](http://bluebiu.com/blog/iterm2-ssh-session-idle.html)
* [mac 下的效率工具 shuttle 介绍](http://blog.qingtian16265.com/2016/04/07/introduce-shuttle/)
* [Shuttle 安装以及配置简介](https://www.bluelzy.com/articles/shuttle_brief_introduction.html)
* [Shuttle - Mac 快捷菜单神器](https://www.jianshu.com/p/1f9091641312)
* [华为云文档：SSH 密钥方式登录](https://support-intl.huaweicloud.com/zh-cn/usermanual-ecs/zh-cn_topic_0017955380.html)
* [华为云文档：怎样长时间保持 SSH 会话连接不断开？](https://support.huaweicloud.com/trouble-ecs/zh-cn_topic_0138293301.html)

使用密钥认证机制远程登录Linux：PUTTYGEN.exe 生成密钥对。