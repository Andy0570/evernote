# Connect

[Connect](https://www.npmjs.com/package/connect)是一个框架，它使用被称为中间件的模块化组件，以可重用的方式实现Web程序中的逻辑。在Connect中，中间件组件是一个函数，它拦截HTTP服务器提供的请求和响应对象，执行逻辑，然后或者结束响应，或者把它传递给下一个中间件组件。Connect用分派器把中间件“连接”在一起。

![image](http://upload-images.jianshu.io/upload_images/2648731-71187dce53e27100.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 一、搭建一个 Connect 程序

最小的connect程序：

```javascript
const connect = require('connect');
var app = connect();
app.listen(3000);
```

这个裸程序没有中间件，所以分派器会用404 Not Found状态码响应它收到的所有HTTP请求。

原理：Connect 分派器会依次调用所有附着的中间件组件，直到其中一个决定响应该请求。如果直到中间件列表末尾还没有组件决定响应，程序会用404作为响应。



# 二、Connect 的工作机制

在Connect中，中间件组件是一个JavaScript函数，按惯例会接受三个参数:一个请求对象，一个响应对象，还有一个通常命名为next的参数，它是一个回调函数，表明这个组件已经完成了它的工作，可以执行下一个中间件组件了。



### 1. 自定义中间件

```javascript
const connect = require('connect');
var app = connect();
app.use(logger);
app.use(hello);
app.listen(3000);

// logger 中间件组件
function logger(req, res, next) {
    // 输出每个HTTP请求的方法和URL··
    console.log('%s %s', req.method, req.url);
    next();
}

// 响应 “Hello World” 的中间件
function hello(req, res) {
    res.setHeader('Content-Type', 'text/plain');
    res.end('Hello World!');
}
```



### 2. use() 函数支持链式调用

```javascript
const connect = require('connect');

// logger 中间件组件
function logger(req, res, next) {
    // 输出每个HTTP请求的方法和URL并调用 next()
    console.log('%s %s', req.method, req.url);
    next();
}

// 响应 “Hello World” 的中间件
function hello(req, res) {
    res.setHeader('Content-Type', 'text/plain');
    res.end('Hello World!');
}

// use() 函数支持链式调用。 
connect()
    .use(logger)
    .use(hello)
    .listen(3000);
```



## 三、中间件的执行顺序很重要

* 当一个组件不调用 `next()` 时，命令链中的后续中间件都不会被调用。



### 1. 用中间件的顺序执行认证

利用中间件的顺序执行机制，添加认证。

假设你已经写了一个叫做 restrictFileAccess 的中间件组件，只允许有效的用户访问文件。有效用户可以继续到下一个中间件组件，如果用户无效，则不会调用 `next()`。

```javascript
const connect = require('connect');

// 利用中间件的位次限制文件访问
connect()
    .use(logger)
    .use(restrictFileAccess) // 只有有效用户才会调用 next()
    .use(serveStaticFiles)
    .use(hello);
```



## 四、挂载中间件和服务器

挂载：给中间件或整个程序定义一个路径前缀。

```javascript
const connect = require('connect');

// 当 .use() 的第一个参数是个字符串时，只有 URL 前缀与之匹配时，connect 才会调用后面的中间件
// restrict 组件确保访问页面的是有效用户;
// admin 组件会给用户呈现管理区。
connect()
    .use(logger)
    .use('/admin', restrict)
    .use('/admin', admin)
    .use(hello)
    .listen(3000);
```



### 1. 认证中间件

```javascript
// 简单的 Basic 认证逻辑
// 借助带着 Base64 编码认证信息的HTTP请求头中的authorization字段进行认证
function restrict(req, res, next) {
    var authorization = req.headers.authorization;
    if (!authorization) {
        // 用 Error 对象做参数调用 next()，相当于通知Connect程序中出现了错误
        return next(new Error('Unauthorized'));
    }

    var parts = authorization.split(' ');
    var scheme = parts[0];
    var auth = new Buffer(parts[1], 'base64').toString().split(':');
    var user = auth[0];
    var pass = auth[1];

    // 根据数据库中的记录检查认证信息的函数
    authenticateWithDatabase(user, pass, function (err) {
        if (err) {
            return next(err);
        }
        // 如果认证信息有效，不带参数调用next()
        next();
    })
}
```



### 2. 显示管理面板的中间件

```javascript
// 路由 admin 请求
function admin(req, res, next) {
    switch (req.url) {
        case '/':
            res.end('try /users');
            break;
        case '/users':
            res.setHeader('Content-Type', 'application/json');
            res.end(JSON.stringify(['tobi', 'loki', 'jane']));
            break;
    }
}

// 💡💡💡
// case 中的字符串是 / 和 /users。
// 而实际请求的字符串是 /admin 和 /admin/users。
// 这是因为在调用中间件之前，Connect从req.url中去掉了前缀，就像URL挂载在/上一样。
```



```javascript
const connect = require('connect');

// 在两个不同的挂载点挂载blog程序
connect()
    .use(logger)
    .use('/blog', blog)
    .use('/posts', blog)
    .use(hello)
    .listen(3000);
```



## 五、创建可配置中间件

创建更通用、可重用的中间件。

为了向开发人员提供可配置的能力，中间件通常会遵循一个简单的惯例：**用函数返回另一个函数**(这是一个强大的JavaScript特性，通常称为闭包)。

```javascript
function setup(options) {
    // 设置逻辑
    // 载这里做中间件的初始化
    return function(req, res, next) {
        // 中间件逻辑
        // 即使被外部函数返回了，仍然可以访问options
    }
}

// 使用如下
app.use(setup({some: 'options'}));
```



### 1. 可配置的logger中间件组件

可配置的中间件可以传入额外的参数来改变他的行为

```javascript
// 可配置的Connect中间件组件
// setup 函数可以用不同的配置调用多次
function setup(format) {

    // logger 组件用正则表达式匹配请求属性
    var regexp = /:(\w+)/g;
    return function logger(req, res, next) { // Connect使用的真实logger组件
        // 用正则表达式格式化请求的日志条目
        var str = format.replace(regexp, function (match, property) {
            return req[property];
        })
        console.log(str);// 输出日志条目到控制台
        next(); // 将控制权交给下一个中间件组件
    }
}

module.exports = setup; // 导出 logger 的 setup 函数
```



### 2. 构建路由中间件组件

路由：把请求URL映射到实现业务逻辑的函数上。

```javascript
// 使用 router 中间件组件
const connect = require('connect');
const router = require('./middleware/router'); // 路由器组件

// 定义路由的对象
var routers = {
    GET: {
        // 其中的每一项都是对请求URL的映射，并包含要调用的回调函数
        '/users': function (req, res) {
            res.end('tobi, loki, ferret');
        },
        '/user/:id': function (req, res, id) {
            res.end('users: ' + id);
        }
    },
    DELETE: {
        '/user/:id': function (req, res, id) {
            res.end('delete user ' + id);
        }
    }
};

connect()
    .use(router(routers)) // 将路由对象传给路由器的 setup 函数
    .listen(3000);
```



路由器组件的复用：

```javascript
const connect = require('connect');
const router = require('./middleware/router'); // 路由器组件

// 把与用户相关路由、管理员相关路由分到不同的模块中，然后在路由器组件中分别引入
connect()
    .use(router(require('./routes/user')))
    .use(router(require('./routes/admin')))
    .listen(3000);
```



![Jietu20181108-111120](http://upload-images.jianshu.io/upload_images/2648731-23d0c94fd6637a5f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```javascript
 // 简单的路由中间件
 const parse = require('url').parse;
 module.exports = function  route(obj) {
     return function (req, res, next) {
         // 检查以确保req.method定义了
         // 如果未定义，调用next()，并停止一切后续操作
         // 程序中自定义的数组中的方法：obj[req.method]
         if (!obj[req.method] ){
             next();
             return; 
         }

         var routes = obj[req.method]; // 查找req.method对应的路径 （数组对象）
         var url = parse(req.url); // 解析 URL，以便跟 pathname 匹配
         var paths = Object.keys(routes); // 将req.method对应的路径存放到数组中

         for (let i = 0; i < paths.length; i++) { // 遍历路径
            var path = paths[i];
            var fn = routes[path];
            path = path.replace(/\//g, '\\/').replace(/:(\w+)/g, '([^\\/]+) ');
            var re = new RegExp('^' + path + '$'); // 构造正则表达式
            var captures = url.pathname.match(re);
            if (captures) {
                // 传递被捕获的分组
                var args = [req, res].concat(captures.slice(1));
                fn.apply(null, args);
                return; // 当有相匹配的函数时，返回，以防止后续的next()调用。
            }
         }
         next();
     }
 }
```

1. 首先检查当前的req.method在routes映射中是否有定义，如果没有则停止进一步处理(即调用next())。
2. 之后它会循环遍历已定义的路径，检查是否有跟当前的req.url相匹配的路径。
3. 如果找到匹配项，则调用匹配项的回调函数，期望完成对HTTP请求的处理。



### 构建一个重写URL的中间件组件

重写URL功能，服务端接收的是 /blog/posts/my-post-title 的请求，基于这个URL最后的文章标题查找文章的ID，然后将URL转换为  /blog/posts/id。

```javascript
'use strict';

const connect = require('connect');
const url = require('url');

const app = connect().use(rewrite).use(showPost).listen(3000);

// 基于缩略名重写请求URL的中间件
var path = url.parse(req.url).pathname;
function rewrite(req, res, next) {
    var match = path.match(/^\/blog\/posts\/(.+)/);
    // 只针对 /blog/posts 请求执行查找
    if (match) {
        findPostIdBySlug(match[1], function (err, id) {
            if (err) {
                return next(err);
            }
            if (!id) {
                return next(new Error('User not found'));
            }
            req.url = '/blog/posts/' + id; // 重写req.url属性，以便后续中间件可以使用真实的ID
            next();
        });
    } else {
        next();
    }
}
```



## 六、使用错误处理中间件

Connect刻意将错误处理做到最简，让开发人员指明应该如何处理错误。

### 1. Connect 的默认错误处理器

```javascript
var connect = require('connect');

connect().use(function hello(req, res) {
    foo(); // ---> ReferrenceError
    res.setHeader('Content-Type', 'text/plain');
    res.end('hello world');
}).listen(3000);

// 500, 'Internal Server Error'
```



### 2. 自行处理程序错误

```javascript
// Connect 中的错误处理中间件
function errorHandler() {
    var env = process.env.NODE_ENV || 'development';

    // 错误处理中间件定义四个参数
    return function (err, req, res, next) {
        res.statusCode = 500;
        switch (env) {
            case 'development':
                res.setHeader('Content-Type', 'application/json');
                res.end(JSON.stringify(err));
                break;
            default:
                res.end('Server error');
                break;
        }
    }
}

// 使用错误处理
connect()
    .use(router(require('./routes/user'))) // 有错误会跳过
    .use(router(require('./routes/admin'))) // 有错误会跳过
    .use(errorHandler())
    .listen(3000);
```

用 **NODE_ENV** 设定程序的模式：Connect 通常是用环境变量 NODE_ENV（process.env.NODE_ENV）在不同的服务器环境之间切换，比如生产和开发环境。



### 3. 使用多个错误处理中间件组件

用中间件的变体做错误处理对于将错误处理问题分离出来很有帮助。






