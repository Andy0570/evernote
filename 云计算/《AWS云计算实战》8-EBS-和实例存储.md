* **文件系统**负责管理文件放在底层的块级别存储的位置。
* 块级别的存储只能运行在操作系统的 EC2 实例上使用。
* AWS 提供两种类型的块级别存储：网络附加存储和实例存储。

## 一、网络附加存储——弹性数据块存储（EBS）

参考：　[Amazon EBS 卷](https://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/EBSVolumes.html?icmpid=docs_ec2_console)

EBS 卷：

* 不属于 EC2 实例的一部分，通过网卡附加到 EC2 实例上。
* [Amazon EBS 卷类型](https://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/EBSVolumeTypes.html)
* EBS 卷是一个独立的资源，它可以独立于EC2 实例存在，但是需要一台 EC2 服务器才能使用 EBS 卷。
* EBS 卷的优势：**它不属于 EC2 的一部分，是独立的资源。**


> 💡「分区」
>
> 创建一个新的 EBS 卷时，必须在上面创建一个文件系统。你还可以在 EBS 卷上创建不同的分区，但是分区不是使用 EBS 卷的最佳实践。应该创建和需求相同容量大小的卷；
>
> 在需要两个单独分区的情况下，直接创建两个卷更适合。

### 挂载卷

```shell
# 查看已经附加的 EBS 卷
$ sudo fdisk -l 

# 创建 ext4 的文件系统
$ sudo mkfs -t ext4 /dev/xvdf

# 挂载文件系统到一个目录
$ sudo mkdir /mnt/volume/
$ sudo mount /dev/xvdf /mnt/volume/

# 查看已经挂载的卷
$ df -h

# 解除挂载
$ sudo umount /mnt/volume/
```

### 性能测试

* 硬盘的性能测试：读操作测试、写操作测试。
* 使用 dd 工具进行性能测试。
* 存储性能取决于 EC2 的实例类型和 EBS 卷的类型。
* EBS 卷必须能够充分使用 EC2 实例提供的带宽。
* 可以把两个甚至更多的 EBS 卷挂载到同一台 EC2，并使用软件 **RAID** 来提升性能。在 Linux 系统中可以使用 `mdadm` 来创建软件 RAID。



### 备份数据

* EBS 卷提供了优化的易于使用的 **EBS 快照功能**来备份 EBS 卷的数据。



## 二、实例存储

参考：[Amazon EC2 实例存储](https://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/InstanceStorage.html)

* 实例存储像物理磁盘一样提供块级别的存储。
* **实例存储是 EC2 的一部分，并且只有在 EC2 正常运行的时候才可用。**
* 不要使用实例存储来存放不能丢失的数据，把它用来存放缓存、临时数据和一些在多个节点间复制数据的应用。
* AWS 提供 SSD 和物理磁盘的实例存储。
* 实例存储专用于特定实例，一些实例类型不支持实例存储卷。
* 如果您需要较高的磁盘性能，我们建议您在生产使用之前对每个磁盘位置执行一次性写入操作，以此来实现硬盘初始化。
* [优化实例存储卷的磁盘性能](https://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/disk-performance.html)



### 备份数据

实例存储卷没有内建的方法来进行备份。可以使用 Cron 和 S3 来定义备份数据。



### 比较块存储解决方案



|                | S3                                   | EBS                                                | 实例存储                                                     |
| -------------- | ------------------------------------ | -------------------------------------------------- | ------------------------------------------------------------ |
| 常见的使用场景 | 集成到应用程序中以保存用户上传的数据 | 为需要块级别存储的数据库或者传统应用程序提供持久化 | 提供临时数据存储或者为内建复制技术来防止数据丢失的应用程序提供高性能存储 |
| 独立的资源     | 是                                   | 是                                                 | 否                                                           |
| 如何访问数据   | HTTPS API                            | EC2 实例/系统调用                                  | EC2 实例/系统调用                                            |
| 是否有文件系统 | 没有                                 | 有                                                 | 有                                                           |
| 防止数据丢失   | 很高                                 | 高                                                 | 地                                                           |
| 每 GB 容量成本 | $$                                   | $$$                                                | $                                                            |
| 运维开销       | 无                                   | 低                                                 | 高                                                           |



### 三、Amazon EFS

[Amazon Elastic File System](https://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/AmazonEFS.html)

> Amazon EFS 提供可扩展文件存储以供和 Amazon EC2 一起使用。您可以创建 EFS 文件系统并配置实例来装载文件系统。您可以使用 EFS 文件系统作为在多个实例上运行的工作负载和应用程序的通用数据源。
>
> 作用：**解决在多台服务器之间共享块数据的需求。**




