[TOC]

### 网络配置文件

Debian/Ubuntu Linux 下网络配置文件路径为：


| 路径 | 描述 |
| --- | --- |
| /etc/network/interfaces | IP 地址、子网掩码等配置文件 |
| /etc/resolv.conf | DNS 配置文件 |
| /etc/hostname | 全局主机名配置文件 |
| /etc/hosts | 设置主机和 IP 绑定信息 |

### `ping` 网络连通性测试

`ping` 命令发送 ICMP ECHO_REQUEST 网络数据包到指定的网络主机，大多数接收这个包的网络设备将会回复它，来允许网络连接验证。
一旦启动，`ping` 命令会持续在特定的时间间隔内（默认是一秒）发送数据包，直到它被中断（Ctrl+C）。
一个成功执行的 `ping` 命令意味着网络的各个部件（网卡，电缆，路由，网关）都处于正常工作状态。

```shell
$ ping ip          # 需要按 Ctrl + C 退出
$ ping -c 10 ip地址    # 指定发送包的数量：10次
$ ping -I 2 ip地址     # 间隔 2 秒

ping localhost        # ping 本机（127.0.0.1）
```

#### 参考
[如何开放 Azure 虚拟机 Ping 功能](https://docs.azure.cn/zh-cn/articles/azure-operations-guide/virtual-machines/aog-virtual-machines-howto-verify-connectivity-with-ping-command)
Web 在线检测工具：<https://ping.eu/ping/>

> **网络安全组设置**
> 在生产环境中，建议使用 VPN 或专用网络连接来连接到要管理的 Azure 资源，而不要向 Internet 公开端口 3389（RDP 端口）。

### 第三方工具：PerfOps CLI

[PerfOps CLI](https://perfops.net/cli) 是一款适合于运维人员使用的命令行工具，方便从多地点执行 `ping`、`traceroute`、`mtr` 等网络基准测试。

传送门：[简书：PerfOps CLI 多地点网络测试工具@独木舟的木](https://www.jianshu.com/p/e92a6dc2ecf5)



### `traceroute` 路由节点追踪

`traceroute` 命令用于追踪数据包在网络上的传输时的全部路径，它默认发送的数据包大小是 40 字节。

安装 `traceroute`：
```shell
apt-get install traceroute
```

测试连接到 baidu.com 经过的路由信息：
```shell
$ traceroute baidu.com
traceroute to baidu.com (220.181.57.216), 30 hops max, 60 byte packets
 1  * * *
 2  * * *
 3  100.68.0.1 (100.68.0.1)  88.665 ms  88.651 ms  88.651 ms
 4  10.44.1.249 (10.44.1.249)  14.341 ms  14.661 ms  14.416 ms
 5  10.44.1.173 (10.44.1.173)  13.740 ms  13.724 ms  13.724 ms
 6  * * *
 7  10.44.3.196 (10.44.3.196)  57.342 ms  56.809 ms  57.039 ms
 8  10.44.3.205 (10.44.3.205)  1.347 ms  1.104 ms  9.666 ms
...
29  * * *
30  * * * （* 可能是防火墙封掉了 ICMP 的返回信息）

# 【说明】
# 每个记录就是一跳，每跳表示一个网关。
# 三个时间（ms）：探测数据包向每个网关发送三个数据包后，网关响应后返回的时间；
```
> 如果在局域网中的不同网段之间，我们可以通过 traceroute 来排查问题所在，是主机的问题还是网关的问题。如果我们通过远程来访问某台服务器遇到问题时，我们用到 traceroute 追踪数据包所经过的网关，提交 IDC 服务商，也有助于解决问题；但目前看来在国内解决这样的问题是比较困难的，就是我们发现问题所在，IDC 服务商也不可能帮助我们解决。

参考：[阿里云文档：ping 丢包或不通时链路测试说明](https://help.aliyun.com/knowledge_detail/40573.html) ⭐️⭐️⭐️

### `mtr` 强大的网络诊断工具 ⭐️⭐️⭐️

[官方 GitHub 仓库](https://github.com/traviscross/mtr)

`mtr` 是一个强大的网络诊断工具。它结合了 `traceroute` 和 `ping` 这两个命令的功能。
格式：`mtr [hostname]`

```
mtr -4 -i 1 api.mch.weixin.qq.com
```


### `netstat` 打印网络连接，路由表，接口统计数据，伪装连接，和多路广播成员

`netstat` 程序被用来检查各种各样的网络设置和统计数据。

```bash
netstat -a     # 列出所有当前的连接
netstat -at    # 列出 TCP 协议的连接
netstat -au    # 列出 UDP 协议的连接
netstat -nr    # 列出路由表
netstat -r     # 查看当前系统路由信息表

$ sudo netstat -tulnp | grep 22    # 查看 22 端口状态
```

查看系统中的网络接口：
```bash
$ netstat -ie
Kernel Interface table
eth0      Link encap:Ethernet  HWaddr fa:16:3e:66:73:1b  
          inet addr:172.16.0.128  Bcast:172.16.0.255  Mask:255.255.255.0 # ip
          inet6 addr: fe80::f816:3eff:fe66:731b/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1 ## UP 表明网络接口生效
          RX packets:173121579 errors:0 dropped:0 overruns:0 frame:0
          TX packets:159848486 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:116581892838 (116.5 GB)  TX bytes:225226017664 (225.2 GB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:5723327 errors:0 dropped:0 overruns:0 frame:0
          TX packets:5723327 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1 
          RX bytes:1764307302 (1.7 GB)  TX bytes:1764307302 (1.7 GB)
          
# 【说明】
# eth0，因特网接口。
# lo，内部回环网络接口，它是一个虚拟接口，系统用它来 “自言自语”。
```

#### `ifconfig` 启动或禁用一个网络接口，同时设置 IP地址、子网掩码以及其它网络项

`ifconfig` 命令用来配置网络或显示当前网络接口状态。

```shell
# 显示当前系统上所有网络接口的配置，同 netstat -ie
$ ifconfig    

# 将网络接口 eth0 的 IP 地址设置为：192.168.1.14，子网掩码设置为：255.255.255.0，
# up：启动网络接口
$ sudo ifconfig eth0 192.168.1.14 netmask 255.255.255.0 up

# down：关闭网络接口
$ sudo ifocnfig eth0 down

# 将网络接口 eth0 的 IP 地址设置为：192.168.0.100，广播地址：192.168.0.100，子网掩码设置为：255.255.255.0，
$ ifconfig eth0 192.168.0.100 broadcast 192.168.0.100 netmask 255.255.255.255 up
```

#### 路由配置
显示内核的网络路由表信息：
```shell
# 查看当前系统路由信息表
$ netstat -r
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
# default 表示默认路由
default         172.16.0.1      0.0.0.0         UG        0 0          0 eth0
169.254.169.254 172.16.0.1      255.255.255.255 UGH       0 0          0 eth0
# 最后一个字节为0表示该地址和本机同处一个网络
172.16.0.0      *               255.255.255.0   U         0 0          0 eth0
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

### Nethogs 流量监控工具 ⭐️⭐️⭐️

1. [Nethogs](http://man.linuxde.net/nethogs) 是一个终端下的网络流量监控工具，可以显示每个进程的带宽占用情况。
```shell
$ sudo apt-get install nethogs    ## 安装 NetHogs 包
$ sudo nethogs eth0               ## 监控 eth0 网卡
```
2. **Ifstat** 是一个网络接口监测工具，监控 I/O 状态 和 CPU 状态。
```shell
$ sudo apt-get install ifstat    ## Ubuntu 下安装 ifstat
$ ifstat -l                      ## 使用
```

### DNS
* [网速变慢？你可能需要先设置好 DNS | 科普](https://sspai.com/post/42125)
* [豆瓣：免费公用 DNS](https://www.douban.com/note/416786398/)

#### `dig` 域名查询工具
`dig` 命令是常用的域名查询工具，可以用来测试域名系统工作是否正常。
参考：[linux dig 命令使用方法](https://www.imooc.com/article/26971?block_id=tuijian_wz)

```shell
# 查询 baidu 域名
$ dig baidu.com

# ⭐️ 第一部分显示 dig 命令的版本和输入的参数。
; <<>> DiG 9.10.6 <<>> baidu.com
;; global options: +cmd
# ⭐️ 第二部分显示服务返回的一些技术详情，比较重要的是 status。如果 status 的值为 NOERROR 则说明本次查询成功结束。
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 11065
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 5, ADDITIONAL: 6

# ⭐️ 第三部分中的 "QUESTION SECTION" 显示我们要查询的域名。
;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;baidu.com.			IN	A

# ⭐️ 第四部分的 "ANSWER SECTION" 是查询到的结果。
;; ANSWER SECTION:
baidu.com.		514	IN	A	220.181.57.216
baidu.com.		514	IN	A	123.125.115.110

;; AUTHORITY SECTION:
baidu.com.		1679	IN	NS	dns.baidu.com.
baidu.com.		1679	IN	NS	ns3.baidu.com.
baidu.com.		1679	IN	NS	ns7.baidu.com.
baidu.com.		1679	IN	NS	ns2.baidu.com.
baidu.com.		1679	IN	NS	ns4.baidu.com.

;; ADDITIONAL SECTION:
dns.baidu.com.		84479	IN	A	202.108.22.220
ns2.baidu.com.		83694	IN	A	220.181.37.10
ns3.baidu.com.		83694	IN	A	112.80.248.64
ns4.baidu.com.		84088	IN	A	14.215.178.80
ns7.baidu.com.		85277	IN	A	180.76.76.92

# ⭐️ 第五部分则是本次查询的一些统计信息，比如用了多长时间，查询了哪个 DNS 服务器，在什么时间进行的查询等等。
;; Query time: 8 msec
;; SERVER: 114.114.115.115#53(114.114.115.115)
;; WHEN: Thu Feb 21 11:24:49 CST 2019
;; MSG SIZE  rcvd: 240
```


### hostname
* [ubuntu 临时或永久修改 hostname 的方法](https://www.jb51.net/article/115258.htm)


```shell
$ sudo vim /etc/hostname
clumsiest

$ sudo vim /etc/hosts
127.0.0.1 localhost
127.0.1.1 clumsiest
52.14.183.223 www.arlingbc.com

# 显示网络节点主机名
$ uname -n
ci-huawei
```

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



![image](https://ws2.sinaimg.cn/large/006tNc79ly1fz0f1ytn28j30y406aq3l.jpg)



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
iptables: /sbin/iptables /usr/share/iptables /usr/share/man/man8/iptables.8.gz 
# 表示已安装

# 如果默认没有安装，请执行此命令安装防火墙
$ sudo apt-get install iptables
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






