原文链接：<https://angeloreale.com/blog/dockerizing-a-node-js-and-mongodb-app-with-ci-cd-pipelines-on-gitlab-for-staging-and-production-environments->

今天我醒来时受到启发，亲身体验了我最近一直在努力解决的一些技术，以便进一步增强我对它们的认识。这些分别是：Docker 和 Gitlab 的 CI/CD 流水线。

这个博客（和整个网站）是在 Node.js 和 MongoDB 中开发的，所以我决定解决将这个堆栈迁移到 Docker 环境的挑战，同时还要为它实现一个持续部署流水线。

我的想法是减少工作量，同时最终在服务器之间迁移（为此我使用了Docker），同时使用 Gitlab 的 CI/CD 流水线简化整个部署过程，这样每次我都会将我的提交推送到我的仓库（在分支'dev'或分支'master'上）它会自动：1）重建相应的分支 Docker 镜像并将其推送到私有仓库。 2）使用 SSH 登录我的 VPS 并执行一系列强制命令使用更新的镜像重建容器。

因此，此过程可确保部署之间更好的一致性，并减少更新之间的工作负载和停机时间（从大约5分钟，同时手动实施，仅仅是自动化的秒数）。

这种方法相当简单。

