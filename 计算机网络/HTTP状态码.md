## 快速预览

```text
200 Ok - [GET]：服务器成功返回用户请求的数据，该操作是幂等的。
201 Created - [POST/PUT/PATCH]：用户新建或修改数据成功。
202 Accepted - [*]：表示一个请求已经进入后台排队（异步任务）
204 No Content - [DELETE]：用户删除数据成功。

400 Invalid Request - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作，该操作是幂等的。
401 Unauthorized - [*]：表示用户没有权限（令牌、用户名、密码错误）。
403 Forbidden - [*] 表示用户得到授权（与401错误相对），但是访问是被禁止的。
404 Not Found - [*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。
406 Not Acceptable - [GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。
409 Conflict - 通用冲突
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
503 Server Unavaliable - 服务端当前无法处理请求
533 INTERNAL SERVER ERROR - [*]：keystore 不存在
534 INTERNAL SERVER ERROR - [*]：keystore 已过期
```


## HTTP 状态码

HTTP 状态码负责表示客户端 HTTP 请求的返回结果、标记服务器端的处理是否正常、通知出现的错误等工作。

状态码的职责是当客户端向服务器端发送请求时，描述返回的请求结果。借助状态码，用户可以知道服务器端是正常处理了请求，还是出现了错误。

![图片来自《图解 HTTP》](https://upload-images.jianshu.io/upload_images/2648731-807d06ebe23d32fa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

状态码如 `200 OK`，以 3 位数字和原因短语组成。数字中的第一位指定了响应类别，后两位无分类。响应类别有以下 5 种：

| 状态码的类别 | 类别                             | 原因短语                   |
| ------------ | -------------------------------- | -------------------------- |
| 1xx          | Informational（信息性状态码）    | 接收的请求正在处理         |
| 2xx          | Success（成功状态码）            | 请求正常处理完毕           |
| 3xx          | Redirection（重定向状态码）      | 需要进行附加操作以完成请求 |
| 4xx          | Client Error（客户端错误状态码） | 服务器无法处理请求         |
| 5xx          | Server Error（服务器错误状态码） | 服务器处理请求出错         |

注：只要**遵守状态码类别的定义**，即使改变 RFC2616 中定义的状态码， 或服务器端自行创建状态码都没问题。

### 1xx 消息

1xx 范围的状态码是保留给底层 HTTP 功能使用的，估计在你的职业生涯里面也用不着手动发送这样一个状态码出来。

| Status Code | Description                                  |
| ----------- | -------------------------------------------- |
| 100         | continue                                     |
| 101         | switching protocols                          |
| 102         | processing                                   |

### 2xx 成功

2xx 范围的状态码是保留给成功消息使用的，你应该尽可能地确保服务器总是发送这些状态码给用户。

| Status Code | Description                              |
| ----------- | -------------------------------------------- |
| 200         | ok，GET 请求成功                       |
| 201         | created，POST 请求已经被实现   |
| 202         | accepted                                     |
| 203         | non-authoritative information |
| 204         | no content，成功处理请求，没有返回任何内容。 |
| 205         | reset content                                |
| 206         | partial content，成功处理了范围请求 |
| 207         | multi-status                                 |
| 208         | already reported                         |
| 226         | im used                                      |

### 3xx 重定向

3xx 范围的状态码是保留给重定向用的。大多数的 API 不会太常使用这类状态码，但是在新的超媒体样式的 API 中会使用更多一些。

| Status Code | Description                                  |
| ----------- | -------------------------------------------- |
| 300         | multiple choices                             |
| 301         | moved permanently，永久性重定向 |
| 302         | found，临时性重定向  |
| 303         | see other，该状态码表示由于请求对应的资源存在着另一个 URI，应使用 GET 方法定向获取请求的资源。 |
| 304         | not modified，资源已找到，但未符合条件请求 |
| 305         | use proxy                                    |
| 307         | temporary redirect ，临时性重定向  |
| 308         | permanent redirect                           |

### 4xx 客户端错误

4xx 范围的状态码是保留给客户端错误用的。例如，客户端提供了一些错误的数据或请求了不存在的内容。这些请求应该是幂等的，不会改变任何服务器的状态。

| Status Code | Description                                  |
| ----------- | -------------------------------------------- |
| 400         | bad request，请求错误                           |
| 401         | unauthorized，身份验证失败，或者未提供身份验证信息     |
| 402         | payment required                             |
| 403         | forbidden，对请求资源的访问被服务器拒绝了 |
| 404         | not found，服务器上无法找到请求的资源 |
| 405         | method not allowed                           |
| 406         | not acceptable                               |
| 407         | proxy authentication required                |
| 408         | request timeout 请求超时                         |
| 409         | conflict                                     |
| 410         | gone                                         |
| 411         | length required                              |
| 412         | precondition failed                          |
| 413         | payload too large                            |
| 414         | uri too long                                 |
| 415         | unsupported media type  不支持的媒体类型          |
| 416         | range not satisfiable                        |
| 417         | expectation failed                           |
| 418         | I'm a teapot                                 |
| 422         | unprocessable entity                         |
| 423         | locked                                       |
| 424         | failed dependency                            |
| 426         | upgrade required 需要升级                     |
| 428         | precondition required                        |
| 429         | too many requests 请求次数过多                 |
| 431         | request header fields too large  请求头数量太大  |

### 5xx 服务器错误

5xx 范围的状态码是保留给服务器端错误用的。这些错误常常是从底层的函数抛出来的，并且开发人员也通常没法处理。发送这类状态码的目的是确保客户端能得到一些响应。收到 5xx 响应后，客户端没办法知道服务器端的状态，所以这类状态码是要尽可能的避免。

| Status Code | Description                                  |
| ----------- | -------------------------------------------- |
| 500         | internal server error，服务器端在执行请求时发生了错误  |
| 501         | not implemented                              |
| 502         | bad gateway，网关错误                           |
| 503         | service unavailable，服务器暂时处于超负载或正在进行停机维护，现在无法处理请求|
| 504         | gateway timeout，网关超时                       |
| 505         | http version not supported，不支持的 HTTP 版本     |
| 506         | variant also negotiates                      |
| 507         | insufficient storage                         |
| 508         | loop detected                                |
| 510         | not extended                                 |
| 511         | network authentication required              |


### 请求成功

* 200 **OK** : 请求执行成功并返回相应数据，如 `GET` 成功。
* 201 **Created** : 对象创建成功并返回相应资源数据，如 `POST` 成功；创建完成后响应头中应该携带头标 `Location` ，指向新建资源的地址。
* 202 **Accepted** : 接受请求，但无法立即完成创建行为，比如其中涉及到一个需要花费若干小时才能完成的任务。返回的实体中应该包含当前状态的信息，以及指向处理状态监视器或状态预测的指针，以便客户端能够获取最新状态。
* 204 **No Content** : 请求执行成功，不返回相应资源数据，如 `PATCH` ， `DELETE` 成功。

### 重定向

**重定向的新地址都需要在响应头 `Location` 中返回**

* 301 **Moved Permanently** : 被请求的资源已永久移动到新位置
* 302 **Found** : 请求的资源现在临时从不同的 URI 响应请求
* 303 **See Other** : 对应当前请求的响应可以在另一个 URI 上被找到，客户端应该使用 `GET` 方法进行请求。比如在创建已经被创建的资源时，可以返回 `303`
* 307 **Temporary Redirect** : 对应当前请求的响应可以在另一个 URI 上被找到，客户端应该保持原有的请求方法进行请求

### 条件请求

* 304 **Not Modified** : 资源自从上次请求后没有再次发生变化，主要使用场景在于实现[数据缓存](#user-content-数据缓存)
* 409 **Conflict** : 请求操作和资源的当前状态存在冲突。主要使用场景在于实现[并发控制](#user-content-并发控制)
* 412 **Precondition Failed** : 服务器在验证在请求的头字段中给出先决条件时，没能满足其中的一个或多个。主要使用场景在于实现[并发控制](#user-content-并发控制)

### 客户端错误

* 400 **Bad Request** : 请求体包含语法错误
* 401 **Unauthorized** : 需要验证用户身份，如果服务器就算是身份验证后也不允许客户访问资源，应该响应 `403 Forbidden` 。如果请求里有 `Authorization` 头，那么必须返回一个 [`WWW-Authenticate`](https://tools.ietf.org/html/rfc7235#section-4.1) 头
* 403 **Forbidden** : 服务器拒绝执行
* 404 **Not Found** : 找不到目标资源
* 405 **Method Not Allowed** : 不允许执行目标方法，响应中应该带有 `Allow` 头，内容为对该资源有效的 HTTP 方法
* 406 **Not Acceptable** : 服务器不支持客户端请求的内容格式，但响应里会包含服务端能够给出的格式的数据，并在 `Content-Type` 中声明格式名称
* 410 **Gone** : 被请求的资源已被删除，只有在确定了这种情况是永久性的时候才可以使用，否则建议使用 `404 Not Found`
* 413 **Payload Too Large** : `POST` 或者 `PUT` 请求的消息实体过大
* 415 **Unsupported Media Type** : 服务器不支持请求中提交的数据的格式
* 422 **Unprocessable Entity** : 请求格式正确，但是由于含有语义错误，无法响应
* 428 **Precondition Required** : 要求先决条件，如果想要请求能成功必须满足一些预设的条件

### 服务端错误

* 500 **Internal Server Error** : 服务器遇到了一个未曾预料的状况，导致了它无法完成对请求的处理。
* 501 **Not Implemented** : 服务器不支持当前请求所需要的某个功能。
* 502 **Bad Gateway** : 作为网关或者代理工作的服务器尝试执行请求时，从上游服务器接收到无效的响应。
* 503 **Service Unavailable** : 由于临时的服务器维护或者过载，服务器当前无法处理请求。这个状况是临时的，并且将在一段时间以后恢复。如果能够预计延迟时间，那么响应中可以包含一个 `Retry-After` 头用以标明这个延迟时间（内容可以为数字，单位为秒；或者是一个 [HTTP 协议指定的时间格式](http://tools.ietf.org/html/rfc2616#section-3.3)）。如果没有给出这个 `Retry-After` 信息，那么客户端应当以处理 500 响应的方式处理它。

`501` 与 `405` 的区别是：`405` 是表示服务端不允许客户端这么做，`501` 是表示客户端或许可以这么做，但服务端还没有实现这个功能


## 参考

* [RFC 2616 - Hypertext Transfer Protocol -- HTTP/1.1](https://www.ietf.org/rfc/rfc2616.txt)
* [RFC 2616 - Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)
* [Hypertext Transfer Protocol (HTTP) Status Code Registry](http://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml)


