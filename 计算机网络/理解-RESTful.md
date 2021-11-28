## REST 的定义

> REST 一词是由 Roy Fielding 博士于 2000 年在他的博士论文 *Architectural Styles and the Design of Network-based Software Architecture* 中提出的，实际指一种有助于创建和组织分布式系统的架构风格。它并不是一个标准或准则，而是**一种基于资源的架构风格**。
>
> 基于 REST 风格构建的 API 应该满足 CS 模式交互、统一的资源接口、透明的分层系统以及支持无状态和缓存等约束条件，另外需要保证 API 的安全性。
>
> 以 REST 风格构建的系统将在性能、组件交互的可扩展性、接口的简单性、可移植性、可靠性、可见性等多个方面得到提升和优化。
>
> ——摘自《RESTful API 开发实战》

> 注：Roy Fielding 是 HTTP 协议（1.0 版和 1.1 版）的主要设计者、Apache 服务器软件的作者之一、Apache 基金会的第一任主席。


REST = Representational State Transfer，表述性状态传递/表现层状态转移。

REST 描述网络中服务器和客户端的交互形式。

**资源在网络中以某种表现形式进行状态转移。**

Resource：资源，即数据。比如 newsfeed，friends 等；
Representational：某种表现形式，比如 JSON，XML，JPEG 等；
State Transfer：状态变化。通过 HTTP 动词（GET、POST、PUT、DELETE）实现。


1. 每一个 URI 代表一种资源；
2. 客户端和服务器之间，传递这种资源的某种表现层（资源的呈现形式）；
3. 客户端通过四个 HTTP 动词，对服务器端资源进行操作，实现 "表现层状态转化"。



💡 总结：**用 URL 定位资源，用 HTTP 动词（GET、POST、PUT、DELETE）描述操作，实现资源的状态转换。**



## REST 的基本原理

REST 基于资源架构，资源可通过基于 HTTP 标准方法的通用接口访问。REST 要求开发人员显式使用 HTTP 方法，并以符合协议定义的方式使用。每个资源都有一个 URL 标识，且都应该支持 HTTP 的通用操作，同时 REST 允许该资源有不同的表现形式，例如文本、XML、JSON 等。REST 客户端可以通过 HTTP 协议（内容协商，服务端以正确的格式响应内容）请求特定的表现形式。

| 数据元素   | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| 资源       | 超文本引用的概念目标，例如 customer/order                    |
| 资源标识符 | 统一资源定位符（URL）或统一资源名称（URN）标识特定的资源     |
| 资源元数据 | 描述资源信息，如标签、作者、源链接、替代位置、别名           |
| 表现形式   | 资源内容——JSON 消息、HTML 文档、JPEG 图片                    |
| 表现元数据 | 描述如何处理表现的信息，如媒介类型、最后修改时间             |
| 控制数据   | 描述如何优化响应处理的信息，例如 if-modified-since、cache-control-expiry |



## 操作类型

通过 HTTP 动词来操作资源。

表一：

| HTTP 方法         | 描述                                              |
| ----------------- | ------------------------------------------------- |
| OPTIONS（不常用） | 获取信息，关于资源的哪些属性是客户端可以操作的    |
| HEAD（不常用）    | 获取资源的元数据（数据的哈希值、最后更新时间...） |
| GET               | 从服务器上获取资源（一项或多项）                  |
| POST              | 在服务器新建一个资源                              |
| PUT               | 在服务器更新资源（更新资源整体）                  |
| PATCH             | 在服务器更新部分资源（更新资源的某一项属性）      |
| DELETE            | 从服务器删除资源                                  |

表二：

| 方法 | 描述 | 响应内容 |
| --- | --- | --- |
| `OPTIONS` | 获取资源支持的所有 HTTP 方法 | - |
| `HEAD` | 只获取请求某个资源返回的头信息 | - |
| `GET` | 从服务器获取某个资源的信息 | 完成请求后返回状态码 `200 OK`<br>完成请求后需要返回被请求的资源详细信息 |
| `POST` | 创建新资源 | 创建完成后返回状态码 `201 Created`<br>完成请求后需要返回被创建的资源详细信息 |
| `PUT` | 完整的替换资源或者创建指定身份的资源 | 如果是创建了资源，则返回 `201 Created`<br>如果是替换了资源，则返回 `200 OK`<br>完成请求后需要返回被修改的资源详细信息 |
| `PATCH` | 局部更新资源 | 完成请求后返回状态码 `200 OK`<br>完成请求后需要返回被修改的资源详细信息 |
| `DELETE` | 删除某个资源 | 完成请求后返回状态码 `204 No Content` |


## 安全性和幂等性

**安全性**：不会改变资源状态，可以理解为只读的；

**幂等性**：无边际效应，多次操作得到相同的结果。

当向系统发送 **GET**、**DELETE** 或 **PUT** 方法时，不论该方法发送多少次，执行的效果应该是一样的。但是 **POST** 方法在集合中创建了实体，因此不是幂等的。

| HTTP 动词 | 安全性 | 幂等性 |
| --------- | ------ | ------ |
| GET       | ✅      | ✅      |
| POST      | ❌      | ❌      |
| PUT       | ❌      | ✅      |
| DELETE    | ❌      | ✅      |

安全性和幂等性均不保证反复请求能拿到相同的 response。以 DELETE 为例，第一次 DELETE 返回 200 表示删除成功，第二次返回 404 提示资源不存在，这是允许的。



## 预期的返回结果

当使用不同的 HTTP 动词向服务器发送请求时，服务器向客户端返回的结果应该符合以下规范：

| HTTP 方法 | 路径                 | response 格式              |
| --------- | -------------------- | -------------------------- |
| GET       | /collection          | 返回资源对象的列表（数组） |
| GET       | /collection/resource | 返回单个资源对象           |
| POST      | /collection          | 返回新生成的资源对象       |
| PUT       | /collection/resource | 返回完整的资源对象         |
| PATCH     | /collection/resource | 返回完整的资源对象         |
| DELETE    | /collection/resource | 返回一个空文档             |



## API 设计策略

1. 螺栓策略；
2. 绿地策略；
3. 敏捷策略；
4. 外观策略；



## RESTful API 设计的最佳实践

### 1. 保持基础 URL 简明直观

简明直观的 URL 设计可以体现 API 的**功能可见性**（接口交互双方不需要文档就可以理解使用这一设计特性）。

对于 Web API 而言，每个资源都应该只有两个基础 URL：

| 序号 | 基础URL         | 描述                 |
| ---- | --------------- | -------------------- |
| 1    | /customers      | 集合                 |
| 2    | /customers/{id} | 集合中的某个特定元素 |

使用 HTTP 动词对资源集合和元素进行操作：

| 资源            | POST 创建 | GET 读取     | PUT 更新   | DELETE 删除  |
| --------------- | --------- | ------------ | ---------- | ------------ |
| /customers      | 新建客户  |  获取客户列表  | 批量更新     | 删除所有客户   |
| /customers/{id} | --    | 展示某个客户 | 客户存在就更新，不存在就返回 404| 删除某个客户 |



### 2. 错误处理

优秀的异常和错误处理设计对 API 设计者来说非常重要。

一个 API 应当以一种已知的可使用的格式来提供有用的错误信息。

API 应当总是返回有意义的 HTTP 状态代码。API 错误通常被分成两种类型：代表客户端问题的 400 系列状态码和代表服务器问题的 500 系列状态码。最简情况下，API 应当把便于使用的 JSON 格式作为 400 系列错误的标准化表示。如果可能 （意思是，如果负载均衡和反向代理能创建自定义的错误实体）, 这也适用于 500 系列错误代码。

```json
{
  "code" : 1234,
  "message" : "Something bad happened",
  "description" : "More details about the error here"
}
```

对 PUT, PATCH 和 POST 请求进行错误验证将需要一个字段分解。下面可能是最好的模式：**使用一个固定的顶层错误代码来验证错误，并在额外的字段中提供详细错误信息**，就像这样:

```json
{
  "code" : 1024,
  "message" : "Validation Failed",
  "errors" : [
    {
      "code" : 5432,
      "field" : "first_name",
      "message" : "First name cannot have fancy characters"
    },
    {
       "code" : 5622,
       "field" : "password",
       "message" : "Password cannot be blank"
    }
  ]
}
```



### 3. HTTP 状态码

一个请求是否成功是由 HTTP 状态码标明的。一个 2XX 的状态码表示请求成功，而一个 4XX 的状态码表示请求失败。

```text
200 OK - [GET]：服务器成功返回用户请求的数据，该操作是幂等的（Idempotent）。
201 CREATED - [POST/PUT/PATCH]：用户新建或修改数据成功。
202 Accepted - [*]：表示一个请求已经进入后台排队（异步任务）
204 NO CONTENT - [DELETE]：用户删除数据成功。

400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作，该操作是幂等的。
401 Unauthorized - [*]：表示用户没有权限（令牌、用户名、密码错误）。
403 Forbidden - [*] 表示用户得到授权（与401错误相对），但是访问是被禁止的。
404 NOT FOUND - [*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。
406 Not Acceptable - [GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。
410 Gone -[GET]：用户请求的资源被永久删除，且不会再得到的。表示资源在终端不再可用。
415 Unsupported Media Type (不支持的媒体类型) - 如果请求中包含了不正确的内容类型
422 Unprocesable entity - [POST/PUT/PATCH] 当创建一个对象时，发生一个验证错误。
429 Too Many Requests (请求过多) - 当请求由于访问速率限制而被拒绝时

/**
* 在接口处理发生错误的时候，如果是客户端请求参数导致的错误，返回 4xx 状态码，
* 如果是服务端自身的处理逻辑错误，返回 5xx 状态码。
* 所有的异常对象都是对这个异常状态的描述，其中 error 字段是错误的描述，detail 字段（可选）是导致错误的详细原因。
*/
500 INTERNAL SERVER ERROR - [*]：服务器发生错误，用户将无法判断发出的请求是否成功
533 INTERNAL SERVER ERROR - [*]：keystore 不存在
534 INTERNAL SERVER ERROR - [*]：keystore 已过期
```

HTTP 状态码参考：

* [Hypertext Transfer Protocol (HTTP) Status Code Registry](http://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml)
* [RFC 2616 - Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)
* [wikiwand: HTTP 状态码](https://www.wikiwand.com/zh-hans/HTTP%E7%8A%B6%E6%80%81%E7%A0%81)
* [RESTful 接口调试中常见返回结果汇总](https://blog.csdn.net/fred_lzy/article/details/51629232)



### 4. 版本控制

 一个好的 RESTful API 会在 URL 中包含版本信息。另一种比较常见的方案是在 HTTP 请求头（Accept 字段）中包含版本信息。但是与很多不同的第三方开发者一起工作之后，我可以很明确的告诉你，在 HTTP 请求头中包含版本信息远没有将其放在 URL 中来的容易（方便），而且将版本号放在 URL 中也更加直观明了。

* API 版本必须是强制性的。
* 向后兼容性：必须在引入新版本 API 的同时保持旧版本 API 仍然可用。

常见的根 URL 示例：

```
// 将 API 放在主域名下：
https://www.example.com/api/v1/*
https://www.example.com/api/v2/*

// 将 API 部署在专用域名之下：
https://api.example.com/v1/*
https://api.example.com/v2/*
```



### 5. 过滤、排序和搜索

API 应该向开发人员提供他们需要的信息，而不是所有信息。

复杂查询可以带上过滤条件，常见参数：

| 常见过滤类型 | 参数示例                 | 描述                                         |
| ------------ | ------------------------ | -------------------------------------------- |
| 偏移         | ?offset=10               | 指定返回记录的开始位置                       |
| 限制         | ?limit=10                | 指定返回记录的数量                           |
| 页数         | ?page=2&per_page=100     | 指定第几页，以及每页的记录数                 |
| 排序         | ?sortby=name&order=asc   | 指定返回结果按照哪个属性排序，以及排序顺序。 |
| 过滤         | ?animal_type_id=1        | 指定筛选条件                                 |
|              | ?type=1&age=16           | 指定筛选条件                                 |
| 投影         | ?whitelist=id,name,email |                                              |
| 局部响应     | ?fields=title,media      | 仅返回部分信息，标题和内容                   |



### 6. 多格式支持（只返回 JSON）

数据交换格式：建议使用基于 JSON 的媒体类型（`Content-Type: application/json`）

```
POST /v1/animal HTTP/1.1
Host: api.example.org
Accept: application/json
Content-Type: application/json
Content-Length: 24

{   
  "name": "Gir",
  "animalType": "12"
}
```

不推荐使用 XML 作为数据交换格式。

参考：[Google 趋势图，比较 XML 和 JSON API](https://trends.google.com/trends/explore?date=all&q=xml%20api,json%20api)



### 7. 端点（EndPoints）

一个端点就是指向特定资源或资源集合的 URL。

API 端点的名词应该使用复数而不是单数。

如果你正在构建一个虚构的 API 来展现几个不同的动物园，每个动物园又包含动物、员工和每个动物的物种，你可能会有如下的端点信息：

* https://api.example.com/v1/**zoos**
* https://api.example.com/v1/**animals**
* https://api.example.com/v1/**animal_types**
* https://api.example.com/v1/**employees**

针对每个端点，你可能想列出所有可行的 HTTP 动词和端点的组合：

| HTTP 方法 | 路径 | 描述 |
| --------- | ---- | ---- |
|GET |/zoos | List all Zoos (ID and Name, not too much detail) |
|GET |/zoos/ZID| Retrieve an entire Zoo object|
|POST |/zoos|Create a new Zoo|
|PUT |/zoos/ZID| Update a Zoo (entire object)|
|PATCH | /zoos/ZID| Update a Zoo (partial object)|
|DELETE |/zoos/ZID|Delete a Zoo|
|GET |/zoos/ZID/animals |Retrieve a listing of Animals (ID and Name).|
|GET |/animals|List all Animals (ID and Name).|
|POST |/animals| Create a new Animal|
|GET |/animals/AID| Retrieve an Animal object|
|PUT |/animals/AID|Update an Animal (entire object)|
|PATCH |/animals/AID| Update an Animal (partial object)|
|GET| /animal_types|Retrieve a listing (ID and Name) of all Animal Types|
|GET |/animal_types/ATID| Retrieve an entire Animal Type object|
|GET| /employees| Retrieve an entire list of Employees|
|GET |/employees/EID| Retreive a specific Employee|
|GET |/zoos/ZID/employees| Retrieve a listing of Employees (ID and Name) who work at this Zoo|
|POST| /employees| Create a new Employee|
|POST| /zoos/ZID/employees| Hire an Employee at a specific Zoo|
|DELETE| /zoos/ZID/employees/EID|Fire an Employee from a specific Zoo|


## 名词释义

常见的数据格式：

* XML（Extensible Markup Language，可扩展标记语言）；
* JSON（JavaScript Object Notation，JavaScript 对象表示法）；

常见的传输协议或 Web 服务：

* SOAP（Simple Object Access Protocol，简单对象访问协议）；
* REST（Representational State Transfer，表述性状态传递）；

其他：

* API（Application Programming Interface，应用程序编程接口）；
* APX（Application Programming Experience，应用编程体验）；
* HATEOAS（Hypermedia as the Engine of Application State， 超媒体作为应用程序状态引擎）



## 参考

* [GitHub：RESTful API 设计参考文献列表 ⭐️⭐️⭐️](https://github.com/aisuhua/restful-api-design-references)
* [HTTP 接口设计指北](https://github.com/bolasblack/http-api-guide)
* [RESTful API 设计最佳实践⭐️⭐️⭐️](https://www.oschina.net/translate/best-practices-for-a-pragmatic-restful-api)
* [好 RESTful API 的设计原则](https://www.cnblogs.com/moonz-wu/p/4211626.html)
* [RESTful - 知乎](https://www.zhihu.com/topic/19579308/top-answers)
* [理解 RESTful 架构@阮一峰](http://www.ruanyifeng.com/blog/2011/09/restful.html)
* [RESTful API 设计指南@阮一峰](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)
* [Restful API 的设计规范](http://novoland.github.io/%E8%AE%BE%E8%AE%A1/2015/08/17/Restful%20API%20%E7%9A%84%E8%AE%BE%E8%AE%A1%E8%A7%84%E8%8C%83.html)
* [Wikiwand：HTTP 状态码](https://www.wikiwand.com/zh-hans/HTTP%E7%8A%B6%E6%80%81%E7%A0%81)
