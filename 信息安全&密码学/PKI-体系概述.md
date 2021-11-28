> CA中心——CA系统——数字证书
>
> CA 中心管理并运营 CA 系统，CA 系统负责颁发数字证书。
>
> 专门负责颁发数字证书的系统称为 CA 系统，负责管理并运营 CA 系统的机构称为 CA 中心。所有与数字证书相关的各种概念和技术，统称为 PKI（Public Key Infrastructure）。

# 传统密码学

* 换位加密法；
* 替换加密法；

# 现代密码学加密基元

加密基元就是一些基础的密码学算法，通过它们才能够构建更多的密码学算法、协议、应用程序。

![](http://upload-images.jianshu.io/upload_images/2648731-38270603beffa050.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

说明：

* **散列函数（散列（hash）、指纹、消息摘要、摘要算法、杂凑函数）**：把任意长度的输入消息数据转化成固定长度的输出数据的一种密码算法。
* **消息验证代码**：验证数据完整性，即数据没有被篡改。
* **数字签名**：RSA私钥加密，公钥解密，结合散列函数。验证消息真实性。
* **伪随机函数（PRF）**：生成任意数量的伪随机数据。
* **RSA**：可以同时用于密钥交换和身份验证（数字签名）。 
* **DHE_RSA**：DHE 算法：密钥协商，RSA 算法：身份验证（数字签名）。
* **ECDHE_RSA**： ECDHE 算法：密钥协商，RSA 算法：身份验证（数字签名）。
* **ECDHE_ECDSA** ：ECDHE 算法：密钥协商，ECDSA 算法：身份验证（数字签名）。



# 密钥管理

![密钥管理](http://upload-images.jianshu.io/upload_images/2648731-573983b50c908f01.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



密钥管理模式：

* 无中心模式；
* 有中心模式；



# PKI 的本质是把非对称密钥管理标准化

PKI 是 Public Key Infrastructure 的缩写，其主要功能是绑定证书持有者的身份和相关的密钥对（通过为公钥及相关的用户身份信息签发数字证书），为用户提供方便的证书申请、证书作废、证书获取、证书状态查询的途径，并利用数字证书及相关的各种服务（证书发布，黑名单发布，时间戳服务等）实现通信中各实体的身份认证、完整性、抗抵赖性和保密性。

![PKI 模式](http://upload-images.jianshu.io/upload_images/2648731-13901e96908eb967.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



* **数字证书**：解决公钥与用户映射关系问题；
* **CA**：解决数字证书签发问题；
  ![CA管理数字证书的全生命周期](http://upload-images.jianshu.io/upload_images/2648731-c6cedbdac6d10fac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* **KMC**：解决私钥的备份与恢复问题；
* **双证书机制**：「签名证书及私钥」只用于签名验签，「加密证书及私钥」只用于加密解密。
* **LDAP**：解决数字证书查询和下载的性能问题，避免 CA 中心成为性能瓶颈。
* **CRL（证书作废列表）** 和 **OSCP（在线证书状态协议）**：方便用户快速获得证书状态。
* **RA**：方便证书业务远程办理、方便证书管理流程与应用系统结合。
* **电子认证服务机构**：保证 CA 系统在数字证书管理方面的规范性、合规性和安全性。



# 数字证书分类

![数字证书分类](http://upload-images.jianshu.io/upload_images/2648731-6b60441505b76229.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## 基于数字证书可以实现四种基本安全功能

1. 身份认证；
2. 保密性；
3. 完整性；
4. 抗抵赖性；



# PKI 基本组件

完整的 PKI 系统必须具有数字证书、认证中心（CA）、证书资料库、证书吊销系统、密钥备份及恢复系统、PKI 应用接口系统等构成部分。

| 组件                     | 描述                                                         |
| ------------------------ | ------------------------------------------------------------ |
| 数字证书                 | 包含了用于签名和加密数据的公钥的电子凭证，是PKI的核心元素    |
| 认证中心（CA）           | 数字证书的申请及签发机关，CA必须具备权威性                   |
| 证书资料库               | 存储已签发的数字证书和公钥，以及相关证书目录，用户可由此获得所需的其他用户的证书及公钥 |
| 证书吊销列表（CRL）/OCSP | 在有效期内吊销的证书列表，在线证书状态协议OCSP是获得证书状态的国际协议 |
| 密钥备份及恢复           | 为避免因用户丢失解密密钥而无法解密合法数据的情况，PKI提供备份与恢复密钥的机制。必须由可信的机构来完成。并且，密钥备份与恢复只能针对解密密钥，签名私钥不能够作备份 |
| PKI应用接口（API）       | 为各种各样的应用提供安全、一致、 可信的方式与PKI交互，确保建立起来的网络环境安全可靠，并降低管理成本 |



# TLS服务安全部署

![TLS服务安全部署](http://upload-images.jianshu.io/upload_images/2648731-d2f42561ea965ab5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

说明：

* 密码套件决定了本次连接采用哪一种加密算法、密钥协商算法、HMAC 算法，即各个密码学算法的组合。



## 密码套件的配置

```shell
# 密码套件名称构成：密钥交换算法-身份验证算法-加密算法（加密方法-加密强度-模式）-HMAC或PRF算法
# 密钥交换算法/密钥协商算法：ECDHE > DHE > RSA
# 身份验证算法：ECDSA 256 > RSA 2048
# 加密算法：AES-256-GCM、
# PRF（伪随机函数）：HMAC、SHA256

# 如：TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
# 如：TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
```

列出当前系统所支持的密码套件列表：

```bash
$ openssl ciphers -V 'ALL:COMPLEMENTOFALL'

# 推荐的配置，同时满足强加密和高性能
ECDHE-ECDSA-AES128-GCM-SHA256 
ECDHE-ECDSA-AES256-GCM-SHA384 
ECDHE-ECDSA-AES128-SHA 
ECDHE-ECDSA-AES256-SHA 
ECDHE-ECDSA-AES128-SHA256 
ECDHE-ECDSA-AES256-SHA384 
ECDHE-RSA-AES128-GCM-SHA256 
ECDHE-RSA-AES256-GCM-SHA384 
ECDHE-RSA-AES128-SHA 
ECDHE-RSA-AES256-SHA 
ECDHE-RSA-AES128-SHA256 
ECDHE-RSA-AES256-SHA384 
DHE-RSA-AES128-GCM-SHA256 
DHE-RSA-AES256-GCM-SHA384 
DHE-RSA-AES128-SHA 
DHE-RSA-AES256-SHA 
DHE-RSA-AES128-SHA256 
DHE-RSA-AES256-SHA256 
EDH-RSA-DES-CBC3-SHA
```

测试某个服务器是否支持特定的密码套件：

```bash
$ openssl s_client -cipher "ECDHE-RSA-AES128-SHA" -connect www.qq.com:443 -tls1_1

# 参数说明
# -cipher 参数表示本次连接支持的密码套件
# -connect 表示连接服务器的 443 端口
# -tls1_1 表示客户端最高支持的 TLS/SSL 版本是 TLS v1.1
```



# HTTPS 最佳实践工具

| 网站     | 文档或工具名称                        | 地址                                                         | 说明                                                         |
| -------- | ------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| SSL Labs | SSL Server Test                       | https://www.ssllabs.com/ssltest/                             | 对服务器的 HTTPS 配置进行测试，指出潜在的问题，并对安全级别打分。 |
| SSL Labs | SSL Client Test                       | https://www.ssllabs.com/ssltest/viewMyClient.html            | 测试客户端的配置情况                                         |
| SSL Labs | SSL Pulse                             | https://www.ssllabs.com/ssl-pulse/                           | 对全球顶尖 HTTPS 网站进行长期跟踪，统计和 HTTPS 有关的一些数据 |
| SSL Labs | SSL and TLS Deployment Best Practices | https://www.ssllabs.com/projects/documentation/              | 最佳部署文档                                                 |
| Mozilla  | 最佳部署文档                          | https://wiki.mozilla.org/Security/Server_Side_TLS            | 重点介绍密码套件配置、HTTPS 潜在的攻击、最佳部署等           |
| Mozilla  | Mozilla SSL Configuration Generator   | https://mozilla.github.io/server-side-tls/ssl-config-generator/ | 用于自动化为服务器配置 HTTPS 协议                            |
| RFC      | Summarizing Known Attacks on TLS      | https://tools.ietf.org/html/rfc7457                          | 详细描述 TLS/SSL 协议，历史上出现的漏洞                      |
| RFC      | Recommendations for Secure Use of TLS | https://tools.ietf.org/html/rfc7525                          | 描述如何更好地部署 HTTPS 网站                                |

——摘自《深入浅出 HTTPS》

# 参考

* [[译] PKCS 是什么？](https://www.jianshu.com/p/b98b12de85f3)
* [详细介绍 RFC 8446（即 TLS 1.3） ](https://www.oschina.net/translate/rfc-8446-aka-tls-1-3)


