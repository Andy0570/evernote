![Node 网络模块](http://upload-images.jianshu.io/upload_images/2648731-80e5304bf921d4df.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 构建 TCP 服务

TCP 是 ISO 模型中的传输层协议：

![OSI模型](http://upload-images.jianshu.io/upload_images/2648731-ac0b4af73485c674.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



TCP 是面向连接的协议，显著特性：三次握手。



## 构建 UDP 服务

未完待续...



## 构建 HTTP 服务

HTTP 服务器：处理HTTP请求+发送HTTP响应。

HTTP服务继承自TCP服务器（net 模块），TCP服务以connection为单位进行服务，HTTP服务以request为单位进行服务。HTTP模块即是将connection到request过程进行了封装。

![HTTP对TCP的封装](http://upload-images.jianshu.io/upload_images/2648731-05df5085b8f1a2b3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



构建 Hello World 示例：

```javascript
'use strict';

// 导入 http 模块
const http = require('http');
// 创建 http server，并传入回调函数
http.createServer(function (req, res) {
    // 回调函数接收 request 和 response 对象
  
    // 将 HTTP 响应状态码 200 写入 response，同时设置 content-type 文本类型
    res.writeHead(200, {'content-type': 'text/plain'});
    // 将 HTTP 响应内容写入 response
    res.end('Hello World\n');

// 设置服务器监听端口 和 IP 地址
}).listen(1337, '127.0.0.1');

console.log('Server running at http://127.0.0.1:1337');
```



使用 curl 工具获取 HTTP 的报文：

报文内容：报文头+报文体

```shell
☁  node [master] ⚡  curl -v http://127.0.0.1:1337
* Rebuilt URL to: http://127.0.0.1:1337/
*   Trying 127.0.0.1... # 1. 三次握手部分
* TCP_NODELAY set
* Connected to 127.0.0.1 (127.0.0.1) port 1337 (#0)
> GET / HTTP/1.1        # 2. 客户端向服务端发送请求报文
> Host: 127.0.0.1:1337
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK       # 3. 服务器完成处理后，向客户端发送响应内容
< content-type: text/plain
< Date: Sun, 28 Oct 2018 07:44:31 GMT
< Connection: keep-alive
< Transfer-Encoding: chunked
<
Hello World
* Connection #0 to host 127.0.0.1 left intact  # 4. 结束会话信息
```

* 对于 TCP 连接的==读==操作，http 模块将其封装为 **ServerRequest** 对象。
* 对于 TCP 连接的==写==操作，http 模块将其封装为 **ServerResponse** 对象。
* 为了重用 TCP 连接，http 模块包含一个默认的客户端代理对象 **http.globalAgent**。
* 如果设置 agent 为 false，就会脱离连接池的管理，使得请求不受并发的限制。
* Agent 对象的 sockets 和 requests 属性分别表示当前连接池中使用中的连接数和处于等待状态的请求数。



## 构建 WebScoket 服务

相比于 HTTP，WebSocket 的优点：

1. 客户端与服务器端只建立一个TCP连接，可以使用更少的连接。
2. WebSocket 服务端可以推送数据到客户端，实现客户端与服务端的全双工通信。
3. 更轻量级的协议头，减少数据传送量。



























