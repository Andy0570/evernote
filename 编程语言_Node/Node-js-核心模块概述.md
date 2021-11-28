## CmmonJS 规范

CommonJS 是一个项目，其目标是在 Web 浏览器之外为 JavaScript 建立模块生态系统的约定。其创建的主要原因是缺乏普遍接受的 JavaScript 脚本模块单元形式，其可以在不同于传统网络浏览器提供的环境中重复使用，例如，运行 JavaScript 脚本的 Web 服务器或本机桌面应用程序。

详细参考：[CommonJS 规范](<https://zhaoda.net/webpack-handbook/commonjs.html>)

## 模块加载原理与加载方式

* Node 中的模块：
  1. **核心模块/原生模块**：Node提供的模块。
  2. **文件模块**：用户编写的模块。
* Node 引入模块的步骤：**路径分析 —> 文件定位 —> 编译执行**。
* 加载速度：核心模块比文件模块加载速度更快。


### 优先从缓存加载

* Node 对引入过的模块都会进行缓存，以减少二次引入时的开销。而且缓存的是编译和执行之后的对象。
* `require()` 方法是一个**同步 I/O 操作**。
* `require()` 方法对相同模块的二次加载一律采用**缓存优先**的方式。
* 加载次序：缓存中加载 - 核心模块 - 文件模块 - node_modules 模块。

## require 导入模块

> 💡💡💡
> **文件扩展名**
> `require()` 方法导入模块时，标识符中可以不包含文件扩展名，即：
> `const mongoose = require('mongoose');`
> 此时，Node.js 会按 .js、.json、.node的次序依次补足扩展名，依次尝试（性能问题：**会调用 fs 模块同步阻塞式地判断文件是否存在**）。
>
> 优化的诀窍：如果是 .node 或者 .json 文件，在传递给 `require()` 的标识符中带上扩展名。

### 1. 导入核心模块

```javascript
// 导入 http 模块
const http = require('http');
```

### 2. 导入文件模块

```javascript
// "./" 前缀指代当前路径
// Node.js 内部会自动查找加载 node_modules 文件夹下的模块
// 模块路径查找规则：当前目录->父目录->...->根目录
const underscore = require('./underscore'); 

// 使用 npm 插件导入模块
import {} from 'underscore';
```

## exports 导出模块

* 在 Node 中，一个文件就是一个模块。
* 在模块中，存在一个 **module** 对象，它代表模块自身，而 **exports** 是 **module** 的属性。

### 1. 示例一

导出模块，与其他模块共享一些属性、方法。

```javascript
const util = {
    // 数组去重
    noRepeat : function(arr) {
        return arr.filter(function(ele, index) {
            return arr.indexOf(ele) == index;
        });
    },
    // 计算数组之和
    add : function(arr) {
        return arr.reduce(function(ele1, ele2) {
            return ele1 + ele2;
        });
    }
};

// 导出模块
module.exports = util;
```

使用：

```javascript
const util = require('./test'); // 导入文件模块
const arr = [1, 2, 3, 3, 2];

let noRepeatArr = util.noRepeat(arr);
let num = util.add(arr);
```



### 2. 示例二

```javascript
// math.js
// 将方法挂载到 exports 对象上作为属性即可定义导出的方式
exports.add = function () {
    var sum = 0,
        i = 0,
        args = arguments,
        l = args.length;
    while (i < 1) {
        sum += args[i++];
    }
    return sum;
}

// program.js
const math = require('./math');
exports.increment = function (val) {
    return math.add(val, 1);
};
```



### exports 与 module.exports

* exports 对象是通过形参的方式传入的，直接赋值形参会改变形参的引用，但并不能改变作用域外的值。测试代码如下:

  ```javascript
  var change = function (a) {
      a = 100;
      console.log(a); // => 100
  };
  
  var a = 10;
  change(a);
  console.log(a); // => 10
  ```

* 如果要达到 require 引入一个类的效果，请赋值给 **module.exports** 对象。



## Node.js 核心模块

核心模块可以分为两部分：
1. C/C++ 内建模块；
2. JavaScript 核心模块；

## http模块——创建 HTTP 服务器、客户端

### 1. Node.js 服务器端

```javascript
// Node.js 服务器端
const http = require('http');

const server = http.createServer(function(req, res) {
    // 向请求的客户端返回响应头信息
    res.writeHead(200, {
        'content-type': 'text/plain'
    });
    // 结束响应
    res.end('Hello, Node.js!');
});

server.listen(3000, function() {
    console.log('listening port 3000');
});
```

打开浏览器，输入地址：http://localhost:3000/ 验证。

* `http.createServer()` 方法返回的是 http 模块封装的一个基于事件的 http 服务器。
* `http.request` 是其封装的一个 http 客户端工具。
* req 是 `http.IncomingMessage` 的实例，表示http请求的信息。
* res 是  `http.ServerResponse` 的实例，表示返回给客户端的信息。



![http.IncomingMessage](http://upload-images.jianshu.io/upload_images/2648731-b860d150e0dcef78.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`http.ServerResponse` 常用方法:

1. **res.writeHead(statusCode, [headers])**：向请求的客户端发送响应头。
2. **res.write(data,[encoding])**：向请求发送内容。
3. **res.end([data],[encoding])**：结束请求。



```javascript
// Node.js 服务器端
const http = require('http');

// http.createServer() 方法添加了一个 request 事件监听
const server = http.createServer(function(req, res) {

    let data = '';
    req.on('data', function(chunk) {
        data += chunk;
    });

    req.on('end', function() {
        let method = req.method;                   // HTTP 请求的方法
        let url = req.url;                         // 请求路径
        let headers = JSON.stringify(req.headers); // HTTP 请求头
        let httpVersion = req.httpVersion;         // HTTP 协议的版本号
        
        // 向请求的客户端发送响应头
        res.writeHead(200, {
            'content-type': 'text/html'
        });

        let dataHtml = '<p>data:' + data + '</p>';
        let methodHtml = '<p>method:' + method + '</p>';
        let urlHtml = '<p>url:' + url + '</p>';
        let headersHtml = '<p>headers:' + headers + '</p>';
        let httpVersionHtml = '<p>httpVersion:' + httpVersion + '</p>';
        let resData = dataHtml + methodHtml + urlHtml + headersHtml + httpVersion;
        res.end(resData); // 结束请求
    });
});

server.listen(3000, function() {
    console.log('listening port 3000');
});
```



### 2. 客户端向 http 服务器发起请求

向 http 服务器发起请求的方法：

* **http.request(options[, callback])**
* **http.get(options[, callback])**，http.request() 使用 HTTP 请求方式 GET 的简便写法。

```javascript
// 客户端向 http 服务器发起请求
const http = require('http');
let reqData = '';

// http.request()：向 http 服务器发起请求
http.request({
    'host': '127.0.0.1',
    'port': '3000',
    'method': 'get'
}, function(res) {
    res.on('data', function(chunk) {
        reqData += chunk;
    });
    res.on('end', function() {
        console.log(reqData);
    });
}).end(); // 结束请求
```



**http.request()** 和 **http.get()** 方法返回的是一个 **http.ClientRequest()** 实例。

![http.ClientRequest](http://upload-images.jianshu.io/upload_images/2648731-fbc078cc481103f6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```javascript
// 客户端向 http 服务器发起请求
const http = require('http');
let reqData = '';
let option = {
    'host': '127.0.0.1',
    'port': '3000'
};

const req = http.request(option);

req.on('response', function(res) {
    res.on('data', function(chunk) {
        reqData += chunk;
    });
    res.on('end',function(){
        console.log(reqData);
    });
});
```



## url 模块——url 地址处理

url 模块是一个分析、解析 url 地址的模块。

![url 模块的3个主要方法](http://upload-images.jianshu.io/upload_images/2648731-73025fbbc60aef80.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### `url.parse(urlString[, parseQueryString[, slashesDenoteHost]])`

示例：解析 url 地址，返回 url 对象：

示例代码访问 url 路径：http://localhost:8080/login?username=andy&password=123456

```javascript
'use strict';

const http = require('http');
const url = require('url');

const server = http.createServer((req, res) => {
  // 打印 req.url
  console.log(req.url);

  // 解析 url，获取 url 的 get 传值
  const result = url.parse(req.url, true);
  console.log(result);
  console.log(result.query.username); // andy
  console.log(result.query.password); // 123456

  // 设置响应头信息
  res.writeHead(200, { 'content-type': 'text/plain;charset=UTF8' });
  res.end('hello world');
}).listen(8080);
```

控制台输出：

```
/login?username=andy&password=123456

Url {
  protocol: null,
  slashes: null,
  auth: null,
  host: null,
  port: null,
  hostname: null,
  hash: null,
  search: '?username=andy&password=123456',
  query:
   [Object: null prototype] { username: 'andy', password: '123456' },
  pathname: '/login',
  path: '/login?username=andy&password=123456',
  href: '/login?username=andy&password=123456' }
  
andy
123456
```



### `url.format(urlObject)`

示例，获取url对象，返回完整的 url 地址：

```javascript
const url = require('url');

let urlObj = {
    'host': 'www.google.com',
    'port': '80',
    'protocol': 'https',
    'search': '?q=node.js',
    'query': 'q=node.js',
    'path': '/'
};
let urlAdress = url.format(urlObj);
console.log(urlAdress); // https://www.google.com?q=node.js
```



### `url.resolve(from, to)`

该模块可用于添加或替换 URL 地址。

示例：解析并返回完整的 URL 地址：

```javascript
const url = require('url');

let urlAddress =  url.resolve('https://www.google.com','/image');
console.log(urlAddress); // https://www.google.com/image
```



## querystring 模块——查询字符串处理

`querystring` 模块提供了一些实用函数，用于解析与格式化 URL 查询字符串。

![querystring 模块](http://upload-images.jianshu.io/upload_images/2648731-726f36c0a18959b0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### `querystring.parse()`，反序列化

```javascript
const querystring = require('querystring');

// 将一个 URL 查询字符串序列化为一个对象（也就是一个键值对的集合）
let str1 = 'keyWord=node.js&name=andy';
let obj1 = querystring.parse(str1);
console.log(obj1); // // { keyWord: 'node.js', name: 'andy' }
```



### `querystring.stringify()`，序列化

```javascript
const querystring = require('querystring');

// 将对象序列化为 URL 查询字符串
let obj2 = {
    name : 'Andy',
    age : 25,
    job : 'Doctor'
};
let str2 = querystring.stringify(obj2);
console.log(str2); // name=Andy&age=25&job=Doctor
```



### 其它方法

* `querystring.escape(str)` 用于对给定的 `str` 进行 URL 编码。该方法是提供给 `querystring.stringify()` 使用的，通常不直接使用。 它之所以对外开放，是为了在需要时可以通过给 `querystring.escape` 赋值一个函数来重写编码的实现。

* `querystring.unescape(str)` 用于对给定的 `str` 进行解码。该方法是提供给 `querystring.parse()` 使用的，通常不直接使用。 它之所以对外开放，是为了在需要时可以通过给 `querystring.unescape` 赋值一个函数来重写解码的实现。

  默认使用 JavaScript 内置的 `decodeURIComponent()` 方法来解码。




## util 模块——实用工具

![util 模块](http://upload-images.jianshu.io/upload_images/2648731-874d1d65fccf7c7d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### `util.inspect()`，返回 `object` 的字符串表示，主要用于调试。

```javascript
const util = require('util');

let obj = {
    keyword: 'node.js',
    name: 'andy',
    marrage: true,
    age: 25
};

// 1.将对象反序列化为一个字符串
// let str = util.inspect(obj);

// 2.将对象反序列化为一个字符串，让控制台带颜色输出
let str = util.inspect(obj, {
    'colors' : true
}); 

console.log(str); // { keyword: [32m'node.js' [39m, name:  [32m'andy' [39m, marrage:  [33mtrue [39m, age:  [33m25 [39m }
```

控制台带颜色输出字符串：

![util.inspect()](http://upload-images.jianshu.io/upload_images/2648731-8d012b8740a1a423.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### `util.format()`，类似 `printf` 的格式字符串。

```javascript
const util = require('util');

util.format('%s is %d', 'andy', 16); // andy is 16

// 参数少于占位符
util.format('%s is %s %s', 'andy', 'smile'); // andy is smile %s

// 参数多于占位符
util.format('%s is ', 'andy', 'smile', 'zy'); // andy is  smile zy

// 没有占位符
util.format('andy', 'is', 'smile'); // andy is smile
```





## path 模块——路径处理

path 模块用于处理文件与目录的路径。

![path 模块](http://upload-images.jianshu.io/upload_images/2648731-32c223e8e5b322d9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

示例代码：

```javascript
const path = require('path');

// path.join()
let outputpath = path.join(__dirname, 'node', 'node.js');
console.log(outputpath); // /Users/huqilin/Desktop/node/test/node/node.js

// path.extname()
let ext = path.extname(path.join(__dirname, 'node', 'node.js'));
console.log(ext); // .js

// path.parse()
const str = 'C/frontEnd/node/node.js';
let obj = path.parse(str);
console.log(obj);

// { root: '',				 //根目录
//   dir: 'C/frontEnd/node', //完整目录
//   base: 'node.js',        //路径最后一个部分
//   ext: '.js',             //拓展名
//   name: 'node' }          //文件名（不带拓展名）
```
实例：`path.join()` 解决 **Error: Missing *** File** 问题：
```javascript
const path = require('path');

// const configFilename = './rss_feeds.txt'; // error

// 解决方法如下：
const configFilename = path.join(__dirname, './rss_feeds.txt');
```


## dns 模块

dns 模块的功能是域名处理和域名解析。

dns 模块包含两类函数：

1. 使用底层操作系统工具进行域名解析，且无需进行网络通信。 这类函数只有一个：[`dns.lookup()`](http://nodejs.cn/s/LJLsTL)。
2. 连接到一个真实的 DNS 服务器进行域名解析，且始终使用网络进行 DNS 查询。 

![dns 模块](http://upload-images.jianshu.io/upload_images/2648731-d86556f2c8eab282.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

示例代码：

```javascript
const dns = require('dns');

let domain = 'baidu.com';

// dns.resolve()
dns.resolve(domain, function(err, address) {
    if (err) {
        console.log(err);
        return;
    }
    console.log(address); // [ '123.125.115.110', '220.181.57.216' ]
});

// dns.lookup()
dns.lookup(domain, function(err, address) {
    if (err) {
        console.log(err);
        return;
    }
    console.log(address); // 123.125.115.110
});

//dns.reverse()
dns.reverse('114.114.114.114', function(err, domain) {
    console.log(domain); // [ 'public1.114dns.com' ]
});
```


