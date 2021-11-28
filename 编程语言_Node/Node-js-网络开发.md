| Node.js 网络通信模块 | 实现的通信方式 |
| -------------------- | -------------- |
| net                  | TCP            |
| http                 | HTTP           |
| http2                | HTTP/2         |
| https                | HTTPS          |
| dgram                | UDP            |


## 构建 TCP 服务器

### 使用 Node.js 创建 TCP 服务器

```javascript
// 引入 net 模块
const net = require('net');

// 创建 TCP 服务器
let server = net.createServer(function(socket) {
  console.log('someone connects');
});

// 设置监听端口
// server.listen() 方法会触发 server 下的 listening 事件。
server.listen(18001, function() {
  console.log('server is listening');
});
```

同上

```javascript
// 引入 net 模块
const net = require('net');

// 创建 TCP 服务器
let server = net.createServer(function(socket) {
  console.log('someone connects');
});

// 设置监听端口
server.listen(18001);

// 手动监听 listening 事件
// 设置监听时的回调函数
server.on('listening', function() {
  console.log('server is listening');
});
```

除了 listening 事件外，TCP 服务器还支持以下事件:

* connection：当有新的链接创建时触发，回调函数的参数为 socket 连接对象。
*  close：TCP服务器关闭的时候触发，回调函数没有参数。
*  error：TCP服务器发生错误的时候触发，回调函数的参数为 error 对象；

```javascript
// 引入 net 模块
const net = require('net');

// 实例话一个服务器对象
let server = new net.server();

// 监听 connection 事件
server.on('connection', function(socket) {
  console.log('someone connects');
});

// 设置监听端口
server.listen(18001);

// 设置监听时的回调函数
server.on('listening', function() {
  console.log('server is listening');
});

// 设置关闭时的回调函数
server.on('close', function() {
  console.log('server closed');
});

// 设置出错时的回调函数
server.on('error', function(error) {
  console.log(error);
});
```



### 查看服务器监听的地址

通过 **server.address()** 方法查看 TCP 服务器监听的地址，并返回一个 JSON 对象。

```javascript
// 引入 net 模块
const net = require('net');

// 创建 TCP 服务器
let server = net.createServer(function(socket) {
  console.log('someone connects');
});

// 设置监听端口
server.listen(18001, function() {
  // 获取地址信息
  let address = server.address();
  // 获取端口地址
  console.log('the port server is ' + address.port);
  console.log('the address server is ' + address.address);
  console.log('the family server is ' + address.family);
});

/*
the port server is 18001
the address server is ::
the family server is IPv6
 */
```



### 查看连接服务器的客户端数量

通过 server.getConnections() 方法获取连接到这个 TCP 服务器的客户端数量。

```javascript
// 引入 net 模块
const net = require('net');

// 创建 TCP 服务器
let server = net.createServer(function(socket) {
  console.log('someone connects');
  // 设置最大连接数，连接数超过后，服务器将拒绝新的连接。
  server.maxConnections = 10;
  server.getConnections(function (err, count) {
    console.log('the count of client is: ' + count);
  )};
});

// 设置监听端口
server.listen(18001, function() {
  //...
});
```



### 获取客户端发送的数据

* socket 对象可以用来获取客户端发送的数据流，每次接收到数据的时候触发 data 事件，通过监听这个事件就可以在回调函数中获取到客户端发送的数据了。
* socket 事件：data、connect、end、error、timeout 等

```javascript
// 引入 net 模块
const net = require('net');

// 创建 TCP 服务器
let server = net.createServer(function(socket) {
  // 监听 data 事件
  socket.on('data', function(data) {
    console.log(data.toString); // 打印 data 数据
  });
});

// 设置监听端口
server.listen(18001, function() {
  console.log('server is listening');
});
```



### 发送数据给客户端

利用 `socket.write()` 可以让 TCP 服务器发送数据给客户端。

```javascript
// 引入 net 模块
const net = require('net');

// 创建 TCP 服务器
let server = net.createServer(function(socket) {

  // 获取地址信息
  let address = server.address();
  let message = 'client, the server addresss is' + JSON.stringify(address);

  // 发送数据
  socket.write(message, function() {
    let writeSize = socket.bytesWritten; // 发送数据的字节数
    console.log(message + 'has send');
    console.log('the size of message is ' + writeSize);
  });

  // 监听 data 事件
  socket.on('data', function(data) {
    console.log(data.toString); // 打印 data 数据
    let readSize = socket.bytesRead; // 接收数据的字节数
    console.log('the size of data is ' + readSize);
  });
});

// 设置监听端口
server.listen(18001, function() {
  console.log('server is listening');
});
```



#### socket 的属性

```javascript
// 引入 net 模块
const net = require('net');

// 创建 TCP 服务器
let server = net.createServer(function(socket) {
  console.log('bytesWritten ' + socket.bytesWritten); // 发送数据字节数
  console.log('bytesRead ' + socket.bytesRead); // 接收数据字节数

  console.log('localPort ' + socket.localPort); // 本地端口地址
  console.log('localAddress ' + socket.localAddress); // 本地 IP 地址

  console.log('remotePort ' + socket.remotePort); // 远程端口地址
  console.log('remoteAddress ' + socket.remoteAddress); // 远程 IP 地址
  console.log('remoteFamily ' + socket.remoteFamily); // 远程 IP 协议簇
});

// 设置监听端口
server.listen(18001, function() {
  console.log('server is listening');
});

```



## 构建 TCP 客户端

### 使用 Node.js 创建 TCP 客户端

* 创建一个 TCP 客户端只需要创建一个连接 TCP 客户端的 **socket** 对象即可。

```javascript
// 引入 net 模块
const net = require('net');

// 创建客户端
let client = new net.Socket();

// 连接服务器
client.connect(18001, '127.0.0.1', function() {
  console.log('connect the server');

  // 发送数据到 TCP 服务器
  client.write('message from client');
})

// 监听 data 事件，获取从 TCP 服务器发送的数据
client.on('data', function(data) {
  console.log('data of server is ' +  data.toString);
});

// 监听 end 事件
client.on('end', function() {
  console.log('data end');
});
```





## 构建 HTTP 服务器

### 创建HTTP服务器

示例一：将一行字符串数据发送给浏览器：

```javascript
// 引入 http 模块
const http = require('http');

// 创建 http 服务器
// http.createServer() 方法添加了一个 request 事件监听
const server = http.createServer(function(req, res) {
    // 设置响应头
    res.writeHead(200, {
        'content-type': 'text/plain' // 设置传输数据类型，服务器或客户端会根据该值来解析传输数据
    });
    // 设置响应的数据
    res.end('hello, Node.js');
});

// 设置服务器监听端口
server.listen(3000, function() {
    console.log('listening port 3000');
});
```



示例二：通过文件模块将文件读取并发送给浏览器：

```javascript
// 引入 http 模块
const http = require('http');
// 引入 fs 模块
const fs = require('fs');

// 创建 http 服务器
// http.createServer() 方法添加了一个 request 事件监听
const server = http.createServer(function(req, res) {
    // 设置响应头
    res.writeHead(200, {
        'content-type': 'text/html'
    }); 
    // 读取文件数据
    let data = fs.readFileSync('./index.html');
    // 响应数据
    res.write(data);
    res.end();
});

// 设置服务器监听端口
server.listen(3000, function() {
    console.log('listening port 3000');
});
```



### HTTP 服务器的路由控制

* 路由就是 URL 到函数的映射。
* 用户通过 URL 地址访问服务器，服务端通过 **req.url** 属性获取到 URL 后对路由进行控制。



示例代码：通过 req.url 对路径处理判断来返回不同的资源，从而做到简单的路由控制：

```javascript
const http = require('http');
const fs = require('fs');
const url = require('url');
const path = require('path');
const mime = require('./mime');

// 创建 http 服务器
const server = http.createServer(function (req, res) {
    let filePath = '.' + url.parse(req.url).pathname;
    if (filePath === './') {
        filePath = './index.html';
    }

    // 判断相应的文件是否存在
    fs.exists(filePath, function (exists) {
        if (exists) {
            // 1.存在则返回相应的数据
            let data = fs.readFileSync(filePath);
            let contentType = mime[path.extname(filePath)];
            // 设置响应头
            res.writeHead(200, {
                'content-type': contentType
            });
            // 响应数据
            res.write(data);
            res.end();
        } else {
            // 2.不存在则返回 404
            res.end('404');
        }
    });
});

// 设置服务器监听端口
server.listen(3000, function () {
    console.log('listening port 3000');
});
```



## 利用 UDP 协议传输数据与发送消息

使用 Node.js 创建服务器，需要使用 dgram 模块：

```javascript
const dgram = require('dgram');
```

> 未完待续...


