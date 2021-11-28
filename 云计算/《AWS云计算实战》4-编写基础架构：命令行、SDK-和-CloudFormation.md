## 一、基础架构即代码

思想：使用高级编程语言来控制 IT 系统。

「基础架构即代码」描述了编写程序来创建与修改基础架构（包括虚拟服务器、网络、存储等）的方法。

### 自动化和 DevOps 运作

DevOps（Development operations）的目标：快速发布开发好的软件，并且没有损失质量。

自动化部署、自动化构建、自动化测试（集成测试）、产品。

自动化基础架构的3种方式：

1. [命令行接口（CLI）](https://docs.aws.amazon.com/zh_cn/cli/latest/userguide/cli-chap-welcome.html)；
2. [开发工具套件（SDK）](https://amazonaws-china.com/cn/getting-started/tools-sdks/?hp=tile&so-exp=below)；
3. [CloudFormation](https://docs.aws.amazon.com/zh_cn/AWSCloudFormation/latest/UserGuide/Welcome.html)；

### 基础架构语言：JIML

描述基础架构：JSON基础架构标记语言（JSON Infrastructure Markup Language，JIML）。

转换步骤： JSON 输入 ➡️ JIML 解释器 ➡️ AWS API 调用。



## 二、使用命令行接口

### 安装 AWS 命令行接口（CLI）

AWS 命令行界面 (CLI) 是用于管理 AWS 服务的统一工具。只通过一个工具进行下载和配置，您可以使用命令行控制多个 AWS 服务并利用脚本来自动执行这些服务。

#### 参考：

- [官方文档：AWS 命令行界面](https://amazonaws-china.com/cn/cli/?hp=tile&so-exp=below)

- [官方文档：AWS Command Line Interface](https://docs.aws.amazon.com/zh_cn/cli/latest/userguide/cli-chap-welcome.html)



在 Mac 上安装 Python 3：

```shell
--------------------------------------------
# 查看 Python 版本，系统自带的 Python 版本是 2.x
$ python --version
Python 2.7.15

# 使用 Homebrew 安装 Python3
$ brew install python3
...
==> python
Python has been installed as
  /usr/local/bin/python3  # Python3 的安装目录
...

# 查看 python3 版本
$ python3 --version 
Python 3.6.4

--------------------------------------------
# 查看 pip 版本
$  pip --version # 默认 python 2.7 的 pip 版本
pip 18.0 from /usr/local/lib/python2.7/site-packages/pip (python 2.7)

$  pip3 --version # python 3.6 的 pip 版本
pip 9.0.1 from /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages (python 3.6)

# 升级 pip3 版本
$ pip3 install --upgrade pip
$ pip3 --version
pip 18.1 from /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/pip (python 3.6)

--------------------------------------------
# 安装 AWS CLI
$ sudo pip3 install awscli

# 验证 AWS CLI 的安装
$ aws --version
aws-cli/1.16.81 Python/3.6.4 Darwin/18.2.0 botocore/1.12.71

# 升级 AWS CLI
pip3 install awscli --upgrade --user
```

参考：[知乎：Mac OS X 上如何切换默认的 Python 版本？](https://www.zhihu.com/question/30941329/answer/51489517)



### 配置 CLI

#### 创建 IAM 用户

示例：

| IAM 用户信息 | 值                                                   |
| ------------ | ---------------------------------------------------- |
| 登录URL      | <https://812677773757.signin.aws.amazon.com/console> |
| IAM 用户名   | mycli                                                |
| 访问类型     | 编程访问                                             |
| 权限         | AdministratorAccess                                  |
| 访问密钥 ID  | AKIAJMJA54KW6HSPM2XA                                 |
| 私有访问密钥 | Nyd1u5NBglomjj5X+Mu7L9cM0ZiBKXv5swj2TJ6D             |

#### 使用 `aws configure` 命令设置 AWS CLI：

```shell
# default 的单个配置文件
$ aws configure 
AWS Access Key ID [None]: AKIAJMJA54KW6HSPM2XA # AWS 访问密钥 ID
AWS Secret Access Key [None]: Nyd1u5NBglomjj5X+Mu7L9cM0ZiBKXv5swj2TJ6D # AWS 私有访问密钥
Default region name [None]: us-east-1 # 默认区域名称，弗吉尼亚北部数据中心（us-east-1）
Default output format [None]: json # 默认输出格式

# 使用 --profile 选项指定配置文件的名称
$ aws configure --profile user2
AWS Access Key ID [None]: AKIAI44QH8DHBEXAMPLE
AWS Secret Access Key [None]: je7MtGbClwBF/2Zp9Utk/h3yCo8nvbEXAMPLEKEY
Default region name [None]: us-east-1
Default output format [None]: json

# 省略 --profile 选项，这样会使用 default 配置文件中存储的设置：
$ aws s3 ls
# 指定 --profile profilename 来使用该名称下存储的设置：
$ aws s3 ls --profile myuser
```

#### 获取所有可用的区域列表：

```shell
$ aws ec2 describe-regions
...

$ aws ec2 describe-regions --output table
----------------------------------------------------------
|                     DescribeRegions                    |
+--------------------------------------------------------+
||                        Regions                       ||
|+-----------------------------------+------------------+|
||             Endpoint              |   RegionName     ||
|+-----------------------------------+------------------+|
||  ec2.ap-south-1.amazonaws.com     |  ap-south-1      ||
||  ec2.eu-west-3.amazonaws.com      |  eu-west-3       ||
||  ec2.eu-north-1.amazonaws.com     |  eu-north-1      ||
||  ec2.eu-west-2.amazonaws.com      |  eu-west-2       ||
||  ec2.eu-west-1.amazonaws.com      |  eu-west-1       ||
||  ec2.ap-northeast-2.amazonaws.com |  ap-northeast-2  ||
||  ec2.ap-northeast-1.amazonaws.com |  ap-northeast-1  ||
||  ec2.sa-east-1.amazonaws.com      |  sa-east-1       ||
||  ec2.ca-central-1.amazonaws.com   |  ca-central-1    ||
||  ec2.ap-southeast-1.amazonaws.com |  ap-southeast-1  ||
||  ec2.ap-southeast-2.amazonaws.com |  ap-southeast-2  ||
||  ec2.eu-central-1.amazonaws.com   |  eu-central-1    ||
||  ec2.us-east-1.amazonaws.com      |  us-east-1       ||
||  ec2.us-east-2.amazonaws.com      |  us-east-2       ||
||  ec2.us-west-1.amazonaws.com      |  us-west-1       ||
||  ec2.us-west-2.amazonaws.com      |  us-west-2       ||
|+-----------------------------------+------------------+|
```

#### 获得所有类型为 t2.micro 的 EC2 实例的列表：

```shell
aws ec2 describe-instances --filters "Name=instance-type,Values=t2.micro"
{
    "Reservations": []
}
```

格式：`aws <service> <action> [--key value]`：

#### help 关键字

* `aws help`——显示所有可用的服务；
* `aws <service> help`——显示某一服务所有可用的操作；
* `aws <service> <action> help`——显示特定服务操作可用的所有选项；



#### 实例脚本：server.sh

```shell
#!/bin/bash -e # 1⃣️ 当命令出错时终止
# You need to install the AWS Command Line Interface from http://aws.amazon.com/cli/
# 2⃣️ 获取 Amazon Linux AMI 的 ID
AMIID=$(aws ec2 describe-images --filters "Name=description, Values=Amazon Linux AMI 2015.03.? x86_64 HVM GP2" --query "Images[0].ImageId" --output text)
# 3⃣️ 获取默认 VPC 的 ID
VPCID=$(aws ec2 describe-vpcs --filter "Name=isDefault, Values=true" --query "Vpcs[0].VpcId" --output text)
# 4⃣️ 获取默认子网的 ID
SUBNETID=$(aws ec2 describe-subnets --filters "Name=vpc-id, Values=$VPCID" --query "Subnets[0].SubnetId" --output text)
# 5⃣️ 创建安全组
SGID=$(aws ec2 create-security-group --group-name mysecuritygroup --description "My security group" --vpc-id $VPCID --output text)
# 6⃣️ 允许入站 SH 连接
aws ec2 authorize-security-group-ingress --group-id $SGID --protocol tcp --port 22 --cidr 0.0.0.0/0
# 7⃣️ 创建并启动服务器
INSTANCEID=$(aws ec2 run-instances --image-id $AMIID --key-name mykey --instance-type t2.micro --security-group-ids $SGID --subnet-id $SUBNETID --query "Instances[0].InstanceId" --output text)
echo "waiting for $INSTANCEID ..."
# 8⃣️ 等到服务器启动
aws ec2 wait instance-running --instance-ids $INSTANCEID
# 9⃣️ 获取服务器的公有域名
PUBLICNAME=$(aws ec2 describe-instances --instance-ids $INSTANCEID --query "Reservations[0].Instances[0].PublicDnsName" --output text)
echo "$INSTANCEID is accepting SSH connections under $PUBLICNAME"
echo "ssh -i mykey.pem ec2-user@$PUBLICNAME"
read -p "Press [Enter] key to terminate $INSTANCEID ..."
# 🔟 终止服务器
aws ec2 terminate-instances --instance-ids $INSTANCEID
echo "terminating $INSTANCEID ..."
# 1⃣️1⃣️ 等到服务器终止
aws ec2 wait instance-terminated --instance-ids $INSTANCEID
# 1⃣️2⃣️ 删除安全组
aws ec2 delete-security-group --group-id $SGID
echo "done."
```



#### 为什么要写脚本？

为什么要写脚本，而不是使用图形化的AWS管理控制台？**脚本可以复用，并且在长时间操作时节省时间。**用户能够使用自己之前的项目中已经可用的模块来快速创建新的架构。通过使自己的基础架构创建自动化，用户也能够加强自己的部署流水线的自动化。
另一个好处是，**脚本将是能想象的最准确的文档（甚至计算机能理解它）**。如果你想在周一重复上周五自己做的事，脚本就是无价之宝。如果你病了，而你的同事需要处理好你的任务，他们会感激你留下的脚本。



## 三、使用 SDK 编程

参考：[官方文档：使用软件开发工具包和工具开始进行构建](https://amazonaws-china.com/cn/getting-started/tools-sdks/?hp=tile&so-exp=below)



## 四、使用蓝图来启动一台虚拟服务器

参考：[官方文档：AWS CloudFormation](https://docs.aws.amazon.com/zh_cn/AWSCloudFormation/latest/UserGuide/Welcome.html) ⭐️⭐️⭐️

参考：[什么是 AWS CloudFormation Designer？](https://docs.aws.amazon.com/zh_cn/AWSCloudFormation/latest/UserGuide/working-with-templates-cfn-designer.html)



早些时候，我们谈到 JIML 来引入基础架构即代码的概念。幸运的是，AWS 已经提供了一个比 JIML更好的工具: **AWS CloudFormation**。**CloudFormation 基于模板来创建基础架构**，也就是我们称作蓝图（模版）的东西。

* 模版使用 JSON 描述了用户的基础架构的最终状态，CloudFormation 能对其进行解析并自动实现它。
* 描述方法：告诉 CloudFormation 需要的基础架构以及组件之间的连接。
* 用户不需要告诉 CloudFormation 应用的创建过程和操作的执行顺序，最重要的是：**组件之间的依赖。**



### 使用 CloudFormation 的好处

* 用统一的方式描述基础架构；
* 它能处理依赖关系；
* 可复制；
* 可自定义；
* 可测试；
* 可更新；（也就是说，更新实例类型时，无需先停止实例-更新-重启balabala...只要更新模版就行了！）
* 最小化人为的误操作；
* 把基础架构文档化；
* 免费；



### CloudFormation 模版解析

![CloudFormation 模版结构](http://upload-images.jianshu.io/upload_images/2648731-77caff3f39f47567.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

CloudFormation 让你可以使用模版来创建和启动资源：

```json
{
	"AWSTemplateFormatVersion": "2010-09-09",    ## 1⃣️ 唯一合法版本
	"Description": "CloudFormation Description", ## 2⃣️ 描述模版的用途
	"Parameters": {
		[...] ## 3⃣️ 定义参数，如：域名、客户ID和数据库密码。
	},
	"Resources": {
		[...] ## 4⃣️ 定义资源，如：虚拟服务器、负载均衡器、弹性IP地址。
	},
	"Outputs": {
		[...] ## 5⃣️ 定义输出。从模版返回一些信息。
	}
}
```

附：[AWS 资源和属性类型参考](https://docs.aws.amazon.com/zh_cn/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html)



#### 参数

```json
	"Parameters": {
		"KeyName": { ## 参数名
			"Description": "Key Pair name",
			"Type": "AWS::EC2::KeyPair::KeyName", ## 参数类型
			"Default": "mykey"
		},
```

#### 资源

```json
	"Resources": {
		"Server": { ## 资源名
			"Type": "AWS::EC2::Instance", ## 资源类型
			"Properties": {
				"ImageId": {"Fn::FindInMap": ["EC2RegionMap", {"Ref": "AWS::Region"}, "AmazonLinuxAMIHVMEBSBacked64bit"]}, ## 一些硬编码设置
				"InstanceType": {"Ref": "InstanceType"},
				"KeyName": {"Ref": "KeyName"},
				"SecurityGroupIds": [{"Ref": "SecurityGroup"}],
				"SubnetId": {"Ref": "Subnet"}
			}
		}
	},
```

#### 输出

```json
	"Outputs": {
		"PublicName": { ## 输出的名字
			"Value": {"Fn::GetAtt": ["Server", "PublicDnsName"]}, ## 输出的值：EC2 服务器的公有DNS名
			"Description": "Public name (connect via SSH as user ec2-user)"
		}
	}
```



#### 完整的示例模版

如果从模板创建一个基础架构，则 CloudFormation 称之为**堆栈**。可以认为模板对应堆栈，就像是类对应对象。模板只存在一次，而许多堆栈可以从同一个模板中被创建。

```json
{
	"AWSTemplateFormatVersion": "2010-09-09",
	"Description": "AWS in Action: chapter 4",
	"Parameters": {
		"KeyName": {
			"Description": "Key Pair name",
			"Type": "AWS::EC2::KeyPair::KeyName",
			"Default": "mykey"
		},
		"VPC": {
			"Description": "Just select the one and only default VPC",
			"Type": "AWS::EC2::VPC::Id"
		},
		"Subnet": {
			"Description": "Just select one of the available subnets",
			"Type": "AWS::EC2::Subnet::Id"
		},
		"InstanceType": {
			"Description": "Select one of the possible instance types",
			"Type": "String",
			"Default": "t2.micro",
			"AllowedValues": ["t2.micro", "t2.small", "t2.medium"]
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
		"SecurityGroup": {
			"Type": "AWS::EC2::SecurityGroup",
			"Properties": {
				"GroupDescription": "My security group",
				"VpcId": {"Ref": "VPC"},
				"SecurityGroupIngress": [{
					"CidrIp": "0.0.0.0/0",
					"FromPort": 22,
					"IpProtocol": "tcp",
					"ToPort": 22
				}]
			}
		},
		"Server": {
			"Type": "AWS::EC2::Instance",
			"Properties": {
				"ImageId": {"Fn::FindInMap": ["EC2RegionMap", {"Ref": "AWS::Region"}, "AmazonLinuxAMIHVMEBSBacked64bit"]},
				"InstanceType": {"Ref": "InstanceType"},
				"KeyName": {"Ref": "KeyName"},
				"SecurityGroupIds": [{"Ref": "SecurityGroup"}],
				"SubnetId": {"Ref": "Subnet"}
			}
		}
	},
	"Outputs": {
		"PublicName": {
			"Value": {"Fn::GetAtt": ["Server", "PublicDnsName"]},
			"Description": "Public name (connect via SSH as user ec2-user)"
		}
	}
}

```



#### CloudFormation 替代方案

* Terraform
* Ansible
* ...

