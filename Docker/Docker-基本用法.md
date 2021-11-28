## 什么是 Docker？

[Docker](https://www.docker.com/) 是以 「Docker 容器」为资源分割和调度的基本单位，封装整个软件运行时环境，为开发者和系统管理员设计的，用于构建、发布和运行分布式应用的平台。它是一个跨平台、可移植并且简单易用的容器解决方案。

Docker 的源代码托管在 GitHub 上，基于 Go 语言开发并遵从 Apache 2.0 开源协议。

Docker 可在容器内部快速自动化地部署应用，并通过操作系统内核技术（namespaces、groups 等）为容器提供资源隔离与安全保障。

## 容器技术的优点

1. 持续部署与测试；
2. 跨云平台支持；
3. 环境标准化和版本控制；
4. 高效资源利用率与隔离；
5. 容器跨平台性与镜像；
6. 易于理解且易用；
7. 应用镜像仓库；

## 在 Ubuntu 系统中安装 Docker

卸载旧版本：
```bash
$ sudo apt-get remove docker docker-engine docker.io containerd runc
```

更新并安装软件包：
```bash
$ sudo apt-get update
$ sudo apt-get dist-upgrade
$ sudo apt-get install apt-transport-https ca-certificates
```

添加使用 HTTPS 传输的软件包以及 CA 证书：
```bash
$ sudo apt-get install apt-transport-https \
                       ca-certificates \
                       curl \
                       gnupg-agent \
                       software-properties-common
```

添加GPG密钥...

```bash
# 添加软件源的 GPG 密钥
$ curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
# 官方源
# $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# 向 source.list 中添加 Docker 软件源
$ sudo add-apt-repository \
    "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
# 官方源
# $ sudo add-apt-repository \
#    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
#    $(lsb_release -cs) \
#    stable"
```

安装 Docker 及相关组件：
```bash
$ sudo apt-get install docker \
                       docker.io \
                       docker-compose
```

开启 Docker 服务：

```bash
$ sudo service docker start
```

关闭 Docker 服务：

```bash
$ sudo service docker stop
```

重启 Docker 服务：

```bash
$ sudo service docker restart
```



### 关于 root 权限

> docker 命令的执行一般都需要获取 root 权限，因为 Docker 的命令行工具 docker 与 docker daemon 是同一个二进制文件，而 Docker daemon 负责接收并执行来自 docker 的命令，它的运行需要 root 权限。同时，从 Docker 0.5.2 版本开始，Docker daemon 默认绑定一个 Unix Socket 来代替原有的 TCP 端口，该 Unix Socket 默认是属于 root 用户的。因此，在执行 docker 命令时，需要使用 sudo 来获取 root 权限。
>
> [Manage Docker as a non-root user](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user)

为了避免每次命令都输入 sudo，可以把用户加入 Docker 用户组：

1. 创建 Docker 用户组：

```bash
$ sudo groupadd docker
```

2. 添加用户到 docker 组：

```bash
$ sudo usermod -aG docker $USER
```



### 使用阿里云镜像加速器

[阿里云](https://www.aliyun.com/) ➡️ 管理控制台 ➡️ 容器镜像服务 ➡️ 镜像加速器

配置七牛云镜像，参考：<https://kirk-enterprise.github.io/hub-docs/#/user-guide/mirror>



## Docker 子命令列表

| 子命令分类       | 子命令                                                       |
| ---------------- | ------------------------------------------------------------ |
| 环境信息       | info、version                                             |
| 容器生命周期管理 | create、exec、kill、start、restart、pause、unpause、rm、run、stop |
| 镜像仓库管理     | login、logout、pull、push、search                          |
| 镜像管理         | build、images、import、load、rmi、save、tag、commit          |
| 容器运维操作     | attach、export、inspect、port、ps、rename、stats、top、wait、cp、diff、update |
| 容器资源管理     | volume、network                                              |
| 日系日志信息     | events、history、logs                                        |

查看子命令详细用法：

```bash
$ docker COMMAND --help
```


![docker命令结构图](http://upload-images.jianshu.io/upload_images/2648731-4991f9e6179a5cde.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 1. Docker 环境信息


```bash
# 检查 Docker 环境信息
$ docker info
...

# 检查安装后的 Docker 版本
$ docker version
...

$ docker --version
$ docker -v
Docker version 18.09.7, build 2d0083d


# 检查 Docker Compose 版本
$ docker-compose --version
docker-compose version 1.21.1, build 5a3f1a3


# 检查 Docker Machine 版本
$ docker-machine --version
docker-machine version 0.14.0, build 89b8332
```



### 2. 容器生命周期管理

#### `docker run` 命令

`docker run` 命令用来基于特定的镜像创建一个容器，并依据选项来控制该容器。

当利用 `docker run` 来创建容器时，Docker 在后台运行的标准操作包括：

* 检查本地是否存在指定的镜像，不存在就从公有仓库下载；
* 利用镜像创建并启动一个容器；
* 分配一个文件系统，并在只读的镜像层外面挂载一层可读写层；
* 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去；
* 从地址池配置一个 ip 地址给容器；
* 执行用户指定的应用程序；
* 执行完毕后容器被终止；

用法：`docker run [OPTIONS] IMAGE [COMMAND][ARG...]`

```bash
# 从 ubuntu 镜像启动一个容器，并执行 echo 命令打印出 “Hello World”。执行完 echo 命令后，容器将停止运行。
$ docker run ubuntu echo "Hello World"

# 使用镜像 ubuntu:latest 启动一个容器，并为它分配一个伪终端执行 /bin/bash 命令
# 进入伪终端后，exit/Ctrl+D 即可退出，同时容器终止运行。
# Ctrl+C：退出到宿主机并关闭容器；
# Ctrl+P/Ctrl+Q：退出到宿主机但保持容器运行
# Ctrl+P+Q：退出容器但不关闭容器
$ docker run -i -t --name mytest ubuntu:latest /bin/bash

# 以后台方式运行一个 Nginx 服务器
$ docker run -d -p 80:80 --rm --name mynginx nginx

# --link name:alias 在两个容器之间建立联系
# 启动 Redis 容器，将从数据库连接到主数据库
$ docker run -it --name redis-master redis /bin/bash
$ docker run -it --name redis-slave1 --link redis-master:master redis /bin/bash
```


| 参数 | 描述 |
| --- | --- |
| -d | 以后台方式运行 |
| -i | 使用交互模式，始终保持输入流开放。 |
| -t | 分配一个伪终端，一般两个参数结合时使用 -it，即可在容器中利用打开的伪终端进行交互操作。 |
| --name | 自定义容器名称，若无此选项，Docker 将为容器随机分配一个名字。 |
| --rm | 容器停止运行后，自动删除容器文件。 |
| -c | 给运行在容器中的所有进程分配 CPU 的 shares 值。 |
| -m | 限制为容器中所有进程分配的内存总量，以 B、K、M、G 为单位。 |
| -v | 挂载 volume。 |
| -p | 映射端口 <host_port:contain_port> |
| --link | 通过 `--link` 选项来建立容器间的连接，不但可以避免容器的 IP 和端口暴露到外网所导致的安全问题，还可以防止容器在重启后 IP 地址变化导致的访问失效，它的原理类似于 DNS 服务器的域名和地址映射。当容器的 IP 地址发生变化时，Docker 将自动维护映射关系中的 IP 地址。 |

`--link` 参数的格式为 `--link name:alias`，其中 `name` 是要连接的容器的名称，`alias` 是这个连接的别名。

#### `docker start/stop/restart` 命令

```bash
# 以后台方式运行一个 Nginx 服务器
$ docker run -d -p 80:80 --name myserver nginx

# 停止运行
$ docker stop myserver

# 删除容器
$ docker rm myserver


$ docker container stop
$ docker container start
$ docker container restart
```

注释：
* `-p` 指定要映射的端口，并且，在一个指定端口上只可以绑定一个容器。支持的格式有 `ip:hostPort:containerPort | ip::containerPort | hostPort:containerPort`。

#### `docker rm` 命令

```bash
# 删除容器
$ docker rm CONTAINER_ID
$ docker rm CONTAINER_NAMES

# 删除一个运行中的容器
$ docker rm -f CONTAINER_ID
$ docker rm -f CONTAINER_NAMES

# 列出所有运行中的容器
$ docker container ls
$ docker ps     

# 列出所有容器
$ docker container ls -a
$ docker ps -a

# 删除所有处于终止状态的容器
$ docker container prune

# 删除容器的同时移除数据卷
$ docker rm -v CONTAINER_ID

$ docker stop $(docker ps -a -q)   # 停止运行所有容器
$ docker rm $(docker ps -a -q)     # 删除所有容器
```



### 3. Docker registry

当用户创建了自己的镜像之后就可以使用 `push` 命令将它上传到公有或者私有仓库，这样下次在另外一台机器上使用这个镜像时，只需要从仓库上 `pull` 下来就可以了。

官方镜像仓库：[Docker Hub](https://hub.docker.com/)

#### `docker pull` 命令

从 Docker Hub 仓库获取所需要的镜像。

用法：

```bash
$ docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]
$ docker pull [OPTIONS] NAME[:TAG]
$ docker pull username/repository<:tag_name>
$ docker pull registry.domain.com/repos:<tag_name>
```

示例：
```bash
# 从Docker Hub 仓库下载 nginx:latest 镜像
$ docker pull nginx

# 从 Docker Hub 仓库下载 Ubuntu:18.04 镜像
$ docker pull ubuntu:18.04
# 同上
$ docker pull registry.hub.docker.com/ubuntu:18.04
```

#### `docker push` 命令

用户也可以在登录（`docker login`）后通过 `docker push` 命令来将镜像推送到 Docker Hub。

```bash
$ docker push SEL/ubuntu
```

#### `docker search` 命令

用户无需登录即可通过 `docker search` 命令来查找官方仓库中的镜像，并利用 `docker pull` 命令来将它下载到本地。

用法：`docker search TEAM`

```bash
# 从官方镜像和所有个人公共镜像中搜索镜像
$ docker search nginx
```


### 4. 镜像管理

#### `docker images` 命令

```bash
# 默认列出最顶层的镜像
$ docker image ls
$ docker images

# 列出所有已下载镜像
$ docker image ls -a

# 显示完整的 IMAGE ID
$ docker image ls --no-trunc

# 只显示镜像 ID
$ docker image ls -q

# 使用过滤参数列出指定镜像
$ docker image ls --filter <选项>
```

#### `docker rmi` 命令

```bash
# 命令格式
$ docker image rm [选项] <镜像1> [<镜像2> ...]

# 删除指定镜像
$ docker rmi IMAGE_ID

# 删除所有镜像
$ docker rmi $(docker image ls -a -q) 
```

> 注意：在删除镜像之前要先用 `docker rm` 删掉依赖于这个镜像的所有容器。

### 5. 容器运维操作

#### `docker attach` 命令

连接到正在运行的容器。

用法：`docker attach [OPTIONS] CONTAINER`

```bash
$ docker attach --sig-proxy=false CONTAINER
```

> 💡
>
> `attach` 命令可以带上 `--sig-proxy=false` 来确保 `CTRL-D` 或 `CTRL-C` 不会关闭容器。


### `docker exec` 命令

* 使用 `attach` 进入容器后，exit 退出终端会导致容器终止；
* 使用 `exec` 进入容器后，exit 退出不会导致容器终止；

```bash
# 命令格式
$ docker exec [OPTIONS] CONTAINER

# Enter the container
$ docker exec -it <container id> /bin/bash
```


#### `docker inspect` 命令

查看镜像和容器的详细信息。

用法：`docker inspect [OPTIONS] CONTAINER|IMAGE [CONTAINER|IMAGE...]`

```bash
# 查看容器的内部IP
$ docker inspect --format='{{.NetworkSettings.IPAddress}}' CONTAINER

# 通过 docker inspect 命令查看所挂载的volume情况
$ docker inspect --format='{{.Config.Volumes}}' CONTAINER
$ docker inspect --format='{{.Mounts}}' CONTAINER
$ docker inspect a9c9 | grep Mounts -A 10
```

#### `docker ps` 命令

查看容器的相关信息，默认只显示正在运行的容器的信息

```shell
$ docker ps     # 列出所有运行中的容器
$ docker ps -a  # 列出所有容器
$ docker ps -l  # 查看最新创建的容器

$ docker ps
CONTAINER ID        IMAGE                         COMMAND                  CREATED             STATUS              PORTS                    NAMES
cc41e7a676a2        hyperledger/fabric-ca:1.2.0   "sh -c 'fabric-ca-..."   12 days ago         Up 12 days          0.0.0.0:7054->7054/tcp   fabric-ca-server
```

#### `docker top` 命令

查看容器中正在进行的进程

用法：`docker top <container_id/container_name>`

```bash
# host上使用此命令可以看到类似的进程信息
$ ps -ef | grep docker 
```

#### `docker commit` 命令

将一个 Docker 容器固化为一个新的镜像。

用法：`docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]`

> 💡
>
> 官方建议通过 docker build 命令结合 Dockerfile 创建和管理镜像

#### `events`、`history` 和 `logs` 命令

查看 Docker 的系统日志信息

```bash
# 打印实时系统日志
$ docker events [OPTIONS]


# 指定镜像的历史版本信息
$ docker history [OPTIONS] IMAGE

$ docker history nginx:latest


# 容器中进程的运行日志
$ docker container logs [OPTIONS] CONTAINER
$ docker container logs [container ID or NAMES]
```
