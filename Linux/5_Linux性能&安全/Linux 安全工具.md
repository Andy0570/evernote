[TOC]

## 一、杀毒软件 [ClamAV](http://www.clamav.net/documents/upgrading-clamav)

ClamAV 是 Linux 上最流行的防病毒软件。它包含完整的防病毒工具库，并且更新迅速。

设置通过代理服务器上网，配置文件：**/etc/clamav/freshclam.conf**，添加如下：

```
HTTPProxyServer 你的IP地址
HTTPProxyPort   端口号
```


### clamAV 的使用

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


## 二、网络安全工具

### NMap 端口扫描工具

* nmap 用于扫描一组主机的网络端口。
* 80端口用于提供 HTTP 服务、22端口接受 SSH 连接、21端口提供FTP服务。

```shell
# 安装 nmap
$ sudo apt install nmap

# 扫描dbl.example.org
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

### Nessus 漏洞扫描工具

参考：[Nessus](https://www.tenable.com/downloads/nessus)
