选择合适的存储机制的因素：

* 存储数据类型；
* 性能要求，数据的读取和写入速度；
* 存储数据量；
* 数据查询方式；
* 数据持久化、可靠性要求；

![数据存储方案](http://upload-images.jianshu.io/upload_images/2648731-78ac27061c272d88.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



# 无服务器的数据存储

从系统管理的角度来看，最方便的存储机制是那些不用维护DBMS的存储：

* 内存存储；
* 基于文件的存储；



## 内存存储

内存存储用变量存放数据。

特点：数据读取写入快、服务器和程序重启后数据丢失。

理想用途：存放少量经常使用的数据。

应用场景示例：跟踪记录最近一次重启服务器后页面访问次数的计数器：

```javascript
const http = require('http');
var counter = 0;

var server = http.createServer(function (req, res) {
    counter ++;
    res.write('I have been accessed' + counter + ' times.');
    res.end();
}).listen(8888);
```



## 基于文件的存储

基于文件的存储，用文件系统存放数据。

缺点：存在多用户的并发问题。



# 关系型数据库管理系统

关系数据库管理系统(RDBMS)可以存储复杂的信息，并且查询起来很容易。



## MySQL

MySQL是最流行的SQL数据库。

npm 模块：

* [mysql](https://www.npmjs.com/package/mysql)
* [sequelize](https://www.npmjs.com/package/sequelize) —— (传说中的 ORM 技术：Object-Relational Mapping，能把关系数据库的表结构映射到对象上) 对数据库进行简单的增删减查。
* [egg-mysql](https://www.npmjs.com/package/egg-mysql)



## PostgreSQL

* PostgreSQL因其与标准的兼容性和健壮性受到认可。
* PostgreSQL支持递归查询和很多特殊的数据类型。
* PostgreSQL 还能使用一些标准的认证方法，比如轻量目录访问协议（LDAP）和通用安全服务应用程序接口 （GSSAPI）。 
* PostgreSQL支持同步复制，这种复制形态会在每次数据操作后对复制进行验证，从而防止数据丢失。

npm 模块：[node-postgres](https://github.com/brianc/node-postgres)

```shell
$ npm install pg
```



# NoSQL 数据库

NoSQL 数据库，即“No SQL” 或“Not Only SQL”。

NoSQL 数据库把性能放在了第一位。因此，对于实时分析或消息传递而言，NoSQL数据库可能是更好的选择。

[mongoose](https://www.npmjs.com/package/mongoose) 是一个很受欢迎的 MongoDB 访问层API。



## Redis

* Redis 非常适合处理那些不需要长期访问的简单数据存储，比如短信和游戏中的数据。
* Redis 擅长处理变化频繁，相对比较简单的数据。
* Redis 把数据存在RAM中，并在磁盘中记录数据的变化。这样做的缺点是它的存储空间有限，但好处
  是数据操作非常快。

![image](http://upload-images.jianshu.io/upload_images/2648731-782a62d0b7809f2f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



教程：[尝试 Redis](http://try.redis.io/)

npm 模块：[redis](https://www.npmjs.com/package/redis)



## MongoDB

MongoDB 是一个通用的非关系型数据库。

MongoDB 数据库把文档存在集合（collection）中，集合中的每个文档都可以有不同的schema。

![MongoDB 中的每个条目都可能有一个完全不同的schema](http://upload-images.jianshu.io/upload_images/2648731-f951c0d8d07b31f0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



npm 模块：

* [node-mongodb-native](https://www.npmjs.com/package/mongodb)



## Mongoose

Mongoose 的模型（模型—视图—控制器中的说法）提供了一个到 MongoDB 集合接口，以及一些实用的功能，比如schema 层次结构，中间件以及数据校验。

* schema 层次结构可以让一个模型跟其他模型关联。
* 中间件可以转换数据，或在操作模型数据的过程中触发逻辑，让删除父数据时对子数据的修剪这样的任务变成自动化的。
* Mongoose 的校验支持让你可以在 schema 层面决定什么样的数据是可接受的，而不是必须手工处理它。






