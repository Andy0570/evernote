The Public-Key Cryptography Standards (PKCS) 是由美国 RSA [数据安全](https://baike.baidu.com/item/%E6%95%B0%E6%8D%AE%E5%AE%89%E5%85%A8/3204964)公司及其合作伙伴制定的一组公钥密码学标准，其中包括证书申请、证书更新、证书作废表发布、扩展证书内容以及[数字签名](https://baike.baidu.com/item/%E6%95%B0%E5%AD%97%E7%AD%BE%E5%90%8D/212550)、[数字信封](https://baike.baidu.com/item/%E6%95%B0%E5%AD%97%E4%BF%A1%E5%B0%81/425647)的格式等方面的一系列相关协议。


到 1999 年底，PKCS 已经公布了以下标准：

### PKCS#1

定义 RSA [公钥密钥](https://baike.baidu.com/item/%E5%85%AC%E5%BC%80%E5%AF%86%E9%92%A5) 算法的加密和签名机制，主要用于组织 [PKCS#7](https://baike.baidu.com/item/PKCS%237) 中所描述的 [数字签名](https://baike.baidu.com/item/%E6%95%B0%E5%AD%97%E7%AD%BE%E5%90%8D)和[数字信封](https://baike.baidu.com/item/%E6%95%B0%E5%AD%97%E4%BF%A1%E5%B0%81)。

### PKCS#3

定义 [Diffie-Hellman](https://baike.baidu.com/item/Diffie-Hellman/9827194) [密钥](https://baike.baidu.com/item/%E5%AF%86%E9%92%A5) 交换协议。

### PKCS#5

描述一种利用从口令派生出来的[安全密钥](https://baike.baidu.com/item/%E5%AE%89%E5%85%A8%E5%AF%86%E9%92%A5)加密字符串的方法。使用 MD2 或 MD5 从口令中派生密钥，并采用 DES-CBC 模式加密。主要用于加密从一个计算机传送到另一个计算机的私人密钥，不能用于加密消息。

**PKCS#5 填充标准**处理的长度只能是 8 字节（8 Byte = 64 bit）。

标签：基于口令的密钥、PBKDF2 算法。

### PKCS#6

描述了[公钥证书](https://baike.baidu.com/item/%E5%85%AC%E9%92%A5%E8%AF%81%E4%B9%A6)的标准语法，主要描述 [X.509 证书](https://baike.baidu.com/item/X.509%E8%AF%81%E4%B9%A6/3238809)的扩展格式。

### PKCS#7

定义一种通用的消息语法，包括 [数字签名](https://baike.baidu.com/item/%E6%95%B0%E5%AD%97%E7%AD%BE%E5%90%8D/212550)和加密等用于增强的加密机制，PKCS#7 与 PEM 兼容，所以不需其他密码操作，就可以将加密的消息转换成 PEM 消息。

**PKCS#7 格式主要用来进行数字签名和数据加密。**

PKCS#7 文件后缀一般是 `.p7b` 或 `.p7c`。

**PKCS#7 填充标准**处理的长度可以是 1 到 255 任意字节。

AES 算法中分组长度没有 8 字节，所以 AES 算法使用 PKCS#7 标准（在 OpenSSL 命令行中默认使用的填充标准即为 PKCS#7 标准）。

### PKCS#8

描述私有密钥信息格式，该信息包括[公开密钥](https://baike.baidu.com/item/%E5%85%AC%E5%BC%80%E5%AF%86%E9%92%A5)算法的私有密钥以及可选的属性集等。

### PKCS#9

定义一些用于 PKCS#6 证书扩展、PKCS#7 数字签名和 PKCS#8 [私钥](https://baike.baidu.com/item/%E7%A7%81%E9%92%A5)加密信息的属性类型。

### PKCS#10

描述证书请求（CSR）语法。

### PKCS#11

PKCS#11：称为 Cyptoki，定义了一套独立于技术的程序设计接口，用于智能卡和 [PCMCIA 卡](https://baike.baidu.com/item/PCMCIA%E5%8D%A1/10405824)之类的加密设备。

### PKCS#12

描述个人信息交换语法标准。描述了将用户[公钥](https://baike.baidu.com/item/%E5%85%AC%E9%92%A5)、私钥、证书和其他相关信息打包的语法。

**PKCS#12 格式可以将证书和密钥对打包成一个文件，还可以对文件进行加密保护。**

PKCS#12 文件后缀一般是 `.pkcs12`、`.pfx`、`.p12`。

### PKCS#13

椭圆曲线[密码体制](https://baike.baidu.com/item/%E5%AF%86%E7%A0%81%E4%BD%93%E5%88%B6)标准。

### PKCS#14

[伪随机数](https://baike.baidu.com/item/%E4%BC%AA%E9%9A%8F%E6%9C%BA%E6%95%B0)生成标准。

### PKCS#15

密码令牌信息格式标准。

### 参考

* [https://baike.baidu.com/item/PKCS/1042350](https://baike.baidu.com/item/PKCS/1042350)

