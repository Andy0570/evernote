![Unsplash](http://upload-images.jianshu.io/upload_images/2648731-8bdb873858fcce6c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## 方法一：scp

### 1. Mac 上传文件到 Linux 服务器

> 语法：
>
> scp 文件名 用户名@服务器ip:目标路径

```shell
$ scp /Users/test/testFile root@www.bkjia.com:/test/

# 上传本地/opt/soft/目录下的文件 nginx-0.5.38.tar.gz 到远程IP地址为 10.10.10.10 的opt/soft/scptest目录
$ scp /opt/soft/nginx-0.5.38.tar.gz root@10.10.10.10:/opt/soft/scptest
```



### 2. Mac 上传文件夹到 Linux 服务器

> 语法：
>
> scp -r 文件夹目录 用户名@服务器ip:目标路径

```shell
$ scp -r /Users/test/testFolder root@www.bkjia.com:/test/

# 上传本地目录/opt/soft/mongodb到远程IP地址为 10.10.10.10 的/opt/soft/scptest的目录中
$ scp -r /opt/soft/mongodb root@10.10.10.10:/opt/soft/scptest
```



### 3. Linux 服务器下载文件到 Mac

> 语法：
>
> scp 用户名@服务器ip:文件路径 目标路径

```shell
$ scp root@www.bkjia.com:/test/testFile /Users/test/

# 从远程IP地址为 10.10.10.10 的服务器上的/opt/soft/的目录中下载 nginx-0.5.38.tar.gz 文件到本地/opt/soft/目录中。
$ scp root@10.10.10.10:/opt/soft/nginx-0.5.38.tar.gz /opt/soft/
```




### 4. Linux 服务器下载文件夹到 Mac

> 语法：
>
> scp -r 用户名@服务器ip:文件路径 目标路径

```shell
$ scp -r root@www.bkjia.com:/test/testFolder /Users/test/

# 从远程IP地址为 10.10.10.10 的服务器上的/opt/soft/中下载 mongodb 目录到本地的/opt/soft/目录来。
$ scp -r root@10.10.10.10:/opt/soft/mongodb /opt/soft/
```



### 5. 使用公私钥连接方式上传 Mac 本地文件到 AWS 服务器 

> 语法：
>
> scp -i {本地 .pem 文件的完整路径}  -r 本地文件夹目录 ec2-user@{实例 IP 地址}:文件路径

```shell
$ scp -i ~/.ssh/MyKeyPair.pem -r /Users/Andy/Desktop/test ec2-user@52.27.212.125:/home/ubuntu/WebServer
```

注意事项：

* 确保你的私钥文件放在了 ~/.ssh/MyKeyPair.pem， 确保这个文件的属性是 400。

  ```shell
  chmod 400 ~/.ssh/mykeypair.pem    #修改私钥文件权限为只读属性
  ```

* 确保 ~/.ssh 目录的属性是 700。

  ```shell
  chmod 700 ~/.ssh    ### 修改 ssh 目录文件权限
  ```

* 确保你本地的公钥~/.ssh/MyKeyPair.pem在服务器 (remote) 的`~/.ssh/authorized_keys`里。



#### SCP 可选参数

```
-1：使用ssh协议版本1；
-2：使用ssh协议版本2；
-4：使用ipv4；
-6：使用ipv6；
-B：以批处理模式运行；
-C：使用压缩；
-F：指定ssh配置文件；
-l：指定宽带限制；
-o：指定使用的ssh选项；
-P：指定远程主机的端口号，注意 -p 已经被 rcp 使用；
-p：保留文件的最后修改时间，最后访问时间和权限模式；
-q：不显示复制进度；
-r：以递归方式复制，即复制文件夹。
-v 显示进度，可以用来查看连接、认证、或是配置错误。
```



## 方法二：sftp

* [Mac 上传文件到 Linux 服务器](https://www.jianshu.com/p/1afd25e7459d)



### 参考

* [Linux 命令大全：scp 命令](http://man.linuxde.net/scp)
* [Linux Mac 之间文件传输](https://yq.aliyun.com/articles/7949)
* [How To Set Up SSH Keys](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2)
* [在 EC2 上创建 root 用户，并使用 root 用户登录](http://www.wangchao.info/1137.html)
