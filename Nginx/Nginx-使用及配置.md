## [Nginx](http://nginx.org/) 目录

```shell
$ cd /etc/nginx
$ ls -l
total 60
drwx------ 2 ubuntu ubuntu 4096 Jun 16 09:27 cert    ## ssl证书目录
drwxr-xr-x 2 root   root   4096 Jul 12  2017 conf.d
-rw-r--r-- 1 root   root   1077 Feb 11  2017 fastcgi.conf
-rw-r--r-- 1 root   root   1007 Feb 11  2017 fastcgi_params
-rw-r--r-- 1 root   root   2837 Feb 11  2017 koi-utf
-rw-r--r-- 1 root   root   2223 Feb 11  2017 koi-win
-rw-r--r-- 1 root   root   3957 Feb 11  2017 mime.types
-rw-r--r-- 1 root   root   1501 Aug 31 07:42 nginx.conf    ## 配置文件
-rw-r--r-- 1 root   root    180 Feb 11  2017 proxy_params
-rw-r--r-- 1 root   root    636 Feb 11  2017 scgi_params
drwxr-xr-x 2 root   root   4096 Aug 31 09:42 sites-available  ## 虚拟主机配置代理目录
drwxr-xr-x 2 root   root   4096 Jun 15 06:39 sites-enabled    ## 启动配置代理目录
drwxr-xr-x 2 root   root   4096 Jun  4 06:03 snippets
-rw-r--r-- 1 root   root    664 Feb 11  2017 uwsgi_params
-rw-r--r-- 1 root   root   3071 Feb 11  2017 win-utf
```

nginx 配置文件的关系：

![](http://upload-images.jianshu.io/upload_images/2648731-65be6b1b7a4876bb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## 常用命令


```shell
## 查看 Nginx 程序文件目录:/usr/sbin/nginx
$ ps  -ef | grep nginx

## 查看 nginx.conf 配置文件目录:/etc/nginx/nginx.conf
$ nginx -t                 
$ vim /etc/nginx/nginx.conf

## 配置文件目录：/etc/nginx

## 虚拟主机配置文件目录：/etc/nginx/sites-available/
## 虚拟主机文件夹目录：/var/www/，详情可在 /etc/nginx/sites-available/ 中配置
## 默认网页文件目录：/usr/share/nginx/html

## 测试配置文件，只检查配置文件是否存在语法错误
$ nginx -t -c <path-to-nginx.conf>
$ sudo nginx -t -c /etc/nginx/nginx.conf

## 启动 Nginx 服务
$ nginx 安装目录 -c <path-to-nginx.conf>
$ sudo /etc/init.d/nginx start

## 停止 Nginx 服务
$ sudo /usr/sbin/nginx -s stop 

## 重启 Nginx 
$ sudo /usr/sbin/nginx -s reload  # 0.8 版本之后的方法
$ kill -HUP pid     # 向 master 进程发送信号从容地重启 Nginx，即服务不中断

$ sudo service nginx start
$ sudo service nginx stop
$ sudo service nginx restart
```

官方配置说明：

* http://wiki.nginx.org/Pitfalls
* http://wiki.nginx.org/QuickStart
* http://wiki.nginx.org/Configuration



> 💡 推荐一个 HTTPS 自动化配置工具：[Mozilla SSL Configuration Generator](https://mozilla.github.io/server-side-tls/ssl-config-generator/)



## Nginx 配置文件：/etc/nginx/nginx.conf

* 参考 [Nginx 基本配置与参数说明](http://www.nginx.cn/76.html) | [Full Example Configuration](https://www.nginx.com/resources/wiki/start/topics/examples/full/)

* `/etc/nginx/nginx.conf` 配置文件，在虚拟主机部分包含了这两个文件：

  ```nginx
  ##
  # Virtual Host Configs
  ##
  
  include /etc/nginx/conf.d/*.conf;  
  include /etc/nginx/sites-enabled/*; 
  ```


## 配置代理目录： /etc/nginx/sites-available/

* **sites-available** 目录中存放着具体的 Server 配置文件。
* 默认配置文件：`/etc/nginx/sites-available/default` 文件详细配置了默认的虚拟主机目录`root /var/www/html`，监听的端口是80。



## 启动配置代理目录：/etc/nginx/sites-enabled/

* **sites-enabled** 目录中存放的是链接文件，每个链接文件都指向 **sites-available** 目录中的配置文件，表示需要让 Nginx 启用哪些配置代理文件。

* 在 **sites-enabled** 下创建 symbol link 链接 **sites-available** 下的配置文件就可以启用代理配置。

  `$ sudo ln -s /etc/nginx/sites-available/availableFileName linkFileName `

* 默认启用的服务：`/etc/nginx/sites-enabled/` 中的 default 软链接指向  `/etc/nginx/sites-available/default`，删除该链接文件，就会关闭该配置代理。

## nginx.conf 配置文件详解

* [Nginx 基本配置与参数说明](http://www.nginx.cn/76.html) 
*  [Full Example Configuration](https://www.nginx.com/resources/wiki/start/topics/examples/full/)



Nginx 配置文件路径：**/etc/nginx/nginx.conf**

```nginx
##
# 全局配置
##

user www-data;             ## 配置 worker 进程的用户和组
worker_processes auto;     ## 配置 worker 进程启动的数量，建议配置为 CPU 核心数
error_log logs/error.log;  ## 全局错误日志
pid /run/nginx.pid;        ## 设置记录主进程 ID 的文件
worker_rlimit_nofile 8192; ## 配置一个工作进程能够接受并发连接的最大数

##
# 工作模式及连接数上限
##
events {
    # epoll 是多路复用 IO（I/O Multiplexing）中的一种方式，
    # 仅用于 Linux 2.6 以上内核，可以大大提高 Nginx 性能
    use epoll
        
    # 单个后台 worker process 进程的最大并发链接数
    # 并发总数 max_clients = worker_professes * worker_connections
	worker_connections 4096;  ## Defaule: 1024
	# multi_accept on;  ## 指明 worker 进程立刻接受新的连接
}

##
# http 模块
##

http {

	##
	# Basic Settings
	##
	
    #sendfile 指令指定 nginx 是否调用 sendfile 函数（zero copy 方式）来输出文件，
    #对于普通应用，必须设为 on,
    #如果用来进行下载等应用磁盘 IO 重负载应用，可设置为 off，
    #以平衡磁盘与网络 I/O 处理速度，降低系统的 uptime.
	sendfile on;
	tcp_nopush on;
	tcp_nodelay on;
	keepalive_timeout 65;      ## 连接超时时间
	types_hash_max_size 2048;  ## 指定散列类型表的最大大小
	# server_tokens off;

	# server_names_hash_bucket_size 64;  # this seems to be required for some vhosts
	# server_name_in_redirect off;
    
	include /etc/nginx/mime.types;  ## 设定 mine 类型
	default_type application/octet-stream;
   
    # 设定请求缓冲
    client_header_buffer_size    128k; # 指定客户端请求头缓存大小，当请求头大于 1KB 时会用到该项
    large_client_header_buffers  4 128k; # 最大数量和最大客户端请求头的大小
    
	##
	# SSL Settings
	##
	
    # 启用所有协议，禁用已废弃的不安全的SSL 2 和SSL 3
	ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
    # 让服务器选择要使用的算法套件
	ssl_prefer_server_ciphers on;

	##
	# Logging Settings
	##

	access_log /var/log/nginx/access.log;  ## 访问日志
	error_log /var/log/nginx/error.log;    ## 错误日志

	##
	# Gzip Settings
	##

	gzip on;
	gzip_disable "msie6";

	# gzip_vary on;
	# gzip_proxied any;
	# gzip_comp_level 6;
	# gzip_buffers 16 8k;
	# gzip_http_version 1.1;
	# gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

	##
	# Virtual Host Configs
	##

	include /etc/nginx/conf.d/*.conf;   # 这个文件夹默认是空的
	include /etc/nginx/sites-enabled/*; # 开启的 Server 服务配置

}

##
# mail 模块
##
        
mail {
	# See sample authentication script at:
	# http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript

	# auth_http localhost/auth.php;
	# pop3_capabilities "TOP" "USER";
	# imap_capabilities "IMAP4rev1" "UIDPLUS";

	server {
		listen     localhost:110;
		protocol   pop3;
		proxy      on;
	}

	server {
		listen     localhost:143;
		protocol   imap;
		proxy      on;
	}
}
```



## 虚拟服务器配置

虚拟服务器配置文件目录： **/etc/nginx/sites-available/** 

自定义配置文件：`sudo vim /etc/nginx/sites-available/arlingbc`

### 配置 HTTP 服务（80端口）

```nginx
# Virtual Host configuration for arlingbc.com
#
# You can move that to a different file under sites-available/ and symlink that
# to sites-enabled/ to enable it.
#

# 丢弃缺乏 Host 头的请求
server {
 	   listen 80;
       return 444;
}

server {
       listen 80;
       listen [::]:80;
       server_name example.com www.example.com;

       # 定义服务器的默认网站根目录位置
       root /var/www/example/;
       
       # Add index.php to the list if you are using PHP
       index index.html index.htm index.nginx-debian.html;

       # access log file 访问日志
       access_log logs/nginx.access.log main;
       
       # 禁止访问隐藏文件
       # Deny all attempts to access hidden files such as .htaccess, .htpasswd, .DS_Store (Mac).
       location ~ /\. {
                deny all;
                access_log off;
                log_not_found off;
       }
    
       # 默认请求
       location / {
                # 首先尝试将请求作为文件提供，然后作为目录，然后回退到显示 404。
        		# try_files 指令将会按照给定它的参数列出顺序进行尝试，第一个被匹配的将会被使用。
                # try_files $uri $uri/ =404;
      
                try_files $uri $uri/ /index.php?path_info=$uri&$args =404;
                access_log off;
                expires max;
       }
    
       # 静态文件，nginx 自己处理
       location ~ ^/(images|javascript|js|css|flash|media|static)/ {
            
           #过期 30 天，静态文件不怎么更新，过期可以设大一点，
           #如果频繁更新，则可以设置得小一点。
           expires 30d;
       }
    
       # .php 请求
       location ~ \.php$ {
                try_files $uri =404;
                include /etc/nginx/fastcgi_params;
                fastcgi_pass unix:/var/run/php5-fpm.sock;
                fastcgi_index index.php;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                fastcgi_intercept_errors on;
       }
    
      # PHP 脚本请求全部转发到 FastCGI 处理. 使用 FastCGI 默认配置.
      # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
      #
      #location ~ \.php$ {
      #       include snippets/fastcgi-php.conf;
      #
      #       # With php7.0-cgi alone:
      #       fastcgi_pass 127.0.0.1:9000;
      #       # With php7.0-fpm:
      #       fastcgi_pass unix:/run/php/php7.0-fpm.sock;
      #}
      
      # 拒绝访问. htaccess 文件，如果 Apache 的文档根与 nginx 的一致
      # deny access to .htaccess files, if Apache's document root
      # concurs with nginx's one
      #
      #location ~ /\.ht {
      #       deny all;
      #}
}
```



### 配置 HTTPS 服务（443端口）

```nginx
##
# 80 port
##

# 默认服务器，丢弃缺乏 Host 头的请求
server {
 	   listen 80;
       return 444;
}

server {
        listen 80;
        listen [::]:80;
        sever_name example.com www.example.com;

        rewrite ^(.*)$ https://$host$1 permanent;  ## 端口转发，301 重定向
}

##
# 443 port
##
server {
    
    ##
    # 阿里云参考配置
    ##
    
    listen 443;
    listen [::]:443;
    server_name example.com www.example.com;
    
    root /var/www/example/;    # 为虚拟服务器指明文档的根目录
    index index.html index.htm; # 给定URL文件
    
    ##
    # 部署 HTTP 严格传输安全（HSTS）
    ##
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload;"
    
    # Note: You should disable gzip for SSL traffic.
    # See: https://bugs.debian.org/773332
    gzip off;
    
    ##
    # SSL configuration
    ##
    
    ssl on;
    ssl_certificate   cert/certfile.pem;    # 证书
    ssl_certificate_key  cert/certfile.key; # 私钥
    ssl_session_timeout 5m; # 设置超时时间
    # 密码套件配置
    # 密码套件名称构成：密钥交换-身份验证-加密算法（算法-强度-模式）-MAC或PRF
    ssl_ciphers ECDHE-RSA-AES128-GCM- SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4; 
    ssl_protocols TLSv1.2; # 设置 SSL/TSL 协议版本号
    ssl_prefer_server_ciphers on; # 控制密码套件优先级，让服务器选择要使用的算法套件
    ssl_buffer_size 1400; # 减少TLS缓冲区大小，可以显著减少首字节时间（《HTTPS权威指南》P416）
    
    ##
    # location configuration
    ##
    
    # ...
}
```



HSTS HTTP 头部标准：

```
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload;
```

参数：

* max-age，服务器告诉某个客户端，在 31536000 秒内，应该实施 HSTS 标准，这段时间内如果客户端重新访问了 HTTPS 网站，max-age 时间就会被重新刷新。
* includeSubDomains，服务器告诉客户端域名下的所有子域名都实施 HSTS 标准，不仅仅是发出 HSTS HTTP 头部的主机才遵循该标准。
* preload，浏览器预存了需要实施 HSTS 标准的网站。



## ngx_http_limit_conn_module 模块

```nginx
http {
    # 最大连接数
    # 分配一个共享内存区域，大小为 10M，用于限制IP
    # 使用 $binary_remote_addr 变量， 可以将每条状态记录的大小减少到 64 个字节，这样 1M 的内存可以保存大约 1 万 6 千个 64 字节的记录。
    limit_conn_zone $binary_remote_addr zone=ips:10m;
    # 分配一个共享内存区域，大小为 10M，用于限制服务器连接数
    limit_conn_zone $server_name zone=servers:10m;
    
    # 设置日志记录级别
    # 当服务器因为频率过高拒绝或者延迟处理请求时可以记下相应级别的日志。
    limit_conn_log_level notice;

    server {
        # 限制每一个IP地址访问限制10个连接
        limit_conn ips 10;
        
        # 服务器提供的最大连接数 1000
        limit_conn servers 1000;
    }
}
```



## ngx_http_limit_req_module 模块


* [Nginx 模块 ngx_http_limit_req_module 限制请求速率](https://blog.csdn.net/loophome/article/details/50767907)
* [nginx 限制请求数 ngx_http_limit_req_module 模块](http://www.ttlsa.com/nginx/nginx-limiting-the-number-of-requests-ngx_http_limit_req_module-module/)

```nginx
http {
    # 最大连接数
    # 分配一个共享内存区域，大小为 10M，限制下载连接数为1
    limit_conn_zone $binary_remote_addr zone=connections:10m;

    # 最大并发数，每秒请求数（r/s），每分钟请求数（r/m）
    # 分配一个设置最大并发数的内存区域，大小 10M，limit_req 限制之前的请求速率 1次/s。
    # 使用 $binary_remote_addr 变量， 可以将每条状态记录的大小减少到 64 个字节，这样 1M 的内存可以保存大约 1 万 6 千个 64 字节的记录。
    limit_req_zone $binary_remote_addr zone=requests:10m rate=1r/s;

    # 设置日志记录级别
    # 当服务器因为频率过高拒绝或者延迟处理请求时可以记下相应级别的日志。
    limit_req_log_level warn;

    # immediately release socket buffer memory on timeout
    reset_timedout_connection on;

    server {
    
        # 仅对 search URL 有效
        location /search {
            
            # 限制速率
            # 最大延迟请求数量 10 个，超过则返回状态码 503
            limit_req zone=requests burst=3 nodelay;
        }
        
        # 限制客户端带宽，
        # 策略：允许小文件自由下载，但对于大文件则启用这种限制
        location /downloads {
            # 首先限制客户端的下载连接数为1 
            limit_conn connections 1;

            # 下载完 1M 内容之后，启用 limit_rate 限制。
            limit_rate_after 1m;
            
            # 限制客户端下载下载内容的速率为 500k/s
            limit_rate 500k;
        }
    }
}
```



## 参考

* [阿里出品：Nginx 开发从入门到精通](https://github.com/taobao/nginx-book)
* [Nginx 入门指南](http://wiki.jikexueyuan.com/project/nginx/)
* [Nginx 中文文档](http://www.nginx.cn/doc/)
* [NGINX 3rd Party Modules](https://www.nginx.com/resources/wiki/modules/index.html)
* [Nginx 反向代理（端口转发）](https://blog.bitzo.cn/2017/07/28/nginx/)
* [5 个提高 Node.js 应用性能的技巧](https://www.jianshu.com/p/b1f247fe2cb6?utm_campaign=maleskine&utm_content=note&utm_medium=reader_share&utm_source=weixin)
* [基于 Debian 系统配置 Nginx 环境的 Node.js 应用教程](https://www.yd631.com/nginx-nodejs/)
* [为什么要使用 Node.js](https://juejin.im/post/57b54f151532bc0063ebfe31)
* [巨头终极对决，Apache、Nginx 与 Node.js 之争](https://www.oschina.net/news/79567/apache-vs-nginx-vs-node-js)
* [图解 HTTPS](https://www.jianshu.com/p/3551a118deeb?utm_campaign=haruki&utm_content=note&utm_medium=reader_share&utm_source=weixin)
* [nginx 强制使用 https 访问 (http 跳转到 https)](https://blog.csdn.net/wzy_1988/article/details/8549290)
* [Nginx 启用 OCSP Stapling 的配置](https://www.jb51.net/article/137208.htm)
* [在 NginX 上为证书配置 OCSP Stapling](https://quchao.com/entry/how-to-configure-ocsp-stapling-on-nginx-for-the-certificates-issued-by-lets-encrypt/)
* [Linux 公社：Nginx 配置项优化详解](https://www.linuxidc.com/Linux/2017-06/144493.htm)


