![保护系统安全](http://upload-images.jianshu.io/upload_images/2648731-d924d9a51e29c087.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 检查安全更新

1⃣️ 在服务器启动时安装安全更新：在用户数据脚本中添加安全更新命令：

```shell
# 检查安全更新
yum -security check-update

# 安装所有更新
yum -y update

# 仅安装安全更新
yum -y -security update
```

2⃣️ 在服务器运行时安装安全更新：使用一个脚本获取所有服务器列表，然后在所有运行的 EC2 实例上安装安全更新：

#### update.sh 在所有服务器上安装安全更新的脚本文件

```sh
#!/bin/bash -ex

PUBLICNAMES=$(aws ec2 describe-instances --filters "Name=instance-state-name,Values=running" --query "Reservations[].Instances[].PublicDnsName" --output text)

for PUBLICNAME in $PUBLICNAMES; do
	ssh -t -o StrictHostKeyChecking=no ec2-user@$PUBLICNAME "sudo yum -y --security update"
done
```



## 保护 AWS 账户安全

使用多重身份验证（MFA）来保护你的root用户。然后你将停止使用root用户，创建一个新用户用于日常操作，然后学会授予一个角色最小权限。

### [IAM 服务](https://docs.aws.amazon.com/zh_cn/IAM/latest/UserGuide/best-practices.html)

root 用户、IAM 用户、IAM 角色的区别：

|                         | root 用户    | IAM 用户 | IAM 角色 |
| ----------------------- | ------------ | -------- | -------- |
| 可以有一个密码          | 总是         | 是       | 否       |
| 可以有一个访问密钥      | 是（不推荐） | 是       | 否       |
| 可以属于一个组          | 否           | 是       | 否       |
| 可以与一个 EC2 实例关联 | 否           | 否       | 是       |

* 密码：登录管理控制台；
* 访问密钥：使用 AWS CLI 登录；

![策略类型](http://upload-images.jianshu.io/upload_images/2648731-1802a667fc79f54b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



IAM 用户和 IAM 角色通过策略进行授权。

最小授权原则。

创建用户组、IAM 用户、IAM 角色，并将 IAM 角色与 EC2 实例关联。

...



## 控制进出虚拟服务器的网络流量

* 防火墙设置原则：只打开必要的端口。
* 默认情况下，所有的入站流量都被拒绝，而所有的出站流量都被允许。



> 💡💡💡 **源和目的地**
>
> **入站安全组规则**筛选基于网络流量的源。源可以是一个IP地址也可以是一个安全组。这样就可以只允许从特定源IP地址范围来的入站流量。
> **出站安全组规则**筛选基于网路流量的目的地。目的地可以是一个IP地址或安全组。可以只允许特定目的IP地址范围的出站流量。
>
> 源 IP 和 目的 IP 针对的都是 EC2 实例外部的 IP 地址。



### 设置安全组规则

```json
	"Resources": {
		"SecurityGroup": { ## 安全组
			"Type": "AWS::EC2::SecurityGroup", ## 安全组资源类型
			"Properties": {  ## 安全组属性
				"GroupDescription": "My security group", ## 安全组描述
				"VpcId": {"Ref": "VPC"} ## 关联安全组与虚拟网络
			}
		},
		"AllowInboundICMP": { ## 允许 ICPM 规则描述
			"Type": "AWS::EC2::SecurityGroupIngress", ## 入站规则类型
			"Properties": { 
				"GroupId": {"Ref": "SecurityGroup"}, ## 关联入站规则与安全组
				"IpProtocol": "icmp", ## 指定协议
				"FromPort": "-1", ## -1，意味着所有端口
				"ToPort": "-1",
				"CidrIp": "0.0.0.0/0" ## 0.0.0.0/0 表示所有源IP地址都被允许
			}
		},
		"Server": {
			"Type": "AWS::EC2::Instance", ## EC2 实例描述
			"Properties": {
				"ImageId": {"Fn::FindInMap": ["EC2RegionMap", {"Ref": "AWS::Region"}, "AmazonLinuxAMIHVMEBSBacked64bit"]}, 
				"InstanceType": "t2.micro",
				"KeyName": {"Ref": "KeyName"},
				"SecurityGroupIds": [{"Ref": "SecurityGroup"}], ## 将安全组关联到 EC2 实例上
				"SubnetId": {"Ref": "Subnet"}
			}
		}
```



允许 ICMP 流量

```json
		"AllowInboundICMP": { ## 允许 ICPM 规则描述
			"Type": "AWS::EC2::SecurityGroupIngress", ## 入站规则类型
			"Properties": {
				"GroupId": {"Ref": "SecurityGroup"}, ## 关联入站规则与安全组
				"IpProtocol": "icmp", ## 指定协议：ICPMP 协议
				"FromPort": "-1", ## -1，意味着所有端口
				"ToPort": "-1",
				"CidrIp": "0.0.0.0/0" ## 0.0.0.0/0 表示所有源IP地址都被允许
			}
		},
```

允许 SSH 流量

```json
		"IpForSSH": { ## 公有 IP 地址参数
			"Description": "Your public IP address to allow SSH access",
			"Type": "String"
		}
		...
		"AllowInboundSSH": { ## 允许 SSH 规则描述
			"Type": "AWS::EC2::SecurityGroupIngress", ## 入站规则类型
			"Properties": {
				"GroupId": {"Ref": "SecurityGroup"}, ## 关联入站规则与安全组
				"IpProtocol": "tcp", ## SSH 基于 TCP 协议
				"FromPort": "22", ## 默认的 SSH 端口号：22
				"ToPort": "22",
				"CidrIp": {"Fn::Join": ["", [{"Ref": "IpForSSH"}, "/32"]]} ## 指定源IP
			}
		},
```

查看你的公有IP地址：<http://api.ipify.org>



### 在云中创建一个私有网络：虚拟私有云（VPC）



#### 公有子网&私有子网

> 💡
>
> 用户应该至少有两个子网，即公有子网和私有子网。公有子网能够路由到互联网，私有子网则不能。
>
> 公有子网：用户的网站服务器。
>
> 私有子网：用户的数据库服务器。
>
> 公有子网和私有子网到唯一区别是，**私有子网不能路由到互联网网关**（ IGW）。
>
> ---
>
> 私有子网如何访问互联网？如何下载安装应用源码？
>
> 在公有子网中使用一个 NAT 服务器，并且创建一条从用户的私有子网到 NAT 服务器的路由。一台 NAT 服务器就是一台用来处理网络地址转换的虚拟服务器。来自用户的私有子网的互联网流量将从 NAT 服务器的公有 IP 地址访问互联网。



#### 网络安全组 & 网络访问控制列表 ACL

[安全组与网络 ACL 的比较](https://docs.aws.amazon.com/zh_cn/vpc/latest/userguide/VPC_Security.html#VPC_Security_Comparison)

安全组与ACL有一个重要的区别：**安全组是有状态的，而 ACL 则没有。**

如果用户允许在安全组上的一个入站端口，那么该入站端口上的请求对应的出站响应也是被允许的。安全组规则将按用户所期望的方式工作。如果用户在安全组上打开端口22，就能通过SSH连接。

ACL 则不同。如果用户仅仅为子网的 ACL 打开入站端口22，仍然不能通过 SSH 访问。除此之外，用户还需要允许出站临时端口，因为 sshd（SSH守护进程）在端口22上接受连接，但却使用临时端口与客户端通信。临时端口从范围1024至65535中选择。
如果用户想从自己的子网发起一个 SSH 连接，就需要打开出站端口22且同时打开入站临时端口。如果对这些都不熟悉，应该使用安全组且在ACL层允许所有。

![AWS 示例](http://upload-images.jianshu.io/upload_images/2648731-38478f059bb11427.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上图含义：来自 Internet 网关的数据流会通过路由表中的路径被路由到合适的子网。与子网相关的网络 ACL 规则控制允许进入子网的数据流。与实例相关的安全组规则控制允许进入实例的数据流。



#### 互联网网关 IGW

**IGW （Internet Gateway）**:互联网网关，会使用网络地址转换（network address translation, NAT）把虚拟服务器的公有IP地址转换成它们的私有IP地址。




#### 附：CIDR （无类域间路由）的概念

一个示例：计算 192.168.23.35/21 计算子网的网络 ID、子网掩码、起止 IP 地址？

CIDR 表示法：192.168.23.35/21

IP 地址：   11000000.10101000.00010111.00100011 （192.168.23.35）

子网掩码：**11111111.11111111.11111**000.00000000 （255.255.248.0）即21个1

网络 ID：   **11000000.10101000.00010**000.00000000 （192.168.16.0）

起始 IP 地址： 11000000.10101000.00010**000.00000001** （192.168.16.1）

结束 IP 地址：11000000.10101000.00010**111.11111110** （192.168.23.254）

广播 IP 地址：11000000.10101000.00010**111.11111111** （192.168.23.255）主机 ID 全1。



#### 附：有4个子网来保护网站应用安全的VPC

![有4个子网来保护网站应用安全的VPC](http://upload-images.jianshu.io/upload_images/2648731-0c9d400846aa4b91.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



##### CloudFormation 模版文件示例：

```JSON
{
	"AWSTemplateFormatVersion": "2010-09-09",
	"Description": "AWS in Action: chapter 6 (VPC)",
	"Parameters": {
		"KeyName": {
			"Description": "Key Pair name",
			"Type": "AWS::EC2::KeyPair::KeyName",
			"Default": "mykey"
		}
	},
	"Mappings": {
		"EC2RegionMap": {
			"ap-northeast-1": {"AmazonLinuxAMIHVMEBSBacked64bit": "ami-cbf90ecb", "AmazonLinuxNATAMIHVMEBSBacked64bit": "ami-03cf3903"},
			"ap-southeast-1": {"AmazonLinuxAMIHVMEBSBacked64bit": "ami-68d8e93a", "AmazonLinuxNATAMIHVMEBSBacked64bit": "ami-b49dace6"},
			"ap-southeast-2": {"AmazonLinuxAMIHVMEBSBacked64bit": "ami-fd9cecc7", "AmazonLinuxNATAMIHVMEBSBacked64bit": "ami-e7ee9edd"},
			"eu-central-1": {"AmazonLinuxAMIHVMEBSBacked64bit": "ami-a8221fb5", "AmazonLinuxNATAMIHVMEBSBacked64bit": "ami-46073a5b"},
			"eu-west-1": {"AmazonLinuxAMIHVMEBSBacked64bit": "ami-a10897d6", "AmazonLinuxNATAMIHVMEBSBacked64bit": "ami-6975eb1e"},
			"sa-east-1": {"AmazonLinuxAMIHVMEBSBacked64bit": "ami-b52890a8", "AmazonLinuxNATAMIHVMEBSBacked64bit": "ami-fbfa41e6"},
			"us-east-1": {"AmazonLinuxAMIHVMEBSBacked64bit": "ami-1ecae776", "AmazonLinuxNATAMIHVMEBSBacked64bit": "ami-303b1458"},
			"us-west-1": {"AmazonLinuxAMIHVMEBSBacked64bit": "ami-d114f295", "AmazonLinuxNATAMIHVMEBSBacked64bit": "ami-7da94839"},
			"us-west-2": {"AmazonLinuxAMIHVMEBSBacked64bit": "ami-e7527ed7", "AmazonLinuxNATAMIHVMEBSBacked64bit": "ami-69ae8259"}
		}
	},
	"Resources": {
		"SecurityGroup": {
			"Type": "AWS::EC2::SecurityGroup",
			"Properties": {
				"GroupDescription": "My security group",
				"VpcId": {"Ref": "VPC"}
			}
		},
		"SecurityGroupIngress": {
			"Type": "AWS::EC2::SecurityGroupIngress",
			"Properties":{
				"IpProtocol": "-1",
				"FromPort": "-1",
				"ToPort": "-1",
				"CidrIp": "0.0.0.0/0",
				"GroupId": {"Ref": "SecurityGroup"}
			}
		},
		"SecurityGroupEgress": {
			"Type": "AWS::EC2::SecurityGroupEgress",
			"Properties":{
				"IpProtocol": "-1",
				"FromPort": "-1",
				"ToPort": "-1",
				"CidrIp": "0.0.0.0/0",
				"GroupId": {"Ref": "SecurityGroup"}
			}
		},
		"VPC": { ## 1.虚拟网络 VPC
			"Type": "AWS::EC2::VPC",
			"Properties": {
				"CidrBlock": "10.0.0.0/16", ## 地址空间
				"EnableDnsHostnames": "true"
			}
		},
		"InternetGateway": {  ## 互联网网关 IGW
			"Type": "AWS::EC2::InternetGateway",
			"Properties": {
			}
		},
		"VPCGatewayAttachment": { ## 将网关关联到VPC
			"Type": "AWS::EC2::VPCGatewayAttachment",
			"Properties": {
				"VpcId": {"Ref": "VPC"},
				"InternetGatewayId": {"Ref": "InternetGateway"}
			}
		},
		"SubnetPublicNAT": { ## 1.1 公有 NAT 子网
			"Type": "AWS::EC2::Subnet",
			"Properties": {
				"AvailabilityZone": {"Fn::Select": ["0", {"Fn::GetAZs": ""}]},
				"CidrBlock": "10.0.0.0/24", ## NAT 子网的地址空间
				"VpcId": {"Ref": "VPC"}
			}
		},
		"RouteTablePublicNAT": {
			"Type": "AWS::EC2::RouteTable",
			"Properties": {
				"VpcId": {"Ref": "VPC"}
			}
		},
		"RouteTableAssociationPublicNAT": {
			"Type": "AWS::EC2::SubnetRouteTableAssociation",
			"Properties": {
				"SubnetId": {"Ref": "SubnetPublicNAT"},
				"RouteTableId": {"Ref": "RouteTablePublicNAT"}
			}
		},
		"RoutePublicNATToInternet": { ## NAT 子网是公有的能路由到互联网
			"Type": "AWS::EC2::Route",
			"Properties": {
				"RouteTableId": {"Ref": "RouteTablePublicNAT"},
				"DestinationCidrBlock": "0.0.0.0/0",
				"GatewayId": {"Ref": "InternetGateway"}
			},
			"DependsOn": "VPCGatewayAttachment"
		},
		"NetworkAclPublicNAT": {
			"Type": "AWS::EC2::NetworkAcl",
			"Properties": {
				"VpcId": {"Ref": "VPC"}
			}
		},
		"SubnetNetworkAclAssociationPublicNAT": {
			"Type": "AWS::EC2::SubnetNetworkAclAssociation",
			"Properties": {
				"SubnetId": {"Ref": "SubnetPublicNAT"},
				"NetworkAclId": {"Ref": "NetworkAclPublicNAT"}
			}
		},
		"NetworkAclEntryInPublicNATHTTP": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAclPublicNAT"},
				"RuleNumber": "100",
				"Protocol": "6",
				"PortRange": {
					"From": "80",
					"To": "80"
				},
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "10.0.0.0/16"
			}
		},
		"NetworkAclEntryInPublicNATHTTPS": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAclPublicNAT"},
				"RuleNumber": "110",
				"Protocol": "6",
				"PortRange": {
					"From": "443",
					"To": "443"
				},
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "10.0.0.0/16"
			}
		},
		"NetworkAclEntryInPublicNATEphemeralPorts": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAclPublicNAT"},
				"RuleNumber": "200",
				"Protocol": "6",
				"PortRange": {
					"From": "1024",
					"To": "65535"
				},
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "0.0.0.0/0"
			}
		},
		"NetworkAclEntryOutPublicNATHTTP": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAclPublicNAT"},
				"RuleNumber": "100",
				"Protocol": "6",
				"PortRange": {
					"From": "80",
					"To": "80"
				},
				"RuleAction": "allow",
				"Egress": "true",
				"CidrBlock": "0.0.0.0/0"
			}
		},
		"NetworkAclEntryOutPublicNATHTTPS": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAclPublicNAT"},
				"RuleNumber": "110",
				"Protocol": "6",
				"PortRange": {
					"From": "443",
					"To": "443"
				},
				"RuleAction": "allow",
				"Egress": "true",
				"CidrBlock": "0.0.0.0/0"
			}
		},
		"NetworkAclEntryOutPublicNATEphemeralPorts": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAclPublicNAT"},
				"RuleNumber": "200",
				"Protocol": "6",
				"PortRange": {
					"From": "1024",
					"To": "65535"
				},
				"RuleAction": "allow",
				"Egress": "true",
				"CidrBlock": "0.0.0.0/0"
			}
		},
		"SubnetPublicSSHBastion": { ## 1.2 公有堡垒主机子网
			"Type": "AWS::EC2::Subnet",
			"Properties": {
				"AvailabilityZone": {"Fn::Select": ["0", {"Fn::GetAZs": ""}]},
				"CidrBlock": "10.0.1.0/24", ## 公有子网地址空间
				"VpcId": {"Ref": "VPC"}
			}
		},
		"RouteTablePublicSSHBastion": { ## 路由表
			"Type": "AWS::EC2::RouteTable",
			"Properties": {
				"VpcId": {"Ref": "VPC"}
			}
		},
		"RouteTableAssociationPublicSSHBastion": { ## 将路由表关联到子网
			"Type": "AWS::EC2::SubnetRouteTableAssociation",
			"Properties": {
				"SubnetId": {"Ref": "SubnetPublicSSHBastion"},
				"RouteTableId": {"Ref": "RouteTablePublicSSHBastion"}
			}
		},
		"RoutePublicSSHBastionToInternet": {
			"Type": "AWS::EC2::Route",
			"Properties": {
				"RouteTableId": {"Ref": "RouteTablePublicSSHBastion"},
				"DestinationCidrBlock": "0.0.0.0/0", ## 将任何地址路由至 IGW
				"GatewayId": {"Ref": "InternetGateway"}
			},
			"DependsOn": "VPCGatewayAttachment"
		},
		"NetworkAclPublicSSHBastion": { ## 公有子网访问控制列表 ACL
			"Type": "AWS::EC2::NetworkAcl",
			"Properties": {
				"VpcId": {"Ref": "VPC"}
			}
		},
		"SubnetNetworkAclAssociationPublicSSHBastion": { ## 关联
			"Type": "AWS::EC2::SubnetNetworkAclAssociation",
			"Properties": {
				"SubnetId": {"Ref": "SubnetPublicSSHBastion"},
				"NetworkAclId": {"Ref": "NetworkAclPublicSSHBastion"}
			}
		},
		"NetworkAclEntryInPublicSSHBastionSSH": { ## 允许来自任何地方的入站 SSH 流量
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAclPublicSSHBastion"},
				"RuleNumber": "100",
				"Protocol": "6",
				"PortRange": {
					"From": "22",
					"To": "22"
				},
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "0.0.0.0/0"
			}
		},
		"NetworkAclEntryInPublicSSHBastionEphemeralPorts": { ## 用于短 TCP/IP 连接的入站临时端口
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAclPublicSSHBastion"},
				"RuleNumber": "200",
				"Protocol": "6",
				"PortRange": {
					"From": "1024",
					"To": "65535"
				},
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "10.0.0.0/16"
			}
		},
		"NetworkAclEntryOutPublicSSHBastionSSH": { ## 允许从 VPC 出站的 SSH 流量
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAclPublicSSHBastion"},
				"RuleNumber": "100",
				"Protocol": "6",
				"PortRange": {
					"From": "22",
					"To": "22"
				},
				"RuleAction": "allow",
				"Egress": "true",
				"CidrBlock": "10.0.0.0/16"
			}
		},
		"NetworkAclEntryOutPublicSSHBastionEphemeralPorts": {  ## 出站临时端口,用于 SSH 出站连接
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAclPublicSSHBastion"},
				"RuleNumber": "200",
				"Protocol": "6",
				"PortRange": {
					"From": "1024",
					"To": "65535"
				},
				"RuleAction": "allow",
				"Egress": "true",
				"CidrBlock": "0.0.0.0/0"
			}
		},
		"SubnetPublicVarnish": {  ## 1.3 Varnish 网络缓存子网，公有子网
			"Type": "AWS::EC2::Subnet",
			"Properties": {
				"AvailabilityZone": {"Fn::Select": ["0", {"Fn::GetAZs": ""}]},
				"CidrBlock": "10.0.2.0/24", ## 地址空间
				"VpcId": {"Ref": "VPC"}
			}
		},
		"RouteTablePublicVarnish": {
			"Type": "AWS::EC2::RouteTable",
			"Properties": {
				"VpcId": {"Ref": "VPC"}
			}
		},
		"RouteTableAssociationPublicVarnish": {
			"Type": "AWS::EC2::SubnetRouteTableAssociation",
			"Properties": {
				"SubnetId": {"Ref": "SubnetPublicVarnish"},
				"RouteTableId": {"Ref": "RouteTablePublicVarnish"}
			}
		},
		"RoutePublicVarnishToInternet": {
			"Type": "AWS::EC2::Route",
			"Properties": {
				"RouteTableId": {"Ref": "RouteTablePublicVarnish"},
				"DestinationCidrBlock": "0.0.0.0/0",
				"GatewayId": {"Ref": "InternetGateway"}
			},
			"DependsOn": "VPCGatewayAttachment"
		},
		"NetworkAclPublicVarnish": {
			"Type": "AWS::EC2::NetworkAcl",
			"Properties": {
				"VpcId": {"Ref": "VPC"}
			}
		},
		"SubnetNetworkAclAssociationPublicVarnish": {
			"Type": "AWS::EC2::SubnetNetworkAclAssociation",
			"Properties": {
				"SubnetId": {"Ref": "SubnetPublicVarnish"},
				"NetworkAclId": {"Ref": "NetworkAclPublicVarnish"}
			}
		},
		"NetworkAclEntryInPublicVarnishSSH": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAclPublicVarnish"},
				"RuleNumber": "100",
				"Protocol": "6",
				"PortRange": {
					"From": "22",
					"To": "22"
				},
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "10.0.1.0/24"
			}
		},
		"NetworkAclEntryInPublicVarnishHTTP": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAclPublicVarnish"},
				"RuleNumber": "110",
				"Protocol": "6",
				"PortRange": {
					"From": "80",
					"To": "80"
				},
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "0.0.0.0/0"
			}
		},
		"NetworkAclEntryInPublicVarnishEphemeralPorts": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAclPublicVarnish"},
				"RuleNumber": "200",
				"Protocol": "6",
				"PortRange": {
					"From": "1024",
					"To": "65535"
				},
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "0.0.0.0/0"
			}
		},
		"NetworkAclEntryOutPublicVarnishHTTP": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAclPublicVarnish"},
				"RuleNumber": "100",
				"Protocol": "6",
				"PortRange": {
					"From": "80",
					"To": "80"
				},
				"RuleAction": "allow",
				"Egress": "true",
				"CidrBlock": "0.0.0.0/0"
			}
		},
		"NetworkAclEntryOutPublicVarnishHTTPS": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAclPublicVarnish"},
				"RuleNumber": "110",
				"Protocol": "6",
				"PortRange": {
					"From": "443",
					"To": "443"
				},
				"RuleAction": "allow",
				"Egress": "true",
				"CidrBlock": "0.0.0.0/0"
			}
		},
		"NetworkAclEntryOutPublicVarnishEphemeralPorts": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAclPublicVarnish"},
				"RuleNumber": "200",
				"Protocol": "6",
				"PortRange": {
					"From": "1024",
					"To": "65535"
				},
				"RuleAction": "allow",
				"Egress": "true",
				"CidrBlock": "0.0.0.0/0"
			}
		},
		"SubnetPrivateApache": { ## 1.4 Apache 网站服务器私有子网
			"Type": "AWS::EC2::Subnet",
			"Properties": {
				"AvailabilityZone": {"Fn::Select": ["0", {"Fn::GetAZs": ""}]},
				"CidrBlock": "10.0.3.0/24", ## 私有子网地址空间
				"VpcId": {"Ref": "VPC"}
			}
		},
		# 公有子网和私有子网到唯一区别是，私有子网不能路由到互联网网关（ IGW）。
		"RouteTablePrivateApache": { ## Apache 私有子网路由表。没有路由到 IGW。
			"Type": "AWS::EC2::RouteTable",
			"Properties": {
				"VpcId": {"Ref": "VPC"}
			}
		},
		"RouteTableAssociationPrivateApache": {
			"Type": "AWS::EC2::SubnetRouteTableAssociation",
			"Properties": {
				"SubnetId": {"Ref": "SubnetPrivateApache"},
				"RouteTableId": {"Ref": "RouteTablePrivateApache"}
			}
		},
		"RoutePrivateApacheToInternet": {
			"Type": "AWS::EC2::Route",
			"Properties": {
				"RouteTableId": {"Ref": "RouteTablePrivateApache"},
				"DestinationCidrBlock": "0.0.0.0/0", 
				"InstanceId": {"Ref": "NatServer"} ## 从 Apache 子网路由到 NAT 实例
			}
		},
		"NetworkAclPrivateApache": { ## Apache 私有子网 ACL 
			"Type": "AWS::EC2::NetworkAcl",
			"Properties": {
				"VpcId": {"Ref": "VPC"}
			}
		},
		"SubnetNetworkAclAssociationPrivateApache": {
			"Type": "AWS::EC2::SubnetNetworkAclAssociation",
			"Properties": {
				"SubnetId": {"Ref": "SubnetPrivateApache"},
				"NetworkAclId": {"Ref": "NetworkAclPrivateApache"}
			}
		},
		"NetworkAclEntryInPrivateApacheSSH": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAclPrivateApache"},
				"RuleNumber": "100",
				"Protocol": "6",
				"PortRange": {
					"From": "22",
					"To": "22"
				},
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "10.0.1.0/24"
			}
		},
		"NetworkAclEntryInPrivateApacheHTTP": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAclPrivateApache"},
				"RuleNumber": "110",
				"Protocol": "6",
				"PortRange": {
					"From": "80",
					"To": "80"
				},
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "10.0.2.0/24"
			}
		},
		"NetworkAclEntryInPrivateApacheEphemeralPorts": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAclPrivateApache"},
				"RuleNumber": "200",
				"Protocol": "6",
				"PortRange": {
					"From": "1024",
					"To": "65535"
				},
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "0.0.0.0/0"
			}
		},
		"NetworkAclEntryOutPrivateApacheHTTP": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAclPrivateApache"},
				"RuleNumber": "100",
				"Protocol": "6",
				"PortRange": {
					"From": "80",
					"To": "80"
				},
				"RuleAction": "allow",
				"Egress": "true",
				"CidrBlock": "0.0.0.0/0"
			}
		},
		"NetworkAclEntryOutPrivateApacheHTTPS": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAclPrivateApache"},
				"RuleNumber": "110",
				"Protocol": "6",
				"PortRange": {
					"From": "443",
					"To": "443"
				},
				"RuleAction": "allow",
				"Egress": "true",
				"CidrBlock": "0.0.0.0/0"
			}
		},
		"NetworkAclEntryOutPrivateApacheEphemeralPorts": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAclPrivateApache"},
				"RuleNumber": "200",
				"Protocol": "6",
				"PortRange": {
					"From": "1024",
					"To": "65535"
				},
				"RuleAction": "allow",
				"Egress": "true",
				"CidrBlock": "10.0.0.0/16"
			}
		},
		"NatServer": { ## NAT 服务器，私有子网的互联网流量将从 NAT 服务器的公有 IP 地址访问互联网。
			"Type": "AWS::EC2::Instance",
			"Properties": {
				"ImageId": {"Fn::FindInMap": ["EC2RegionMap", {"Ref": "AWS::Region"}, "AmazonLinuxNATAMIHVMEBSBacked64bit"]}, ## AWS 提供了配置好的 NAT 实例映像
				"InstanceType": "t2.micro",
				"KeyName": {"Ref": "KeyName"},
				"NetworkInterfaces": [{
					"AssociatePublicIpAddress": "true", ## 分配公有 IP 地址，所有私有子网到互联网流量的源地址
					"DeleteOnTermination": "true",
					"SubnetId": {"Ref": "SubnetPublicNAT"},
					"DeviceIndex": "0",
					"GroupSet": [{"Ref": "SecurityGroup"}]
				}],
				"SourceDestCheck": "false", ## 默认情况下，一个实例必须是它发送的网络流量的源或者目的地。 对 NAT 实例禁用此检查。
				"UserData": {"Fn::Base64": {"Fn::Join": ["", [
					"#!/bin/bash -ex\n",
					"/opt/aws/bin/cfn-signal --stack ", {"Ref": "AWS::StackName"}, " --resource NatServer --region ", {"Ref": "AWS::Region"}, "\n"
				]]}}
			},
			"CreationPolicy": {
				"ResourceSignal": {
					"Timeout": "PT5M"
				}
			},
			"DependsOn": "VPCGatewayAttachment"
		},
		"BastionHost": { ## 堡垒主机
			"Type": "AWS::EC2::Instance",
			"Properties": {
				"ImageId": {"Fn::FindInMap": ["EC2RegionMap", {"Ref": "AWS::Region"}, "AmazonLinuxAMIHVMEBSBacked64bit"]},
				"InstanceType": "t2.micro",
				"KeyName": {"Ref": "KeyName"},
				"NetworkInterfaces": [{
					"AssociatePublicIpAddress": "true", ## 分配一个公有 IP 地址
					"DeleteOnTermination": "true",
					"SubnetId": {"Ref": "SubnetPublicSSHBastion"}, ## 在堡垒主机子网中启动
					"DeviceIndex": "0",
					"GroupSet": [{"Ref": "SecurityGroup"}] ## 安全组允许所有流量
				}]
			},
			"DependsOn": "VPCGatewayAttachment"
		},
		"VarnishServer": { ## Varnish 服务器
			"Type": "AWS::EC2::Instance",
			"Properties": {
				"ImageId": {"Fn::FindInMap": ["EC2RegionMap", {"Ref": "AWS::Region"}, "AmazonLinuxAMIHVMEBSBacked64bit"]},
				"InstanceType": "t2.micro",
				"KeyName": {"Ref": "KeyName"},
				"NetworkInterfaces": [{
					"AssociatePublicIpAddress": "true",
					"DeleteOnTermination": "true",
					"SubnetId": {"Ref": "SubnetPublicVarnish"},
					"DeviceIndex": "0",
					"GroupSet": [{"Ref": "SecurityGroup"}]
				}],
				"UserData": {"Fn::Base64": {"Fn::Join": ["", [
					"#!/bin/bash -ex\n",
					"yum -y install varnish-3.0.7\n",
					"cat > /etc/varnish/default.vcl << EOF\n",
					"backend default {\n",
					"  .host = \"", {"Fn::GetAtt": ["ApacheServer", "PrivateIp"]} ,"\";\n",
					"  .port = \"80\";\n",
					"}\n",
					"EOF\n",
					"sed -i.bak \"s/^VARNISH_LISTEN_PORT=.*/VARNISH_LISTEN_PORT=80/\" /etc/sysconfig/varnish\n",
					"service varnish start\n",
					"/opt/aws/bin/cfn-signal --stack ", {"Ref": "AWS::StackName"}, " --resource VarnishServer --region ", {"Ref": "AWS::Region"}, "\n"
				]]}}
			},
			"CreationPolicy": {
				"ResourceSignal": {
					"Timeout": "PT5M"
				}
			},
			"DependsOn": "VPCGatewayAttachment"
		},
		"ApacheServer": { ## Apache 服务器
			"Type": "AWS::EC2::Instance",
			"Properties": {
				"ImageId": {"Fn::FindInMap": ["EC2RegionMap", {"Ref": "AWS::Region"}, "AmazonLinuxAMIHVMEBSBacked64bit"]},
				"InstanceType": "t2.micro",
				"KeyName": {"Ref": "KeyName"},
				"NetworkInterfaces": [{
					"AssociatePublicIpAddress": "false", ## 不分配公有 IP 地址。
					"DeleteOnTermination": "true",
					"SubnetId": {"Ref": "SubnetPrivateApache"}, ## 在私有子网中启动
					"DeviceIndex": "0",
					"GroupSet": [{"Ref": "SecurityGroup"}]
				}],
				"UserData": {"Fn::Base64": {"Fn::Join": ["", [
					"#!/bin/bash -ex\n",
					"yum -y install httpd\n", ## 从互联网安装 Apache
					"service httpd start\n",
					"/opt/aws/bin/cfn-signal --stack ", {"Ref": "AWS::StackName"}, " --resource ApacheServer --region ", {"Ref": "AWS::Region"}, "\n"
				]]}}
			},
			"CreationPolicy": {
				"ResourceSignal": {
					"Timeout": "PT10M"
				}
			},
			"DependsOn": "NatServer"
		}
	},
	"Outputs": {
		"BastionHostPublicName": {
			"Value": {"Fn::GetAtt": ["BastionHost", "PublicDnsName"]},
			"Description": "connect via SSH as user ec2-user"
		},
		"VarnishServerPublicName": {
			"Value": {"Fn::GetAtt": ["VarnishServer", "PublicDnsName"]},
			"Description": "handles HTTP requests"
		},
		"VarnishServerPrivateIp": {
			"Value": {"Fn::GetAtt": ["VarnishServer", "PrivateIp"]},
			"Description": "connect via SSH from bastion host"
		},
		"ApacheServerPrivateIp": {
			"Value": {"Fn::GetAtt": ["ApacheServer", "PrivateIp"]},
			"Description": "connect via SSH from bastion host"
		}
	}
}

```














