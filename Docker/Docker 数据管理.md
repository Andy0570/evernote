### 创建一个新的数据卷

使用 `docker run` 命令时，可以通过 `--mount` 标记来创建一个数据卷并挂载到容器里。

创建一个名为 web 容器，并加载一个数据卷到容器的 `/webapp` 目录:
```bash
$ docker run -d -P \
         --name web \
         # -v my-vol:/wepapp \
         --mount source=my-vol,target=/webapp \
         training/webapp \
         python app.py
```



### 挂载一个主机目录作为数据卷

加载主机的 `/src/webapp` 目录到容器的 `/opt/webapp` 目录：
```bash
$ docker run -d -P \
         --name web \
         # -v /src/webapp:/opt/webapp \
         --mount type=bind,source=/src/webapp,target=/opt/webapp \
         training/webapp \
         python app.py
```

> 注意：Dockerfile 中不支持这种用法，这是因为 Dockerfile 是为了移植和分享用的。然而，不同操作系统的路径格式不一样，所以目前还不能支持。

Docker 挂载数据卷的默认权限是**读写**，用户也可以通过 `readonly` 指定为**只读**。

```bash
$ docker run -d -P \
         --name web \
         # -v /src/webapp:/opt/webapp:ro \
         --mount type=bind,source=/src/webapp,target=/opt/webapp,readonly \
         training/webapp \
         python app.py
```

### 挂载一个主机文件作为数据卷

从主机挂载单个文件到容器中：
```bash
$ docker run --rm -it \
        # -v $HOME/.bash_history:/root/.bash_history \
        --mount type=bind,source=$HOME/.bash_history,target=/root/.bash_history \
        ubuntu:18.04 \
        bash
```

> 注意：如果直接挂载一个文件，很多文件编辑工具，包括 `vi` 或者 `sed --in-place`，可能会造成文件 inode 的改变，从 Docker 1.1.0 起，这会导致报错误信息。所以最简单的办法就**直接挂载文件的父目录**。






