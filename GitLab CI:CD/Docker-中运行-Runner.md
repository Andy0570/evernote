# 获取最新 gitlab-runner 镜像

```bash
docker pull gitlab/gitlab-runner:latest
```

# 停止并删除现有容器

```bash
docker stop gitlab-runner && docker rm gitlab-runner
```

# 启动容器

```bash
docker run --detach \
           --name gitlab-runner \
           --restart always \
           --volume /srv/gitlab-runner/config:/etc/gitlab-runner \
           --volume /var/run/docker.sock:/var/run/docker.sock \
           gitlab/gitlab-runner:latest
```

# 在 Docker 容器中注册 Runner

```bash
docker run gitlab/gitlab-runner \
           register \
           --rm \
           --volume /srv/gitlab-runner/config:/etc/gitlab-runner \
           --non-interactive \
           --executor "docker" \
           --docker-image alpine:latest \
           --url "https://gitlab.com/" \
           --registration-token "s8SsZxu7Bk_aa5yF9kRY" \
           --description "docker-runner" \
           --tag-list "huaweiCloud,docker,nodejs-server" \
           --run-untagged="true" \
           --locked="false" \
           --access-level="not_protected"
```

# 查看日志

```bash
docker logs gitlab-runner
```
