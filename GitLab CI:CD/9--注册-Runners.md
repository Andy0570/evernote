原文链接：<https://docs.gitlab.com/runner/register/index.html>

## 要求

在注册 Runner 之前，您需要先：

* 将其安装在与安装 GitLab 的位置不同的服务器上。
* 通过 GitLab 的界面获取共享或特定 Runner 的令牌。

## GNU/Linux

...


## macOS

...


## Windows

...


## FreeBSD

...


## Docker

在 [Run GitLab Runner in a container](https://docs.gitlab.com/runner/install/docker.html) 之后，应遵循这些说明。在本节中，您将启动一个临时的 gitlab-runner 容器来注册您在安装期间创建的容器。完成注册后，生成的配置将写入您选择的配置卷中（如：`/srv/gitlab-runner/config`），Runner 将自动加载并使用该配置卷。

要使用 Docker 容器注册 Runner：

1. 运行注册命令：

```bash
docker run --rm -t -i -v /srv/gitlab-runner/config:/etc/gitlab-runner gitlab/gitlab-runner register
```

> 注意：如果你在安装期间使用了 `/srv/gitlab-runner/config` 以外的配置卷，则应使用正确的卷以上更新命令。

2. 输入您的 GitLab 实例 URL：

```bash
Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com )
https://gitlab.com
```

3. 输入您获得的令牌以注册 Runner：

```bash
Please enter the gitlab-ci token for this runner
xxx
```

4. 输入 Runner 的描述，您可以稍后在 GitLab 的 UI 中更改：

```bash
Please enter the gitlab-ci description for this runner
[hostname] my-runner
```

5. 输入 [与 Runner 关联的标签](https://docs.gitlab.com/ee/ci/runners/#using-tags)，您可以稍后在 GitLab 的 UI 中更改：

```bash
Please enter the gitlab-ci tags for this runner (comma separated):
my-tag,another-tag
```

6. 输入 [Runner executor](https://docs.gitlab.com/runner/executors/README.html)

```bash
Please enter the executor: ssh, docker+machine, docker-ssh+machine, kubernetes, docker, parallels, virtualbox, docker-ssh, shell:
docker
```

7. 如果您选择 Docker 作为 Runner 的执行者，则会要求设置未在 `.gitlab-ci.yml` 中定义的用于该项目的默认镜像：

```bash
Please enter the Docker image (eg. ruby:2.1):
alpine:latest
```

## 单行注册命令

如果要使用非交互模式注册 Runner，可以使用 `register` 子命令或使用其等效的环境变量。`要查看所有register` 子命令的列表，请使用：

```bash
gitlab-runner register -h
```

要使用最常用的选项注册 Runner，您可以执行：

```bash
sudo gitlab-runner register \
  --non-interactive \
  --url "https://gitlab.com/" \
  --registration-token "PROJECT_REGISTRATION_TOKEN" \
  --executor "docker" \
  --docker-image alpine:latest \
  --description "docker-runner" \
  --tag-list "docker,aws" \
  --run-untagged="true" \
  --locked="false" \
  --access-level="not_protected"
```

如果您在 Docker 容器中运行 Runner，则 `register` 命令将如下所示：

```bash
docker run --rm -v /srv/gitlab-runner/config:/etc/gitlab-runner gitlab/gitlab-runner register \
  --non-interactive \
  --executor "docker" \
  --docker-image alpine:latest \
  --url "https://gitlab.com/" \
  --registration-token "PROJECT_REGISTRATION_TOKEN" \
  --description "docker-runner" \
  --tag-list "docker,aws" \
  --run-untagged="true" \
  --locked="false" \
  --access-level="not_protected"
```

## `[[runners]]` 配置模版文件

某些 Runner 配置设置无法使用环境变量或命令行选项进行设置。

例如：

* 环境变量不支持切片。
* 对于整个 Kubernetes 执行程序卷树的设置，命令行选项支持在国际上不可用。

对于由任何类型的自动化处理的环境，例如 GitLab Runner 官方 Helm 图表，这都是一个问题。在这些情况下，唯一的解决方案是在注册 Runner 之后手动更新 `config.toml` 文件。这不太理想，容易出错，而且不可靠。特别是当完成相同 Runner 安装的多个注册时。

使用配置模板文件可以解决此问题。

要使用配置模板文件，请将路径传递给文件以注册以下内容：

* `--template-config` 命令行选项。
* `TEMPLATE_CONFIG_FILE` 环境变量。

模版配置文件支持：

* 只有一个 `[[runners]]` 部分。
* 非全局选项。

...
