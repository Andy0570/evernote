原文链接：<https://docs.gitlab.com/ee/ci/runners/README.html>

## Configuring GitLab Runners


在 GitLab CI 中，Runners 运行 `.gitlab-ci.yml` 文件中定义的代码。它们是隔离（虚拟）机器，通过GitLab CI的协调器API获取作业。

Runner 可以特定于某个项目，也可以为 GitLab CI 中的任何项目提供服务。为所有项目提供服务的 Runner 称为共享 Runner。

理想情况下，GitLab Runner 不应与 GitLab 安装在同一台机器上。阅读 [requirements documentation
](https://gitlab.com/help/install/requirements.md#gitlab-runner) 以获取更多信息。

### 共享、特定和分组 Runners

安装 Runner 后，您可以将其注册为共享或特定 Runner。如果您具有 GitLab 实例的管理员访问权限，则只能注册共享 Runner。

共享和特定 Runner 之间的主要区别是：

* **Shared Runners** 对于多个项目之间具有类似要求的作业非常有用。您可以拥有一个或多个可以处理多个项目的 Runners，而不是让多个 Runner 为支持多个项目。这样可以更轻松地维护和更新它们。Shared Runners 使用 [公平使用队列](https://gitlab.com/help/ci/runners/README.md#how-shared-runners-pick-jobs) 方式来处理作业。与使用 FIFO （先进先出）队列的特定 Runner 相比，这可以防止项目创建数百个可能导致吃掉所有可用共享 Runners 资源的作业的情况（一个项目创建了数百个作业，消耗了所有可用的 Runners）。
* **Specific Runners** 对于具有特殊要求的作业或具有特定需求的项目非常有用。如果某个作业有特定的要求，您可以考虑设置特定 Runner，而不必为所有 Runner 执行此操作。例如，如果要部署某个项目，可以设置特定 Runner 以获得正确的凭据。在这种情况下，[usage of tags](https://gitlab.com/help/ci/runners/README.md#using-tags) 可能是有用的。特定 Runner 运行时使用 FIFO 队列处理作业。
* **Group Runners**：当您在一个分组下有多个项目并且希望所有项目都可以访问同一组 Runners 时，Group Runners 非常有用。Group Runners 使用 FIFO 队列处理作业。

特定 Runner 仅针对指定的项目运行。共享 Runner 可以为已在 **Settings > CI/CD** 启用 “**Allow shared Runners**”选项的所有项目运行作业。

对 CI 活动有很高要求的项目也可以从使用特定 Runners 中受益。通过特定 Runner ，您可以保证 Runner 不被另一个项目的作业所阻碍。

您也可以设置一个特定 Runner 被多个项目使用。与共享 Runner 的区别在于您必须在每个项目下明确启用该特定 Runner，以便该特定 Runner 能够运行其作业。

特定 Runner 不会自动与分叉项目共享。fork 会复制克隆存储库的 CI 设置（作业，允许共享等）。

## 注册共享 Runner

如果您是 GitLab 实例的管理员，则只能注册共享 Runner。

1. 在 `admin/runners` 页面上获取 shared-Runner 的 token 值。

![image](https://upload-images.jianshu.io/upload_images/2648731-134800de0b3c2005.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. [注册 Runner](https://docs.gitlab.com/runner/register/)

默认情况下，共享 Runner 从 GitLab 8.2 开始启用，但可以在项目的 **Settings ➔ CI/CD** 页面下，点击 **Disable shared Runners** 按钮以禁用共享 Runner。以前版本的 GitLab 默认共享Runner 是不可用的。

## 注册特定 Runner

注册特定 Runner 可以通过两种方式完成：

1. 使用项目注册令牌创建 Runner。
2. 将共享 Runner 转换为特定 Runner（单向，仅限管理员使用）。

### 使用项目注册令牌注册特定 Runner

要创建一个没有 GitLab 实例管理员权限的特定 Runner，请访问要在 GitLab 中使 Runner 工作的项目：

1. 转到 **Settings > CI/CD** 页面下获取令牌。
2. [注册 Runner](https://docs.gitlab.com/runner/register/)

## 注册分组 Runner
...


## 锁定特定 Runner，使其无法为其他项目启用

...



## 将 Runner 分配给另一个项目
...

## 受保护的 Runner
...

## 手动清除 Runners 缓存
...

## 共享 Runner 如何选择作业
...

## 有效地使用共享 Runners
...

## Runner 中的攻击向量
...

## 确定 Runner 的 IP 地址

了解 Runner 的 IP 地址可能很有用，这样您就可以解决该 Runner 的问题。GitLab 通过查看在轮询作业时对 GitLab 发出的 HTTP 请求的来源来存储和显示 IP 地址。IP 地址可以始终保持最新，因此如果 Runner 的 IP 地址更改了，它将在 GitLab 中自动更新。

共享 Runner 和特定 Runner 的 IP 地址可以在不同的地方找到。

### 共享 Runner

要查看共享 Runner 的 IP 地址，您必须具有 GitLab 实例的管理员访问权限。要确定这点：

1. 查看 **Admin area ➔ Overview ➔ Runners**。
2. 在表中查找 Runner，您应该可以看到 “IP Address”列。

![image](https://upload-images.jianshu.io/upload_images/2648731-66e4dc83d808378e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 特定 Runner

您可以通过以下方式找到特定项目的 Runner 的 IP 地址：

1. 查看项目的 **Settings ➔ CI/CD** 页面。
2. 找到 Runner 并单击它的 ID，它将链接并导航到详细信息页面。
3. 在详细信息页面上，您应该看到“IP地址”行。

![image](https://upload-images.jianshu.io/upload_images/2648731-4c22f406e5f8b333.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




