原文链接：<https://gitlab.com/help/ci/enable_or_disable_ci.md>

## How to enable or disable GitLab CI/CD

要有效使用 GitLab CI/CD，您需要：
* 一个存储在项目根目录下的有效 `.gitlab-ci.yml` 文件。
* 一个正确设置的 [runner](https://gitlab.com/help/ci/runners/README.md)。

您可以阅读我们的 [快速入门指南](https://gitlab.com/help/ci/quick_start/README.md) 以帮助您入门 GitLab CI/CD。

如果您使用的是 Jenkins 或 Drone CI 等外部 CI/CD 服务，建议您禁用 GitLab CI/CD，以免与提交状态 API 发生冲突。

GitLab CI/CD 功能在项目的 `/pipelinelines`（流水线）和 `/jobs`（作业）目录下。禁用项目中的 GitLab CI/CD 不会删除任何以前配置的作业。实际上，你仍然可以访问 `/pipelinelines`（流水线）和 `/jobs`（作业）页面，尽管它在左侧边栏菜单中是隐藏的。

GitLab CI/CD 在新安装时是默认启用的，当然，你也可以禁用它：

* 在单个项目的设置下单独设置。
* 对于从 source 源或通过 Omnibus 安装的 GitLab，分别修改 `gitlab.yml` 和 `gitlab.rb` 中的设置，从而实现全站范围下的管理设置。

## 单个项目下的设置

可以在项目的 **Settings > General > Permissions** 下找到启用或禁用 GitLab CI/CD 的设置。选择“禁用”，“仅限团队成员”或“有权访问的所有人”选项之一，然后点击**保存更改**以使设置生效。

> 注：如果是私有项目，权限只可以配置为 `Only Project Members`（仅限团队成员）。

![image](https://upload-images.jianshu.io/upload_images/2648731-80d64461b5472f22.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 全站范围下的管理设置

对于从源和 Omnibus 安装的 GitLab，您可以通过分别修改 `gitlab.yml` 和 `gitlab.rb` 中的设置，实现全站范围下的管理设置。

有两点需要注意：

* 禁用 GitLab CI/CD 只会影响新创建的项目。在此修改之前启用它的项目将像以前一样工作。
* 即使您禁用 GitLab CI/CD，用户仍然可以在单独的项目设置中开启。

对于源代码安装，请使用编辑器打开 `gitlab.yml` 并将 `builds` 设置为 `false`：

```yaml
## Default project features settings
default_projects_features:
  issues: true
  merge_requests: true
  wiki: true
  snippets: false
  builds: false
```

保存文件并重新启动 GitLab：

```bash
sudo service gitlab restart
```

对于 Omnibus 安装的 GitLab，请编辑 `/etc/gitlab/gitlab.rb` 并添加以下行：

```bash
gitlab_rails['gitlab_default_projects_features_builds'] = false
```

保存文件并重新配置GitLab：

```bash
sudo gitlab-ctl reconfigure
```
