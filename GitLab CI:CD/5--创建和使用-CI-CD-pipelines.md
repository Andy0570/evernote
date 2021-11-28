原文链接：<https://gitlab.com/help/ci/pipelines.md>

> 注：**Pipelines** 在本文中译为“流水线”。**Jobs** 在本文中译为“作业”。

# 介绍

Pipelines 是持续集成、交付和部署的顶级组件。

Pipelines 包括：

* 定义要运行的作业。例如，运行代码编译或测试。
* 定义何时以及如何运行各个 Stages（阶段）。例如，测试仅在代码编译后运行。

如果有足够多的并发 Runners，则 Runners 并行执行同一阶段中的多个作业。

如果所有的作业都在同一个 stage 中：

* 成功，管道进入下一 stage 阶段。
* 失败，下一 stage 阶段没有（通常）执行，Pipelines 提前结束。

> 注意：如果您有一个来自 GitLab 的镜像存储库，您可能需要在项目的 **Settings > Repository > Pull from a remote repository > Trigger pipelines for mirror updates** 以启用并触发流水线。

## 简单的流水线示例：

举个例子，想象一个由四个阶段组成的流水线，按以下顺序执行：

* `build`，使用名为 `compile` 的作业。
* `test`，有两个名为 `test` 和 `test2` 的作业。
* `staging`，有一个名为 `deploy-to-stage` 的作业。
* `production`，有一项名为 `deploy-to-prod` 的作业。

# 流水线可视化

流水线可以是具有许多顺序执行和并行执行作业的复杂结构。

为了更容易理解流水线的流程，GitLab 具有用于查看流水线及其状态的流水线图。

流水线图可以以两种不同的方式显示，具体取决于您访问图表的页面。

> 注意：GitLab 在流水线图（如下所示）中显示时将这些阶段的名称大写。

## 常规流水线图

常规流水线图显示每个阶段的作业名称。当您在单个流水线页面上时，可以找到常规流水线图。例如：

![image](https://upload-images.jianshu.io/upload_images/2648731-4e0b8e02fc3760b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 迷你流水线图

迷你流水线图占用的空间更少，可以快速通知您所有作业是否通过或出现故障。导航到以下内容时可以找到迷你流水线图：

* 流水线索引页面。
* 单个提交页面。
* 合并请求页面。

迷你流水线图允许您查看单个提交的所有相关作业以及管道的每个阶段的最终结果。这使您可以快速查看失败并修复它。

迷你流水线图中的阶段是可折叠的。将鼠标悬停在它们上方，然后单击以展开其作业。


| 迷你流水线图 | 扩展迷你流水线图 |
| --- | --- |
| ![image](https://upload-images.jianshu.io/upload_images/2648731-c39b4afb066305b6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) | ![image](https://upload-images.jianshu.io/upload_images/2648731-63663fca5b9b4276.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) |


## 流水线图中的作业排序

作业排序取决于流水线图的类型。对于常规流水线图，作业按名称排序。对于迷你流水线图（在GitLab 9.0中引入），作业按严重性排序，然后按名称排序。

严重性的顺序是：

* failed
* warning
* pending
* running
* manual
* scheduled
* canceled
* success
* skipped
* created

例如：

![image](https://upload-images.jianshu.io/upload_images/2648731-23b75f02645b9da9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 如何计算流水线持续时间

给定流水线的总运行时间不包括重试和挂起（排队）时间。

每个作业都表示为一个 `Period（时期）`，包括：

* `Period#first`（当作业开始时）；
* `Period#last`（当作业结束时）；

一个简单的例子是：

* A (1, 3)
* B (2, 4)
* C (6, 7)

在示例中：

* A 从 1 开始到 3 结束。
* B 从 2 开始到 4 结束。
* C 从 6 开始到 7 结束。

在视觉上，它可以被视为：

```
0  1  2  3  4  5  6  7
   AAAAAAA
      BBBBBBB
                  CCCC
```

A，B 和 C 的并集是（1,4）和（6,7）。因此，总运行时间为：

```
(4 - 1) + (7 - 6) => 4
```

## 作业日志部分的扩展和折叠

作业日志分为可折叠或展开的部分。

在以下示例中：

* 扩展了两个部分并且可以折叠。
* 一个部分已折叠并且可以展开。

![image](https://upload-images.jianshu.io/upload_images/2648731-d30e0731cefb64e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 配置流水线

流水线及其组件作业和 stages 在各自项目的 `.gitlab-ci.yml` 文件中定义。

特别是：

* 作业是基本配置组件。
* 阶段是使用 `stages` 关键字定义的。

有关所有可用的配置选项，请参阅 [GitLab CI/CD 管道配置参考](https://docs.gitlab.com/ce/ci/yaml/README.html)。

## 设置和 schedules（计划）

除了通过 `.gitlab-ci.yml` 配置作业外，还可以通过 GitLab UI 获得其他配置选项：

* 每个项目的流水线设置。有关更多信息，请参阅[流水线设置](https://docs.gitlab.com/ce/user/project/pipelines/settings.html)。
* schedules 计划。有关更多信息，请参阅[流水线计划](https://docs.gitlab.com/ce/user/project/pipelines/schedules.html)。


## 分组作业

如果您有许多类似的作业，您的流水线图将变得冗长且难以阅读。

出于这个原因，类似的作业可以自动组合在一起。如果作业名称以某种方式格式化，它们将在常规管道图（而不是迷你图）中折叠为单个组。

如果您没有在其中看到重试或取消按钮，您将知道流水线何时对作业进行分组。将鼠标悬停在它们上方将显示分组作业的数量。单击以展开它们。

![image](https://upload-images.jianshu.io/upload_images/2648731-d5254fcfec32de3d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 配置分组

在流水线配置文件中，作业名称必须包含两个用以下之一分隔的数字（您甚至可以交替使用它们）：

* 空格；
* 斜杠（`/`）;
* 冒号（`:`）;

> 注意：更具体地说，它使用这个正则表达式：`\d+[\s:\/\\]+\d+\s*`。

### 分组如何工作

通过从左到右比较两个数字来对作业进行排序。您通常希望第一个是索引，第二个是总数。
例如，以下作业将分组在名为 `test` 的作业下：

* `test 0 3`
* `test 1 3`
* `test 2 3`

以下作业将分组在名为` test ruby` 的作业下：

* `test 1:2 ruby`
* `test 2:2 ruby`

以下作业也将分组在名为 `test ruby` 的作业下：

* 1/3 test ruby
* 2/3 test ruby
* 3/3 test ruby

## 合并请求的流水线

GitLab 支持配置仅针对合并请求运行的管道。有关更多信息，请参阅合并请求的管道。

## Badges 徽章

流水线状态和测试覆盖率报告标记可用于每个项目并可配置。

有关向项目添加流水线标记的信息，请参阅[流水线标记](https://docs.gitlab.com/ce/user/project/pipelines/settings.html#pipeline-badges)。

# 多项目流水线

不同项目的流水线可以组合在一起并可视化。

有关更多信息，请参阅[多项目流水线](https://docs.gitlab.com/ce/ci/multi_project_pipelines.html)。

# 使用流水线

通常情况下，流水线可以自动执行，一旦创建就不需要干预。但是，有些情况下您需要与流水线进行交互。这些记录如下。

## 手动执行流水线

可以使用预定义或手动指定的变量手动执行流水线。

如果需要在流水线的正常操作之外获取流水线的结果（例如，代码构建），则可以执行此操作。

要手动执行流水线：

1. 导航到您的项目的 **CI/CD > Pipelines**。
2. 单击 **Run Pipeline** 按钮。
3. 在 **Run Pipeline** 页面中：
    1. 在 **Create for** 字段中选择要运行流水线的分支。
    2. 输入流水线运行所需的任何环境变量。
    3. 单击 **Create Pipeline** 按钮。

管道将按配置执行作业。

## 访问流水线

您可以在项目的 **CI/CD > Pipelines** 页面下找到当前和历史运行的流水线。单击一个流水线，将显示为该流水线运行的作业。

![image](https://upload-images.jianshu.io/upload_images/2648731-c0e7c9e5307e0a9b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您还可以通过导航到 **Pipelines** 选项卡来访问合并请求的流水线。

## 访问单个作业

访问某一条流水线时，您可以看到该流水线下的相关作业。单击单个作业将显示其作业跟踪，并允许您：

* 取消作业。
* 重试作业。
* 删除作业跟踪。

## 查看作业运行失败的原因

当流水线出现故障或被允许失败时，有几个地方可以快速检查失败的原因：

* 在流水线图中，在流水线详细信息视图上。
* 在流水线小部件中，在合并请求和提交页面中。
* 在作业视图中，在作业的全局和详细视图中。

在任何地方，如果将鼠标悬停在失败的作业上，您可以看到失败的原因。

![image](https://upload-images.jianshu.io/upload_images/2648731-f7c25af446ca0b13.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从 GitLab 10.8 版本起，您还可以在“作业详细信息”页面上查看失败的原因。

## 流水线图的手动操作

使用 `when：manual` 参数配置的手动操作允许您在流水线中向前移动之前需要手动交互。您可以直接从流水线图中执行此操作。只需单击播放按钮即可执行该特定作业。例如，您的流水线自动启动，但需要手动操作才能部署到生产环境。在下面的示例中，生产阶段具有手动操作的作业。

![image](https://upload-images.jianshu.io/upload_images/2648731-86b99eeb59250396.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 运行手动作业时指定变量

运行手动作业时，您可以提供其他特定于作业的变量。您可以使用其他变量从要运行的手动作业的作业页面执行此操作。当您想要使用环境变量更改作业的执行时，这非常有用。

[图片上传失败...(image-972008-1566114335164)]

## 延迟流水线图中的作业

如果您不想立即运行作业，可以使用 `when：delayed` 参数来延迟作业执行一段时间。

这对于逐步推出新代码的定时增量转出特别有用。

例如，如果您开始推出新代码并且：

* 用户不会遇到麻烦，GitLab 可以自动完成从 0％ 到 100％ 的部署。
* 用户遇到新代码时出现问题，您可以通过取消流水线并回滚到上一个稳定版本来停止定时增量部署。

![image](https://upload-images.jianshu.io/upload_images/2648731-2656e3bc6a478618.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 使用 API

GitLab 提供 API 端点：

* 执行基本功能。有关更多信息，请参阅 [Pipelines API](https://docs.gitlab.com/ce/api/pipelines.html)。
* 维护流水线计划。有关更多信息，请参阅 [流水线计划 API](https://docs.gitlab.com/ce/api/pipeline_schedules.html)。
* 触发流水线运行。有关更多信息，请参阅：
    * [Triggering pipelines through the API](https://docs.gitlab.com/ce/ci/triggers/README.html).
    * [Pipeline triggers API](https://docs.gitlab.com/ce/api/pipeline_triggers.html).


## 在一个阶段中执行多个手动操作

可以使用 “Play all manual” 按钮同时启动单个阶段中的多个手动操作。用户单击此按钮后，将触发每个单独的手动操作并刷新到更新状态。

此功能仅适用于：

* 对于至少具有开发者访问权限的用户。
* 如果 stage 包含手动操作。

# 受保护分支的安全性

在受保护的分支上执行流水线时，将强制执行严格的安全模型。

仅当允许用户合并或推送该特定分支时，才允许在受保护的分支上执行以下操作：

* 手动运行流水线（使用 Web UI 或流水线 API）。
* 运行预定的流水线。
* 使用触发器运行流水线。
* 触发现有流水线上的手动操作。
* 重试或取消现有作业（使用 Web UI 或流水线 API）。

标记为受保护的变量只能在受保护的分支上运行的作业访问，从而防止不受信任的用户无意中访问敏感信息（如部署凭据和令牌）。

标记为受保护的运行程序只能在受保护的分支上运行作业，从而避免在受保护的运行程序上执行不受信任的代码，并保留部署密钥和其他凭据不被无意访问。为了确保在受保护的 Runner 上执行的作业不会使用常规 Runner，必须相应地对其进行标记。




