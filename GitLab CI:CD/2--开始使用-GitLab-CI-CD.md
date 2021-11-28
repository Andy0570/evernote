原文链接：<https://gitlab.com/help/ci/quick_start/README>

## 开始使用 GitLab CI/CD

> 注意：从 8.0 版本开始，GitLab [持续集成](https://about.gitlab.com/product/continuous-integration/)（CI）完全集成到 GitLab 中，并在所有项目中默认启用。

> 注意：请记住，只有项目维护者和管理员才有权访问项目的设置。

> 注意：从 Jenkins 转到 GitLab？查看将您已有的 pipelines 转换为我们的格式的[参考](https://docs.gitlab.com/ce/ci/jenkins/index.html)。

GitLab 提供持续集成服务。如果你将 `.gitlab-ci.yml` 文件添加到存储库的根目录下，并将 GitLab 项目配置为使用 [**Runner**](https://docs.gitlab.com/ce/ci/runners/README.html)，则每次提交或推送都会触发 CI [pipelines](https://gitlab.com/help/ci/pipelines.md)（注：pipelines 官方译为“流水线”，本文中亦以此称之）。

`.gitlab-ci.yml` 文件告诉 GitLab runner 该做什么。默认情况下，它运行一个包含三个 [stages（阶段）](https://gitlab.com/help/ci/yaml/README.md#stages) 的流水线：`build`、`test` 和 `deploy`。你不需要使用所有这三个 stage；没有作业的 stage 会被忽略。

如果一切运行正常（没有非零返回值），你会看到一个与提交相关的漂亮的绿色复选标记 ✅。这样你可以在查看代码之前，轻松查看提交是否导致任何测试失败问题。

大多数项目使用 GitLab 的 CI 服务来运行测试套件，以便开发人员在修改某些内容时立即获得反馈。

使用持续交付和持续部署将测试代码自动部署到测试和生产环境的趋势越来越明显。

简而言之，实现一个可运行的 CI 服务所需的步骤可归纳为：

* 将 `.gitlab-ci.yml` 文件添加到存储库的根目录下。
* 配置一个 Runner。

自此，每次向你的 Git 存储库执行 `push` 操作时，Runner 将自动启动流水线，流水线将显示在项目的 **Pipelines** 页面下。

---

本指南假设您拥有：

* 一个正在运行的大于 8.0 版本的 GitLab 实例或正在使用 [GitLab.com](https://gitlab.com/)。
* 一个存储在 GitLab 中的项目，并且您希望使用 CI 服务。
* 可以访问该项目的维护者或项目所有者。

让我们分解它并努力解决 GitLab CI 难题。

## 创建一个 `.gitlab-ci.yml` 文件

在创建 `.gitlab-ci.yml` 文件之前，让我们先简要解释一下这是什么。

### 什么是 `.gitlab-ci.yml`

您可以在 `.gitlab-ci.yml` 文件中配置 CI 对项目执行的操作。该文件位于存储库的根目录中。

当向您的存储库执行任何 `push` 操作时，GitLab 将查找 `.gitlab-ci.yml` 文件，并根据该文件提交的内容在 **Runners** 上启动一个作业。

因为 `.gitlab-ci.yml` 存储在 Git 存储库中并且受版本控制，旧版本仍然可以成功构建，分支可以轻松地使用 CI，分支可以具有不同的流水线和作业，并且您有的单一可信的来源用于持续集成。您可以在我们的 [博客](https://about.gitlab.com/2015/05/06/why-were-replacing-gitlab-ci-jobs-with-gitlab-ci-dot-yml/) 中详细了解我们使用 `.gitlab-ci.yml` 的原因。

### 创建一个简单的 `.gitlab-ci.yml` 文件

> 注意：`.gitlab-ci.yml` 是一个 **YAML** 格式的文件，因此您必须特别注意缩进。始终使用空格，而不是制表符。

您需要在存储库的根目录中创建名为 `.gitlab-ci.yml` 的文件。下面是 Ruby on Rails 项目的示例。

```yaml
image: "ruby:2.5"

before_script:
  - apt-get update -qq && apt-get install -y -qq sqlite3 libsqlite3-dev nodejs
  - ruby -v
  - which ruby
  - gem install bundler --no-document
  - bundle install --jobs $(nproc)  "${FLAGS[@]}"

rspec:
  script:
    - bundle exec rspec

rubocop:
  script:
    - bundle exec rubocop
```

这是最简单的配置，适用于大多数 Ruby 应用程序：

1. 定义两个作业 `rspec` 和 `rubocop`（名称是任意的）分别表示要执行的两种不同命令。
2. 在每个 job 作业被执行之前，会先执行 `before_script` 中定义的命令。

`.gitlab-ci.yml` 文件定义了具有运行方式和时间约束的作业集。作业被定义为具有名称的顶级元素（在我们的示例中为 `rspec` 和 `rubocop`），并且始终必须包含 `script` 关键字。 Jobs 用于创建 jobs 作业，然后由 Runners 选择并在 Runner 的环境下执行。

重要的是每项 job 作业都是相互独立运作的。

如果要检查项目中的 `.gitlab-ci.yml` 配置是否有效，则在项目命名空间的 `/-/ci/lint` 页面下有一个 Lint 工具。您还可以在你的项目的 **CI/CD ➔ Pipelines** 和 **Pipelines ➔ Jobs** 页面下找到 “CI Lint” 按钮。

有关更多信息和完整的.gitlab-ci.yml语法，请阅读 [GitLab CI/CD 管道配置参考文档](https://docs.gitlab.com/ce/ci/yaml/README.html) 。

## 将 `.gitlab-ci.yml` 推送到 GitLab

一旦你创建了 `.gitlab-ci.yml` 文件，你应该将它添加到你的 Git 存储库并将其推送到 GitLab。

```bash
git add .gitlab-ci.yml
git commit -m "Add .gitlab-ci.yml"
git push origin master
```

现在，如果您转到 “**Pipelines**（流水线）”页面，您将看到 Pipelines 处于待处理状态。

> 注意：如果您有一个来自 GitLab 的镜像存储库，您可能需要在项目的 **Settings > Repository > Pull from a remote repository > Trigger pipelines for mirror updates** 以启用并触发流水线。

您也可以转到 “**Commits**” 页面，注意提交 SHA 哈希值旁边的小暂停图标。

![image](https://upload-images.jianshu.io/upload_images/2648731-b47b1dc6d1b8d744.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击它，您将被重定向到该特定提交的 job 页面。

![image](https://upload-images.jianshu.io/upload_images/2648731-d89d963d4a9c6fad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

请注意，有一个挂起的作业以我们在 `.gitlab-ci.yml` 中写的命名。橙色的标识 “**stuck**（卡住）”表示尚未为此作业配置 Runner。


## 配置 Runner

在 GitLab 中，Runners 运行您在 `.gitlab-ci.yml` 中定义的作业。Runner 可以是虚拟机，VPS，裸机，docker 容器甚至是容器集群。GitLab 和 Runners 通过 API 进行通信。所以唯一的要求是部署 Runner 的机器可以访问 GitLab 服务器。

Runner 可以特定于某个项目，也可以同时为 GitLab 中的多个项目提供服务。如果它服务于所有项目，则称为 *Shared Runner*。

在 [Runners](https://docs.gitlab.com/ce/ci/runners/README.html) 文档中查找有关不同 Runners 的更多信息。

您可以通过转到 **Settings ➔ CI/CD** 找到是否为您的项目分配了任何 Runner。设置一个 Runner 既简单又直接。由 GitLab 支持的官方 Runner 是用 Go 语言编写的，其文档可以在 <https://docs.gitlab.com/runner/> 中找到。

要获得 Runner 功能，您需要执行以下两个步骤：

1. [安装它](https://docs.gitlab.com/runner/install/)。
2. [配置它](https://docs.gitlab.com/ce/ci/runners/README.html#registering-a-specific-runner)。

按照上面的链接设置您自己的 Runner 或使用 Shared Runner，如下一节所述。

设置好 Runner 后，您应该在项目的 **Settings ➔ CI/CD** 中的 Runners 页面上看到它。

![image](https://upload-images.jianshu.io/upload_images/2648731-9a6c5bf5f9fb7349.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 共享 Runners

如果您使用 GitLab.com，您可以使用 GitLab Inc. 提供的共享 Runners。

这些是在 GitLab 的基础架构上运行的特殊虚拟机，可以构建任何项目。

要启用**共享 Runners**，您必须转到项目的 **Settings ➔ CI/CD**，然后单击 **Enable shared runners**。

## 查看流水线和作业的状态

成功配置好 Runner 后，您应该会看到上次提交的状态从挂起更改为运行、成功或失败。您可以转到项目的 “**Pipelines**” 页面来查看所有的流水线。

![image](https://upload-images.jianshu.io/upload_images/2648731-2f83ce51c65699f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

或者您可以通过 **Pipelines ➔ Jobs** 页面查看所有的作业。

![image](https://upload-images.jianshu.io/upload_images/2648731-0eb959b942644684.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过单击作业的状态，您将能够看到该作业的日志。这对于诊断工作失败或行为与预期不同的原因非常重要。

![image](https://upload-images.jianshu.io/upload_images/2648731-ce51ab23f01040bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您还可以在 GitLab 的各个页面中查看任何提交的状态，例如 **Commits** 提交和 **Merge requests** 合并请求。










