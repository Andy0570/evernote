原文链接：<https://docs.gitlab.com/ce/ci/introduction/>

在本文档中，我们将概述**持续集成**（Continuous Integration），**持续交付**（Continuous Delivery）和**持续部署**（Continuous Deployment）的概念，以及 GitLab CI/CD 的介绍。

> 开箱即用的管理系统可以将维护工具链所花费的时间减少 10％ 以上。观看我们的“[精通持续软件开发](https://about.gitlab.com/webcast/mastering-ci-cd/)”网络广播，了解连续方法以及 GitLab 内置 CI 如何帮助您简化和扩展软件开发。

# CI/CD 方法简介

软件开发的持续开发方法基于自动执行脚本，以最大限度地减少在开发应用程序时引入错误的可能性。从新代码的开发到部署，仅需较少的人为干预甚至根本不需要人工干预。

它涉及在每次小迭代中不断构建，测试和部署更改后的代码，从而减少基于先前有缺陷或失败的版本来开发新版本的可能性。

持续开发方法有三种主要方法，每种方法都根据最适合您的策略进行应用。

## 持续集成

考虑一个应用程序，其代码存储在 GitLab 中的 Git 存储库中。开发人员每天多次推送代码更改。对于每次推送到存储库，您都可以创建一组脚本来**自动构建和测试应用程序**，从而减少向应用程序引入错误的可能性。

这种做法被称为**持续集成**；对于提交给应用程序的每个更改 - 甚至是开发分支 - 它都是自动且连续地构建和测试的，确保所引入的更改通过您为应用程序建立的所有测试，指南和代码合规性标准。

GitLab 本身就是使用持续集成作为软件开发方法的一个例子。对于项目的每次推送，都会有一组脚本来检查代码。

## 持续交付

持续交付是持续集成的下一个步骤。您的应用程序不仅在推送到代码库的每个代码更改时都构建和测试，而且，作为一个额外的步骤，它也会**连续部署**，尽管部署是手动触发的。

此方法可确保自动检查代码，但需要人工干预才能手动并策略性地触发更改后的部署。

## 持续部署

持续部署也是持续集成的下一步，类似于持续交付。不同之处在于，您不必手动部署应用程序，而是将其设置为**自动部署**。完全不需要人工干预就可以部署您的应用程序。


# GitLab CI/CD 简介

GitLab CI/CD 是 GitLab 内置的强大工具，允许您将所有连续方法（持续集成，交付和部署）应用于您的软件，而无需集成或使用第三方应用程序。

## GitLab CI/CD 如何工作

要使用 GitLab CI/CD，您只需要一个托管在 Git 存储库中的应用程序代码库，并在一个名为 `.gitlab-ci.yml` 的文件中指定构建，测试和部署脚本，该文件位于你的存储库的根路径中。

在此文件中，您可以定义要运行的脚本，定义包含和缓存依赖项，选择要按顺序运行的命令以及要并行运行的命令，定义要部署应用程序的位置，以及指定是否将要自动运行脚本或手动触发任何脚本。熟悉 GitLab CI/CD 后，您可以在配置文件中添加更多高级步骤。

要向该文件添加脚本，您需要按照适合您的应用程序的顺序组织它们，并且与您希望执行的测试一致。要想象可视化过程，请假设您添加到配置文件中的所有脚本与您在计算机终端上运行的命令相同。

将 `.gitlab-ci.yml` 配置文件添加到存储库后，GitLab 将检测到它并使用名为 GitLab Runner 的工具运行脚本，该工具与您的终端类似。

脚本被分组到 **jobs** 作业中，它们一起组成一个 **pipeline** 流水线。`.gitlab-ci.yml` 文件的极简示例可能包含：

```yaml
before_script:
  - apt-get install rubygems ruby-dev -y

run-test:
  script:
    - ruby --version
```

`before_script` 属性将在运行任何内容之前为您的应用程序安装依赖项，而名为 `run-test` 的 **job** 作业将打印当前系统的 Ruby 版本。它们两者构成了在每次推送到存储库的任何分支时触发的 **pipeline** 流水线。

GitLab CI/CD 不仅可以执行您设置的 **jobs** 作业，还可以显示执行过程中发生的情况，如终端中所示：

![image](https://upload-images.jianshu.io/upload_images/2648731-c3fd40617543e9b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您可以为应用创建计划（strategy），GitLab 会根据您定义的内容自动运行 **pipeline** 流水线。您的流水线状态也由 GitLab 显示：

![image](https://upload-images.jianshu.io/upload_images/2648731-454de1816566c863.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

最后，如果出现任何问题，您可以轻松回滚（[roll back](https://docs.gitlab.com/ce/ci/environments.html#retrying-and-rolling-back)）所有更改：

![image](https://upload-images.jianshu.io/upload_images/2648731-4bbfc1f61e74db75.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 基本的 CI/CD 工作流程

请考虑以下示例，了解 GitLab CI/CD 如何适应常见的开发工作流程。

假设您已在一个问题中讨论过代码实现，并在本地处理您提出的更改。将提交推送到 GitLab 远程存储库中的 feature 分支后，将触发为项目设置的 CI/CD 流水线。这时，GitLab CI/CD：

* 运行自动脚本（顺序或并行）
    * 构建并测试您的应用。
    * 在 [Review Apps](https://docs.gitlab.com/ce/ci/review_apps/index.html) 下预览每个合并请求的更改，就像您在 localhost 中看到的那样。 


一旦您对实施感到满意：

* 让您的代码提交审核并等待被批准。
* 将 feature 分支合并到 default 分支。
    * GitLab CI/CD 会自动将更改部署到生产环境中。 
* 最后，如果出现问题，您和您的团队可以轻松地将其回滚。

![image](https://upload-images.jianshu.io/upload_images/2648731-18e389cdc9bc5ab1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

GitLab CI/CD 的功能不仅限于此，但这个工作流程体现了 GitLab 跟踪整个过程的能力，而无需任何外部工具来交付您的软件。而且，最有用的是，您可以通过 GitLab UI 可视化所有步骤。

## 深入了解 CI/CD 基本工作流程

如果我们深入了解基本工作流程，我们可以在 DevOps 生命周期的每个阶段看到 GitLab 中可用的功能，如下图所示。

![image](https://upload-images.jianshu.io/upload_images/2648731-94e8b2d1c681c4a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果您从左到右查看图像，您将看到 GitLab 为每个阶段（验证，打包，发布）提供的一些功能。

1. **验证**（Verify）
    * 使用持续集成自动构建和测试您的应用程序。
    * 通过 [GitLab Code Quality](https://docs.gitlab.com/ce/user/project/merge_requests/code_quality.html) 分析您的源代码质量。
    * 使用 [浏览器性能测试](https://docs.gitlab.com/ce/user/project/merge_requests/browser_performance_testing.html) 确定代码更改对性能的影响。
    * 执行一系列测试，例如 [Container Scanning](https://docs.gitlab.com/ce/user/application_security/container_scanning/index.html)，[Dependency Scanning](https://docs.gitlab.com/ce/user/application_security/dependency_scanning/index.html) 和 [JUnit tests](https://docs.gitlab.com/ce/ci/junit_test_reports.html)。
    * 使用 [Review Apps](https://docs.gitlab.com/ce/ci/review_apps/index.html) 部署您的更改，以预览每个分支上的应用程序更改。
2. **打包**（Package）：
    * 使用 [Container Registry](https://docs.gitlab.com/ce/user/project/container_registry.html) 存储 Docker 镜像。
    * 使用 [NPM Registry](https://docs.gitlab.com/ce/user/project/packages/npm_registry.html) 存储 NPM 包。
    * 使用 [Maven Repository](https://docs.gitlab.com/ce/user/project/packages/maven_repository.html) 存储 Maven 工件。
3. **发布**（Release）:
    * 持续部署，自动将您的应用部署到生产环境。
    * 持续交付，手动点击将您的应用部署到生产环境。
    * 使用 [GitLab Pages](https://docs.gitlab.com/ce/user/project/pages/index.html) 部署静态网站。
    * 只向部分 pod 提供功能，并允许一部分用户群使用 [Canary Deployments](https://docs.gitlab.com/ce/user/project/canary_deployments.html) 访问临时部署的功能。
    * 在 [Feture Flags](https://docs.gitlab.com/ce/user/project/operations/feature_flags.html) 后面部署功能。
    * 使用 [GitLab Releases](https://docs.gitlab.com/ce/user/project/releases/index.html) 向任何 Git 标签添加发行说明。
    * 通过 [Deploy Boards](https://docs.gitlab.com/ce/user/project/deploy_boards.html) 查看在 Kubernetes 上运行的每个 CI 环境的当前运行状况和状态。
    * 使用 [Auto Deploy](https://docs.gitlab.com/ce/topics/autodevops/index.html#auto-deploy) 将应用程序部署到 Kubernetes 集群中的生产环境。

使用 GitLab CI/CD，您还可以：

* 使用 [Auto DevOps](https://docs.gitlab.com/ce/topics/autodevops/index.html) 轻松设置应用程序的整个生命周期。
* 将您的应用程序部署到不同的环境中。
* 安装你自己的 [GitLab Runner](https://docs.gitlab.com/runner/)。
* [安排流水线](https://docs.gitlab.com/ce/user/project/pipelines/schedules.html)。
* 使用 [Security Test reports](https://docs.gitlab.com/ce/user/project/merge_requests/index.html#security-reports-ultimate) 检查应用程序漏洞。

要查看所有 CI/CD 功能，请导航回 CI/CD 索引。

观看视频 [GitLab CI Live Demo](https://www.youtube.com/watch?v=pBe4t1CD8Fc)，深入了解 GitLab CI/CD。

## 首次设置 GitLab CI/CD

要开始使用 GitLab CI/CD，您需要熟悉 `.gitlab-ci.yml` 配置文件语法及其属性。

本文档 [介绍 GitLab CI/CD 在 GitLab Pages 范围内的概念](https://docs.gitlab.com/ce/user/project/pages/getting_started_part_four.html)，用于部署静态网站。虽然它适用于想要从头开始编写自己的 Pages 脚本的用户，但它也可以作为 GitLab CI/CD 设置过程的介绍。它涵盖了编写 CI/CD 配置文件的第一个常规步骤，因此我们建议您通读它以了解 GitLab 的 CI/CD 逻辑，并学习如何为任何应用程序编写自己的脚本（或调整现有脚本）。

要深入了解 GitLab 的 CI/CD 配置选项，请查看 [`.gitlab-ci.yml` 完整参考](https://docs.gitlab.com/ce/ci/yaml/README.html)。

