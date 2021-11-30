## 通过 Dockerfile 创建镜像的步骤

1. 创建一个 Dockerfile 文件，该文件包含一些如何创建镜像的指令。
2. 编辑 Dockerfile 文件。
3. 使用 `docker build` 命令生成镜像。

## Dockerfile 基本语法

* Dockerfile reference: <https://docs.docker.com/engine/reference/builder/>
* Dockerfile 最佳实践：<https://yeasy.gitbooks.io/docker_practice/content/appendix/best_practices.html>
* docker-library/docs：<https://github.com/docker-library/docs>

Dockerfile 文件示例：

```Dockerfile
# 井号开头的表示注释文本
FROM ubuntu:14.04

MAINTAINER Docker Newbee <newbee@docker.com>
RUN apt-get -qq update \
    && apt-get -qqy install ruby ruby-dev \
    && gem install sinatra

# put my local web site in myApp folder to /var/www
# 复制本地文件到镜像
ADD myApp /var/www

# expose httpd port
# 向外部开放端口
EXPOSE 80

# the command to run
# 描述容器启动后运行的程序
CMD ["/usr/sbin/apachectl", "-D", "FOREGROUND"]
```

Dockerfile 基本语法：
* 使用 `#` 来注释；
* `FROM` 指令：告诉 Docker 使用哪个镜像作为基础镜像；
* `MAINTAINER` 指令：描述维护者的信息；
* `RUN` 指令会在创建中运行，比如安装一个软件包，在这里使用 apt-get 来安装了一些软件。
* `ADD` 指令：复制本地文件到镜像；
* `EXPOSE` 指令：向外部开放端口；
* `CMD` 指令：描述容器启动后运行的程序；
* `VOLUME` 指令：添加一个或者多个新的卷到由该镜像创建的任意容器。


## 使用 `docker build` 命令生成镜像

命令格式：

```bash
docker build [选项] <上下文路径/URL/->
```

示例：
```bash
$ docker build -t="ouruser/sinatra:v2" .
$ docker build -t nginx:v3 .
$ docker build -t <your username>/node-web-app .

# -t 标记添加 tag，指定新的镜像的用户信息。
# . 指定上下文目录，这里表示 Dockerfile 所在的路径（当前目录），也可以替换为一个具体的 Dockerfile 的路径。
```

## Dockerfile 指令详情



### COPY 复制文件

`COPY` 指令将从构建上下文目录中 <源路径> 的文件/目录复制到新的一层的镜像内的 <目标路径> 位置。

语法：

```
COPY [--chown=<user>:<group>] <源路径>... <目标路径>
COPY [--chown=<user>:<group>] ["<源路径1>",... "<目标路径>"]
```


### ADD 更高级的复制文件

`ADD` 指令和 `COPY` 的格式和性质基本一致。但是在 `COPY` 基础上增加了一些功能。


在 Docker 官方的 Dockerfile 最佳实践文档 中要求，尽可能的使用 `COPY`，因为 `COPY` 的语义很明确，就是复制文件而已，而 `ADD` 则包含了更复杂的功能，其行为也不一定很清晰。最适合使用 `ADD` 的场合，就是所提及的需要自动解压缩的场合。

### CMD 容器启动命令

CMD 指令用于指定默认的容器主进程的启动命令。

### ENTRYPOINT 入口点

指定容器启动程序及参数。

### ENV 设置环境变量

### VOLUME 定义匿名卷

将 `/data` 目录挂载为匿名卷： 
```bash
VOLUME /data
```

### EXPOSE 声明端口

`EXPOSE` 仅仅是声明容器打算使用什么端口而已，并不会自动在宿主进行端口映射


### WORKDIR 指定工作目录

### 参考
* [使用 Dockerfile 定制镜像](https://yeasy.gitbooks.io/docker_practice/content/image/build.html)

