AWS 区块链模版号称可以在几分钟内完成创建并部署区块链网络。

使用 AWS 区块链模版可以搭建两种类型的区块链网络：

![AWS 区块链网络](https://upload-images.jianshu.io/upload_images/2648731-f29117e3b8d65843.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


具体搭建步骤可以参考[AWS Blockchain Templates 开发人员指南](https://docs.aws.amazon.com/zh_cn/blockchain-templates/latest/developerguide/what-are-blockchain-templates.html)，里面有关于搭建 Ethereum 的详细步骤，文档中的**「先决条件」**设置项是用于搭建 Ethereum 网络的，对 Fabric 网络并不适用，所以这里说一下搭建超级账本的 Fabric。

![使用模版创建的 Hyperledger Fabric 网络](http://upload-images.jianshu.io/upload_images/2648731-d20383598af3d99b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



在使用模版快速创建堆栈前，务必要提前设置好的相关内容：

![官方说明](http://upload-images.jianshu.io/upload_images/2648731-4b98159a9e25c285.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

说明：

* 一个有权使用相关服务的 IAM 账户（[IAM账户](https://docs.aws.amazon.com/zh_cn/IAM/latest/UserGuide/id_roles.html)，可以理解为在 AWS 根账户下设置子账户，比如说，你可以给你的同事设置一个子账户（子账户有独立的登录链接、用户名、密码），然后为这个子账户开通（登录AWS控制台的）相关操作权限，然后你的同事就可以用这个子账户登录AWS控制台了。

* 设置一个密钥对（公钥+私钥），用来通过 SSH 方式登录 EC2 实例的。

* **设置一个 IAM 权限策略文件。**

* **创建一个具有一个公有子网的 VPC 网络。**

* 设置一个安全组（安全组充当防火墙功能），入站流量开通 22 端口、80 端口。


以上的5个前提条件设置正确了，我们就可以用区块链模版创建 Fabric 网络了，下面具体说一下画红框的比较难的两个配置：



### 设置 IAM 角色并附加权限策略

设置步骤：

1. AWS控制台——服务——IAM——角色——创建角色，设置勾选如下：

![image](http://upload-images.jianshu.io/upload_images/2648731-4458136b1676541f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



2. 创建策略，Tab页面选 JSON，把下面👇的 JSON 内容复制进去：

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ecr:GetAuthorizationToken",
                "ecr:BatchCheckLayerAvailability",
                "ecr:GetDownloadUrlForLayer",
                "ecr:GetRepositoryPolicy",
                "ecr:DescribeRepositories",
                "ecr:ListImages",
                "ecr:DescribeImages",
                "ecr:BatchGetImage",
"s3:Get*",
                "s3:List*"
            ],
            "Resource": "*"
        }
] }
```

![image](http://upload-images.jianshu.io/upload_images/2648731-1fac8fead2d47f47.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击右下角的**「Review Policy」**,设置这个权限策略文件的名称（myFabricPolicy）和描述（...），最后点击**「Create Policy」**：

![image](http://upload-images.jianshu.io/upload_images/2648731-62542f422827dc6a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



3. 回到我们刚才创建角色的页面，添加刚刚创建的策略 （myFabricPolicy）给这个角色，找的时候可以点击**「删选策略」**——**「客户托管」**，☑️勾选完之后点击下一步审核：

   ![image](http://upload-images.jianshu.io/upload_images/2648731-69be4c6b4160ff76.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4. 审核页面，给 IAM 角色设置一个角色名称（myFabricIAM）,点击**「创建角色」**：

   ![image](http://upload-images.jianshu.io/upload_images/2648731-7078bd8e14887fb7.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5. 创建完之后，可以点击我们刚刚创建的 IAM 角色查看详情，记住这里的**实例配置文件 ARN**，在使用区块链模版创建 Fabric 网络时会使用到的。

   ![image](http://upload-images.jianshu.io/upload_images/2648731-a3a42a9173653890.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 设置 VPC

设置如下：

AWS控制台——服务——VPC——在VPC控制面板中点击蓝色的按钮「**Launch VPC Wizard**」,选择带有单个公有子网的 VPC：

![image](http://upload-images.jianshu.io/upload_images/2648731-712ac83bec196ce3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

设置 VPC 名称、子网名称，其他值为默认值。

![image](http://upload-images.jianshu.io/upload_images/2648731-4d02528f2362584e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 使用 AWS 区块链模版创建 Fabric 网络

在 [AWS Blockchain Templates 开发人员指南](https://docs.aws.amazon.com/zh_cn/blockchain-templates/latest/developerguide/what-are-blockchain-templates.html) 的Hyperledger Fabric 部分点击启动链接：

![image](http://upload-images.jianshu.io/upload_images/2648731-2515a59315b48bc5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

设置参考如下：

![image](http://upload-images.jianshu.io/upload_images/2648731-fc380a65eda1f04a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



创建之后，喝一杯咖啡☕️等一会儿...

![image](http://upload-images.jianshu.io/upload_images/2648731-41534a6c1606d4ef.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

等状态显示为「**CREATE_COMPLETE**」就OKK了。🎉🎉🎉



### 参考

* [hyperledger/fabric](https://github.com/hyperledger/fabric)
* [Hyperledger 源码分析之 Fabric](https://github.com/yeasy/hyperledger_code_fabric)
* [AWS Blockchain Templates 开发人员指南](https://docs.aws.amazon.com/zh_cn/blockchain-templates/latest/developerguide/what-are-blockchain-templates.html)
* [Tutorial: Get started with blockchain using the AWS Hyperledger Fabric template](https://lists.hyperledger.org/g/fabric/topic/tutorial_get_started_with/21382525?p=,,,20,0,0,0::recentpostdate%2Fsticky,,,20,2,20,21382525)
* [How to create a Service Fabric standalone cluster with AWS EC2 instances](https://blogs.msdn.microsoft.com/azureservicefabric/2017/05/18/tutorial-how-to-create-a-service-fabric-standalone-cluster-with-aws-ec2-instances/)

