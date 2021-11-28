JSON Web Token（JWT）是目前最流行的跨域身份验证（CORS）解决方案。本文介绍 JWT 的原理和用法。
官网地址：[戳我](https://jwt.io/)

# 跨域身份验证的问题

Internet 服务离不开用户的身份验证。一般过程如下：

1. 用户向服务器发送用户名和密码。
2. 服务器验证通过后，在当前对话（session）中保存相关数据，比如用户角色、登录时间等。
3. 服务器向用户返回 session_id，写入到用户的 Cookie 中。
4. 用户随后的每一次请求，都会通过 Cookie，将 session_id 传回服务器。
5. 服务器收到 session_id 并对比之前保存的数据，由此确认用户的身份。

![](https://upload-images.jianshu.io/upload_images/2648731-254d75fca890c84d.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这种模式最大的问题是，没有分布式架构，不支持横向扩展。如果仅使用一台服务器，该模式完全没有问题。但是，如果是服务器群集，或者是跨域的服务导向架构，则需要一个统一的 session 数据库来实现会话数据共享，这样负载均衡下的每台服务器才可以正确的验证用户身份。

举一个实际中常见的单点登陆需求：站点 A 和站点 B 提供同一公司的相关服务。现在要求用户只需要登录其中一个网站，然后它就会自动登录到另一个网站。怎么做？

一种解决方案是通过持久化 session 数据，写入数据库或文件持久层。所有服务器收到请求后，都从持久层请求数据。该解决方案的优点在于架构清晰，而缺点是架构修改比较费劲，整个服务的验证逻辑层都需要重写，工作量相对较大。而且由于依赖于持久层的数据库或文件系统，会有单点故障的风险，如果持久层发生异常，整个认证体系都会挂掉。

![](https://upload-images.jianshu.io/upload_images/2648731-82d7a045a8ebc752.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

还有一种相对灵活的解决方案，通过客户端保存数据，而服务器根本不保存会话数据，每个请求都被发送回服务器。 JWT 是这种解决方案的代表。

![](https://upload-images.jianshu.io/upload_images/2648731-21ef7c2834886dde.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# JWT 的原理

JWT 的原理是，服务器的身份验证通过后，生成一个 JSON 对象并将其发送回用户，如下所示：
```json
{
  "UserName": "zhangsan",
  "Role": "Admin",
  "Expire": "2019-07-12 01:57:00"
}
```

之后，当客户端与服务器通信时，客户端在请求中返回该 JSON 对象。服务器仅依赖于这个 JSON 对象来标识用户身份。为了防止用户篡改数据，服务器将在生成对象时添加签名（有关详细信息，请参阅下文）。

服务器不保存任何会话数据，即服务器变为无状态，使其更容易扩展。

# JWT 的数据结构

实际上，一个 JWT 数据类似下图所示：

![](https://upload-images.jianshu.io/upload_images/2648731-bd62ac6b11d549ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

该对象是一个很长的字符串，字符之间通过 "." 分隔符分为三个子串。注意 JWT 对象为一个长字串，各字串之间也没有换行符，此处为了演示需要，我们特意分行并用不同颜色表示了。每一个子串表示了一个功能块，总共有以下三个部分：

* Header（JWT 头部）
* Payload（有效载荷）
* Signature（签名）

将它们写成一行如下所示：

![](https://upload-images.jianshu.io/upload_images/2648731-1de04217b5d97afa.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 1. JWT 头

JWT 头是描述 JWT 元数据的 JSON 对象，通常如下所示。

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

上面的代码中：
* `alg` 属性表示签名算法，默认为 HMAC SHA256（写成 HS256）；
* `typ` 属性表示令牌的类型，JWT 令牌统一写为 JWT。

最后，使用 Base64URL 算法将上述 JSON 对象转换为字符串。

## 2. 有效载荷

有效载荷，是 JWT 的主体内容部分，也是一个 JSON 对象，用来存放实际需要传递的数据。 JWT 指定七个默认字段供选择：

* iss (issuer)：令牌的签发人；
* exp (expiration time)：过期的时间戳，以秒为单位；
* sub (subject)：令牌的主题（很少使用）；
* aud (audience)：令牌的受众（很少使用）；
* nbf (Not Before)：生效时间，在此之前不可用；
* iat (Issued At)：令牌的签发时间；
* jti (JWT ID)：令牌的唯一标识符，用于防止 JWT 被重复使用或重放；

除以上默认字段外，我们也可以自定义字段类型，如：

```json
{
  "sub": "1234567890",
  "name": "chongchong",
  "admin": true
}
```

注意，默认情况下 JWT 是未加密的，任何人都可以读取其内容，因此不要把秘密信息放在这个部分，以防止信息泄露。

这个 JSON 对象也会使用 Base64URL 算法转换为字符串。

## 3. 哈希签名

哈希签名是对前面两部分数据进行签名，通过指定的算法生成哈希值，防止数据被篡改。

首先，需要指定一个密钥（secret）。该密钥仅保存在服务器中，并且不能向用户公开。然后，使用 JWT 头中指定的签名算法（默认情况下为 HMAC SHA256）根据以下公式生成签名：

```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

在计算出哈希签名后，JWT 头，有效载荷和哈希签名这三部分组合成一个字符串，每个部分用 "." 分隔，就构成了整个 JWT 对象。

### 4. Base64URL 算法

如前所述，JWT 头和有效载荷序列化的算法中都用到了 Base64URL。该算法和常见 Base64 算法类似，但稍有差别。

作为令牌的 JWT 可能会被放在 URL 中（例如 api.example/?token=xxx）。 而 Base64 中用到的三个字符 "+"，"/" 和 "=" 在 URL 中有特殊含义，因此 Base64URL 中对他们做了替换："=" 去掉，"+" 用 "-" 替换，"/" 用 "_" 替换，这就是 Base64URL 算法。

# JWT 的使用方式

客户端收到服务器返回的 JWT 后，将其存储在 Cookie 或 localStorage 中。

此后，客户端每次与服务器通信，都要带上这个 JWT。如果将它存储在 Cookie 中，就可以自动发送，但是这样不能实现跨域。因此，一般将它放在 HTTP 请求头（Header）的 `Authorization` 字段中。

```
Authorization: Bearer <token>
```

另一种做法是，当跨域时，将 JWT 放在 POST 请求体中。

# JWT 的特点

1. JWT 默认不加密，但也是可以加密的。生成原始 Token 后，可以用密钥再加密一次。
2. 没有对 JWT 进行加密时，请不要将秘密数据写入 JWT 中。
3. JWT 不仅可用于认证，还可用于信息交换。有效使用 JWT，可以减少服务器查询数据库的次数。
4. JWT 的最大缺点是，由于服务器不保存 session 状态，因此无法在使用过程中废止某个 token，或者更改 token 的权限。也就是说，一旦 JWT 签发，在有效期内将会一直有效，除非服务器部署额外的逻辑。
5. JWT 本身包含认证信息，因此一旦泄露，任何人都可以获得该令牌的所有权限。为了减少盗用，JWT 的有效期不宜设置太长。对于某些重要操作，使用时应该再次进行身份验证。
6. 为了减少盗用，JWT 不建议使用 HTTP 协议来传输，而是使用加密的 HTTPS 协议进行传输。

# 参考
- [http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)
- [Introduction to JSON Web Tokens](https://jwt.io/introduction/)
- [JWT 超详细分析](https://learnku.com/articles/17883)
- [JWT 完整使用详解](https://learnku.com/articles/10885/full-use-of-jwt)
- [Learn how to use JSON Web Tokens (JWT) for Authentication](https://github.com/dwyl/learn-json-web-tokens/blob/master/README.md)
- [JSON Web Token 入门教程@阮一峰](http://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html)
- [JSON Web Token（JWT）原理和用法介绍](https://www.cnblogs.com/renhui/p/10179661.html)
- [10 分钟了解 JSON Web 令牌（JWT）
](https://baijiahao.baidu.com/s?id=1608021814182894637&wfr=spider&for=pc&isFailFlag=1)
- [深入了解 Json Web Token 之概念篇](https://www.freebuf.com/articles/web/180874.html)
- [浅谈 OAuth 和 OpenID 相关技术 ](http://www.sohu.com/a/280388046_505923)
- [GitHub:jwt-node](https://github.com/mattrobenolt/jwt-node)
- [GitHub:egg-jwt](https://github.com/okoala/egg-jwt)
- [https://github.com/auth0/node-jsonwebtoken](https://github.com/auth0/node-jsonwebtoken)
