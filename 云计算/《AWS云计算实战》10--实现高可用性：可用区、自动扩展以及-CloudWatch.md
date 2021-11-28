如何基于 EC2 实例搭建一个高可用性架构？
哈佛研究团队对高可用性的定义：在一年内需要满足 99.99% 的正常运行。

### 高可用性&容错

* **一个高可用性系统**可以在较短的停机时间内自动从故障中恢复。
* **容错系统**要求系统提供的服务不会因为一个组件失效而无法提供服务。

### AWS 提供了构建基于 EC2 实例的高可用系统的工具：

* 利用 CloudWatch 监控虚拟服务器的运行状况。如果需要，自动触发故障恢复；
* 使用多个隔离的数据中心；
* 使用自动扩展（auto-scaling）确保拥有一定数量的虚拟服务器用以自动替换失效的实例；

## 一、使用 CloudWatch 恢复失效的服务器

[官方文档：**Amazon CloudWatch**](https://docs.aws.amazon.com/zh_cn/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html)

[向 Amazon CloudWatch 警报添加恢复操作](https://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/UsingAlarmActions.html)

AWS CloudWatch 是一个提供监测指标、日志和告警的服务。

**「系统状态检查」**通常是检测网络连接、供电或者物理主机上的软件或硬件是否存在问题。

常用的策略：**将出现故障的虚拟服务器恢复到另一台物理主机之上。**

失效恢复步骤：

1. 物理服务器的失效导致运行其上的虚拟服务器失效。
2. EC2 服务检测到运行中断并报告给 CloudWatch 的度量。
3. CloudWatch 告警触发了对于虚拟服务器的恢复。
4. 在另一台物理服务器上启动这个虚拟服务器。
5. EBS 卷和弹性 IP 将被连接到新的虚拟服务器并保持原样。



⚠️ 只有具有以下特性的 EC2 实例支持恢复操作：

* 使用以下实例类型之一：A1、C3、C4、C5、C5n、M3、M4、M5、M5a、R3、R4、R5、R5a、T2、T3、X1 或 X1e
* 使用 `default` 或 `dedicated` 实例租赁
* 仅使用 EBS 卷 (不配置实例存储卷)。有关更多信息，请参阅[已禁用 “恢复此实例”](https://aws.amazon.com/premiumsupport/knowledge-center/recover-this-instance-cloudwatch-enable/)。



### 建立一个 CloudWatch 告警

示例模版代码：

虚拟服务器的状态由 AWS 每分钟检查一次，告警服务检查 StatusCheckFailed_System 的指标。如果有连续的 5 个失效的报告，告警将被触发。

```json
"RecoveryAlarm": { # 创建一个 CloudWatch 告警用以监控虚拟服务器的运行状况
    "Type": "AWS::CloudWatch::Alarm",
    "Properties": {
        "AlarmDescription": "Recover server when underlying hardware fails.", # 描述
        "Namespace": "AWS/EC2" , # 监控器指标由 EC2 提供，命名空间用的是 AWS/EC2
        "MetricName": "StatusCheckFailed_System", # 运行状况检查的指标名称，EC2 实例包含的系统故障检查的名称
        "Statistic": "Minimum", # 统计函数被用于检测的指标，即使最小的状态检查失效也会得到通知
        "Period": "60", # 统计函数用来计算应用的时间，以秒为单位，并且必须是60的倍数
        "EvaluationPeriods": "5", # 用于将数据与阈值进行比较的周期数
        "ComparisonOperator": "GreaterThanThreshold",
        "Threshold": "0", # 阈值触发告警
        "AlarmActions": [{"Fn::Join": ["", ["arn:aws:automate:", { "Ref": "AWS::Region"}, ":ec2:recover"]]}], # 告警触发动作。使用针对 EC2 实例的预定义的恢复操作。
        "Dimensions": [{"Name": "InstanceId", "Value": {"Ref": "Server"}}] # 纬度。虚拟服务器自身就是一个度量的纬度，将统计函数的输出结果与阈值进行比较的操作。
    }
}
```



### 基于 CloudWatch 对虚拟服务器监控与恢复

> 在基础设施上部署 Jenkins，以实现敏捷开发流程。
>
> Jenkins 是一个持续集成服务器（CI ），支持自动化软件测试、构建和部署。

操作步骤：

1. 在云计算环境中建立一个虚拟网络（VPC）；
2. 在 VPC 中启动一个虚拟服务器，并通过初始化启动程序（bootstrap）自动安装 Jenkins；
3. 创建一个 CloudWatch 告警服务，监控虚拟服务器的运行状况；

启动一个含有 EC2 实例与 CloudWatch 告警触发服务器失效恢复的 CloudWatch 模版：

```shell
# 使用由8～40个字符和数字组成的密码替换$Password。
# 一个 Jenkins 服务器将会自动安装在一个虚拟服务器上。
# 也就是说，该 Jenkins 服务器运行在带自恢复功能的虚拟服务器上。
aws cloudformation create-stack \
--stack-name jenkins-recovery \ 
--template-url https://s3.amazonaws.com/awsinaction/chapter11/recovery.json \ # 模版代码url
--parameters ParameterKey=JenkinsAdminPassword,ParameterValue=$Password
```

查询堆栈的输出结果：

```shell
aws cloudformation describe-stacks \
--stack-name jenkins-recovery \
--query Stacks[0].Outputs
```

清理资源：

```shell
# 清理资源
aws cloudformation delete-stack --stack-name jenkins-recovery

# 查看资源状态，直到返回堆栈不存在的错误信息。
aws cloudformation describe-stacks --stack-name jenkins-recovery
```



CloudWatch 模版代码：<https://s3.amazonaws.com/awsinaction/chapter11/recovery.json>


```json
{
	"AWSTemplateFormatVersion": "2010-09-09",
	"Description": "AWS in Action: chapter 11 (Jenkins (CI server) running on EC2 with AWS CloudWatch recovery)",
	"Parameters": {
		"KeyName": {
			"Description": "Key Pair name",
			"Type": "AWS::EC2::KeyPair::KeyName",
			"Default": "mykey"
		},
		"JenkinsAdminPassword": {
			"Description": "Password for Jenkins admin user",
			"Type": "String",
			"AllowedPattern" : "[a-zA-Z0-9]*",
			"MinLength" : "8",
			"MaxLength" : "42"
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
		"VPC": {
			"Type": "AWS::EC2::VPC",
			"Properties": {
				"EnableDnsSupport": "true",
				"EnableDnsHostnames": "true",
				"CidrBlock": "10.0.0.0/16",
				"Tags": [
					{
						"Key": "Name",
						"Value": "jenkins-recovery"
					}
				]
			}
		},
		"Subnet": {
			"Type": "AWS::EC2::Subnet",
			"Properties": {
				"VpcId": {
					"Ref": "VPC"
				},
				"AvailabilityZone": {"Fn::Select": ["0", {"Fn::GetAZs": ""}]},
				"CidrBlock": "10.0.0.0/24",
				"Tags": [
					{
						"Key": "Name",
						"Value": "jenkins-recovery"
					}
				]
			}
		},
		"InternetGateway": {
			"Type": "AWS::EC2::InternetGateway",
			"Properties": {
				"Tags": [
					{
						"Key": "Name",
						"Value": "jenkins-recovery"
					}
				]
			}
		},
		"GatewayToInternet": {
			"Type": "AWS::EC2::VPCGatewayAttachment",
			"Properties": {
				"VpcId": {
					"Ref": "VPC"
				},
				"InternetGatewayId": {
					"Ref": "InternetGateway"
				}
			}
		},
		"RouteTable": {
			"Type": "AWS::EC2::RouteTable",
			"Properties": {
				"VpcId": {
					"Ref": "VPC"
				},
				"Tags": [
					{
						"Key": "Name",
						"Value": "jenkins-recovery"
					}
				]
			}
		},
		"InternetRoute": {
			"Type": "AWS::EC2::Route",
			"Properties": {
				"RouteTableId": {
					"Ref": "RouteTable"
				},
				"DestinationCidrBlock": "0.0.0.0/0",
				"GatewayId": {
					"Ref": "InternetGateway"
				}
			},
			"DependsOn": "GatewayToInternet"			
		},
		"RouteTableAssociation": {
			"Type": "AWS::EC2::SubnetRouteTableAssociation",
			"Properties": {
				"SubnetId": {
					"Ref": "Subnet"
				},
				"RouteTableId": {
					"Ref": "RouteTable"
				}
			}
		},
		"NetworkAcl": {
			"Type": "AWS::EC2::NetworkAcl",
			"Properties": {
				"VpcId": {
					"Ref": "VPC"
				},
				"Tags": [
					{
						"Key": "Name",
						"Value": "jenkins-recovery"
					}
				]
			}
		},
		"NetworkAceSSH": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {
					"Ref": "NetworkAcl"
				},
				"RuleNumber": "10",
				"Protocol": "6",
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "0.0.0.0/0",
				"PortRange": {
					"From": "22",
					"To": "22"
				}
			}
		},
		"NetworkAceJenkinsHTTP": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {
					"Ref": "NetworkAcl"
				},
				"RuleNumber": "11",
				"Protocol": "6",
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "0.0.0.0/0",
				"PortRange": {
					"From": "8080",
					"To": "8080"
				}
			}
		},
		"NetworkAceNTP": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {
					"Ref": "NetworkAcl"
				},
				"RuleNumber": "20",
				"Protocol": "17",
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "0.0.0.0/0",
				"PortRange": {
					"From": "123",
					"To": "123"
				}
			}
		},
		"NetworkAceICMP": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {
					"Ref": "NetworkAcl"
				},
				"RuleNumber": "30",
				"Protocol": "1",
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "0.0.0.0/0",
				"Icmp": {
					"Code": "-1",
					"Type": "-1"
				}
			}
		},
		"NetworkAceHighPortsTCP": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {
					"Ref": "NetworkAcl"
				},
				"RuleNumber": "40",
				"Protocol": "6",
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "0.0.0.0/0",
				"PortRange": {
					"From": "1024",
					"To": "65535"
				}
			}
		},
		"NetworkAceHighPortsUDP": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {
					"Ref": "NetworkAcl"
				},
				"RuleNumber": "41",
				"Protocol": "17",
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "0.0.0.0/0",
				"PortRange": {
					"From": "1024",
					"To": "65535"
				}
			}
		},
		"NetworkAceEgress": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {
					"Ref": "NetworkAcl"
				},
				"RuleNumber": "10",
				"Protocol": "-1",
				"RuleAction": "allow",
				"Egress": "true",
				"CidrBlock": "0.0.0.0/0",
				"PortRange": {
					"From": "0",
					"To": "65535"
				}
			}
		},
		"NetworkAclAssociation": {
			"Type": "AWS::EC2::SubnetNetworkAclAssociation",
			"Properties": {
				"SubnetId": {
					"Ref": "Subnet"
				},
				"NetworkAclId": {
					"Ref": "NetworkAcl"
				}
			}
		},
		"SecurityGroup": {
			"Type": "AWS::EC2::SecurityGroup",
			"Properties": {
				"GroupDescription": "SecurityGroupforjenkins",
				"VpcId": {
					"Ref": "VPC"
				},
				"Tags": [
					{
						"Key": "Name",
						"Value": "jenkins-recovery"
					}
				],
				"SecurityGroupIngress": [
					{
						"IpProtocol": "tcp",
						"FromPort": "22",
						"ToPort": "22",
						"CidrIp": "0.0.0.0/0"
					},
					{
						"IpProtocol": "tcp",
						"FromPort": "8080",
						"ToPort": "8080",
						"CidrIp": "0.0.0.0/0"
					},
					{
						"IpProtocol": "icmp",
						"FromPort": "-1",
						"ToPort": "-1",
						"CidrIp": "0.0.0.0/0"
					}
				]
			}
		},
		# 使用弹性 IP 提供的公有IP地址将会在服务器恢复以后保持一致。
		"ElasticIP": {
			"Type": "AWS::EC2::EIP",
			"Properties": {
				"InstanceId": {"Ref": "Server"},
				"Domain": "vpc"
			},
			"DependsOn": "GatewayToInternet"
		},
		"Server": { # 启动一个虚拟服务器来运行 Jenkins 服务器
			"Type": "AWS::EC2::Instance",
			"Properties": {
				"ImageId": {"Fn::FindInMap": ["EC2RegionMap", {"Ref": "AWS::Region"}, "AmazonLinuxAMIHVMEBSBacked64bit"]},
				"InstanceType": "t2.micro", # 恢复的是 t2 类型的实例
				"KeyName": {"Ref": "KeyName"},
				"SecurityGroupIds": [{"Ref": "SecurityGroup"}],
				"SubnetId": {"Ref": "Subnet"},
				"UserData": {"Fn::Base64": {"Fn::Join": ["", [ # 用户数据中包含安装 Jenkins 服务器的脚本
					"#!/bin/bash -ex\n",
					"wget http://pkg.jenkins-ci.org/redhat/jenkins-1.616-1.1.noarch.rpm\n",
					"rpm --install jenkins-1.616-1.1.noarch.rpm\n",
					"sed -i -e 's/JENKINS_ARGS=\\\"\\\"/JENKINS_ARGS=\\\"--argumentsRealm.passwd.admin=", {"Ref": "JenkinsAdminPassword"}, " --argumentsRealm.roles.admin=admin\\\"/g' /etc/sysconfig/jenkins\n",
					"echo \"<?xml version='1.0' encoding='UTF-8'?><hudson><version>1.0</version><useSecurity>true</useSecurity><authorizationStrategy class=\\\"hudson.security.FullControlOnceLoggedInAuthorizationStrategy\\\"/><securityRealm class=\\\"hudson.security.LegacySecurityRealm\\\"/></hudson>\" > /var/lib/jenkins/config.xml\n",
					"service jenkins start\n"
				]]}},
				"Tags": [
					{
						"Key": "Name",
						"Value": "jenkins-recovery"
					}
				]
			},
			"DependsOn": "GatewayToInternet"
		},
		"RecoveryAlarm": { # 创建一个 CloudWatch 告警用以监控虚拟服务器的运行状况
			"Type": "AWS::CloudWatch::Alarm",
			"Properties": {
				"AlarmDescription": "Recover server when underlying hardware fails.",
				"Namespace": "AWS/EC2" , # 监控器指标由 EC2 提供，命名空间用的是 AWS/EC2
				"MetricName": "StatusCheckFailed_System", # 运行状况检查的指标名称，EC2 实例包含的系统故障检查的名称
				"Statistic": "Minimum", # 统计函数被用于检测的指标，即使最小的状态检查失效也会得到通知
				"Period": "60", # 统计函数用来计算应用的时间，以秒为单位，并且必须是60的倍数
				"EvaluationPeriods": "5", # 将数据与阈值进行比较的周期数
				"ComparisonOperator": "GreaterThanThreshold",
				"Threshold": "0", # 触发告警的阈值
				"AlarmActions": [{"Fn::Join": ["", ["arn:aws:automate:", { "Ref": "AWS::Region"}, ":ec2:recover"]]}], # 告警情况下采取的动作。使用针对 EC2 实例的预定义的恢复操作。
				"Dimensions": [{"Name": "InstanceId", "Value": {"Ref": "Server"}}] # 虚拟服务器指标的一个纬度。虚拟服务器自身就是一个度量的纬度，将统计函数的输出结果与阈值进行比较的操作。
			}
	    }
	},
	"Outputs": {
		"JenkinsURL": {
			"Description": "URL to access web interface of Jenkins server.",
			"Value": {"Fn::Join": ["", ["http://", {"Ref": "ElasticIP"}, ":8080"]]}
		},
		"User": {
			"Description": "Administrator user for Jenkins.",
			"Value": "admin"
		},
		"Password": {
			"Description": "Password for Jenkins administrator user.",
			"Value": {"Ref": "JenkinsAdminPassword"}
		}
	}
}
```



## 二、从数据中心故障中恢复

> 如何在多个可用区（AZ）甚至多个区域（regions）中搭建一个高可用的架构？

在多个可用区内搭建一个高可用的架构的注意事项：

* 存储在网络附加存储的数据故障转移到另一个数据中心后默认不可用。
* 在另一个数据中心不能用同一个私有 IP 地址启动新的虚拟服务器。另外，恢复后不能自动保持同一个公有 IP 地址。



### 可用区：每个区域有多个数据中心

[AWS 全球基础设施](https://amazonaws-china.com/cn/about-aws/global-infrastructure/)

> 每个 AWS 区域（region）都包含多个可用区（Availability Zone）和数据中心。
>
> AWS 区域提供多个在物理上独立且隔离的可用区，这些可用区通过延迟低、吞吐量高且冗余性高的网络连接在一起。



查看在自己的账号中的所有可用区域：

```bash
aws ec2 describe-regions
{
    "Regions": [
        {
            "Endpoint": "ec2.eu-north-1.amazonaws.com",
            "RegionName": "eu-north-1"
        },
        {
            "Endpoint": "ec2.ap-south-1.amazonaws.com",
            "RegionName": "ap-south-1"
        },
        {
            "Endpoint": "ec2.eu-west-3.amazonaws.com",
            "RegionName": "eu-west-3"
        },
        {
            "Endpoint": "ec2.eu-west-2.amazonaws.com",
            "RegionName": "eu-west-2"
        },
        {
            "Endpoint": "ec2.eu-west-1.amazonaws.com",
            "RegionName": "eu-west-1"
        },
        {
            "Endpoint": "ec2.ap-northeast-2.amazonaws.com",
            "RegionName": "ap-northeast-2"
        },
        {
            "Endpoint": "ec2.ap-northeast-1.amazonaws.com",
            "RegionName": "ap-northeast-1"
        },
        {
            "Endpoint": "ec2.sa-east-1.amazonaws.com",
            "RegionName": "sa-east-1"
        },
        {
            "Endpoint": "ec2.ca-central-1.amazonaws.com",
            "RegionName": "ca-central-1"
        },
        {
            "Endpoint": "ec2.ap-southeast-1.amazonaws.com",
            "RegionName": "ap-southeast-1"
        },
        {
            "Endpoint": "ec2.ap-southeast-2.amazonaws.com",
            "RegionName": "ap-southeast-2"
        },
        {
            "Endpoint": "ec2.eu-central-1.amazonaws.com",
            "RegionName": "eu-central-1"
        },
        {
            "Endpoint": "ec2.us-east-1.amazonaws.com",
            "RegionName": "us-east-1"
        },
        {
            "Endpoint": "ec2.us-east-2.amazonaws.com",
            "RegionName": "us-east-2"
        },
        {
            "Endpoint": "ec2.us-west-1.amazonaws.com",
            "RegionName": "us-west-1"
        },
        {
            "Endpoint": "ec2.us-west-2.amazonaws.com",
            "RegionName": "us-west-2"
        }
    ]
}
```



列出某一个区域中的所有可用区：

格式：`aws ec2 describe-availability-zones --region $RegionName`

```shell
 aws ec2 describe-availability-zones --region us-east-1
{
    "AvailabilityZones": [
        {
            "State": "available",
            "Messages": [],
            "RegionName": "us-east-1",
            "ZoneName": "us-east-1a",
            "ZoneId": "use1-az2"
        },
        {
            "State": "available",
            "Messages": [],
            "RegionName": "us-east-1",
            "ZoneName": "us-east-1b",
            "ZoneId": "use1-az4"
        },
        {
            "State": "available",
            "Messages": [],
            "RegionName": "us-east-1",
            "ZoneName": "us-east-1c",
            "ZoneId": "use1-az6"
        },
        {
            "State": "available",
            "Messages": [],
            "RegionName": "us-east-1",
            "ZoneName": "us-east-1d",
            "ZoneId": "use1-az1"
        },
        {
            "State": "available",
            "Messages": [],
            "RegionName": "us-east-1",
            "ZoneName": "us-east-1e",
            "ZoneId": "use1-az3"
        },
        {
            "State": "available",
            "Messages": [],
            "RegionName": "us-east-1",
            "ZoneName": "us-east-1f",
            "ZoneId": "use1-az5"
        }
    ]
}
```



 在 VPC 内定义一个私有网络：

![虚拟私有网络 VPC](http://upload-images.jianshu.io/upload_images/2648731-4e79d637949e7696.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

💡💡💡 要点：VPC 总是绑定到一个区域，VPC 内的子网仅仅和可用区关联。



### 使用自动扩展确保虚拟服务器一直运行

[**AWS Auto Scaling**](https://docs.aws.amazon.com/zh_cn/autoscaling/plans/userguide/what-is-aws-auto-scaling.html)

自动扩展需要的配置信息：

* 「启动配置」包含了虚拟服务器启动的所有信息：实例类型（虚拟服务器的大小）和启动所需的映像（AMI）。
* 「自动扩展组」告诉 EC2 按指定的启动配置启动的虚拟服务器数量，如何监控实例，应该在哪个子网中启动。



 部分配置文件：

```json
"LaunchConfiguration": {
    "Type": "AWS::AutoScaling::LaunchConfiguration",
    "Properties": { # 用于自动扩展的启动配置
        "InstanceMonitoring": false,
        "ImageId": {"Ref": "AMISnapshot"}, # 启动虚拟服务器的映像
        "KeyName": {"Ref": "KeyName"},
        "SecurityGroups": [{"Ref": "SecurityGroupJenkins"}],
        "AssociatePublicIpAddress": true,
        "InstanceType": "t2.micro", # 虚拟服务器类型
        "UserData": {
            "Fn::Base64": {
                "Fn::Join": [
                    "",
                    [
                        "#!/bin/bash -ex\n",
                        "wget http://pkg.jenkins-ci.org/redhat/jenkins-1.616-1.1.noarch.rpm\n",
                        "rpm --install jenkins-1.616-1.1.noarch.rpm\n",
                        "sed -i -e 's/JENKINS_ARGS=\\\"\\\"/JENKINS_ARGS=\\\"--argumentsRealm.passwd.admin=", {"Ref": "JenkinsAdminPassword"}, " --argumentsRealm.roles.admin=admin\\\"/g' /etc/sysconfig/jenkins\n",
                        "echo \"<?xml version='1.0' encoding='UTF-8'?><hudson><version>1.0</version><useSecurity>true</useSecurity><authorizationStrategy class=\\\"hudson.security.FullControlOnceLoggedInAuthorizationStrategy\\\"/><securityRealm class=\\\"hudson.security.LegacySecurityRealm\\\"/></hudson>\" > /var/lib/jenkins/config.xml\n",
                        "service jenkins start\n"
                    ]
                ]
            }
        }
    }
},
"AutoScalingGroup": { # 自动扩展组负责启动虚拟服务器
    "Type": "AWS::AutoScaling::AutoScalingGroup",
    "Properties": {
        "LaunchConfigurationName": {"Ref": "LaunchConfiguration"}, # 关联到启动配置
        "Tags": [
            {
                "Key": "Name",
                "Value": "jenkins-multiaz",
                "PropagateAtLaunch": true
            }
        ],
        "DesiredCapacity": 1, # EC2 实例的需要数量
        "MinSize": 1, # EC2 实例的最小数量
        "MaxSize": 1, # EC2 实例的最小数量
        "VPCZoneIdentifier": [{"Ref": "SubnetA"}, {"Ref": "SubnetB"}], # 在子网A和子网B中启动虚拟服务器
        "TerminationPolicies": ["Default"],
        "HealthCheckGracePeriod": 600,
        "HealthCheckType": "EC2" # 使用 EC2 内部的健康状况检查
    },
    "DependsOn": "GatewayToInternet"
}
```



### 在另一个可用区中通过自动扩展恢复失效的虚拟服务器



支持在不同的可用区恢复虚拟服务器的示例：

```shell
# 创建堆栈
# 使用由8～40个字符和数字组成的密码替换$Password。
aws cloudformation create-stack \
--stack-name jenkins-multiaz \ 
--template-url https://s3.amazonaws.com/awsinaction/chapter11/multiaz.json \ # 模版代码url
--parameters ParameterKey=JenkinsAdminPassword,ParameterValue=$Password

# 获得虚拟服务器的公有 IP 地址
aws ec2 describe-instances --filters "Name=tag:Name,\
Values=jenkins-nultiza" "Name=instance-state-code,Values=16" \
--query "Reservations[0].Instances[0].\
[InstanceId, PublicIpAddress, PrivateIpAddress, SubnetId]"

# 终止虚拟服务器
aws ec2 terminate-instances --instance-ids $InstanceId
```

该方法存在的问题：

1. 数据丢失；
2. Jenkins 服务器的公有 IP 地址、私有 IP 地址会改变。



部分模版代码：

```json
"LaunchConfiguration": { # 用于自动扩展的启动配置
    "Type": "AWS::AutoScaling::LaunchConfiguration",
    "Properties": {
        "InstanceMonitoring": false,
        "ImageId": {"Fn::FindInMap": ["EC2RegionMap", {"Ref": "AWS::Region"}, "AmazonLinuxAMIHVMEBSBacked64bit"]}, # 虚拟服务器映像
        "KeyName": {"Ref": "KeyName"}, # SSH 密钥对
        "SecurityGroups": [{"Ref": "SecurityGroupJenkins"}], # 关联的安全组
        "AssociatePublicIpAddress": true, # 公有 IP 地址
        "InstanceType": "t2.micro", # 虚拟服务器类型
        "UserData": { # 引导安装 Jenkins CI 服务器期间执行的脚本
            "Fn::Base64": {
                "Fn::Join": [
                    "",
                    [
                        "#!/bin/bash -ex\n",
                        "wget http://pkg.jenkins-ci.org/redhat/jenkins-1.616-1.1.noarch.rpm\n",
                        "rpm --install jenkins-1.616-1.1.noarch.rpm\n",
                        "sed -i -e 's/JENKINS_ARGS=\\\"\\\"/JENKINS_ARGS=\\\"--argumentsRealm.passwd.admin=", {"Ref": "JenkinsAdminPassword"}, " --argumentsRealm.roles.admin=admin\\\"/g' /etc/sysconfig/jenkins\n",
                        "echo \"<?xml version='1.0' encoding='UTF-8'?><hudson><version>1.0</version><useSecurity>true</useSecurity><authorizationStrategy class=\\\"hudson.security.FullControlOnceLoggedInAuthorizationStrategy\\\"/><securityRealm class=\\\"hudson.security.LegacySecurityRealm\\\"/></hudson>\" > /var/lib/jenkins/config.xml\n",
                        "service jenkins start\n"
                    ]
                ]
            }
        }
    }
},
# 要点：虚拟服务器的子网没有在启动配置中定义，而是在自动扩展组中定义的。
"AutoScalingGroup": { # 自动扩展组负责启动虚拟服务器
    "Type": "AWS::AutoScaling::AutoScalingGroup",
    "Properties": {
        "LaunchConfigurationName": {"Ref": "LaunchConfiguration"}, # 关联到启动配置
        "Tags": [ # 自动扩展组标签
            {
                "Key": "Name",
                "Value": "jenkins-multiaz",
                "PropagateAtLaunch": true
            }
        ],
        "DesiredCapacity": 1, # EC2 实例的需要个数
        "MinSize": 1, # EC2 实例的最小个数
        "MaxSize": 1, # EC2 实例的最大个数
        "VPCZoneIdentifier": [{"Ref": "SubnetA"}, {"Ref": "SubnetB"}], # 在子网A（创建在可用区A）和子网B（在可用区B）中启动虚拟服务器
        "HealthCheckGracePeriod": 600,
        "HealthCheckType": "EC2" # 使用 EC2 服务器内部的运行状况检查
    },
    "DependsOn": "GatewayToInternet"
}
```





### 网络附加存储恢复

Jenkins 服务器直接在磁盘上存储数据。可以给 EBS 卷创建快照。使 EBS 快照存储在 S3 中，以便在多个可用区中保持可用。



实现方式：使用 EBS 快照在另一个可用区中恢复一台虚拟服务器，并且没有丢失存储在 EBS 卷里面的完整状态数据。



```shell
# 创建快照：
aws ec2 create-image --instance-id $InstanceId --name jenkins-multiaz

# 检查当前状态
aws ec2 describe-images --image-id $ImageId --query "Images[].State"

# 更新启动配置
aws cloudformation update-stack \
--stack-name jenkins-multiaz \ 
--template-url https://s3.amazonaws.com/awsinaction/chapter11/multiaz.json \ # 模版代码url
--parameters \
ParameterKey=JenkinsAdminPassword,UsePreviousValue=true, \
ParameterKey=AMISnapshot,ParameterValue=$Password
```





### 网络接口恢复

默认情况下，通过自动扩展启动的虚拟服务器不能把弹性公网 IP 作为公有 IP。

可以通过自动扩展在虚拟服务器引导程序中分配一个弹性 IP。并且关联这个公有 IP地址。

```shell
aws cloudformation create-stack \
--stack-name jenkins-elasticip \
--template-url https://s3.amazonaws.com/awsinaction/chapter11/multiaz-elasticip.json \
--parameters ParameterKey=JenkinsAdminPassword,ParameterValue=$Password \
--capabilities CAPABILITY_IAM
```



CloudFormation 模版代码：

```json
{
	"AWSTemplateFormatVersion": "2010-09-09",
	"Description": "AWS in Action: chapter 11 (Jenkins (CI server) running with Auto Scaling Group over multiple AZs)",
	"Parameters": {
		"KeyName": {
			"Description": "Key Pair name",
			"Type": "AWS::EC2::KeyPair::KeyName",
			"Default": "mykey"
		},
		"JenkinsAdminPassword": {
			"Description": "Password for Jenkins admin user",
			"Type": "String",
			"AllowedPattern" : "[a-zA-Z0-9]*",
			"MinLength" : "8",
			"MaxLength" : "42"
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
		"VPC": {
			"Type": "AWS::EC2::VPC",
			"Properties": {
				"EnableDnsSupport": "true",
				"EnableDnsHostnames": "true",
				"CidrBlock": "10.0.0.0/16",
				"Tags": [
					{
						"Key": "Name",
						"Value": "jenkins-multiaz"
					}
				]
			}
		},
		"SubnetA": {
			"Type": "AWS::EC2::Subnet",
			"Properties": {
				"VpcId": {
					"Ref": "VPC"
				},
				"AvailabilityZone": {"Fn::Select": ["0", {"Fn::GetAZs": ""}]},
				"CidrBlock": "10.0.0.0/24",
				"Tags": [
					{
						"Key": "Name",
						"Value": "jenkins-multiaz"
					}
				]
			}
		},
		"SubnetB": {
			"Type": "AWS::EC2::Subnet",
			"Properties": {
				"VpcId": {
					"Ref": "VPC"
				},
				"AvailabilityZone": {"Fn::Select": ["1", {"Fn::GetAZs": ""}]},
				"CidrBlock": "10.0.1.0/24",
				"Tags": [
					{
						"Key": "Name",
						"Value": "jenkins-multiaz"
					}
				]
			}
		},
		"InternetGateway": {
			"Type": "AWS::EC2::InternetGateway",
			"Properties": {
				"Tags": [
					{
						"Key": "Name",
						"Value": "jenkins-multiaz"
					}
				]
			}
		},
		"GatewayToInternet": {
			"Type": "AWS::EC2::VPCGatewayAttachment",
			"Properties": {
				"VpcId": {
					"Ref": "VPC"
				},
				"InternetGatewayId": {
					"Ref": "InternetGateway"
				}
			}
		},
		"RouteTable": {
			"Type": "AWS::EC2::RouteTable",
			"Properties": {
				"VpcId": {
					"Ref": "VPC"
				},
				"Tags": [
					{
						"Key": "Name",
						"Value": "jenkins-multiaz"
					}
				]
			}
		},
		"InternetRoute": {
			"Type": "AWS::EC2::Route",
			"Properties": {
				"RouteTableId": {
					"Ref": "RouteTable"
				},
				"DestinationCidrBlock": "0.0.0.0/0",
				"GatewayId": {
					"Ref": "InternetGateway"
				}
			},
			"DependsOn": "GatewayToInternet"
		},
		"RouteTableAssociationA": {
			"Type": "AWS::EC2::SubnetRouteTableAssociation",
			"Properties": {
				"SubnetId": {
					"Ref": "SubnetA"
				},
				"RouteTableId": {
					"Ref": "RouteTable"
				}
			}
		},
		"RouteTableAssociationB": {
			"Type": "AWS::EC2::SubnetRouteTableAssociation",
			"Properties": {
				"SubnetId": {
					"Ref": "SubnetB"
				},
				"RouteTableId": {
					"Ref": "RouteTable"
				}
			}
		},
		"NetworkAcl": {
			"Type": "AWS::EC2::NetworkAcl",
			"Properties": {
				"VpcId": {
					"Ref": "VPC"
				},
				"Tags": [
					{
						"Key": "Name",
						"Value": "jenkins-multiaz"
					}
				]
			}
		},
		"NetworkAceSSH": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {
					"Ref": "NetworkAcl"
				},
				"RuleNumber": "10",
				"Protocol": "6",
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "0.0.0.0/0",
				"PortRange": {
					"From": "22",
					"To": "22"
				}
			}
		},
		"NetworkAceJenkinsHTTP": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {
					"Ref": "NetworkAcl"
				},
				"RuleNumber": "11",
				"Protocol": "6",
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "0.0.0.0/0",
				"PortRange": {
					"From": "8080",
					"To": "8080"
				}
			}
		},
		"NetworkAceNTP": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {
					"Ref": "NetworkAcl"
				},
				"RuleNumber": "20",
				"Protocol": "17",
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "0.0.0.0/0",
				"PortRange": {
					"From": "123",
					"To": "123"
				}
			}
		},
		"NetworkAceICMP": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {
					"Ref": "NetworkAcl"
				},
				"RuleNumber": "30",
				"Protocol": "1",
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "0.0.0.0/0",
				"Icmp": {
					"Code": "-1",
					"Type": "-1"
				}
			}
		},
		"NetworkAceHighPortsTCP": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {
					"Ref": "NetworkAcl"
				},
				"RuleNumber": "40",
				"Protocol": "6",
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "0.0.0.0/0",
				"PortRange": {
					"From": "1024",
					"To": "65535"
				}
			}
		},
		"NetworkAceHighPortsUDP": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {
					"Ref": "NetworkAcl"
				},
				"RuleNumber": "41",
				"Protocol": "17",
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "0.0.0.0/0",
				"PortRange": {
					"From": "1024",
					"To": "65535"
				}
			}
		},
		"NetworkAceEgress": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {
					"Ref": "NetworkAcl"
				},
				"RuleNumber": "10",
				"Protocol": "-1",
				"RuleAction": "allow",
				"Egress": "true",
				"CidrBlock": "0.0.0.0/0",
				"PortRange": {
					"From": "0",
					"To": "65535"
				}
			}
		},
		"NetworkAclAssociationA": {
			"Type": "AWS::EC2::SubnetNetworkAclAssociation",
			"Properties": {
				"SubnetId": {
					"Ref": "SubnetA"
				},
				"NetworkAclId": {
					"Ref": "NetworkAcl"
				}
			}
		},
		"NetworkAclAssociationB": {
			"Type": "AWS::EC2::SubnetNetworkAclAssociation",
			"Properties": {
				"SubnetId": {
					"Ref": "SubnetB"
				},
				"NetworkAclId": {
					"Ref": "NetworkAcl"
				}
			}
		},
		"SecurityGroupJenkins": {
			"Type": "AWS::EC2::SecurityGroup",
			"Properties": {
				"GroupDescription": "SecurityGroupforjenkins",
				"VpcId": {
					"Ref": "VPC"
				},
				"Tags": [
					{
						"Key": "Name",
						"Value": "jenkins-multiaz"
					}
				],
				"SecurityGroupIngress": [
					{
						"IpProtocol": "tcp",
						"FromPort": "22",
						"ToPort": "22",
						"CidrIp": "0.0.0.0/0"
					},
					{
						"IpProtocol": "tcp",
						"FromPort": "8080",
						"ToPort": "8080",
						"CidrIp": "0.0.0.0/0"
					},
					{
						"IpProtocol": "icmp",
						"FromPort": "-1",
						"ToPort": "-1",
						"CidrIp": "0.0.0.0/0"
					}
				]
			}
		},
		"IamRole": { # 创建一个用于 EC2 实例的 IAM 角色
			"Type": "AWS::IAM::Role",
			"Properties": {
				"AssumeRolePolicyDocument": {
					"Version": "2012-10-17",
					"Statement": [
						{
							"Effect": "Allow",
							"Principal": {"Service": ["ec2.amazonaws.com"]
						},
							"Action": ["sts:AssumeRole"]
						}
					]
				},
				"Path": "/",
				"Policies": [
					{
						"PolicyName": "root",
						"PolicyDocument": {
							"Version": "2012-10-17",
							"Statement": [
								{
									"Action": ["ec2:AssociateAddress"], # 使用此 IAM 角色的EC2 实例允许关联弹性IP
									"Resource": ["*"],
									"Effect": "Allow"
								}
							]
						}
					}
				]
			}
		},
		"IamInstanceProfile": {
			"Type": "AWS::IAM::InstanceProfile",
			"Properties": {
				"Path": "/",
				"Roles": [{"Ref": "IamRole"}]
			}
		},
		"ElasticIP": { # 为运行 jenkins 的虚拟服务器分配弹性 IP
			"Type": "AWS::EC2::EIP",
			"Properties": {
				"Domain": "vpc" # 为 VPC 创建弹性 IP
			},
			"DependsOn": "GatewayToInternet" 
		},
		"LaunchConfiguration": {
			"Type": "AWS::AutoScaling::LaunchConfiguration",
			"Properties": {
				"InstanceMonitoring": false,
				"IamInstanceProfile": {"Ref": "IamInstanceProfile"},
				"ImageId": {"Fn::FindInMap": ["EC2RegionMap", {"Ref": "AWS::Region"}, "AmazonLinuxAMIHVMEBSBacked64bit"]},
				"KeyName": {"Ref": "KeyName"},
				"SecurityGroups": [{"Ref": "SecurityGroupJenkins"}],
				"AssociatePublicIpAddress": true,
				"InstanceType": "t2.micro",
				"UserData": {
					"Fn::Base64": {
						"Fn::Join": [
							"",
							[
								"#!/bin/bash -ex\n",
								"aws configure set default.region ", {"Ref": "AWS::Region"},"\n", "INSTANCE_ID=`curl -s http://169.254.169.254/latest/meta-data/instance-id`\n" # 从实例元数据获得实例
								"INSTANCE_ID=`curl -s http://169.254.169.254/latest/meta-data/instance-id`\n",
								"aws ec2 associate-address --instance-id $INSTANCE_ID --allocation-id ", {"Fn::GetAtt": ["ElasticIP", "AllocationId"]}, "\n", # 给虚拟服务器关联弹性 IP
								"wget http://pkg.jenkins-ci.org/redhat/jenkins-1.616-1.1.noarch.rpm\n",
								"rpm --install jenkins-1.616-1.1.noarch.rpm\n",
								"sed -i -e 's/JENKINS_ARGS=\\\"\\\"/JENKINS_ARGS=\\\"--argumentsRealm.passwd.admin=", {"Ref": "JenkinsAdminPassword"}, " --argumentsRealm.roles.admin=admin\\\"/g' /etc/sysconfig/jenkins\n",
								"echo \"<?xml version='1.0' encoding='UTF-8'?><hudson><version>1.0</version><useSecurity>true</useSecurity><authorizationStrategy class=\\\"hudson.security.FullControlOnceLoggedInAuthorizationStrategy\\\"/><securityRealm class=\\\"hudson.security.LegacySecurityRealm\\\"/></hudson>\" > /var/lib/jenkins/config.xml\n",
								"service jenkins start\n"
							]
						]
					}
				}
			}
		},
		"AutoScalingGroup": {
			"Type": "AWS::AutoScaling::AutoScalingGroup",
			"Properties": {
				"LaunchConfigurationName": {"Ref": "LaunchConfiguration"},
				"Tags": [
					{
						"Key": "Name",
						"Value": "jenkins-elasticip",
						"PropagateAtLaunch": true
					}
				],
				"DesiredCapacity": 1,
				"MinSize": 1,
				"MaxSize": 1,
				"VPCZoneIdentifier": [{"Ref": "SubnetA"}, {"Ref": "SubnetB"}],
				"HealthCheckGracePeriod": 600,
				"HealthCheckType": "EC2"
			},
			"DependsOn": "GatewayToInternet"
		}
	},
	"Outputs": {
		"JenkinsURL": {
			"Description": "URL to access web interface of Jenkins server.",
			"Value": {"Fn::Join": ["", ["http://", {"Ref": "ElasticIP"}, ":8080"]]}
		},
		"User": {
			"Description": "Administrator user for Jenkins.",
			"Value": "admin"
		},
		"Password": {
			"Description": "Password for Jenkins administrator user.",
			"Value": {"Ref": "JenkinsAdminPassword"}
		}
	}
}
```





## 分析灾难恢复的需求

名词解释：

恢复时间目标（RTO）：让系统从失败中恢复的时间。

恢复点目标（RPO）：由失败导致的可接受数据丢失的时间点。



### 单个虚拟服务器的RTO和RPO的比较

| 虚拟服务器可用性方案         | RTO         | RPO                                                          | 可用性                                                 |
| ---------------------------- | ----------- | ------------------------------------------------------------ | ------------------------------------------------------ |
| 通过 CloudWatch 告警触发恢复 | 大约 10 min | 没有数据丢失                                                 | 从虚拟服务器失效中恢复，但并不能从整个可用区故障中恢复 |
| 通过自动扩展恢复             | 大约 10 min | 自最后一次快照的所有数据丢失。快照的时间花费在 30 min到 24 h | 从虚拟服务器失效中恢复，并且从整个可用区故障中恢复     |



如果想在可用区之外恢复并降低 RPO，应该尝试**实现无状态服务器**。使用存储服务，如 RDS、S3 和 DynamoDB 服务可以做到这一点。






