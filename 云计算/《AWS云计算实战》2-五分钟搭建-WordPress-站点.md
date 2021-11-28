本文内容：使用 [AWS CloudFormation](https://docs.aws.amazon.com/zh_cn/AWSCloudFormation/latest/UserGuide/Welcome.html) 实现基础设施的自动化部署。

## WordPress 基础设施架构

需要使用4个不同的 AWS 服务实现 WordPress 站点项目：

* 弹性负载均衡（Elastic Load Balancing，ELB）——AWS 提供的弹性负载均衡服务。负载均衡器用于将流量分发到 Web 服务器。
* 弹性计算云（Elastic Computer Cloud，EC2）——EC2 服务提供的虚拟服务器。
  
  > 如果 EC2 的 CPU 利用率高于 80%，应该添加第三台服务器，以防止 Web 页面加载的时间过长。
* 适用于 MySQL 的关系型数据库服务（Relational Database Service for MySQL）——AWS 的关系数据库服务（RDS）提供了对 MySQL 的支持。
  
  > 使用 RDS 的好处是，AWS 平台会自动完成数据库备份。
* 安全组（Security group）——安全组类似于防火墙。

![wordpress 基础设施架构](http://upload-images.jianshu.io/upload_images/2648731-ca98c25c7ad36cc3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<!-- more -->

## 准备工作：创建并设置密钥对

1. 创建密钥对并下载一个名为 **mykey.pem** 的私钥文件。
2. Mac 系统下，`shift+cmd+.`显示隐藏文件，将 **mykey.pem** 文件放到 **.ssh** 目录下，设置密钥文件访问权限仅自己可见：

```bash
chmod 400 mykey.pem
```

## CloudFormation（堆栈）

> AWS CloudFormation 允许您快速和轻松地部署自己的基础设施资源和应用程序在 AWS 上。
>
> AWS CloudFormation 是一项服务，可帮助您对 Amazon Web Services 资源进行建模和设置，以便能花较少的时间管理这些资源，而将更多的时间花在运行于 AWS 中的应用程序上。您创建一个描述您所需的所有 AWS 资源（如 Amazon EC2 实例或 Amazon RDS 数据库实例）的模板，并且 AWS CloudFormation 将负责为您设置和配置这些资源。


指定的 Amazon S3 模板 URL：<https://s3.amazonaws.com/awsinaction/chapter2/template.json>


简单4步就可以创建一个 **CloudFormation**。

![堆栈实例](http://upload-images.jianshu.io/upload_images/2648731-cbde28ca1d76658f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用 **CloudFormation（堆栈）**的好处是：**你可以随时按需自动化创建或删除基础设施。**


## 估算成本

使用 [AWS 简单月度计算器](http://calculator.s3.amazonaws.com/index.html?key=cloudformation/c08f47ad-184c-4a69-90a5-1238619ff57e&lng=zh_CN#) 分析博客站点基础设施的成本。

![Jietu20190102-134656](http://upload-images.jianshu.io/upload_images/2648731-eea6bfaa232c0ffd.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## 📎 配置文件

```json
{
	"AWSTemplateFormatVersion": "2010-09-09",
	"Description": "AWS in Action: chapter 2",
	"Parameters": {
		"KeyName": { // 密钥对
			"Description": "Key Pair name",
			"Type": "AWS::EC2::KeyPair::KeyName",
			"Default": "mykey"
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
		"VPC": { // 虚拟网络
			"Type": "AWS::EC2::VPC",
			"Properties": {
				"CidrBlock": "172.31.0.0/16",
				"EnableDnsHostnames": "true"
			}
		},
		"InternetGateway": { // 网关
			"Type": "AWS::EC2::InternetGateway",
			"Properties": {
			}
		},
		"VPCGatewayAttachment": { // VPC 网关关联设置
			"Type": "AWS::EC2::VPCGatewayAttachment",
			"Properties": {
				"VpcId": {"Ref": "VPC"},
				"InternetGatewayId": {"Ref": "InternetGateway"}
			}
		},
		"SubnetA": { // 虚拟网络子网A
			"Type": "AWS::EC2::Subnet",
			"Properties": {
				"AvailabilityZone": {"Fn::Select": ["0", {"Fn::GetAZs": ""}]},
				"CidrBlock": "172.31.38.0/24",
				"VpcId": {"Ref": "VPC"}
			}
		},
		"SubnetB": { // 虚拟网络子网B
			"Type": "AWS::EC2::Subnet",
			"Properties": {
				"AvailabilityZone": {"Fn::Select": ["1", {"Fn::GetAZs": ""}]},
				"CidrBlock": "172.31.37.0/24",
				"VpcId": {"Ref": "VPC"}
			}
		},
		"RouteTable": { // 路由表
			"Type": "AWS::EC2::RouteTable",
			"Properties": {
				"VpcId": {"Ref": "VPC"}
			}
		},
		"RouteTableAssociationA": { // 路由表关联到子网A
			"Type": "AWS::EC2::SubnetRouteTableAssociation",
			"Properties": {
				"SubnetId": {"Ref": "SubnetA"},
				"RouteTableId": {"Ref": "RouteTable"}
			}
		},
		"RouteTableAssociationB": { // 路由表关联到子网B
			"Type": "AWS::EC2::SubnetRouteTableAssociation",
			"Properties": {
				"SubnetId": {"Ref": "SubnetB"},
				"RouteTableId": {"Ref": "RouteTable"}
			}
		},
		"RoutePublicNATToInternet": { // NAT 路由设置
			"Type": "AWS::EC2::Route",
			"Properties": {
				"RouteTableId": {"Ref": "RouteTable"},
				"DestinationCidrBlock": "0.0.0.0/0",
				"GatewayId": {"Ref": "InternetGateway"}
			},
			"DependsOn": "VPCGatewayAttachment"
		},
		"NetworkAcl": {
			"Type": "AWS::EC2::NetworkAcl",
			"Properties": {
				"VpcId": {"Ref": "VPC"}
			}
		},
		"SubnetNetworkAclAssociationA": {
			"Type": "AWS::EC2::SubnetNetworkAclAssociation",
			"Properties": {
				"SubnetId": {"Ref": "SubnetA"},
				"NetworkAclId": {"Ref": "NetworkAcl"}
			}
		},
		"SubnetNetworkAclAssociationB": {
			"Type": "AWS::EC2::SubnetNetworkAclAssociation",
			"Properties": {
				"SubnetId": {"Ref": "SubnetB"},
				"NetworkAclId": {"Ref": "NetworkAcl"}
			}
		},
		"NetworkAclEntryIngress": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAcl"},
				"RuleNumber": "100",
				"Protocol": "-1",
				"RuleAction": "allow",
				"Egress": "false",
				"CidrBlock": "0.0.0.0/0"
			}
		},
		"NetworkAclEntryEgress": {
			"Type": "AWS::EC2::NetworkAclEntry",
			"Properties": {
				"NetworkAclId": {"Ref": "NetworkAcl"},
				"RuleNumber": "100",
				"Protocol": "-1",
				"RuleAction": "allow",
				"Egress": "true",
				"CidrBlock": "0.0.0.0/0"
			}
		},
		"LoadBalancer": { // 负载均衡
			"Type": "AWS::ElasticLoadBalancing::LoadBalancer",
			"Properties": {
				"Subnets": [{"Ref": "SubnetA"}, {"Ref": "SubnetB"}],
				"LoadBalancerName": "awsinaction-elb",
				"Listeners": [{ // 监听 80 端口
					"InstancePort": "80",
					"InstanceProtocol": "HTTP",
					"LoadBalancerPort": "80",
					"Protocol": "HTTP"
				}],
				"HealthCheck": { // 端口状态检测设置
					"HealthyThreshold": "2",
					"Interval": "5",
					"Target": "TCP:80",
					"Timeout": "3",
					"UnhealthyThreshold": "2"
				},
				"SecurityGroups": [{"Ref": "LoadBalancerSecurityGroup"}], // 安全组设置
				"Scheme": "internet-facing"
			},
			"DependsOn": "VPCGatewayAttachment"
		},
		"LoadBalancerSecurityGroup": { // 负载均衡安全组设置
			"Type": "AWS::EC2::SecurityGroup",
			"Properties": {
				"GroupDescription": "awsinaction-elb-sg",
				"VpcId": {"Ref": "VPC"},
				"SecurityGroupIngress": [{
					"CidrIp": "0.0.0.0/0",
					"FromPort": 80,
					"IpProtocol": "tcp",
					"ToPort": 80
				}]
			}
		},
		"WebServerSecurityGroup": { // Web 服务安全组，打开80、22
			"Type": "AWS::EC2::SecurityGroup",
			"Properties": {
				"GroupDescription": "awsinaction-sg",
				"VpcId": {"Ref": "VPC"},
				"SecurityGroupIngress": [{
					"CidrIp": "0.0.0.0/0",
					"FromPort": 22,
					"IpProtocol": "tcp",
					"ToPort": 22
				}, {
					"FromPort": 80,
					"IpProtocol": "tcp",
					"SourceSecurityGroupId": {"Ref": "LoadBalancerSecurityGroup"},
					"ToPort": 80
				}]
			}
		},
		"DatabaseSecurityGroup": { // 数据库安全组
			"Type": "AWS::EC2::SecurityGroup",
			"Properties": {
				"GroupDescription": "awsinaction-db-sg",
				"VpcId": {"Ref": "VPC"},
				"SecurityGroupIngress": [{
					"IpProtocol": "tcp",
					"FromPort": "3306",
					"ToPort": "3306",
					"SourceSecurityGroupId": {"Ref": "WebServerSecurityGroup"}
				}]
			}
		},
		"Database": { // 数据库设置
			"Type": "AWS::RDS::DBInstance",
			"DeletionPolicy": "Delete",
			"Properties": {
				"AllocatedStorage": "5",
				"BackupRetentionPeriod": "0",
				"DBInstanceClass": "db.t2.micro",
				"DBInstanceIdentifier": "awsinaction-db",
				"DBName": "wordpress",
				"Engine": "MySQL",
				"MasterUsername": "wordpress",
				"MasterUserPassword": "wordpress",
				"VPCSecurityGroups": [{"Fn::GetAtt": ["DatabaseSecurityGroup", "GroupId"]}],
				"DBSubnetGroupName": {"Ref": "DBSubnetGroup"}
			},
			"DependsOn": "VPCGatewayAttachment"
		},
		"DBSubnetGroup" : {
			"Type" : "AWS::RDS::DBSubnetGroup",
			"Properties" : {
				"DBSubnetGroupDescription" : "DB subnet group",
				"SubnetIds": [{"Ref": "SubnetA"}, {"Ref": "SubnetB"}]
			}
		},
		"LaunchConfiguration": { // 启动配置
			"Type": "AWS::AutoScaling::LaunchConfiguration",
			"Metadata": {
				"AWS::CloudFormation::Init": {
					"config": {
						"packages": {
							"yum": {
								"php": [],
								"php-mysql": [],
								"mysql": [],
								"httpd": []
							}
						},
						"sources": {
							"/var/www/html": "https://wordpress.org/wordpress-4.2.4.tar.gz"
						},
						"files": {
							"/tmp/config": { // 虚拟机启动初始化 shell 脚本
								"content": {"Fn::Join": ["", [
									"#!/bin/bash -ex\n",
									"cp /var/www/html/wordpress/wp-config-sample.php /var/www/html/wordpress/wp-config.php\n",
									"sed -i \"s/'database_name_here'/'wordpress'/g\" wp-config.php\n",
									"sed -i \"s/'username_here'/'wordpress'/g\" wp-config.php\n",
									"sed -i \"s/'password_here'/'wordpress'/g\" wp-config.php\n",
									"sed -i \"s/'localhost'/'", {"Fn::GetAtt": ["Database", "Endpoint.Address"]}, "'/g\" wp-config.php\n",
									"chmod -R 777 wp-content/ \n"
								]]},
								"mode": "000500",
								"owner": "root",
								"group": "root"
							}
						},
						"commands": {
							"01_config": {
								"command": "/tmp/config",
								"cwd": "/var/www/html/wordpress"
							}
						},
						"services": {
							"sysvinit": {
								"httpd": {
									"enabled": "true",
									"ensureRunning": "true"
								}
							}
						}
					}
				}
			},
			"Properties": {
				"EbsOptimized": false,
				"ImageId": {"Fn::FindInMap": ["EC2RegionMap", {"Ref": "AWS::Region"}, "AmazonLinuxAMIHVMEBSBacked64bit"]},
				"InstanceType": "t2.micro",
				"SecurityGroups": [{"Ref": "WebServerSecurityGroup"}],
				"KeyName": {"Ref": "KeyName"},
				"AssociatePublicIpAddress": true,
				"UserData": {"Fn::Base64": {"Fn::Join": ["", [
					"#!/bin/bash -ex\n",
					"yum update -y aws-cfn-bootstrap\n",
					"/opt/aws/bin/cfn-init -v --stack ", {"Ref": "AWS::StackName"}, " --resource LaunchConfiguration --region ", {"Ref": "AWS::Region"}, "\n",
					"/opt/aws/bin/cfn-signal -e $? --stack ", {"Ref": "AWS::StackName"}, " --resource AutoScalingGroup --region ", {"Ref": "AWS::Region"}, "\n"
				]]}}
			}
		},
		"AutoScalingGroup": {
			"Type": "AWS::AutoScaling::AutoScalingGroup",
			"Properties": {
				"LoadBalancerNames": [{"Ref": "LoadBalancer"}],
				"LaunchConfigurationName": {"Ref": "LaunchConfiguration"},
				"MinSize": "2",
				"MaxSize": "2",
				"DesiredCapacity": "2",
				"VPCZoneIdentifier": [{"Ref": "SubnetA"}, {"Ref": "SubnetB"}]
			},
			"CreationPolicy": {
				"ResourceSignal": {
					"Timeout": "PT10M"
				}
			},
			"DependsOn": "VPCGatewayAttachment"
		}
	},
	"Outputs": {
		"URL": {
			"Value": {"Fn::Join": ["", ["http://", {"Fn::GetAtt": ["LoadBalancer", "DNSName"]}, "/wordpress"]]},
			"Description": "Wordpress URL"
		}
	}
}
```


