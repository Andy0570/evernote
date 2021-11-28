
### 1.在 Docker 容器中运行 Nginx 服务：

```bash
$ docker run \
-d \
-p 80:80 \
--rm \
--name mynginx \
--volume "/var/www/arlingbc":/usr/share/nginx/html \
nginx
```

### 2. 将容器中的 Nginx 配置文件拷贝到当前目录下:

```bash
$ docker cp mynginx:/etc/nginx .
```

nginx 配置文件会被拷贝到 ~/nginx 目录下:

![](http://upload-images.jianshu.io/upload_images/2648731-a02193a1601025e1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

注释：
* 配置文件 `nginx.conf` 末尾有一行代码：`include /etc/nginx/conf.d/*.conf;`，表示它会包含 `conf.d` 目录中的所有配置文件。
* 当前，`conf.d` 目录下有一个 `default.conf` 文件。也就是说，`nginx.conf` 文件会包含 `default.conf` 文件。



### 3. 停止运行 Nginx 服务（mynginx）

```bash
$ docker stop mynginx
```



### 4. 配置 Nginx 文件

Nginx 配置文件已经拷贝到本地用户目录下，你可以像往常一样配置 Nginx 文件。关于 Nginx 如何配置，可以参考[Nginx 使用及配置](https://www.jianshu.com/p/849343f679aa)



注意点：

* `~/nginx/conf.d/default.conf` 中 server 的 root 路径应该是：`/usr/share/nginx/html`。

  ![](http://upload-images.jianshu.io/upload_images/2648731-450e4659f8d9ca26.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* `~/nginx/nginx.conf` 中 `include /etc/nginx/conf.d/*.conf;` 保持不变。



### 5. 运行 Nginx 服务:

```bash
$ docker run /
-d /
--name nginx_arlingbc /
--volume "/var/www/arlingbc":/usr/share/nginx/html /
--volume "/home/ubuntu/nginx":/etc/nginx /
-p 80:80 /
-p 443:443 /
nginx

# 参数说明
# -d：以后台方式运行 Nginx 服务。
# --name：自定义容器名称。
# --volume：挂载volume，将容器目录映射到本地目录。
# -p：映射端口，将容器的端口暴露给宿主机的端口。
```

### 参考

* [Nginx 容器教程](http://www.ruanyifeng.com/blog/2018/02/nginx-docker.html)
* [Docker 架设 Nginx 服务器](https://www.jianshu.com/p/2057576bfc5a)
