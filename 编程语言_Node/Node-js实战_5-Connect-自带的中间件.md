> 💡
> 这一章是《Node.js 实战》笔记，但是书中的源码实现与官方文档有出入，Connect “自带”的中间件好像是需要单个独立导入才能使用。
> 以下仅作为记录，实际项目使用待研究验证。

# Connect 自带的中间件

Connect 自带的中间件可以满足常见的Web程序开发需求：

* 会话管理；
* cookie 解析；
* 请求主体解析
* 请求日志
* ...


下面的这些中间件是独立呈现的，需要npm单独导入

![Connect 中间件](http://upload-images.jianshu.io/upload_images/2648731-ad9d16499615d6f7.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image](http://upload-images.jianshu.io/upload_images/2648731-df413019b2f14436.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## 解析 cookie、请求主体和查询字符串的中间件



### cookieParser()：解析HTTP cookie

Connect 的 cookie 解析器支持常规cookie、签名cookie和特殊的JSON cookie。
req.cookies默认是用常规未签名cookie组装而成的。如果你想支持session()中间件要求的签名cookie，在创建cookieParser()实例时要传入一个加密用的字符串。

```javascript
const connect = require('connect');

// 作为参数传给cookieParser()的秘钥用来对cookie签名和解签
var app = connect()
    .use(connect.cookieParser('tobi is a cook ferret')) 
    .use(function (req, res) {
        console.log(req.cookies);
        console.log(req.signedCookies);
        res.end('hello\n');
    })
    .listen(3000);

// 不带cookie
// curl http://localhost:3000/

// 常规cookie
// curl http://localhost:3000/ -H "Cookie: foo=bar, bar=baz"
```



#### 签名cookie

签名cookie更适合敏感数据，因为用它可以验证cookie数据的完整性，有助于防止中间人攻击。有效的签名cookie放在req.signedCookies对象中。

假设你设定了一个键为name，值为luna的签名cookie。cookieParser会将cookie编码为luna.PQLM0wNvqOQEObZXUkWbS5m6Wlg。每个请求中的哈希值都会检查，如果cookie完好无损地传上来，它会被解析为req.signedCookies.name:



#### JSON cookie

特别的JSON cookie带有前缀j:，告诉Connect它是一个串行化的JSON。 JSON cookie既可以是签名的，也可以是未签名的。

```shell
curl http://localhost:3000/ -H 'Cookie: foo=bar, bar=j:{"foo":"bar"}'
```



#### 设定出站cookie

cookieParser() 中间件没有提供任何通过 Set-Cookie 响应头向HTTP客户端写出站cookie的功能。但Connect可以通过 res.setHeader()函数写入多个Set-Cookie响应头。

```javascript
const connect = require('connect');

var app = connect()
    .use(function (req, res) {
        res.setHeader('Set-Cookie', 'foo=bar'); // 设定cookie：foo=bar
        res.setHeader('Set-Cookie', 'tobi=ferret; Expires=Tue, 08 Jun 2021 10:18:14 GMT'); // 设定cookie有效期
        res.end();
    }).listen(3000);

// curl http://localhost:3000/ --head
```



### bodyParser()：解析请求主体

bodyParser() 组件为你提供了req.body 属性，可以用来解析 JSON、x-www-form-urlencoded 和multipart/form-data请求。如果是 multipart/form-data 请求，比如文件上传，则还有req.files 对象。

```javascript
var app = connect()
    .use(connect.bodyParser())
    .use(function(req, res) {
        // ...注册用户
        res.end('Registered new user: ' + req.body.username);
    }).listen(3000);
```

因为 bodyParser() 是根据 Content-Type 解析数据的，输入形式是抽象的，所以你的程序中只需要关心req.body数据对象的结果。



### limit()：请求主体的限制

目的：帮助过滤巨型的请求。



### query()：查询字符串解析

* bodyParser()：解析 POST 请求
* query()：解析 GET 请求。

query中间件可以将请求发送过来的查询字符串以JSON格式作为响应返回去。



## 实现 Web 程序核心功能的中间件

* logger()：提供灵活的请求日志;
* favicon()：帮你处理/favicon.ico请求（favicon 是网站的小图标）;
* methodOverride()：让没有能力的客户端透明地重写req.method;
* vhost()：在一个服务器上设置多个网站(虚拟主机);
* session()：管理会话数据。



## 处理 Web 程序安全的中间件

* basicAuth()：为保护数据提供了HTTP基本认证;
* csrf()：实现对跨站请求伪造(CSRF)攻击的防护;
* errorhandler()：帮你在开发过程中进行调试。



## 提供静态文件服务的中间件

* static()：将文件系统中给定根目录下的文件返回给客户端;
* compress()：压缩响应，很适合跟static()一起使用;
* directory()：当请求的是目录时，返回那个目录的列表。


