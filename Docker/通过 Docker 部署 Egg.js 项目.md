
> 本文主要内容：创建一个简单的 Egg.js 项目，通过编写 Dockerfile 文件的方式来构建 Docker 镜像并运行。


### 1. 使用脚手架初始化 Egg.js 项目

```bash
# 全局模式下安装 egg-init
$ npm install -g egg-init

# 初始化 Egg.js 项目，项目名称为：docker-web-app
$ egg-init docker-web-app --type=simple

# 安装依赖
$ cd docker-web-app && npm install

# 运行测试
$ npm run dev
$ curl localhost:7001
```

### 2. 编写 Dockerfile 文件

使用 Visual Studio Code 打开项目，首先安装一个 Visual Studio Code 插件：[Docker](https://github.com/microsoft/vscode-docker)。

通过这个插件可以自动生成 Dockerfile 及其相关的模版文件。

> Press `F1` and search for `Docker: Add Docker Files to Workspace` to generate `Dockerfile`, `docker-compose.yml`, `docker-compose.debug.yml`, and `.dockerignore` files for your workspace type.

#### 修改 package.json 文件

Docker 中执行，start 脚本去掉 `--daemon` 参数，修改 package.json 配置如下：

```json
{
  "scripts": {
    "start": "egg-scripts start",
  }
}
```

#### 修改 Dockerfile 文件

修改 Dockerfile 内容如下：

```yaml
FROM node:10.13-alpine

# 定义环境变量
ENV NODE_ENV production

# RUN apk --update add tzdata &&
#   cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime &&
#   echo "Asia/Shanghai" >/etc/timezone &&
#   apk del tzdata

# 创建应用程序工作目录
WORKDIR /usr/src/app

# 将项目配置文件拷贝到应用程序工作目录下
COPY ["package.json", "package-lock.json*", "npm-shrinkwrap.json*", "./"]

# 安装应用程序依赖
RUN npm install --production --registry=https://registry.npm.taobao.org

# Bundle app source
COPY . .

EXPOSE 7001

CMD npm start
```

#### 修改 .dockerignore 文件

注：通过 Docker 插件自动生成 `Dockerfile` 文件时，同时也会自动生成 `.dockerignore` 等几个文件。

该文件中可以添加你认为不需要拷贝到镜像中的文件或者目录，比如 `logs` 目录，我认为应用的日志信息不需要添加到镜像中。

```
node_modules
npm-debug.log
logs
Dockerfile*
docker-compose*
.dockerignore
.git
.gitignore
.env
*/bin
*/obj
README.md
LICENSE
.vscode
```

### 3. 构建镜像

进入到 `Dockerfile` 所在的那个目录中，运行以下命令构建 Docker 镜像：

```bash
docker build -t <your username>/docker-web-app .
```


### 4. 启动容器

根据构建的镜像启动容器：

```bash
docker run -p 7001:7001 -d <your username>/docker-web-app
```

把你应用程序的输出打印出来：

```bash
# 查看运行中的容器
$ docker ps
$ docker container ls

# 可能你的容器没有运行成功，那么可以查看所有容器（包括终止的）
$ docker ps -a

# 打印 app 输出
$ docker logs <container id>
```

如果你需要进入容器中，请运行 `exec` 命令：

```bash
$ docker exec -it <container id> /bin/bash
```

### 5. 测试

为测试你的应用程序，给出与 Docker 映射过的端口号：

```bash
$ docker ps
CONTAINER ID        IMAGE                      COMMAND                  CREATED             STATUS              PORTS                    NAMES
d7cc72bc2e2f        523013205/docker-web-app   "/bin/sh -c 'npm sta…"   33 minutes ago      Up 33 minutes       0.0.0.0:7001->7001/tcp   affectionate_satoshi
```

在上面的例子中，在容器中 Docker 把端口号 `7001` 映射到你机器上的 `7001` 。

现在你可以使用 `curl`（如果需要的话请通过 `sudo apt-get install curl` 安装）调用你的程序了：

```bash
$ curl -i localhost:7001
HTTP/1.1 200 OK
set-cookie: csrfToken=TsTOHeuHNJQ_Zy7TWZYM_KH5; path=/
content-type: text/plain; charset=utf-8
content-length: 7
x-frame-options: SAMEORIGIN
x-xss-protection: 1; mode=block
x-content-type-options: nosniff
x-download-options: noopen
x-readtime: 8
keep-alive: timeout=5
Date: Thu, 05 Sep 2019 08:58:07 GMT
Connection: keep-alive

hi, egg%
```

### 6. 删除容器

```bash
# 首先查看运行中的容器，获取该容器的 id
$ docker ps
CONTAINER ID        IMAGE                      COMMAND                  CREATED             STATUS              PORTS                    NAMES
d7cc72bc2e2f        523013205/docker-web-app   "/bin/sh -c 'npm sta…"   41 minutes ago      Up 41 minutes       0.0.0.0:7001->7001/tcp   affectionate_satoshi

# 停止运行容器（输入容器 id 的前几位即可）
$ docker stop d7cc
d7cc

# 删除容器
$ docker rm d7cc
d7cc

# 查看所有容器，验证是否成功删除
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

### 7. 上传镜像到 Docker Hub

首先要在 Docker Hub 上创建一个账户，然后通过 `docker login` 命令输入用户名密码以登录到 Docker Hub，最后将构建的镜像文件上传到 Docker Hub。

```bash
# 查看本地镜像列表
$ docker images
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
523013205/docker-web-app   latest              2897b974acc6        42 minutes ago      104MB
node                       10.13-alpine        93f2dcbcddfe        9 months ago        70.3MB

# 登录到 Docker Hub
$ docker login
Authenticating with existing credentials...
Login Succeeded

# 推送本地镜像文件到 Docker Hub 仓库
$ docker push 523013205/docker-web-app
The push refers to repository [docker.io/523013205/docker-web-app]
f51c2bfbdf22: Pushed
748c644cea92: Pushed
a9b6dc1df341: Pushed
bcbcc63d8ba0: Pushed
2faeaaebb113: Mounted from library/node
387bc77dd3f2: Mounted from library/node
df64d3292fd6: Mounted from library/node
latest: digest: sha256:4523b1486cc21a87dea7386d5063ad3abb066016fbc12dd4c7632747d0d68ede size: 1789
```

### 8. 通过云服务部署镜像

登录到一台云服务器（Ubuntu 系统）上，下载 Docker Hub 上的镜像并部署。

```bash
# 首先需要安装 Docker，此处略
...

# 下载镜像
$ docker pull 523013205/docker-web-app
Using default tag: latest
latest: Pulling from 523013205/docker-web-app
4fe2ade4980c: Pull complete 
c245f6a8ecc5: Pull complete 
82bdc9503d50: Pull complete 
6dd4fabb14e4: Pull complete 
67cc2425a047: Pull complete 
66d6d1f5912f: Pull complete 
1c22a4a3cf3b: Pull complete 
Digest: sha256:4523b1486cc21a87dea7386d5063ad3abb066016fbc12dd4c7632747d0d68ede
Status: Downloaded newer image for 523013205/docker-web-app:latest

# 查看镜像
$ docker images
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
523013205/docker-web-app      latest              2897b974acc6        About an hour ago   104MB

# 运行容器
$ docker run -p 7001:7001 -d 523013205/docker-web-app
1e793006f0f1e52b8d142c1e12d7f247557beb2e4c4364eee3d8cfdd64855c99

# 测试连接
$ curl -i localhost:7001
HTTP/1.1 200 OK
set-cookie: csrfToken=RQqmHP_7JlBjnweOXEW4S6CO; path=/
content-type: text/plain; charset=utf-8
content-length: 7
x-frame-options: SAMEORIGIN
x-xss-protection: 1; mode=block
x-content-type-options: nosniff
x-download-options: noopen
x-readtime: 6
keep-alive: timeout=5
Date: Thu, 05 Sep 2019 09:27:06 GMT
Connection: keep-alive

hi, egg
```


### 参考
* [把一个 Node.js web 应用程序给 Docker 化](https://nodejs.org/zh-cn/docs/guides/nodejs-docker-webapp/)
* [docker-node](https://github.com/nodejs/docker-node/blob/master/README.md#how-to-use-this-image)
* [Docker and Node.js Best Practices](https://github.com/nodejs/docker-node/blob/master/docs/BestPractices.md)
* [eggjs 装入 Docker](https://www.yuque.com/cloud-dev/aliyun-in-action/nllubi)
* [eggjs + docker 最佳实践](https://github.com/eggjs/egg/issues/1431)