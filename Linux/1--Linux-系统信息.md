### 参考：
* [Linux入门教程](http://wiki.jikexueyuan.com/project/linux/)
* [Gitbook：The Linux Command Line 中文版](https://billie66.gitbooks.io/tlcl-cn/content/chap00/introduction.html)
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

![Linux 命令速查表](https://upload-images.jianshu.io/upload_images/2648731-2c9d2ef99d3a67f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 一、系统

### 查看当前操作系统
```
$ uname -o
GNU/Linux
```

### 查看当前系统版本

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

### 查看系统语言设置

```shell
echo $LANG
en_US.UTF-8
```

### `date` 显示系统当前的时间和日期
```shell
$ date
Wed Jan  9 08:41:43 UTC 2019
```

### `cal` 显示当前月份的日历
```shell
$ cal
    January 2019
Su Mo Tu We Th Fr Sa
       1  2  3  4  5
 6  7  8  9 10 11 12
13 14 15 16 17 18 19
20 21 22 23 24 25 26
27 28 29 30 31
```

### `exit` 结束终端会话
同：Ctrl+D
```shell
$ exit
```

## 二、CPU


## 三、内存

### `free` 显示空闲内存的数量
```shell
$ free
              total        used        free      shared  buff/cache   available
Mem:        1014500      717980       82392       11356      214128       99704
```

## 四、磁盘

### `df` 查看磁盘剩余空间的数量
```shell
$ df
Filesystem     1K-blocks    Used Available Use% Mounted on
udev              499260       0    499260   0% /dev
tmpfs             101452   11204     90248  12% /run
/dev/xvda1       8065444 5823432   2225628  73% /
tmpfs             507248     128    507120   1% /dev/shm
tmpfs               5120       0      5120   0% /run/lock
tmpfs             507248       0    507248   0% /sys/fs/cgroup
/dev/loop4         16768   16768         0 100% /snap/amazon-ssm-agent/734
tmpfs             101452       0    101452   0% /run/user/1000
/dev/loop0         16896   16896         0 100% /snap/amazon-ssm-agent/784
/dev/loop2         90368   90368         0 100% /snap/core/5897
/dev/loop5         18432   18432         0 100% /snap/amazon-ssm-agent/930
/dev/loop3         91648   91648         0 100% /snap/core/6034
/dev/loop6         91648   91648         0 100% /snap/core/6130
```

## 五、网络

## 六、进程

 
