### Mac 上安装 AWS CLI 上的 plugin

```shell
# 1.下载文件
$ curl "https://s3.amazonaws.com/session-manager-downloads/plugin/latest/mac/sessionmanager-bundle.zip" -o "sessionmanager-bundle.zip"

# 2.解压文件
$ unzip sessionmanager-bundle.zip

# 3.安装插件
$ sudo ./sessionmanager-bundle/install -i /usr/local/sessionmanagerplugin -b /usr/local/bin/session-manager-plugin

# 4.插件安装成功后，检验插件的安装情况
$ session-manager-plugin --version
1.0.37.0
```

执行命令进行登录：
```shell
aws ssm start-session --target instance-id
```

### 参考

* [AWS Systems Manager 用户指南](https://docs.aws.amazon.com/zh_cn/systems-manager/latest/userguide/what-is-systems-manager.html)
* [使用 Amazon EC2 Systems Manager 取代堡垒机](https://amazonaws-china.com/cn/blogs/china/replacing-a-bastion-host-with-amazon-ec2-systems-manager/)
* [忘记堡垒机，使用 Session Manager 登录和管理 EC2 主机](https://amazonaws-china.com/cn/blogs/china/session-manager-register-ec2/)
