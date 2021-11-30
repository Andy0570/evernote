OAuth 背后的思想是，提供一种能与第三方匿名共享资源的身份验证技术。匿名资源共享可以理解为无需提供用户身份就能访问信息和资源的方式。

OAuth 为客户端提供了一种代表资源拥有者访问受保护资源的方法。在客户端访问受保护资源之前，它必须先从资源拥有者获取授权（访问许可），然后用访问许可交换访问令牌（代表许可的作用域、持续时间和其它属性）。客户端通过向资源服务器出示访问令牌来访问受保护资源。

访问令牌提供了一个抽象层，将不同的授权结构（如用户名密码、断言）替换成资源服务器可以理解的单一令牌。这种抽象使得分发短期有效的访问令牌成为可能，也使得资源服务器不必理解多种多样的授权机制。

## 角色

OAuth 定义了四个角色：

* **资源所有者（Resource Owner）**：能够授予对受保护资源的访问权限的实体，即用户。
* **资源服务器（Resource Server）**：服务提供商存放用户生成的资源的服务器。
* **客户端（Client）**：第三方应用程序。
* **授权服务器（Authorization Server）**：服务提供商专门用来处理授权认证的服务器。

## 协议流程

```
     +--------+                               +---------------+
     |        |--(A)- Authorization Request ->|   Resource    |
     |        |                               |     Owner     |
     |        |<-(B)-- Authorization Grant ---|               |
     |        |                               +---------------+
     |        |
     |        |                               +---------------+
     |        |--(C)-- Authorization Grant -->| Authorization |
     | Client |                               |     Server    |
     |        |<-(D)----- Access Token -------|               |
     |        |                               +---------------+
     |        |
     |        |                               +---------------+
     |        |--(E)----- Access Token ------>|    Resource   |
     |        |                               |     Server    |
     |        |<-(F)--- Protected Resource ---|               |
     +--------+                               +---------------+
```

（A）「**第三方应用程序**」要求「**资源所有者**」给予访问其资源的授权。
（B）「**资源所有者**」同意授权，并向「**第三方应用程序**」返回一个授权码。
（C）「**第三方应用程序**」使用上一步获得的授权码，向「**授权服务器**」申请资源访问凭证（Access Token），即访问令牌。
（D）「**授权服务器**」对「**第三方应用程序**」进行权限认证，认证通过后，向「**第三方应用程序**」返回一个该「**资源所有者**」的资源访问凭证（Access Token）。
（E）「**第三方应用程序**」使用获得的资源访问凭证（Access Token），向「**资源服务器**」请求相关资源。
（F）「**资源服务器**」验证凭证（Access Token）无误后，将「**第三方应用程序**」请求的资源返回。

## 客户端的授权模式

客户端必须得到用户的授权（authorization grant），才能获得令牌（access token）。OAuth 2.0 定义了四种授权方式。

* 授权码模式（Authorization Code Grant）；
* 简化模式（Implicit Grant）；
* 密码模式（Resource Owner Password Credentials Grant）；
* 客户端模式（Client Credentials Grant）；

## 授权码模式

授权码模式（Authorization Code Grant）是功能最完整、流程最严密的授权模式。它的特点就是通过客户端的后台服务器，与"服务提供商"的认证服务器进行互动。

```
     +----------+
     | Resource |
     |   Owner  |
     |          |
     +----------+
          ^
          |
         (B)
     +----|-----+          Client Identifier      +---------------+
     |         -+----(A)-- & Redirection URI ---->|               |
     |  User-   |                                 | Authorization |
     |  Agent  -+----(B)-- User authenticates --->|     Server    |
     |          |                                 |               |
     |         -+----(C)-- Authorization Code ---<|               |
     +-|----|---+                                 +---------------+
       |    |                                         ^      v
      (A)  (C)                                        |      |
       |    |                                         |      |
       ^    v                                         |      |
     +---------+                                      |      |
     |         |>---(D)-- Authorization Code ---------'      |
     |  Client |          & Redirection URI                  |
     |         |                                             |
     |         |<---(E)----- Access Token -------------------'
     +---------+       (w/ Optional Refresh Token)
```

（A）用户访问客户端，后者将前者导向认证服务器。
（B）用户选择是否给予客户端授权。
（C）假设用户给予授权，认证服务器将用户导向客户端事先指定的 "重定向 URI"（redirection URI），同时附上一个授权码。
（D）客户端收到授权码，附上早先的 "重定向 URI"，向认证服务器申请令牌。这一步是在客户端的后台的服务器上完成的，对用户不可见。
（E）认证服务器核对了授权码和重定向 URI，确认无误后，向客户端发送访问令牌（access token）和更新令牌（refresh token）。

### 申请客户端 ID 和密钥

当第三方网站想要获取用户在资源服务器上的资源时，通常需要提前向资源服务器登记以申请接入，获得客户端ID和密钥信息，比如我通过向 [GitHub](https://github.com/settings/applications/new) 申请开通 OAuth2.0 服务时，返回的密钥信息如下：

| 参数 | 内容 |
| ---  | --- |
| Client ID | 9fdd83d1eb9983cbacbb |
| Client Secret | e0e62d3b1813affdss7c26827f7a9f5fc665840e |

* Client ID：应用的唯一标识。
* Client Secret：应用对应的密钥，访问用户资源时用来验证应用的合法性。


### 授权码

* 授权码在基于重定向的授权流程中使用。一个授权码只能使用一次。
* 官方建议授权码的有效期最多设置为 10 分钟。
* 授权码和客户端 ID 、客户端重定向 URL 是一一对应关系。

客户端请求体参数：

| 参数 | 必要性 | 说明 |
| --- | --- | --- |
| response_type | 必需参数 | 授权类型，此处的值固定为 `code` |
| client_id | 必需参数 | 客户端 id |
| redirect_uri | 可选参数 | 重定向 URL |
| scope | 可选参数 | 表示申请的权限范围 |
| state | 推荐参数 | 表示客户端的当前状态，可以指定任意值，认证服务器会原封不动地返回这个值。 |

客户端请求示例：

```bash
GET /authorize?response_type=code&client_id=s6BhdRkqt3&state=xyz
        &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb HTTP/1.1
Host: server.example.com
```


授权服务器返回带授权码的响应体参数：

| 参数 | 必要性 | 说明 |
| --- | --- | --- |
| code | 必需参数 | 授权码，用于绑定客户端重定向 URL 和客户端标识符的访问权限 |
| state | 必需参数 | 该参数用于防跨站请求伪造（CSRF），如果该参数由客户端上传了，则服务端需要原样返回 |


授权服务器返回示例：

```bash
HTTP/1.1 302 Found
Location: https://client.example.com/cb?code=SplxlOBeZQQYbYS6WxSbIA
        &state=xyz
```

错误响应参数：

| 参数 | 必要性 | 说明 |
| --- | --- | --- |
| error | 必需参数 | 错误码 |
| error_description | 可选参数 | 提供额外信息的一段人类可读的文字，用来帮助理解和解决发生的错误 |
| error_uri | 可选参数 | 指明了一个人类可读的网页 URI，带有关于错误的信息，用来为终端用户提供与错误有关的额外信息。 |
| state | 必需参数 | 如果 “state” 参数在客户端授权请求中存在，则这个参数是必需的。需要精确地设置成从客户端接收到的值。 |

授权服务器在错误响应中包含下列错误码之一：

| 错误代码 | 说明 |
| --- | --- |
invalid_request  | 请求缺少某个必需参数，包含一个不支持的参数或参数值，或者格式不正确。
invalid_client   | 提供的客户端标识符是无效的。
unauthorized_client  | 客户端没有权限使用该请求的响应类型。
redirect_uri_mismatch  | 提供的重定向 URI 与预先注册的值不匹配。
access_denied   | 终端用户或授权服务器拒绝了请求。
unsupported_response_type  | 请求的响应类型不为授权服务器所支持。
invalid_scope    | 请求的作用域是无效的、未知的，或格式不正确的。




### 访问令牌

* 访问令牌是用于访问受保护资源的短期令牌。
* 客户端（通常是第三方服务器后台执行）需要通过授权码获取访问令牌。

客户端请求体参数：

| 参数 | 必要性 | 说明 |
| --- | --- | --- |
| grant_type | 必须 | 表示使用的授权模式，此处的值固定为 `authorization_code` |
| code | 必须 | 上一步获得的授权码 |
| redirect_uri | 必须 | 重定向 URL，必须与上一步中的参数值保持一致 |
| client_id | 必须 | 客户端 ID |
| client_secret | 必须 | 客户端密钥 |


客户端请求示例：

```bash
POST /token HTTP/1.1
Host: server.example.com
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&code=SplxlOBeZQQYbYS6WxSbIA
&redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb
```


「客户端的后台服务器」请求「授权服务器」获取访问令牌成功后的响应体参数：


| 参数 | 必要性 | 说明 |
| --- | --- | --- |
| access_token | 必须 | 授权服务器签发的访问令牌 |
| token_type | 必须 | 令牌类型，告知客户端如何利用访问令牌请求用户资源，可以是 bearer 类型、mac 类型 或 JSON Web Token 类型 |
| expires_in | 推荐 | 存活时间，单位为秒。如果省略该参数，必须以其他方式设置访问令牌的过期时间 |
| refresh_token | 可选 | 更新令牌，表示使用相同的授权许可刷新访问令牌，以获取下一次的访问令牌 |
| scope | 可选 | 可以访问的资源范围，如果与客户端申请的范围一致，此项可省略 |


服务端响应示例：

```bash
 HTTP/1.1 200 OK
 Content-Type: application/json;charset=UTF-8
 Cache-Control: no-store
 Pragma: no-cache

 {
   "access_token":"2YotnFZFEjr1zCsicMWpAA",
   "token_type":"example",
   "expires_in":3600,
   "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
   "scope":"",
   "example_parameter":"example_value"
 }
```

### 更新令牌

如果用户访问的时候，客户端的 "访问令牌" 已经过期，则需要使用 "更新令牌" 申请一个新的访问令牌。

客户端更新令牌请求参数：

| 参数 | 必要性 | 说明 |
| --- | --- | --- |
| grant_type | 必须 | 表示使用的授权模式，此处的值固定为 `refresh_token` |
| refresh_token | 必须 | 早前收到的更新令牌 |
| scope | 可选 | 表示申请的授权范围，不可以超出上一次申请的范围，如果省略该参数，则表示与上一次一致。 |
| client_id | 必须 | 客户端 ID |
| client_secret | 必须 | 客户端密钥 |

客户端请求示例：

```bash
POST /token HTTP/1.1
Host: server.example.com
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&refresh_token=tGzv3JOkF0XG5Qx2TlKWIA
```


### 处理错误

OAuth 规范中，为了帮助实现者查明错误原因，提供了错误代码，见 [RFC-6749](https://tools.ietf.org/html/rfc6749#section-4.1.1)


| 错误代码 | 说明 |
| --- | --- |
| `invalid_request` | 参数缺失，参数值无效或参数重复 |
| `unauthorized_client` | 恶意请求 |
| `access_denied` | 资源属主或授权服务器拒绝请求 |
| `unsupported_response_type` | 授权服务器不支持使用这个方法获得授权码 |
| `invalid_scope` | 请求的范围无效、未知或损坏 |
| `invalid_grant` | 提供的授权许可或刷新令牌无效、过期、或者客户端详情（重定向 uri、客户端自身）与授权请求中定义的不一致 |
| `server_error` | 无法给客户端发送 500 错误时可使用内部服务器错误 |
| `temporarily_unavailable` | 服务器目前无法处理请求。重定向无法返回 503 错误时，可以使用这个代码 |


## 参考

* [RFC6749 - The OAuth 2.0 Authorization Framework](http://www.rfcreader.com/#rfc6749)
* [OAuth 2.0 中文译本](http://blog.sina.com.cn/s/blog_63d7b9a10100ofr3.html)
* [知乎 - OAuth2.0 认证和授权机制讲解](https://zhuanlan.zhihu.com/p/20913727)
* [阮一峰的网络日志 - 理解 OAuth 2.0](https://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)
* [阮一峰的网络日志 - OAuth 2.0 的一个简单解释](http://www.ruanyifeng.com/blog/2019/04/oauth_design.html)
* [阮一峰的网络日志 - OAuth 2.0 的四种方式](http://www.ruanyifeng.com/blog/2019/04/oauth-grant-types.html)
* [阮一峰的网络日志 - GitHub OAuth 第三方登录示例教程](http://www.ruanyifeng.com/blog/2019/04/github-oauth.html)
* [脚本之家 - nodejs 实现 OAuth2.0 授权服务认证](https://www.jb51.net/article/131668.htm)
* [CNode - 使用 Egg.js 快速开发 OAuth 2.0 授权服务](https://cnodejs.org/topic/592b2aedba8670562a40f60b)
* [腾讯开放平台：OAuth2.0 开发文档](http://wiki.open.qq.com/wiki/website/OAuth2.0%E5%BC%80%E5%8F%91%E6%96%87%E6%A1%A3)