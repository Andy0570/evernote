原文链接：<https://docs.gitlab.com/runner/install/docker.html>

本文描述的是如何在 Docker 容器中运行 GitLab Runner 的方法。

## 通常的 GitLab Runner Docker 镜像用法

GitLab Runner 的 Docker 镜像（基于 Ubuntu 或 Alpine Linux）被设计为围绕标准 `gitlab-runner` 命令的包装器，就像 GitLab Runner 直接安装在主机上一样。

一般规则是每个 GitLab Runner 命令通常会执行为：

```bash
gitlab-runner [Runner command and options...]
```

可以执行为：

```bash
docker run [chosen docker options...] gitlab/gitlab-runner [Runner command and options...]
```

例如，想要获取 GitLab Runner 命令的顶级帮助信息可以执行：

```bash
docker run --rm -t -i gitlab/gitlab-runner --help

NAME:
   gitlab-runner - a GitLab Runner

USAGE:
   gitlab-runner [global options] command [command options] [arguments...]

VERSION:
   10.7.0 (7c273476)

(...)
```

简而言之，命令 `gitlab-runner` 被 `docker run [docker options] gitlab/gitlab-runner` 替换，而 Runner 命令的其余部分保留，就像 [注册文档](https://docs.gitlab.com/runner/register/index.html) 中描述的那样。唯一的区别是 `gitlab-runner` 命令是在 Docker 容器内执行的。

## Docker 镜像的安装和配置

1. 首先安装 Docker：
   
```bash
curl -sSL https://get.docker.com/ | sh
```
    
2. 你需要将配置卷安装到 `gitlab-runner` 容器中以用于配置和其他资源：

```bash
docker run -d --name gitlab-runner --restart always \
  -v /srv/gitlab-runner/config:/etc/gitlab-runner \
  -v /var/run/docker.sock:/var/run/docker.sock \
  gitlab/gitlab-runner:latest
```
    
> 💡提示：在 macOS 系统中，使用 `/Users/Shared` 代替 `/srv`。
    
或者，您也可以使用配置容器来装入自定义数据卷：
    
    
```bash
docker run -d --name gitlab-runner-config \
    -v /etc/gitlab-runner \
    busybox:latest \
    /bin/true
```

然后，运行 Runner：

```bash
docker run -d --name gitlab-runner --restart always \
    -v /var/run/docker.sock:/var/run/docker.sock \
    --volumes-from gitlab-runner-config \
    gitlab/gitlab-runner:latest
```
3. 按照 [ Docker section of Registering Runners](https://docs.gitlab.com/runner/register/index.html#docker) 中的说明注册刚刚启动的 Runner。

请务必阅读 [FAQ](https://docs.gitlab.com/runner/faq/README.html) 部分，其中介绍了GitLab Runner的一些最常见问题。

## 更新

拉取 gitlab/gitlab-runner 最新版本：

```bash
docker pull gitlab/gitlab-runner:latest
```

停止并删除现有容器：

```bash
docker stop gitlab-runner && docker rm gitlab-runner
```

像原来一样启动容器：

```bash
docker run -d --name gitlab-runner --restart always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /srv/gitlab-runner/config:/etc/gitlab-runner \
  gitlab/gitlab-runner:latest
```

> 注意：您需要使用与最初安装数据卷相同的方法（`-v /srv/gitlab-runner/config:/etc/gitlab-runner` 或者 `--volumes-from gitlab-runner-config`）。


## 查看 GitLab Runner 日志

当 GitLab Runner 作为前台任务启动时（无论是本地安装的二进制文件还是 Docker 容器内），日志都会打印到标准输出。
当 GitLab Runner 作为系统服务启动时（例如使用 Systemd），在大多数情况下，日志通过 Syslog 或其他系统日志记录机制记录。
当 GitLab Runner 作为基于 Docker 的服务启动时，因为 `gitlab-runner ...` 命令是容器的主要进程，所以可以使用 `docker logs` 命令读取日志。

例如，如果使用以下命令启动 GitLab Runner：

```bash
docker run -d --name gitlab-runner --restart always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /srv/gitlab-runner/config:/etc/gitlab-runner \
  gitlab/gitlab-runner:latest
```

您可以通过以下方式获取日志：

```bash
docker logs gitlab-runner
```

其中 `gitlab-runner` 是启动 GitLab Runner 时，用第一个命令 `--name gitlab-runner` 设置的容器名称。

您可以在 [Docker文档页面](https://docs.docker.com/engine/reference/commandline/logs/) 上找到有关处理容器日志的更多信息。

## 安装受信任的 SSL 服务器证书

如果您（自搭建的）的 GitLab CI 服务使用自签名 SSL 证书，那么您应确保 `gitlab-runner` 容器信任 GitLab CI 服务器证书，以便它们能够相互通信。

`gitlab/gitlab-runner` 镜像被配置为在 `/etc/gitlab-runner/certs/ca.crt` 中查找受信任的 SSL 证书，但是可以使用 `-e "CA_CERTIFICATES_PATH=/DIR/CERT"` 配置选项来更改。

将 `ca.crt` 文件复制到数据卷（或容器）上的 `certs` 目录中。`ca.crt` 文件应包含您希望 gitlab-runner 信任的所有服务器的根证书。gitlab-runner 容器将在启动时导入 `ca.crt` 文件，因此如果您的容器已在运行，则可能需要重新启动它才能使更改生效。


## Docker 镜像

镜像源 `gitlab/gitlab-runner:latest` 基于 Ubuntu 系统，请参阅 [gitlab-org/gitlab-runner](https://gitlab.com/gitlab-org/gitlab-runner/tree/master/dockerfiles) 源代码，了解 Ubuntu 和 Alpine 镜像的可能构建说明。

您也可以使用用于 [Alpine Linux](https://www.alpinelinux.org) 系统下的名为 `gitlab/gitlab-runner:alpine` 的镜像，它占用空间小得多（Ubuntu 系统下大约 400 MB  vs Alpine 系统下大约 100 MB ）。

## SELinux

一些发行版（CentOS，RedHat，Fedora）默认使用 SELinux 来增强底层系统的安全性。

处理此类配置时必须特别小心。

1. 如果要使用 Docker 执行程序在容器中运行构建，需要访问 `/var/run/docker.sock`。但是，如果您在执行模式下存在 SELinux，则在访问 `/var/run/docker.sock` 时会看到权限被拒绝。需要安装 `selinux-dockersock` 解决该问题：[https://github.com/dpw/selinux-dockersock](https://github.com/dpw/selinux-dockersock)。
2. 请确保在主机上创建了一个永久目录：`mkdir -p /srv/gitlab-runner/config`。
3. 在卷中使用 `:Z` 运行 Docker。

```bash
docker run -d --name gitlab-runner --restart always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /srv/gitlab-runner/config:/etc/gitlab-runner:Z \
  gitlab/gitlab-runner:latest
```

有关原因和解决方案的更多信息，请点击此处：<http://www.projectatomic.io/blog/2015/06/using-volumes-with-docker-can-cause-problems-with-selinux/>

