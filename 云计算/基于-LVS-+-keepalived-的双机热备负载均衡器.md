# 1. LVS

## 1.1 LVS 原理

### 1.1.1 LVS 集群中的 IP 负载均衡技术

![image](http://upload-images.jianshu.io/upload_images/2648731-5bb8ec5f53181777.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

详情参考：[**LVS 集群中的 IP 负载均衡技术 @章文嵩博士**](http://www.linuxvirtualserver.org/zh/lvs3.html)


### 1.1.2 LVS 集群的负载调度

![LVS 内核中的连接调度算法](http://upload-images.jianshu.io/upload_images/2648731-65d0569190676c54.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

详情参考：[**LVS 集群的负载调度 @章文嵩博士**](http://www.linuxvirtualserver.org/zh/lvs4.html)



查看 ipvsadm 版本：

```shell
# ipvsadm --version
ipvsadm v1.28 2015/02/09 (compiled with popt and IPVS v1.2.1)
```



# 2. Keepalived



## 2.1 Keepalived 架构

![Keepalived 架构图](http://upload-images.jianshu.io/upload_images/2648731-f9f5b655f4c710ac.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





## 2.2 Keepalived 安装及配置

### 2.2.1 查询本机网络接口

查询本机 `IP` 地址命令：`ip addr list`、`ip addr show`、`ip a`

```shell
# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc fq_codel state UP group default qlen 1000
    link/ether 02:a2:63:89:23:50 brd ff:ff:ff:ff:ff:ff
    inet 10.0.0.62/24 brd 10.0.0.255 scope global dynamic eth0
       valid_lft 3505sec preferred_lft 3505sec
    inet6 fe80::a2:63ff:fe89:2350/64 scope link 
       valid_lft forever preferred_lft forever
       
# 配置并启动网络接口
# 配置虚拟IP
# 将网络接口 eth0:1 的 IP 地址设置为：172.27.0.10，子网掩码设置为：255.255.255.255，
# up：启动网络接口
# down：关闭网络接口
ifconfig eth0:1 172.27.0.10 broadcast 172.27.0.10 netmask 255.255.255.255 up

# 关闭网络接口
ifconfig eth0:1 172.27.0.10 broadcast 172.27.0.10 netmask 255.255.255.255 down
```



### 2.2.2 修改系统配置文件

#### 2.2.2.1 编辑系统配置文件：`vim /etc/sysctl.conf`

前端 LVS 配置：

```shell
# Uncomment the next line to enable packet forwarding for IPv4
# 开启 IPv4 数据包转发功能
net.ipv4.ip_forward=1
# 允许绑定非本机的IP
net.ipv4.ip_nonlocal_bind=1
```

参考：

* [ECS 做负载均衡需要用户做额外的配置吗？](<https://help.aliyun.com/knowledge_detail/39428.html>)
* [Linux 实例常用内核网络参数介绍与常见问题处理](<https://help.aliyun.com/knowledge_detail/41334.html>)

后端 Server 配置：

```shell
# Uncomment the next two lines to enable Spoof protection (reverse-path filter)
# Turn on Source Address Verification in all interfaces to
# prevent some spoofing attacks
net.ipv4.conf.default.rp_filter=0
net.ipv4.conf.all.rp_filter=0
net.ipv4.conf.eth0.rp_filter=0
```



#### 2.2.2.2 使配置文件生效

每次系统启动时，初始化脚本 /etc/rc.d/rc.sysinit 会读取 /etc/sysctl.conf 文件的内容，所以修改此文件后每次系统重启时都会开启 IP 转发功能。但是只修改 sysctl 文件不会马上生效，如果需要使修改马上生效可以执行下面的命令：

```shell
# 使配置文件马上生效
$ sudo sysctl -p
net.ipv4.ip_forward = 1
```



#### 2.2.2.3 验证配置是否生效

```shell
# 查看 ip_forward 文件内容，默认值 0 表示禁止 IP 转发，1 表示开启 IP 转发功能。
$ cat /proc/sys/net/ipv4/ip_forward
1

$ cat /proc/sys/net/ipv4/ip_nonlocal_bind
1
```



### 2.2.3 下载安装 Keepalived

```shell
# 1. 更新 apt 软件包
apt-get update && apt-get upgrade

# 2. 安装相关软件包
apt-get install -y libssl-dev openssl libpopt-dev
apt install jq
apt install awscli

# 3. 下载安装 Keepalived
apt-get install keepalived

# 4. 查看 Keepalived 安装版本
keepalived --version
Keepalived v1.3.9 (10/21,2017)
...
```



## 2.3 Keepalived 配置文件详解

> 💡💡💡
>
> Keepalived 配置文件官方文档：<http://www.keepalived.org/manpage.html>

* keepalived 的配置文件路径为：**/etc/keepalived/keepalived.conf**
* Keepalived 的配置文件都是以块（block）的形式组织的，每个块的内容都包含在 {} 中，以 “#” 和 “!” 开头的行都是注释。
* 根据 Keepalived 配置文件所实现的功能，可以将配置分为三类：
  1. 全局配置（Global Configuration）；
  2. VRRPD 配置；
     1. VRRP 同步组配置；
     2. VRRP 实例配置；
  3. LVS 配置；

![Keepalived.conf 文件结构](http://upload-images.jianshu.io/upload_images/2648731-f6ce14bb882f755f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





```json
# Define the script used to check if haproxy is still working
# 检查 haproxy 是否正常运行的脚本
vrrp_script chk_haproxy { 
    script "killall -0 haproxy"
    interval 2 
    weight 2 
}
```





### 2.3.1 Keepalived 的全局配置

全局配置就是对整个 Keepalived 都生效的配置。

```json
# Configuration File for keepalived

# ------ 全局配置 ------
# 报警邮件通知设置
# 全局配置以 “global_defs” 作为标识符
global_defs {
    notification_email {
        your@emailaddress.com    # 报警邮件地址，可以设置多个，每行一个。
    							 # 开启邮件报警需要开启本机的 Sendmail 服务。 
    }
    notification_email_from loadb01@domain.ext  # 报警邮件发送的地址。
    smtp_server 127.0.0.1     # 邮件的 SMTP 服务器地址。
    smtp_connect_timeout 15   # 连接 SMTP 服务器的超时时间。
    router_id lVS_DEVER       # 运行 Keepalived 服务器的一个标识符，
							  # 是发邮件时显示在邮件主题中的信息。
}
```

        

### 2.3.2 Keepalived 的 VRRPD 配置

VRRPD 配置是 Keepalived 所有配置的核心，主要用来实现 Keepalived  的**高可用**功能。
从结构上来看，VRRPD 配置可以分为 **VRRP 同步组配置** 和 **VRRP 实例配置**。



#### 2.3.2.1 VRRP 同步组配置

同步组是相对于多个 VRRP 实例而言的，在多个 VRRP 实例的环境中，每个 VRRP 实例所对应的网络环境会有所不同，假设一个实例处于网段A，另一个实例处于网段B，而如果 VRPD 只配置了A网段的检测，那么当B网段主机出现故障时， VRRPD 会认为自身仍处于正常状态，进而不会进行主备节点的切换，这样问题就出现了。同步组就是用来解决这个问题的，将所有 VRRP 实例都加入到同步组中，这样任何一个实例出现问题，都会导致 Keepalived进行主备切换。

```json
vrrp_sync_group G1 {
    # G1 同步组包含 VI_1、VI_2、VI_5 三个 VRRP 实例
    group {
       # name of the vrrp_instance (see below)
       # Set of VRRP_Instance string
       VI_1
       VI_2
       VI_5
    }
   # to MASTER transition
   # 指定当 Keepalived 进入 Master 状态时要执行的脚本，
   # 这个脚本可以是一个状态报警脚本，也可以是一个服务管理脚本。
   # Keepalived 允许脚本传入参数，因此灵活性很强。
   notify_master "usr/local/bin/vrrp.mast arg1 arg2"

   # to BACKUP transition
   # 指定当 Keepalived 进入 Backup 状态时要执行的脚本，
   # 这个脚本可以是一个状态报警脚本，也可以是一个服务管理脚本。
   notify_backup "usr/local/bin/vrrp.back arg1 arg2"

   # FAULT transition
   # 指定当 Keepalived 进入 Fault 状态时要执行的脚本
   notify_fault "usr/local/bin/vrrp.fault arg1 arg2"

   # executed when stopping vrrp
   # 指定 Keepalived 程序终止时需要执行的脚本。
   notify_stop "usr/local/bin/vrrp.stop arg1 arg2"                                   
}

vrrp_sync_group G2 {
   # G2 同步组包含 VI_3、VI_4 三个 VRRP 实例
   group {
       VI_3
       VI_4
    }                                
}
```



#### 2.3.2.2 VRRP 实例配置

VRRP 实例配置——配置 Keepalived 的高可用功能。

VRRP 实例段主要用来配置节点角色（主或从）、实例绑定的网络接口、节点间验证机制、集群服务 IP 等。



从调度器的配置与主调度器基本相同，也包括全局参数、热备参数、服务器池配置，只需要调整 router_id、state、prority 参数即可，其余内容完全相同，配置完成以后重启keepalived服务。

```json
# ------ 虚拟服务器配置 ------
# vrrp 实例配置
# VRRP 配置以 “vrrp_instance” 作为标识符
vrrp_instance VI_1 {
    state MASTER        # 指定 Keepalived 的角色。
    					# MASTER 表示此主机是主服务器，Backup 表示此主机是备用服务器。
    					# 因此，在另一台服务器上设置该值为 BACKUP。
    interface eth0      # 指定 HA 监测网络的接口。
    virtual_router_id 51 # 虚拟路由ID(0-255)，在一个VRRP实例中主备服务器ID必须一样
    priority 101        # 定义节点优先级。set this to 100 on the other machine
    advert_int 1        # 用于设定 MASTER 与 BACKUP 主机之间同步检查的时间间隔，单位是秒。
    mcast_src_ip <IPADDR> # 发送多播包的地址。
    garp_master_delay 10
    smtp_alert          # Activate email notifications
  
    # 设置一些额外的网络监控接口
    track_interface {
    eth0
    eth1
	}
    
    # 设定节点间通信验证类型和密码
    authentication {
        auth_type PSAA            # 验证类型，有 PASS 和 AH 两种模式。
        auth_pass myPassw0rd      # Set this to some secret phrase
    }
  
    # The virtual ip address shared between the two loadbalancers
    # 虚拟 IP 地址
    virtual_ipaddress {
        #<IPADDR>/<MASK> brd <IPADDR> dev <STRING> scope <SCOPT> label <LABEL>
        192.168.0.200
        192.168.200.17 dev eth1
        192.168.200.18 dev eth2
    }
     
    # Use the script above to check if we should fail over
    track_script {
        chk_haproxy
    }
	
	nopreempt          # 设置高可用集群中的不抢占功能。
                       # nopreempt 表示主节点故障恢复后不再切回到主节点，让服务一直在备用节点下工作，直到备用节点出现故障才会进行切换。
    preemtp_delay 300  # 设置抢占延时时间，单位是秒。
}
```



### 2.3.3 Keepalived 的 LVS 配置

由于 Keepalived 属于 LVS 的扩展项目，因此，Keepalived 可以与 LVS无缝整合，轻松搭建一套高性能的负载均衡集群系统。

LVS 段的内容有两部分组成：

1. real_server 段；
2.  健康检测段；

```json
# 虚拟服务器配置
# LVS 段的配置以“virtual_server”作为开始标识。
virtual_server 192.168.80.200 80 {  # 设置虚拟 IP 地址和服务端口
    delay_loop 6 # 设置健康检查的时间间隔，单位是秒                    
    lb_kind DR # 设置 LVS IP负载均衡模式，有NAT、TUN、DR三个模式
    lb_algo rr # 设置 LVS 负载调度算法，rr                  
    persistence_timeout 50 # 会话保持时间，单位是秒。
    persistence_granularity <NETMASK> # 子网掩码。表示持久连接的粒度。默认是255.255.255.255
    protocol TCP # 指定转发协议类型。有 TCP 和 UDP 两种选项。
    ha_suspend   # 节点状态从 Master 到 Backup 切换时，暂不启用 real server 节点的健康检查。
    virtualhost <string> # 健康检查时，指定 Web 服务器的虚拟主机地址。
    sorry_server <IPADDR> <PORT> # 当所有 real server 失效后，指定的 Web 服务器的虚拟主机地址。
    
    # -------- real_server 段配置 --------
    real_server 192.168.80.102 80 {  # 指定 real server1 的IP地址
        weight 3   # 配置节点权值，数字越大权重越高
        inhibit_on_faliure # 在检测到 real_server 节点失效后，把它的 “Weight” 值设置为0，而不是从 IPVS 中删除。
        notify_up <STRING> | <QUOTED-STRING> # 在检测到 real_server 节点服务处于 UP 状态后执行的脚本
        notify_down <STRING> | <QUOTED-STRING> # 在检测到 real_server 节点服务处于 DOWN 状态后执行的脚本
        
        # -------- 健康检测段配置 -------
        TCP_CHECK { # 配置为 TCP_CHECK 检测方式
        	connect_port 80      # 健康检查的端口
        	connect_timeout 10   # 无响应超时时间。单位是秒。     
        	retry 3       # 重试次数
        	delay_before_retry 3 # 重试间隔 
        }  
    }  
    real_server 192.168.80.103 80 {  # 指定 real server2 的IP地址
        weight 3  # 配置节点权值，数字越大权重越高  
        TCP_CHECK {  
          connect_port 80
        	connect_timeout 10  
        	retry 3  
        	delay_before_retry 3    
        }  
     }  
}
```



## 2.4 启动与停止服务

```shell
# 方式一
/etc/init.d/keepalived status # 查看状态
/etc/init.d/keepalived stop  # 停止
/etc/init.d/keepalived start # 启动

# 方式二
sudo service keepalived status
sudo service keepalived start 
sudo service keepalived stop
sudo service keepalived restart
```



## 2.5 测试

### 2.5.1 查看服务进程状态：`ip`

必须要有三个keepalived的进程，这三个分别是keepalived父进程，vrrp子进程、check子进程；两个子进程都被系统watchlog看管，两个子进程各自负责复杂自己的事



#### 2.5.2 其次`ip addr`查看虚拟 ip 是否创建成功，ping 虚拟 ip 是否相通。



#### 2.5.3 查看集群中服务器ip信息：`ipvsadm -ln`



#### 2.5.4 查看 Keepalived 日志：`cat /var/log/syslog | grep Keepalived`

[简书：ipvsadm 指令详解](https://www.jianshu.com/p/18d0d7169289)

```shell
# 查看 ipvsadm 版本
$ ipvsadm --version
ipvsadm v1.28 2015/02/09 (compiled with popt and IPVS v1.2.1)

# 查看集群中服务器ip信息
# -l、-list:列出虚拟服务器表
# -n:IP 地址和端口号将以数字格式打印，而不是作为主机名和服务分别打印，这是默认设置。
ipvsadm -ln
IP Virtual Server version 1.2.1 (size=4096)
Prot LocalAddress:Port Scheduler Flags
  -> RemoteAddress:Port           Forward Weight ActiveConn InActConn
TCP  10.0.0.100:80 lc persistent 60
  -> 10.0.0.102:80                Route   3      0          0
  -> 10.0.0.202:80                Route   3      0          0

# -c:连接输出。使用此选项的 list 命令将列出当前的 IPVS 连接
ipvsadm -lnc
IPVS connection entries
pro expire state       source             virtual            destination
TCP 00:48  NONE        111.187.40.59:0    10.0.0.138:80      10.0.0.214:80
TCP 00:59  SYN_RECV    111.187.40.59:5196 10.0.0.138:80      10.0.0.214:80
TCP 00:59  SYN_RECV    111.187.40.59:5197 10.0.0.138:80      10.0.0.214:80
TCP 00:59  SYN_RECV    111.187.40.59:5198 10.0.0.138:80      10.0.0.214:80

# 查看
arp -n
Address                  HWtype  HWaddress           Flags Mask            Iface
10.0.0.202               ether   02:fb:fd:00:12:ae   C                     eth0
10.0.0.1                 ether   02:06:a0:5b:39:d2   C                     eth0
10.0.0.2                 ether   02:06:a0:5b:39:d2   C                     eth0
10.0.0.102               ether   02:2b:7d:01:b6:68   C                     eth0
10.0.0.199               ether   02:63:16:9e:f2:08   C                     eth0
10.0.0.100                       (incomplete)                              eth0
```



####  2.5.5 验证 VIP 漂移

```shell
systemctl stop keepalived.service
```



#### 2.5.6 测试抓取数据包

```shell
tcpdump -i eth0 -p -n -vv port 80
```



# AWS 下的部署实例

### 参考

* [AWS 部署 keepalived实战](https://blog.csdn.net/qijkkwcw/article/details/80900579)
* [如何在AWS中正确使用Keepalived](https://blog.csdn.net/xialingming/article/details/81291573)
* [Assign a floating (secondary) IP address in AWS VPC](https://codepen.io/tsabat/post/assign-a-floating-secondary-ip-address-in-aws-vpc)
* [Leveraging Multiple IP Addresses for Virtual IP Address Fail-over in 6 Simple Steps](https://aws.amazon.com/cn/articles/leveraging-multiple-ip-addresses-for-virtual-ip-address-fail-over-in-6-simple-steps/)





在私有环境中部署keepalived本身并没有好说的，但是aws中存在诸多限制，其中三个需要明确指出的： 
1. VRRP协议的组播被禁止，也就是说keepalived需要调整心跳的通知方式，修改/etc/keepalived/keepalived.conf配置文件中的通知为单播模式，一般配 web 服务器上数量较少，所以也不会损失太多网络带宽。 

   ```json
   unicast_src_ip 172.*.*.1 # 本机IP
   
   unicast_peer {
   172.*.*.124 # 备用节点 IP
   }
   ```

   [AWS 常见问题汇总](https://aws.amazon.com/cn/blogs/china/aws-architecture-failure-common-problems/)

2. 配置了VIP（floating ip）以后，AWS的VPC并不知道，尝试下arp也学习不到该vip的mac地址，所以必须要通过配置ENI（Elastic Network Interface弹性网络接口）使得该VIP可达。 

3. 然而当你关了Master以后，ENI的网络配置并不会自动切换漂移IP，为了故障切换，我利用切换VIP的通知机制，通过调用添加和移除private-ip-addresses的方式达到漂移IP的效果。下面详细介绍。



还要新建一个 IAM 账户，以允许运行 AWS CLI 命令，并在服务器上进行注册。

```
aws configure
```



### 绑定辅助私有IP ⭐️⭐️⭐️

亚马逊云虽然不允许广播 ARP，但可以用命令行指定网卡（ENI）的 Secondary-private-ip-address。这个命令的具体形式如下：

[AWS CLI Command Reference：assign-private-ip-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/assign-private-ip-addresses.html)

```shell
# 将一个或多个辅助私有 IP 地址分配给指定的网络接口。
# ENI：网卡实例 ID
# VIP：虚拟 IP 地址
aws ec2 assign-private-ip-addresses \
--network-interface-id $ENI \ 
--private-ip-addresses $VIP \ 
--allow-reassignment


# 示例：指定一个IP地址（10.0.0.100）作为网卡的私有IP地址
aws ec2 assign-private-ip-addresses --network-interface-id eni-0a8a8f03710ecbb9e --private-ip-addresses 10.0.0.100 --allow-reassignment


aws ec2 assign-private-ip-addresses --network-interface-id eni-0eff36fde0c99958e --private-ip-addresses 10.0.0.100 --allow-reassignment
```



keepalived 配置：

```
notify_master "/home/keepalived/scripts/mysql_be_master.sh 172.*.*.201 eni-*"
```


## 弹性IP配置的脚本文件

###  add_eip.sh

作用：分离弹性 ip，并将弹性 ip 绑定到自身网口上

授权：chmod -R 711 /etc/keepalived/scripts

```shell
#/bin/bash
EIP=13.*.*.201                          # 弹性 ip 地址（公网 ip）
INSTANCE_ID=i-0b2d*************   #此服务器（EC2）的实例的 id
aws ec2 disassociate-address --public-ip $EIP

aws ec2 associate-address --public-ip $EIP --instance-id $INSTANCE_ID
```



### del_eip.sh

授权             chmod +x ./add_eip.sh    chmod -R 711 add_eip.sh

```shell
#/bin/bash
EIP=13.*.*.201                                 #弹性 ip 地址（公网 ip）
INSTANCE_ID=i-0b2d*************   #备服务器（EC2）的实例的 id
aws ec2 disassociate-address --public-ip $EIP

aws ec2 associate-address --public-ip $EIP --instance-id $INSTANCE_ID


# 取消关联需要传关联 ID，而不是分配 ID。
aws ec2 disassociate-address --association-id $ASSOCIATION_ID
aws ec2 disassociate-address --association-id eipassoc-09fe640e36c9d53b1
```



把外网 EIP 作为 VIP，先释放，再关联给一个网卡的辅助私有IP地址：

参考：[AWS CLI:associate-address](<https://docs.aws.amazon.com/zh_cn/cli/latest/reference/ec2/associate-address.html>)

```shell
aws ec2 disassociate-address --public-ip $EIP

aws ec2 associate-address \
  --allocation-id eipalloc-09ce336dc7a1e8da7 \ # 分配 ID
  --network-interface-id eni-0a8a8f03710ecbb9e \
  --private-ip-address 10.0.0.100 \
  --allow-reassociation


# 关联弹性公网 IP 到 网卡
aws ec2 associate-address --allocation-id eipalloc-09ce336dc7a1e8da7 --network-interface-id eni-0a8a8f03710ecbb9e --private-ip-address 10.0.0.100 --allow-reassociation
```



```shell
# 根据实例 ID 找到网卡 ID
aws ec2 describe-instances \
  --instance-ids i-0260f608cf698958c | \
  jq -r \
  '.Reservations[0].Instances[0].NetworkInterfaces[0].NetworkInterfaceId'
  

# 实例
aws ec2 describe-instances --instance-ids i-0260f608cf698958c | jq -r '.Reservations[0].Instances[0].NetworkInterfaces[0].NetworkInterfaceId'
eni-0a8a8f03710ecbb9e
```





# 配置文件

## EC2_01

```json
# Keepalived的全局配置
global_defs {
    router_id LVS_Master

    # 通知脚本设置
    script_user root        
    enable_script_security  
}

# VRRP的实例Group_LVS_1配置
vrrp_instance Group_LVS_1 {
    state MASTER
    interface eth0
    virtual_router_id 51
    priority 100
    advert_int 1

    track_interface {
        eth0
    }

    # AWS 上 VRRP 协议的组播被禁止，因此这里配置心跳通知为单播模式
    unicast_src_ip 10.0.0.36  
    unicast_peer {            
        10.0.0.56
    }

    authentication {
        auth_type PASS
        auth_pass 123456
    }

    virtual_ipaddress {
        10.0.0.100
    }

    # 切换虚拟 IP 的脚本文件
    notify_master "/etc/keepalived/scripts/assign_private_ip.sh 18.223.129.244 10.0.0.100 i-0260f608cf698958c"
    notify_backup "/etc/keepalived/scripts/unassign_private_ip.sh 18.223.129.244 10.0.0.100 i-0260f608cf698958c"
}

# Virtual Server 的配置 for Real Service
virtual_server 10.0.0.100 80 {
    delay_loop 5
    lb_algo lc
    lb_kind DR
    persistence_timeout 60
    protocol TCP

    # Real Server 1 的配置
    real_server 10.0.0.80 80 {
        weight 3
        inhibit_on_failure
        TCP_CHECK {
            connect_timeout 10
            retry 3
            delay_before_retry 3
            connect_port 80
        }
    }

    # Real Server 2 的配置
    real_server 10.0.0.194 80 {
        weight 3
        inhibit_on_failure
        TCP_CHECK {
            connect_timeout 10
            retry 3
            delay_before_retry 3
            connect_port 80
        }
    }
}
```



## EC2_02

```json
# Keepalived的全局配置
global_defs {
    router_id LVS_Master

    # 通知脚本设置
    script_user root
    enable_script_security
}

# VRRP的实例Group_LVS_1配置
vrrp_instance Group_LVS_1 {
    state BACKUP
    interface eth0
    virtual_router_id 51
    priority 90
    advert_int 1

    track_interface {
        eth0
    }

    # AWS 上 VRRP 协议的组播被禁止，因此这里配置心跳通知为单播模式
    unicast_src_ip 10.0.0.56
    unicast_peer {            
        10.0.0.36
    }

    authentication {
        auth_type PASS
        auth_pass 123456
    }

    virtual_ipaddress {
        10.0.0.100
    }

    # 切换虚拟 IP 的脚本文件
    notify_master "/etc/keepalived/scripts/assign_private_ip.sh 18.223.129.244 10.0.0.100  i-0f74aba2dc5e49679"
    notify_backup "/etc/keepalived/scripts/unassign_private_ip.sh 18.223.129.244 10.0.0.100 i-0f74aba2dc5e49679"
}

# Virtual Server 的配置 for Real Service
virtual_server 10.0.0.100 80 {
    delay_loop 5
    lb_algo lc
    lb_kind DR
    persistence_timeout 60
    protocol TCP

    # Real Server 1 的配置
    real_server 10.0.0.80 80 {
        weight 3
        inhibit_on_failure
        TCP_CHECK {
            connect_timeout 10
            retry 3
            delay_before_retry 3
            connect_port 80
        }
    }

    # Real Server 2 的配置
    real_server 10.0.0.194 80 {
        weight 3
        inhibit_on_failure
        TCP_CHECK {
            connect_timeout 10
            retry 3
            delay_before_retry 3
            connect_port 80
        }
    }
}
```



### Server 服务器脚本

```
#!/bin/bash
# 配置realserver的lo和运用noarp
# chmod 755 /usr/local/sbin/server
# 执行server，启动realserver的LVS服务，/etc/init.d/lvsrs start

VIP=10.0.0.100
/sbin/ifconfig lo:0 $VIP broadcast $VIP netmask 255.255.255.255 up
/sbin/route add -host $VIP dev lo:0

echo "1" >/proc/sys/net/ipv4/conf/lo/arp_ignore
echo "2" >/proc/sys/net/ipv4/conf/lo/arp_announce
echo "1" >/proc/sys/net/ipv4/conf/all/arp_ignore
echo "2" >/proc/sys/net/ipv4/conf/all/arp_announce

sysctl -p
#end
```



### /etc/init.d/lvsrs start

```
#!/bin/bash
# 配置realserver的lo和noarp
# chmod 755 /etc/init.d/lvsrs
# 执行server，启动realserver的LVS服务，/etc/init.d/lvsrs start

VIP=10.0.0.100

/etc/rc.d/init.d/functions

case "$1" in
start)
 ifconfig lo:0 $VIP netmask 255.255.255.255 broadcast $VIP
 /sbin/route add -host $VIP dev lo:0
 echo "1" >/proc/sys/net/ipv4/conf/lo/arp_ignore
 echo "2" >/proc/sys/net/ipv4/conf/lo/arp_announce
 echo "1" >/proc/sys/net/ipv4/conf/all/arp_ignore
 echo "2" >/proc/sys/net/ipv4/conf/all/arp_announce
 sysctl -p >/dev/null 2>&1
 echo "RealServer Start OK"
 ;;
stop)
 ifconfig lo:0 down
 route del $VIP >/dev/null 2>&1
 echo "0" >/proc/sys/net/ipv4/conf/lo/arp_ignore
 echo "0" >/proc/sys/net/ipv4/conf/lo/arp_announce
 echo "0" >/proc/sys/net/ipv4/conf/all/arp_ignore
 echo "0" >/proc/sys/net/ipv4/conf/all/arp_announce
 echo "RealServer Stoped"
 ;;
*)
 echo "Usage: $0 {start|stop}"
 exit 1
esac

exit 0
```


## 参考

### 1. LVS
* [LVS 项目中文文档@章文嵩](http://www.linuxvirtualserver.org/zh/index.html) ⭐️⭐️⭐️
* [LVS 中文站点](http://zh.linuxvirtualserver.org/)
* [简书：LVS 原理介绍](https://www.jianshu.com/p/8a61de3f8be9)
* [cnblogs：使用 LVS 实现负载均衡原理及安装配置详解](https://www.cnblogs.com/liwei0526vip/p/6370103.html)
* [LVS 原理详解及部署之二：LVS 原理详解（3 种工作方式 8 种调度算法）](http://www.it165.net/admin/html/201401/2248.html)
* [LVS 集群：两种 VS/DR 模式](https://blog.51cto.com/hoolee/1406270)
* [LVS 集群：ipvsadm 使用及 VS/NAT 实现](https://blog.51cto.com/hoolee/1405810)
* [InfoQ：LVS 在大规模网络环境中的应用](<https://infoq.cn/article/2014/10/lvs-use-at-large-scala-network>)

### 2. Keepalived

* [keepalived 官网](http://www.keepalived.org/manpage.html)
* [GitHub: Keepalived](https://github.com/acassen/keepalived)
* [简书：Keepalived in AWS](https://www.jianshu.com/p/14a2a9db62ec?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation)
* [51CTO：aws ec2 keepalived 的高可用构建](https://blog.51cto.com/morrowind/2352984?source=drh)
* [CSDN：AWS 部署 keepalived 实战](https://blog.csdn.net/qijkkwcw/article/details/80900579)
* [在 AWS 上部署 keepalived](http://www.voidcn.com/article/p-xhsoeccn-bsb.html)

### 3. 第三方博客、文章

* [【大型网站技术实践】初级篇：借助LVS+Keepalived实现负载均衡](https://www.cnblogs.com/edisonchou/p/4281978.html)
* [简书：lvs+keepalived群集配置](https://www.jianshu.com/p/e146a7a14b4b)
* [LVS+Keepalived实现高可用群集](https://blog.51cto.com/dengqi/1308318)
* [LVS Keepalived双机高可用负载均衡搭建](https://www.centos.bz/2017/07/lvs-keepalived-ha-loadbalace/)
* [Ubuntu安装keepalived](https://www.cnblogs.com/guoximing/p/9390269.html)
* [Setting up keepalived on Ubuntu (load balancing using HAProxy on Ubuntu part 2)](https://www.leaseweb.com/labs/2011/09/setting-up-keepalived-on-ubuntu-load-balancing-using-haproxy-on-ubuntu-part-2/)
* [LVS负载均衡在Ubuntu环境下部署详解](https://blog.csdn.net/jeeper_/article/details/50683047)
* [Nginx/LVS/HAProxy 负载均衡软件的优缺点详解（转自云栖社区）](https://cloud.tencent.com/developer/article/1139968)
* [深入理解负载均衡](https://cloud.tencent.com/developer/article/1359063)【Tip：里面有一张负载均衡双机热备GIF图可以看看】
* 双机热备架构图：https://ask.qcloudimg.com/http-save/yehe-1305760/28jixn9vop.gif
* [阿里云文档：ECS 做负载均衡需要用户做额外的配置吗？](<https://help.aliyun.com/knowledge_detail/39428.html>)
* [阿里云文档：Linux 实例常用内核网络参数介绍与常见问题处理](<https://help.aliyun.com/knowledge_detail/41334.html>)
* [阿里云文档：ping 丢包或不通时链路测试说明](<https://help.aliyun.com/knowledge_detail/40573.html>)
