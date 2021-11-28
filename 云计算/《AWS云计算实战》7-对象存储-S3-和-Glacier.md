| AWS 对象存储服务                                             | 描述                   |
| ------------------------------------------------------------ | ---------------------- |
| [AWS S3](https://amazonaws-china.com/cn/s3/?nc2=h_m1)        | AWS 提供的对象存储服务 |
| [AWS Glacier](https://docs.aws.amazon.com/zh_cn/amazonglacier/latest/dev/introduction.html) | 备份和归档的存储服务   |

## 对象存储

**对象存储**可以帮助用户存储图片、视频、文档和可执行文件。

* 在对象存储中，数据存储为对象，每个对象包含：一个全球唯一的标识符、元数据和数据本身。
* 客户可以直接操作「元数据」来管理和查询数据。
* 「元数据」还可以用来存储访问权限信息和其他管理任务。

## Amazon S3

> Amazon S3 将数据作为对象存储在被称为 “存储桶” 的资源中。您可以在一个存储桶中尽可能多地存储对象，并写入、读取和删除您的存储桶中的对象。对象大小最多可为 5 TB。

* 无限存储空间、高可用、高度持久化。
* 可存储任何类型的数据，如图片、文档和二进制文件。
* ~~单个对象的容量不超过 5 TB~~。单个存档最大可达 40TB。
* 上传/下载对象方式：HTTPS 协议、命令行工具（CLI）、SDK、第三方工具。
* S3 使用「存储桶」组织对象。「存储桶」是对象的容器。用户可以创建最多100个存储桶。
* 建议选择域名（如 com.google.* ）或者公司名称作为「存储桶」名的前缀。



### 示例：备份用户的数据

使用 AWS CLI 命令行工具上传数据。并从 S3 下载数据。

1⃣️ 为数据创建一个 S3 存储桶。

```shell
# 格式
$ aws s3 mb s3://com.domain-$YourName

$ aws s3 mb s3://com.arlingbc-andy
make_bucket: com.arlingbc-andy
```

2⃣️ 从本地的目录上传数据到 S3 存储桶。

```shell
# 格式
# sync：比较 $Path 目录和 S3 存储桶里的 /backup 目录 ,只上传新的或者修改过的文件
# $Path：为目录路径
$ aws s3 sync $Path s3://com.domain-$YourName/backup

$ aws s3 sync /Users/huqilin/Desktop/image s3://com.arlingbc-andy/backup
upload: image/sunset.jpeg to s3://com.arlingbc-andy/backup/sunset.jpeg
```

3⃣️ 测试数据恢复。

```shell
# 格式
$ aws s3 cp --recursive s3://com.domain-$YourName/backup $Path

$ aws s3 cp --recursive s3://com.arlingbc-andy/backup /Users/huqilin/Desktop/image 
```

4⃣️ 为存储桶激活版本保护（启用版本控制）。

默认情况下，S3 存储桶禁用了版本保护功能（同名文件会被覆盖）。

```
$ aws s3api put-bucket-versioning --bucket com.arlingbc-andy --versioning-configuration Status=Enabled
```

5⃣️  获取所有对象和版本

```shell
$ aws s3api list-object-versions --bucket com.arlingbc-andy
```

6⃣️ 资源清理。

```shell
$ aws s3 rb --force s3://com.arlingbc-andy
delete: s3://com.arlingbc-andy/backup/sunset.jpeg
remove_bucket failed: s3://com.arlingbc-andy An error occurred (BucketNotEmpty) when calling the DeleteBucket operation: The bucket you tried to delete is not empty. You must delete all versions in the bucket.
```

> **移除存储桶造成 BucketNotEmpty 报错**
> 如果激活了存储桶的版本功能，删除存储桶时将报错 BucketNotEmpty。这种情况下请使用管理控制台来删除存储桶。



## 归档对象以优化成本

> **Amazon S3 Glacier** 是一款安全、持久且成本极低的云存储服务，适用于数据存档和长期备份。它能够提供 99.999999999% 的持久性以及全面的安全与合规功能，可以帮助满足最严格的监管要求。Amazon S3 Glacier 提供就地查询功能，允许您针对静态存档数据直接运行强大的分析。客户能以每月每 GB 低至 0.004 USD 的价格存储数据，与本地解决方案相比，显著降低了成本。为了保持成本低廉，同时满足各种数据检索需求，Amazon S3 Glacier 提供三种访问存档的选项，各自的检索时间从数分钟到数小时不等。

在 Glacier 中存储数据的成本大概是 S3 中的 1/3。

|                | S3                                    | Glacier                               |
| -------------- | ------------------------------------- | ------------------------------------- |
| 每 GB 容量成本 | 0.03 美元                             | 0.01 美元                             |
| 数据访问速度   | 立即可以访问                          | 在提交请求 3～5h 后                   |
| 持久性         | 设计为年度 99.999999999% 的数据持久性 | 设计为年度 99.999999999% 的数据持久性 |



### 示例：创建 S3 存储桶配合 Glacier 使用

集成 S3 和 Glacier 以降低存储数据的成本。

可以为存储桶添加多条生命周期规则。

**生命周期规则**：在给定的日期之后归档或者删除对象，它还可以帮助把 S3 的对象归档到 Glacier。 

添加生命周期转换规则，「创建制定天数后转换到 Amazon Glacier」。



#### AWS S3 Glacier 数据检索策略

* 加急检索：存档通常在 1-5 分钟内可供使用。

  > 当您偶尔需要加急请求档案子集时，可以使用加急检索来快速访问您的数据。除了最大的存档 (250MB+) 以外，对于使用加急检索方式访问的所有数据，通常在 1-5 分钟内即可使用。有两种加急检索：按需和预置。按需请求类似于 EC2 按需实例，绝大部分时间都可以使用。预置请求可保证在您需要时获得数据。
  >
  > 加急检索针对偶尔需要紧急请求少量存档的情况进行了优化。除了最大的存档 (250MB+) 以外，对于使用加急检索方式访问的所有数据，通常在 1-5 分钟内即可使用。如果您的应用程序或工作负载需要确保加急检索在您需要时就能使用，则您应该考虑使用**预置容量**。

* 标准检索：档通常在 3-5 小时内可供访问。

  > 标准检索是在数小时内访问数据的低成本方式。例如，您可以使用标准检索来恢复备份数据，检索存档的数据以便当天进行编辑和分发，或者获取并分析日志以便在数小时内做出业务决策。

* 批量检索:

  > 批量检索是 S3 Glacier 提供的成本最低的检索方式，使您能够以低廉的成本在一天内检索大量数据，甚至可以达到 PB 级。批量检索通常在 5-12 小时内完成。
  >
  > 批量检索的设计宗旨是让客户以经济高效的方式获取大量数据，以便满足非紧急使用案例的需要，例如对数 PB 的原始视频内容进行转码或分析大量基因组序列。

*  预置容量

  > 预置容量可确保加急检索所检索的容量在您需要时即可使用。每单位容量可确保每 5 分钟内至少可执行 3 次加急检索，并提供高达 150MB/s 的检索吞吐量。
  >
  > 如果您需要满足特定的加急检索速率要求，则可以预置检索容量。如果没有预置容量，则只有在发出请求时有容量可用的情况下，才会接受加急检索请求。



###  示例：使用程序的方式存储对象

S3 可以通过 HTTPS 和 API 来访问。也就是说，用户可以集成 S3 （SDK）到应用程序里，用程序调用 API 来提交请求到 S3。

在应用程序中集成 S3 有助于实现**无状态的服务器架构**。

...



### 示例：使用 S3 来实现静态网站托管

1⃣️ 创建一个新的 S3 存储桶；

```shell
aws s3 mb s3://static.arlingbc.com
```

2⃣️ 上传文件（helloworld.html）到 S3；

```shell
aws s3 cp /Users/huqilin/Desktop/helloworld.html s3://static.arlingbc.com/helloworld.html
upload: ./helloworld.html to s3://static.arlingbc.com/helloworld.html
```

3⃣️ 添加存储桶策略到存储桶；

 配置存储桶策略，以允许所有人查看和下载该存储桶里的文档。

[存储桶策略生成器](https://awspolicygen.s3.amazonaws.com/policygen.html)

#### bucketpolicy.json

```json
{
  "Id": "Policy1547005345057",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Stmt1547005342595",
      "Action": [
        "s3:GetObject"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:s3:::static.arlingbc.com/*",
      "Principal": "*"
    }
  ]
}
```

上传存储策略：

```shell
aws s3api put-bucket-policy --bucket static.arlingbc.com --policy file:///Users/huqilin/Desktop/bucketpolicy.json
```



4⃣️ 激活和配置 S3 服务静态网站；

```shell
aws s3 website s3://static.arlingbc.com --index-document helloworld.html
```

配置完成，访问路径为：`$BucketName.s3-website-$Region.amazonaws.com`

即：<https://s3.us-east-2.amazonaws.com/static.arlingbc.com/helloworld.html>

5⃣️ 使用别名（CNAME）关联一个自定义的域名到 S3 的存储桶；

为自己的域名添加一个 CNAME 别名记录，让记录指向 S3 存储桶的端点。

>  CNAME 别名记录只在满足下面条件的时候生效。
>
> * 存储桶名必须和 CNAME  别名记录一样。要创建一个 CNAME 给 static.yourdomain.com，存储桶名也必须是 static.yourdomain.com。
> * CNAME 别名记录不适用于主域名。可以给子域名创建别名记录的资源。如 static 或者 www 这样前缀的域名。如果想关联主域名到S3存储桶，需要使用AWS提供的 Route53 的 DNS 服务。

6⃣️ 清理资源

```shell
aws s3 rb --force s3://static.arlingbc.com
```



## 对象存储的内部机制

###  确保数据一致性

* S3 是最终一致性：如果更新一个对象然后尝试读取，对象可能还包含旧的版本。在有些情况下，最新的版本可以访问到。

* S3 上创建、更新或者删除对象的操作是原子操作。
* 在上传**新对象**之后，立即提交的读请求会读到一致的数据。但是在**更新或者删除操作**之后的读请求操作将返回最终一致的结果。

### 选择合适的键

挑战：如何合理地设置主键来实现 S3 的 I/O 性能的最大化？

* 为所有对象的键在开头的部分使用相同的字符串，将限制 S3 存储桶的最大 I/O 性能。（不合理的命名示例，像：image1.png、image2.png、image3.png、image4.png、image5.png...）
* 为了改善 S3 的 I/O 性能，不要使用开头相同的字符串作为键。
* 可以为每个对象的键添加散列（MD5）前缀的方式解决这个问题。

