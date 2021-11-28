# 自动化部署

部署：安装、更新和配置应用程序及其依赖的组件。

在 AWS 上部署应用的3个工具：

1. AWS CloudFormation；
   * 在引导流程结束时运行一个脚本来部署应用。
2. [AWS Elastic Beanstalk](https://amazonaws-china.com/cn/elasticbeanstalk/?c=7&pt=8)；
   * 适合部署普通 Web 站点应用。
3. AWS OpsWorks；
   * 支持部署互相依赖的多层架构应用。


## 一、使用 CloudFormation 在服务器启动时运行脚本

> AWS CloudFormation 为您提供了一种通用语言来描述和预配置您的云环境中的所有基础设施资源。CloudFormation 使您可以跨所有地区和账户使用简单的文本文件以自动化的安全方式为您的应用程序需要的所有资源建模并对其进行预配置。该文件是您的云环境的单一信任源。 



在服务器启动时运行一个脚本是简单、有用并且灵活地进行自动化部署的方法。

三个步骤：

1. 启动一台仅有操作系统的虚拟服务器；
2. 在引导程序完成后执行一个脚本；
3. 使用脚本安装并配置应用程序；



### 在虚拟服务器上部署 OpenSwan 作为 VPN 服务器

#### vpn-create-cloudformation-stack.sh 文件

```sh
#!/bin/bash -ex

# 获取默认 VPC
vpc=$(aws ec2 describe-vpcs --filter "Name=isDefault, Values=true" --query "Vpcs[0].VpcId" --output text)
# 获取默认子网
subnet=$(aws ec2 describe-subnets --filters Name=vpc-id,Values=$vpc --query Subnets[0].SubnetId --output text)
# 创建一个随机密码
sharedsecret=$(openssl rand -base64 30)
user=vpn
# 创建一个随机共享密钥
password=$(openssl rand -base64 30)

# 创建一个 CloudFormation 堆栈
aws cloudformation create-stack --stack-name vpn --template-url \
https://s3.amazonaws.com/awsinaction/chapter5/vpn-cloudformation.json \ # 1⃣️ CloudFormation 模版文件
--parameters ParameterKey=KeyName,ParameterValue=mykey \
ParameterKey=VPC,ParameterValue=$vpc \
ParameterKey=Subnet,ParameterValue=$subnet \
ParameterKey=IPSecSharedSecret,ParameterValue=$sharedsecret \
ParameterKey=VPNUser,ParameterValue=$user \
ParameterKey=VPNPassword,ParameterValue=$password

# 如果状态不是 COMPLETE，请在 1 min后重试
while [[ `aws cloudformation describe-stacks --stack-name vpn --query Stacks[0].StackStatus` != *"COMPLETE"* ]]
do
	sleep 10
done
aws cloudformation describe-stacks --stack-name vpn --query Stacks[0].Outputs
```

####  vpn-cloudformation.json  ——使用用户数据来初始化一台虚拟服务器

```json
# 使用用户数据来初始化一台虚拟服务器
{
	"AWSTemplateFormatVersion": "2010-09-09",
	"Description": "AWS in Action: chapter 5 (OpenSwan acting as VPN IPSec endpoint)",
	"Parameters": { # 参数，使模版复用成为可能
		"KeyName": { 
			"Description": "key for SSH access",
			"Type": "AWS::EC2::KeyPair::KeyName",
			"ConstraintDescription": "Must be the name of an existing key pair."
		},
		"VPC": {
			"Description": "Just select the one and only default VPC.",
			"Type": "AWS::EC2::VPC::Id"
		},
		"Subnet": {
			"Description": "Just select one of the available subnets.",
			"Type": "AWS::EC2::Subnet::Id"
		},
		"IPSecSharedSecret": {
			"Description": "The shared secret key for IPSec.",
			"Type": "String"
		},
		"VPNUser": {
			"Description": "The VPN user.",
			"Type": "String"
		},
		"VPNPassword": {
			"Description": "The VPN password.",
			"Type": "String"
		}
	},
	"Mappings": {
		"EC2RegionMap": {
			"ap-northeast-1": {"AmazonLinuxAMIHVMEBSBacked64bit": "ami-cbf90ecb"},
			"ap-southeast-1": {"AmazonLinuxAMIHVMEBSBacked64bit": "ami-68d8e93a"},
			"ap-southeast-2": {"AmazonLinuxAMIHVMEBSBacked64bit": "ami-fd9cecc7"},
			"eu-central-1": {"AmazonLinuxAMIHVMEBSBacked64bit": "ami-a8221fb5"},
			"eu-west-1": {"AmazonLinuxAMIHVMEBSBacked64bit": "ami-a10897d6"},
			"sa-east-1": {"AmazonLinuxAMIHVMEBSBacked64bit": "ami-b52890a8"},
			"us-east-1": {"AmazonLinuxAMIHVMEBSBacked64bit": "ami-1ecae776"},
			"us-west-1": {"AmazonLinuxAMIHVMEBSBacked64bit": "ami-d114f295"},
			"us-west-2": {"AmazonLinuxAMIHVMEBSBacked64bit": "ami-e7527ed7"}
		}
	},
	"Resources": {
		"EC2Instance": { # 描述虚拟服务器
			"Type": "AWS::EC2::Instance",
			"Properties": {
				"InstanceType": "t2.micro",
				"SecurityGroupIds": [{"Ref": "InstanceSecurityGroup"}],
				"KeyName": {"Ref": "KeyName"},
				"ImageId": {"Fn::FindInMap": ["EC2RegionMap", {"Ref": "AWS::Region"}, "AmazonLinuxAMIHVMEBSBacked64bit"]},
				"SubnetId": {"Ref": "Subnet"},
				"UserData": {"Fn::Base64": {"Fn::Join": ["", [ # 为虚拟服务器定义一个 shell 脚本作为用户数据
					"#!/bin/bash -ex\n",
					"export IPSEC_PSK=", {"Ref": "IPSecSharedSecret"}, "\n",
					"export VPN_USER=", {"Ref": "VPNUser"}, "\n",
					"export VPN_PASSWORD=", {"Ref": "VPNPassword"}, "\n",
					"export STACK_NAME=", {"Ref": "AWS::StackName"}, "\n",
					"export REGION=", {"Ref": "AWS::Region"}, "\n",
					"curl -s https://raw.githubusercontent.com/AWSinAction/code/master/chapter5/vpn-setup.sh | bash -ex\n" # 2⃣️ 通过http获取shell脚本并执行
				]]}}
			}
		},
		"InstanceSecurityGroup": {
			"Type": "AWS::EC2::SecurityGroup",
			"Properties": {
				"GroupDescription": "Enable access to VPN server",
				"VpcId": {"Ref": "VPC"},
				"SecurityGroupIngress": [
					{
						"IpProtocol": "tcp",
						"FromPort": "22",
						"ToPort": "22",
						"CidrIp": "0.0.0.0/0"
					},
					{
						"IpProtocol": "udp",
						"FromPort": "500",
						"ToPort": "500",
						"CidrIp": "0.0.0.0/0"
					},
					{
						"IpProtocol": "udp",
						"FromPort": "1701",
						"ToPort": "1701",
						"CidrIp": "0.0.0.0/0"
					},
					{
						"IpProtocol": "udp",
						"FromPort": "4500",
						"ToPort": "4500",
						"CidrIp": "0.0.0.0/0"
					}
				]
			}
		}
	},
	"Outputs": {
		"ServerIP": {
			"Description": "Public IP address of the vpn server",
			"Value": {"Fn::GetAtt": ["EC2Instance", "PublicIp"]}
		},
		"IPSecSharedSecret": {
			"Description": "The shared key for the VPN connection (IPSec)",
			"Value": {"Ref": "IPSecSharedSecret"}
		},
		"VPNUser": {
			"Description": "The username for the vpn connection",
			"Value": {"Ref": "VPNUser"}
		},
		"VPNPassword": {
			"Description": "The password for the vpn connection",
			"Value": {"Ref": "VPNPassword"}
		}
	}
}
```



#### vpn-setup.sh - 使用脚本安装并配置一个 VPN 服务器

```sh
#!/bin/bash -ex

#param IPSEC_PSK the shared secret
#param VPN_USER the vpn username
#param VPN_PASSWORD the vpn password
#param STACK_NAME
#param REGION
 
# 获取虚拟服务器私有 IP 地址
PRIVATE_IP=`curl -s http://169.254.169.254/latest/meta-data/local-ipv4`
 # 获取虚拟服务器公有 IP 地址
PUBLIC_IP=`curl -s http://169.254.169.254/latest/meta-data/public-ipv4`

# 向包管理器 yum 添加额外程序包
yum-config-manager --enable epel && yum clean all
# 安装软件程序包
yum install -y openswan xl2tpd

# 为 IPSec 写一个包含共享密钥的文件
cat > /etc/ipsec.conf <<EOF
version 2.0
 
config setup
	nat_traversal=yes
	virtual_private=%v4:10.0.0.0/8,%v4:192.168.0.0/16,%v4:172.16.0.0/12,%v4:25.0.0.0/8,%v6:fd00::/8,%v6:fe80::/10
	oe=off
	protostack=netkey
	nhelpers=0
	interfaces=%defaultroute

conn vpnpsk
	auto=add
	left=$PRIVATE_IP
	leftid=$PUBLIC_IP
	leftsubnet=$PRIVATE_IP/32
	leftnexthop=%defaultroute
	leftprotoport=17/1701
	rightprotoport=17/%any
	right=%any
	rightsubnetwithin=0.0.0.0/0
	forceencaps=yes
	authby=secret
	pfs=no
	type=transport
	auth=esp
	ike=3des-sha1
	phase2alg=3des-sha1
	dpddelay=30
	dpdtimeout=120
	dpdaction=clear
EOF
 
 # 为 IPSec 写配置文件
cat > /etc/ipsec.secrets <<EOF
$PUBLIC_IP %any : PSK "${IPSEC_PSK}"
EOF
 
 # 为 L2TP 管道写配置文件
cat > /etc/xl2tpd/xl2tpd.conf <<EOF
[global]
port = 1701

[lns default]
ip range = 192.168.42.10-192.168.42.250
local ip = 192.168.42.1
require chap = yes
refuse pap = yes
require authentication = yes
name = l2tpd
pppoptfile = /etc/ppp/options.xl2tpd
length bit = yes
EOF

 # 为 PPP 服务写配置文件
cat > /etc/ppp/chap-secrets <<EOF
${VPN_USER} l2tpd ${VPN_PASSWORD} *
EOF

cat > /etc/ppp/options.xl2tpd <<EOF
ipcp-accept-local
ipcp-accept-remote
ms-dns 8.8.8.8
ms-dns 8.8.4.4
noccp
auth
crtscts
idle 1800
mtu 1280
mru 1280
lock
connect-delay 5000
EOF
 
iptables -t nat -A POSTROUTING -s 192.168.42.0/24 -o eth0 -j MASQUERADE
echo 1 > /proc/sys/net/ipv4/ip_forward
iptables-save > /etc/iptables.rules

mkdir -p /etc/network/if-pre-up.d
cat > /etc/network/if-pre-up.d/iptablesload <<EOF
#!/bin/sh
iptables-restore < /etc/iptables.rules
echo 1 > /proc/sys/net/ipv4/ip_forward
exit 0
EOF

# 启动 VPN 服务器需要的服务
service ipsec start && service xl2tpd start
# 配置 VPN 服务器的运行等级
chkconfig ipsec on && chkconfig xl2tpd on

/opt/aws/bin/cfn-signal --stack $STACK_NAME --resource EC2Instance --region $REGION
```

终止虚拟服务器并清理环境：

```
aws cloudformation delete-stack --slack-name vpn
```



## 二、使用 Elastic Beanstalk 部署一个简单的网站应用

> AWS [Elastic Beanstalk](https://amazonaws-china.com/cn/elasticbeanstalk/?c=7&pt=8) 是一项易于使用的服务，用于在熟悉的服务器（例如 Apache 、Nginx、Passenger 和 IIS ）上部署和扩展使用 Java、.NET、PHP、Node.js、Python、Ruby、GO 和 Docker 开发的 Web 应用程序和服务。
>
> 您只需上传代码，Elastic Beanstalk 即可自动处理包括容量预配置、负载均衡、自动扩展和应用程序运行状况监控在内的部署工作。同时，您能够完全控制为应用程序提供支持的 AWS 资源，并可以随时访问底层资源。
>
> Elastic Beanstalk 不额外收费 – 您只需为存储和运行应用程序所需的 AWS 资源付费。
>
> 参考：[**AWS Elastic Beanstalk**](https://docs.aws.amazon.com/zh_cn/elasticbeanstalk/latest/dg/Welcome.html)

### Elastic Beanstalk 的组成部分

* 应用；
* 版本；
* 配置模版；
* 环境；



### 使用 Elastic Beanstalk 部署一个 Node.js 应用：Etherpad

> Etherpad 是一个开源的在线编辑器，支持多人协同编辑。

创建步骤：

1. 创建应用：逻辑上的容器；
2. 创建版本：指向特定 Etherpad 版本的指针；
3. 创建环境：Etherpad 运行的地方；



使用 Elastic Beanstalk 和 AWS CLI 创建应用、版本和环境，并部署一个 Etherpad 服务：

1⃣️ 为 Elastic Beanstalk 服务创建一个应用：

```sh
aws elasticbeanstalk create-application --application-name etherpad
```



2⃣️ 为 AWS Elastic Beanstalk 创建版本

```sh
aws elasticbeanstalk create-application-version \
--application-name etherpad --version-label 1.5.2 \
--source-bundle S3Bucket=awsincation,S3Key=chapter5/etherpad.zip
```



3⃣️ 用 Elastic Beanstalk 创建一个环境来执行 Etherpad

获取最新的 Node.js 环境脚本：

```sh
aws elasticbeanstalk list-available-solution-stacks --output text \
--query "SolutionStacks[?contains(@, 'running Node.js')] | [0]" \

64bit Amazon Linux 2015.03 v1.4.6 running Node.js
```



```sh
aws elasticbeanstalk create-environment --environment-name etherpad \
--application-name etherpad \
--option-settings Namespace=aws:elasticbeanstalk:environment,\
OptionName=EnvironmentType,Value=SingleInstance \ ## 启动一个不可变规模且无负载均衡的单台虚拟服务器
--solution-stalk-name "$SolutionStackName" \
--version-label 1.5.2
```

4⃣️ 检查 Etherpad 环境的状态

```
aws elasticbeanstalk describe-environments --environment-names etherpad
```



## 三、使用 OpsWorks 部署多层架构的应用

> AWS OpsWorks 是一款配置管理服务，提供 Chef 和 Puppet 的托管实例。Chef 和 Puppet 是自动化平台，允许您使用代码来自动配置服务器。借助 OpsWorks，您可以使用 Chef 和 Puppet 自动完成所有 [Amazon EC2](https://aws.amazon.com/cn/ec2/) 实例或本地计算环境中的服务器配置、部署和管理。OpsWorks 提供三种产品：[AWS Opsworks for Chef Automate](https://aws.amazon.com/cn/opsworks/chefautomate/)、[AWS OpsWorks for Puppet Enterprise](https://aws.amazon.com/cn/opsworks/puppetenterprise/) 和 [AWS OpsWorks Stacks](https://aws.amazon.com/cn/opsworks/stacks/)。



**AWS OpsWorks**——一个帮助用户部署多层架构应用的服务

部署是在 **Chef** 的帮助下进行控制的，Chef 是一个配置管理工具。Chef 使用在 cookbook 中组织的 recipe 来部署应用到任意系统中。用户可以使用标准 recipe 或自行创建。

示例：使用 OpsWorks 部署一个 IRC 聊天应用。。。


