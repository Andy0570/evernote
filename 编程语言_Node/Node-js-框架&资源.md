## [nodeframework](http://nodeframework.com/) 上的框架分类

![Node.js 框架](http://upload-images.jianshu.io/upload_images/2648731-e9e72b9150b85b15.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 框架的定义

* **API 框架**——用于搭建 Web API 的库，有协助组织程序结构的框架支持。LoopBack将自己定义为这类框架。
* **HTTP 服务器库**——所有基于 Express 的项目都可以归为这一类，包括 Koa 和 Kraken.js 。这些库帮我们围绕 HTTP 动词和路由搭建程序。
* **HTTP服务器框架**——用来搭建模块化 HTTP 服务器的框架。hapi 就是这种框架。
* **Web MVC 框架**——模型-视图-控制器框架，Sail.js 就是这种框架。
* **全栈框架**——这些框架在服务器端和浏览器上用的都是  JavaScript，并且两端可以共享代码。这被称为**同构代码**。 DerbyJS 是个全栈 MVC 框架。



### Koa

| 库类别   | HTTP 服务器库                                   |
| -------- | ----------------------------------------------- |
| 功能特性 | 基于生成器的中间件，请求/响应模型               |
| 建议应用 | 轻型 Web 程序、不严格的 HTTP API、单页 Web 程序 |
| 插件架构 | 中间件                                          |
| 文档     | <https://koa.bootcss.com/>                      |
| 热门程度 | [GitHub](https://github.com/koajs/koa) 24198 ⭐️  |
| 授权许可 | MIT                                             |

> Koa 轻便、极简，在中间件中使用 ES2015 生成器语法。适合依赖外部 Web API 的单页 Web 程序。



### Kraken

| 库类别   | HTTP 服务器库                                                |
| -------- | ------------------------------------------------------------ |
| 功能特性 | 对项目结构要求严格、模型、模版（Dust）、安全强化（Lusca）、配置管理、国际化 |
| 建议应用 | 企业 Web 程序                                                |
| 插件架构 | Express 中间件                                               |
| 文档     | <https://www.kraken.com/help/api>                            |
| 热门程度 | [GitHub](https://github.com/krakenjs/kraken-js) 4698 ⭐️       |
| 授权许可 | Apache 2.0                                                   |

>  Kraken 是基于 Express 的，添加了安全特性。可以用于 MVC。



### hapi

| 库类别   | HTTP 服务器库                                    |
| -------- | ------------------------------------------------ |
| 功能特性 | 高层服务器容器抽象，安全的头部信息               |
| 建议应用 | 单页 Web 程序、HTTP API                          |
| 插件架构 | hapi 插件                                        |
| 文档     | <https://hapijs.com/>                            |
| 热门程度 | [GitHub](https://github.com/hapijs/hapi) 10000 ⭐️ |
| 授权许可 | BSD 3                                            |

> hapi 的重点是 HTTP 服务器和路由。适合由很多小服务器组成的轻便后台。



### Sails.js

| 库类别   | MVC 框架                                                |
| -------- | ------------------------------------------------------- |
| 功能特性 | 有支持数据库的 ORM，生成 REST API，WebSocket            |
| 建议应用 | Rails 风格的 MVC 程序                                   |
| 插件架构 | Express 中间件                                          |
| 文档     | <http://sailsjs.org/>                                   |
| 热门程度 | [GitHub](https://github.com/balderdashy/sails/) 19976 ⭐️ |
| 授权许可 | BSD 3                                                   |

>  Sails.js 是 Rails/Django 风格的 MVC 框架。有 ORM 和模板系统。



### DerbyJS

| 库类别   | 全栈框架                                           |
| -------- | -------------------------------------------------- |
| 功能特性 | 有支持数据库的 ORM（Racer），同构                  |
| 建议应用 | 有服务器端支持的单页 Web 程序                      |
| 插件架构 | DerbyJS 插件                                       |
| 文档     | <https://derbyjs.com/>                             |
| 热门程度 | [GitHub](https://github.com/derbyjs/derby/) 4358 ⭐️ |
| 授权许可 | MIT                                                |

>  DerbyJS 是个同构框架，适合实时程序。



### Flatiron.js

| 库类别   | 模块化 MVC 框架                                       |
| -------- | ----------------------------------------------------- |
| 功能特性 | 数据库管理层（Resourceful），解耦的可重用模块         |
| 建议应用 | 轻量的 MVC 程序，在其他框架中使用 Flatiron 模块       |
| 插件架构 | Broadway 插件 API                                     |
| 文档     | <https://github.com/flatiron/flatiron>                |
| 热门程度 | [GitHub](https://github.com/flatiron/flatiron) 1338 ⭐️ |
| 授权许可 | MIT                                                   |

> Flatiron 是一组解耦的模块，既可以当作 Web MVC 框架来用，也可以当作更轻便的 Express库。 Flatiron 跟 Connect 中间件是兼容的。



### LoopBack

| 库类别   | API 框架                                                 |
| -------- | -------------------------------------------------------- |
| 功能特性 | ORM、API 用户界面、WebScoket、客户端 SDK（包括 iOS）     |
| 建议应用 | 支持多客户端的 API（移动端、桌面端、Web）                |
| 插件架构 | Express 中间件                                           |
| 文档     | <https://loopback.io/>                                   |
| 热门程度 | [GitHub](https://github.com/strongloop/loopback) 12092 ⭐️ |
| 授权许可 | 双许可：MIT 和 StrongLoop 认购协议                       |

>  LoopBack 帮我们省掉了写套路化代码的工作。它可以快速生成带有数据库支持的 REST API，并有个 API 管理界面。

### 如何选择 Node 框架
![选择 Node 框架](http://upload-images.jianshu.io/upload_images/2648731-3a935214f44acf57.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 提升应用安全性的 Node 模块
* Lusca：[krakenjs](http://krakenjs.com) 中的一个安全模块。
* [Helmet](https://helmetjs.github.io/)：通过设置各种 HTTP 头来帮助您保护 Express 应用程序。
* nsp 模块：审计 Node 应用的 `package.json` 和 `npm- shrinkwrap.json`文件，检查有没有已知漏洞。



## 流行的 npm 包

* [Prettier](https://prettier.io/)——让你可以专注于你正在写的代码，而不是去关心代码的格式；
  Prettier 是一种代码格式化程序，它以特定的方式为你格式化代码。
* [eslint](https://eslint.org/)——保持代码整洁；
* [date-fns](https://date-fns.org/)——moment.js 的轻量级替代品；
* [lodash](https://lodash.com/)——主要用于 `throttle()` 和 `debounce()` 函数；
* [rambda](https://ramdajs.com/)——如果你真的喜欢函数式编程。
* [cross-env](<https://www.npmjs.com/package/cross-env>)
* [Yarn](<https://yarn.bootcss.com/>)——快速、可靠、安全的依赖管理工具。
* [art-template](<https://aui.github.io/art-template/zh-cn/docs/>)——JavaScript 模版引擎




## 文章资源
* [【知乎 Live】狼叔：如何正确的学习 Node.js](https://i5ting.github.io/How-to-learn-node-correctly/)
* [PDF：深入浅出 Node.js](http://okj45byt5.bkt.clouddn.com/%E6%B7%B1%E5%85%A5%E6%B5%85%E5%87%BANode.js.pdf)
* [阿里云出品：七天学会Node.js](http://nqdeng.github.io/7-days-nodejs/)
* [如何挑选高质量的 Node.js 模块](https://github.com/atian25/blog/issues/19)
* [node.js](https://www.jianshu.com/p/fbf4a7c94e61) - 基础科普性文章，值得观看⭐️⭐️⭐️
* [RESTful API 完整介绍](https://github.com/aisuhua/restful-api-design-references)
* [GitHub: process](https://github.com/ElemeFE/node-interview/blob/master/sections/zh-cn/process.md) 与 node.js 进程相关的一篇文章


