AWS 的解决方案：

* [弹性负载均衡器（ELB）](https://amazonaws-china.com/cn/elasticloadbalancing/?nc2=h_m1)，用于同步解耦。
  * Application Load Balancer；
  * 网络负载均衡器；
  * Classic Load Balancer；
* 消息队列服务（SQS），用于异步解耦。

## 一、利用负载均衡器实现同步解耦

实现将流量与 Web 服务器解耦。

![负载均衡器同步解耦服务器](http://upload-images.jianshu.io/upload_images/2648731-a85d7ef55cc3602f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 负载均衡器将传入的请求路由到两个后端的Web服务器之一；
* 负载均衡器可以通过 DNS 从互联网访问；
* Web 服务器只能通过负载均衡器访问；



CloudFormation 示例模版代码：

```json
{
	"AWSTemplateFormatVersion": "2010-09-09",
	"Description": "AWS in Action: chapter 12 (Load Balancer)",
	"Parameters": {
		"KeyName": {
			"Description": "Key Pair name",
			"Type": "AWS::EC2::KeyPair::KeyName",
			"Default": "mykey"
		},
		"NumberOfServers": {
			"Description": "Number of servers",
			"Type": "Number",
			"Default": "2",
			"MinValue": "2",
			"MaxValue": "4"
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
				"CidrBlock": "172.31.0.0/16",
				"EnableDnsHostnames": "true"
			}
		},
		"InternetGateway": {
			"Type": "AWS::EC2::InternetGateway",
			"Properties": {
			}
		},
		"VPCGatewayAttachment": {
			"Type": "AWS::EC2::VPCGatewayAttachment",
			"Properties": {
				"VpcId": {"Ref": "VPC"},
				"InternetGatewayId": {"Ref": "InternetGateway"}
			}
		},
		"Subnet": {
			"Type": "AWS::EC2::Subnet",
			"Properties": {
				"AvailabilityZone": {"Fn::Select": ["0", {"Fn::GetAZs": ""}]},
				"CidrBlock": "172.31.38.0/24",
				"VpcId": {"Ref": "VPC"}
			}
		},
		"RouteTable": {
			"Type": "AWS::EC2::RouteTable",
			"Properties": {
				"VpcId": {"Ref": "VPC"}
			}
		},
		"RouteTableAssociation": {
			"Type": "AWS::EC2::SubnetRouteTableAssociation",
			"Properties": {
				"SubnetId": {"Ref": "Subnet"},
				"RouteTableId": {"Ref": "RouteTable"}
			}
		},
		"RoutePublicNATToInternet": {
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
		"SubnetNetworkAclAssociation": {
			"Type": "AWS::EC2::SubnetNetworkAclAssociation",
			"Properties": {
				"SubnetId": {"Ref": "Subnet"},
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
		"LoadBalancerSecurityGroup": { # 负载均衡器安全组设置
			"Type": "AWS::EC2::SecurityGroup",
			"Properties": {
				"GroupDescription": "elb-sg",
				"VpcId": {"Ref": "VPC"},
				"SecurityGroupIngress": [{
					"CidrIp": "0.0.0.0/0",
					"FromPort": 80, # 负载均衡器只接受 80 端口的流量
					"IpProtocol": "tcp",
					"ToPort": 80
				}]
			}
		},
		"LoadBalancer": {
			"Type": "AWS::ElasticLoadBalancing::LoadBalancer",
			"Properties": {
				"Subnets": [{"Ref": "Subnet"}], # 将 ELB 附加到子网上
				"LoadBalancerName": "elb",
				"Listeners": [{ # 映射负载均衡器的端口到其后端的服务器端口
					"InstancePort": "80",
					"InstanceProtocol": "HTTP",
					"LoadBalancerPort": "80",
					"Protocol": "HTTP"
				}],
				"HealthCheck": { # ELB 健康状态检查
					"Target": "HTTP:80/index.html", # 检查对/index.html 返回的状态码是否为 200
					"Interval": "10", # 每 10s 进行一次检查
					"Timeout": "5", # 超时时间为 5s
					"UnhealthyThreshold": "2", # 检查连续失败2次，则认为是运行状况不好的
					"HealthyThreshold": "3", # 检查连续通过 3 次才能认为是运行状况良好的
				},
				"SecurityGroups": [{"Ref": "LoadBalancerSecurityGroup"}], # 关联安全组
				"Scheme": "internet-facing" # 设置为公开可访问
			},
			"DependsOn": "VPCGatewayAttachment"
		},
		"WebServerSecurityGroup": {
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
		"LaunchConfiguration": {
			"Type": "AWS::AutoScaling::LaunchConfiguration",
			"Metadata": {
				"AWS::CloudFormation::Init": {
					"config": {
						"packages": {
							"yum": {
								"httpd": []
							}
						},
						"files": {
							"/tmp/config": {
								"content": {"Fn::Join": ["", [
									"#!/bin/bash -ex\n",
									"PRIVATE_IP=`curl -s http://169.254.169.254/latest/meta-data/local-ipv4`\n",
									"echo \"<html><head><title>$PRIVATE_IP</title></head><body><h1>$PRIVATE_IP</h1></body></html>\" > index.html\n"
								]]},
								"mode": "000500",
								"owner": "root",
								"group": "root"
							}
						},
						"commands": {
							"01_config": {
								"command": "/tmp/config",
								"cwd": "/var/www/html"
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
		"AutoScalingGroup": { # 自动扩展
			"Type": "AWS::AutoScaling::AutoScalingGroup",
			"Properties": {
				"LoadBalancerNames": [{"Ref": "LoadBalancer"}], # 将自动扩展组连接到 ELB
				"LaunchConfigurationName": {"Ref": "LaunchConfiguration"},
				"MinSize": {"Ref": "NumberOfServers"}, # 最小尺寸
				"MaxSize": {"Ref": "NumberOfServers"}, # 最大尺寸
				"DesiredCapacity": {"Ref": "NumberOfServers"}, # 容量
				"VPCZoneIdentifier": [{"Ref": "Subnet"}]
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
			"Value": {"Fn::Join": ["", ["http://", {"Fn::GetAtt": ["LoadBalancer", "DNSName"]}]]},
			"Description": "Load Balancer URL"
		}
	}
}

```



### 更多使用场景

* 处理 TCP 流量；
* ELB 支持 SSL 证书。
* 记录日志，并将日志存储到 S3 上。
* 跨可用区的负载均衡。（**建议启用跨可用区负载均衡**，默认情况下是禁用的）。



## 二、利用消息队列实现异步解耦

[Amazon Simple Queue Service](https://amazonaws-china.com/cn/sqs/?nc2=h_m1)



> ### 适用于微服务、分布式系统和无服务器应用程序的完全托管的消息队列
>
> Amazon Simple Queue Service (SQS) 是一种完全托管的消息队列服务，可让您分离和扩展微服务、分布式系统和无服务器应用程序。SQS 消除了与管理和运营消息型中间件相关的复杂性和开销，并使开发人员能够专注于重要工作。借助 SQS，您可以在软件组件之间发送、存储和接收任何规模的消息，而不会丢失消息，并且无需其他服务即可保持可用。使用 AWS 控制台、命令行界面或您选择的 SDK 和三个简单的命令，在几分钟内即可开始使用 SQS。
>
> SQS 提供两种消息队列类型。标准队列提供最高吞吐量、最大努力排序和至少一次传送。SQS FIFO 队列旨在确保按照消息的发送顺序对消息进行严格一次处理。

