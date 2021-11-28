> ⚠️
> 经过测试，在 DR 模式和 TUN 模式下，由于 Web Server 返回的 IP 数据包无法穿透 AWS 的 VPC 网关，因此只有 NAT 模式是可行的。

## LVS+Keepalived 架构

![LVS+Keepalived](http://upload-images.jianshu.io/upload_images/2648731-ea05d149e7717685.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## EC2 实例

| Name   | 实例 ID             | IPv4 公有      | 私有 IP    | 备注                  |
| ------ | ------------------- | -------------- | ---------- | --------------------- |
| EC2_01 | i-0260f608cf698958c | 18.223.43.247  | 10.0.0.36  | LVS Master            |
|        |                     | 18.223.129.244 | 10.0.0.100 | Elastic IP            |
| EC2_02 | i-0f74aba2dc5e49679 | 18.216.101.69  | 10.0.0.56  | LVS Backup            |
|        |                     |                |            | Elastic IP            |
| EC2_03 | i-04cbd780d34525342 | 18.188.155.29  | 10.0.0.80  | Nginx HTTP Web Server |
| EC2_04 | i-08506e354ce830468 | 18.217.52.127  | 10.0.0.194 | Nginx HTTP Web Server |



## 一、编辑系统配置文件：`vim /etc/sysctl.conf`

EC2_01、EC2_02 实例修改如下：

```shell
# Uncomment the next line to enable packet forwarding for IPv4
# 开启 IPv4 数据包转发功能
net.ipv4.ip_forward=1
# 允许绑定非本机的IP
net.ipv4.ip_nonlocal_bind=1
```

EC2_03、EC2_04 实例修改如下：

```shell
# Uncomment the next two lines to enable Spoof protection (reverse-path filter)
# Turn on Source Address Verification in all interfaces to
# prevent some spoofing attacks
net.ipv4.conf.default.rp_filter=0
net.ipv4.conf.all.rp_filter=0
net.ipv4.conf.eth0.rp_filter=0
```

使配置文件立即生效：

```shell
$ sudo sysctl -p
```
参考：

* [ECS 做负载均衡需要用户做额外的配置吗？](<https://help.aliyun.com/knowledge_detail/39428.html>)
* [Linux 实例常用内核网络参数介绍与常见问题处理](<https://help.aliyun.com/knowledge_detail/41334.html>)







## 二、下载安装 Keepalived 等相关软件

```shell
# 1. 更新 apt 软件包
apt-get update && apt-get upgrade

# 2. 安装相关软件包
apt-get install -y libssl-dev openssl libpopt-dev
apt install jq
apt install awscli

# 3. 配置 AWS CLI 
$ aws configure 
AWS Access Key ID [None]:  # AWS 访问密钥 ID
AWS Secret Access Key [None]:  # AWS 私有访问密钥
Default region name [None]: us-east-1 # 默认区域名称，弗吉尼亚北部数据中心（us-east-1）
Default output format [None]: json # 默认输出格式

# 3. 下载安装 Keepalived
apt-get install keepalived

# 4. 查看 Keepalived 安装版本
keepalived --version
Keepalived v1.3.9 (10/21,2017)
...
```



## 三、keepalived.conf 配置文件

### 1. EC2_01 Keepalived.conf 文件

```json
! Configuration File for keepalived

# 全局配置
global_defs {

    # 邮件通知设置
    notification_email {
       testuser@163.com
    }
    notification_email_from andywhm@163.com
    smtp_server 192.168.200.1
    smtp_connect_timeout 30
    router_id LVS_Master

    # 通知脚本设置
    script_user root        # 设置脚本运行账户为 root
    enable_script_security  # 设置脚本可运行

}

# VRRP 的实例 Group_LVS_1 配置
vrrp_instance Group_LVS_1 {
    state MASTER　　　　　　  # 配置为主服务
    interface eth0
    virtual_router_id 51      # 虚拟路由标识
    priority 100              # 定义优先级，数字越大，优先级越高  
    advert_int 1              # 主备服务器之间同步检查的时间间隔

    track_interface {
        eth0
    }

    # AWS 上 VRRP 协议的组播被禁止，因此这里配置心跳通知为单播模式
    unicast_src_ip 10.0.0.36  # 本机 IP
    unicast_peer {            # 单播对象 IP
        10.0.0.56
    }
    # nopreempt                 # 设置 HA 集群的不抢占功能
    # preemtp_delay 300         # 设置抢占延时时间，单位是秒。

    authentication {          # 验证类型和密码
        auth_type PASS
        auth_pass 123456
    }
    
    virtual_ipaddress {       # 虚拟 IP 地址
        10.0.0.100
    }

    # 切换虚拟 IP 的脚本文件
    notify_master "/etc/keepalived/scripts/assign_private_ip.sh eipalloc-09ce336dc7a1e8da7 10.0.0.100 i-0260f608cf698958c"
    notify_backup "/etc/keepalived/scripts/unassign_private_ip.sh eipalloc-09ce336dc7a1e8da7 10.0.0.100 i-0260f608cf698958c"
}

# LVS 虚拟服务器配置
virtual_server 10.0.0.100 80 {
    delay_loop 6              # 设置健康状态检查时间间隔，单位是秒。
    lb_algo rr                # 设置 LVS 负载调度算法，rr 为轮叫算法
    lb_kind DR                # 设置 LVS IP负载均衡模式，有NAT、TUN、DR三个模式
    protocol TCP              # 指定转发协议类型，TCP
    persistence_timeout 60    # 会话保持时间

    # Real Server Nginx 1 的配置
    real_server 10.0.0.80 80 {
        weight 3
	inhibit_on_failure
        TCP_CHECK {
            connect_timeout 10　　　　 # 响应超时时间
            retry 3　　　　　　        # 超时重试次数
            delay_before_retry 3　　　 # 超时重试间隔
            connect_port 80
        }
    }

    # Real Server Nginx_2 的配置
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



### 2. EC2_02 Keepalived.conf 文件

```json
! Configuration File for keepalived

# 全局配置
global_defs {

    # 邮件通知设置
    notification_email {
       testuser@163.com
    }
    notification_email_from andywhm@163.com
    smtp_server 192.168.200.1
    smtp_connect_timeout 30
    router_id LVS_Master

    # 通知脚本设置
    script_user root        # 设置脚本运行账户为 root
    enable_script_security  # 设置脚本可运行

}

# VRRP 的实例 Group_LVS_1 配置
vrrp_instance Group_LVS_1 {
    state BACKUP　　　　　    # 配置为从服务器
    interface eth0
    virtual_router_id 51      # 虚拟路由标识
    priority 90               # 定义优先级，数字越大，优先级越高  
    advert_int 1              # 主备服务器之间同步检查的时间间隔

    track_interface {
        eth0
    }

    # AWS 上 VRRP 协议的组播被禁止，因此这里配置心跳通知为单播模式
    unicast_src_ip 10.0.0.56  # 本机 IP
    unicast_peer {            # 单播对象 IP
        10.0.0.36
    }
    # nopreempt                 # 设置 HA 集群的不抢占功能
    # preemtp_delay 300         # 设置抢占延时时间，单位是秒。

    authentication {          # 验证类型和密码
        auth_type PASS
        auth_pass 123456
    }
    
    virtual_ipaddress {       # 虚拟 IP 地址
        10.0.0.100
    }

    # 切换虚拟 IP 的脚本文件
    notify_master "/etc/keepalived/scripts/assign_private_ip.sh eipalloc-09ce336dc7a1e8da7 10.0.0.100 i-0f74aba2dc5e49679"
    notify_backup "/etc/keepalived/scripts/unassign_private_ip.sh eipalloc-09ce336dc7a1e8da7 10.0.0.100 i-0f74aba2dc5e49679"
}

# LVS 虚拟服务器配置
virtual_server 10.0.0.100 80 {
    delay_loop 6              # 设置健康状态检查时间间隔，单位是秒。
    lb_kind DR                # 设置 LVS IP负载均衡模式，有NAT、TUN、DR三个模式
    lb_algo rr                # 设置 LVS 负载调度算法，rr 为轮叫算法
    protocol TCP              # 指定转发协议类型，TCP
    persistence_timeout 60    # 会话保持时间

    # Real Server Nginx 1 的配置
    real_server 10.0.0.80 80 {
        weight 3
	inhibit_on_failure
        TCP_CHECK {
            connect_timeout 10　　　　 # 响应超时时间
            retry 3　　　　　　        # 超时重试次数
            delay_before_retry 3　　　 # 超时重试间隔
            connect_port 80
        }
    }

    # Real Server Nginx_2 的配置
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



### 3. shell 脚本切换IP：/etc/keepalive/scripts/assign_private_ip.sh

```shell
#!/bin/bash
###### ###### ###### ###### ###### ###### ###### ######
# Description:
#
# attaches an IP of your choice to the primary NIC
# an instance you specify
#
# Setup:
#
# You need, at a minimum, the following permissions:
# {
# "Statement": [
# {
# "Action": [
# "ec2:AssignPrivateIpAddresses",
# "ec2:DescribeInstances"
# ],
# "Effect": "Allow",
# "Resource": "*"
# }
# ]
# }
#
# Usage:
#
# ./assign_private_ip.sh allocation_id private_ip_address instance_id
#
# Example:
# ./assign_private_ip.sh 'eipalloc-09ce336dc7a1e8da7' '10.0.0.100' 'i-100ffabd'
#
###### ###### ###### ###### ###### ###### ###### ######


# http://www.davidpashley.com/articles/writing-robust-shell-scripts/
set -o errexit
set -o nounset

ASSOCIATION_ID=$1
PRIVATE_IP=$2
INSTANCE_ID=$3

# 1.根据实例 ID 找到网卡 ID
ENI=$(\
  aws ec2 describe-instances \
  --instance-ids $INSTANCE_ID | \
  jq -r \
  '.Reservations[0].Instances[0].NetworkInterfaces[0].NetworkInterfaceId' \
)

# 2.绑定辅助私有 IP 地址到网卡
aws ec2 assign-private-ip-addresses \
  --network-interface-id $ENI \
  --private-ip-addresses $PRIVATE_IP \
  --allow-reassignment

# 3 关联弹性 IP 地址到辅助私有 IP 地址
aws ec2 associate-address \
  --allocation-id  $ASSOCIATION_ID \
  --network-interface-id $ENI \
  --private-ip-address $PRIVATE_IP \
  --allow-reassociation

echo "Adding ELASTIC IP to ENI $ENI"
```





### 4. shell 脚本切换IP：/etc/keepalive/scripts/unassign_private_ip.sh

```shell
#!/bin/bash 
###### ###### ###### ###### ###### ###### ###### ######
# Description:
#
# attaches an IP of your choice to the primary NIC
# an instance you specify
#
# Setup:
#
# You need, at a minimum, the following permissions:
# {
# "Statement": [
# {
# "Action": [
# "ec2:AssignPrivateIpAddresses",
# "ec2:DescribeInstances"
# ],
# "Effect": "Allow",
# "Resource": "*"
# }
# ]
# }
#
# Usage:
#
# ./unassign_private_ip.sh ASSOCIATION_ID private_ip_address instance_id
#
# Example:
# ./unassign_private_ip.sh 'eipassoc-09fe640e36c9d53b1' '10.0.0.100' 'i-100ffabd'
#
###### ###### ###### ###### ###### ###### ###### ######


# http://www.davidpashley.com/articles/writing-robust-shell-scripts/
set -o errexit
set -o nounset

ASSOCIATION_ID=$1
PRIVATE_IP=$2
INSTANCE_ID=$3

# 1.根据实例 ID 找到网卡 ID
ENI=$(\
  aws ec2 describe-instances \
  --instance-ids $INSTANCE_ID | \
  jq -r \
  '.Reservations[0].Instances[0].NetworkInterfaces[0].NetworkInterfaceId' \
)

# 2.解绑辅助私有 IP 地址
aws ec2 unassign-private-ip-addresses \
  --network-interface-id $ENI \
  --private-ip-addresses $PRIVATE_IP

# 3.1 取消关联弹性 IP 地址，需要传关联 ID（不是分配 ID）。
# aws ec2 disassociate-address --association-id $ASSOCIATION_ID

echo "Removing ELASTIC IP from ENI $ENI"

```



⚠️ scripts 脚本文件需要设置用户权限：

```bash
root@ip-10-0-0-56:/etc/keepalived# mkdir scripts
root@ip-10-0-0-56:/etc/keepalived# chmod 744 scripts/

root@ip-10-0-0-56:/etc/keepalived/scripts# vim assign_private_ip.sh
root@ip-10-0-0-56:/etc/keepalived/scripts# vim unassign_private_ip.sh
root@ip-10-0-0-56:/etc/keepalived/scripts# chmod -R 744 assign_private_ip.sh
root@ip-10-0-0-56:/etc/keepalived/scripts# chmod -R 744 unassign_private_ip.sh
```



## 四、EC2_03、EC2_04 Script 文件

EC2_03、EC2_04 运行 Nginx 服务：

```shell
$ apt-get update && apt-get upgrade
$ apt-get install nginx
```

设置 Script 文件（/etc/init.d/lvsrs）：

```shell
$ sudo -s
$ vim /etc/init.d/lvsrs
$ chmod 755 /etc/init.d/lvsrs
$ /etc/init.d/lvsrs start
```



### /etc/init.d/lvsrs 文件

```shell
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
