原文链接：<https://docs.gitlab.com/ee/ci/yaml/>

在一个项目中，GitLab CI/CD 流水线通过使用名为 `.gitlab-ci.yml` 的 **YAML** 格式文件进行配置。

`.gitlab-ci.yml` 文件定义了流水线的文件结构和执行顺序，并确定：
* 使用 GitLab Runner 执行什么。
* 遇到特定条件时要做出哪些决定。例如，当进程成功或失败时。

本主题介绍 CI/CD 流水线配置。有关其他 CI/CD 配置信息，请参阅：

* [GitLab CI/CD Variables](https://docs.gitlab.com/ce/ci/variables/README.html)，用于配置流水线运行的环境。
* [GitLab Runner advanced configuration](https://docs.gitlab.com/runner/configuration/advanced-configuration.html)，用于配置 GitLab Runner。

我们也提供配置流水线的完整示例：

* 有关 GitLab CI 的快速介绍，请按照我们的[快速入门指南](https://docs.gitlab.com/ce/ci/quick_start/README.html)进行操作。
* 有关示例的集合，请查看 [GitLab CI/CD Examples](https://docs.gitlab.com/ce/ci/examples/README.html)。
* 查看企业中使用的大型 `.gitlab-ci.yml` 文件，请查看 [`.gitlab-ci.yml` file for `gitlab-ce`](https://gitlab.com/gitlab-org/gitlab-ce/blob/master/.gitlab-ci.yml)。

> 注意：如果您有一个从 GitLab 拉取过来的镜像存储库，您可能需要在项目的 **Settings > Repository > Pull from a remote repository > Trigger pipelines for mirror updates** 以启用并触发流水线。

# 介绍

流水线的配置从作业开始。作业是 `.gitlab-ci.yml` 文件中最基本的元素。

作业是：
* 定义它们应在什么条件下执行的约束。
* 具有任意名称的顶级元素，并且必须至少包含 `script` 项。
* 不限制可以定义的作业数量。

例如：

```yaml
job1:
  script: "execute-script-for-job1"

job2:
  script: "execute-script-for-job2"
```

上面的示例是最简单的 CI/CD 配置，包含两个单独的作业，其中每个作业执行不同的命令。当然，命令可以直接执行代码（`./configure;make;make install`）或者运行位于存储库中的脚本文件（`test.sh`）。

作业会被 Runner 选中并在 Runner 的所在的环境中执行。重要的是，每项作业都是相互独立运作的。

## 验证 `.gitlab-ci.yml` 文件

GitLab CI 的每个实例都有一个名为 Lint 的嵌入式调试工具，它可以验证 `.gitlab-ci.yml` 文件的内容。您可以在项目命名空间的 `ci/lint` 页面下找到 Lint。例如，<https://gitlab.example.com/gitlab-org/project-123/-/ci/lint>。

## 不可用的作业名称

每个作业都必须具有一个唯一的名称，但有一些**保留的关键字不能用于作业名称**：

* `image`
* `services`
* `stages`
* `types`
* `before_script`
* `after_script`
* `variables`
* `cache`

## 使用保留关键字

如果在使用特定值时遇到验证错误（例如，`true` 或 `false`），请尝试：

* 引用他们。
* 将它们更改为其他形式，例如，`/bin/true`。


# 配置参数

作业是通过一系列用于定义作业行为的参数来组成的。
下表列出了作业的可用参数：


| 关键字 | 描述 |
| --- | --- |
| `script` | 由 Runner 执行的 Shell 脚本。 |
| `image` | 使用 docker 镜像。以下这些也可用：`image:name` 和 `image:entrypoint` |
| `services` | 使用 docker 服务镜像。以下这些也可用：`services:name` 、`services:alias`、`services:entrypoint` 和 `services:command` |
| `before_script` | 包括一组在作业被执行之前执行的命令 |
| `after_script` | 包括一组在作业被执行之后执行的命令 |
| `stages` | 定义流水线中的阶段 |
| `stage` | 定义一个作业阶段（默认：`test`） |
| `only` | 创建作业的限制条件。以下这些也可用：`only:refs`, `only:kubernetes`, `only:variables` 和 `only:changes` |
| `except` | 不创建作业的限制条件。以下这些也可用：`except:refs`, `except:kubernetes`, `except:variables` 和 `except:changes` |
| `tags` | 用于选择 Runner 的标签列表。 |
| `allow_failure` | 允许作业执行失败。失败的作业无助于提交状态 |
| `when` | 什么时候执行作业。以下这些也可用：`when:manual` 和 `when:delayed` |
| `environment` | 作业被部署的环境的名称。以下这些也可用：`environment:name`, `environment:url`, `environment:on_stop` 和 `environment:action` |
| `cache` | 用于后续运行的缓存文件列表。以下这些也可用：`cache:paths`, `cache:key`, `cache:untracked` 和 `cache:policy` |
| `artifacts` | 成功附加到作业中的文件和目录列表。以下这些也可用：`artifacts:paths`, `artifacts:name`, `artifacts:untracked`, `artifacts:when`, `artifacts:expire_in`, `artifacts:reports` 和 `artifacts:reports:junit` <br> 在 [企业版 GitLab](https://about.gitlab.com/pricing/) 中，这些是可用的：`artifacts:reports:codequality`, `artifacts:reports:sast`, `artifacts:reports:dependency_scanning`, `artifacts:reports:container_scanning`, `artifacts:reports:dast`, `artifacts:reports:license_management`, `artifacts:reports:performance` 和 `artifacts:reports:metrics` |
| `dependencies` | 作业被执行时所要依赖的其他作业，以便您可以在它们之间传递 artifacts。 |
| `coverage` | 设置给定作业执行时的代码覆盖率要求 |
| `retry` | 在发生故障时，自动重试作业的次数和时机 |
| `parallel` | 应该并行运行多少个作业实例 |
| `trigger` | 定义下游流水线触发器。 |
| `include` | 允许此作业包含外部 YAML 文件。以下这些也可用：`include:local`, `include:file`, `include:template` 和 `include:remote` |
| `extends` | 此作业将继承的配置条目 |
| `pages` | 上传作业执行的结果以用于 GitLab Pages。 |
| `variables` | 在作业级别中定义的作业变量  |

> 注意：参数 `types` 和 `type` 已失效。


# 设置默认参数

使用 `default:` 关键字可以将某些参数全局设置为所有作业都可用的默认值。特定于作业中的相同参数配置可以覆盖默认参数。

可以在 `default:` 块中定义以下作业参数：

* `image`
* `services`
* `before_script`
* `after_script`
* `cache`

在以下示例中，`ruby:2.5` 镜像被设置为除 `rspec 2.6` 作业（该作业使用`ruby:2.6` 映像）之外的所有作业的默认镜像。

```yaml
default:
  image: ruby:2.5

rspec:
  script: bundle exec rspec

rspec 2.6:
  image: ruby:2.6
  script: bundle exec rspec
```

# 参数详情

以下是用于配置 CI/CD 流水线的参数的详细说明。

...

