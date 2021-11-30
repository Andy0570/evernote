![](http://upload-images.jianshu.io/upload_images/2648731-507723910a744d63.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# OpenSSL 概述

## OpenSSL 的结构

![OpenSSL 的结构](http://upload-images.jianshu.io/upload_images/2648731-9dfae57b7aa0ee8c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## OpenSSL 目录功能对照表

| 目录名  | 功能描述                                                     |
| ------- | ------------------------------------------------------------ |
| Crypto  | 存放 OpenSSL 所有加密算法源码文件和相关标注如 X.509 源码文件，是 OpenSSL 中最重要的目录，包含了 OpenSSL 密码算法库的所有内容 |
| SSL     | 存放 OpenSSL 中 SSL 协议各个版本和 TLS 1.0 协议源码文件，包含了 OpenSSL 协议库的所有内容 |
| Apps    | 存放 OpenSSL 中所有应用程序源码文件，如 CA、X509 等应用程序的源文件就存放在这里 |
| Doc     | 存放了 OpenSSL 中所有的使用说明文档，包含三个部分：应用程序说明文档、加密算法库 API 说明文档及 SSL 协议API说明文档 |
| Demos   | 存放了一些基于 OpenSSL 的应用程序例子，这些例子一般都很简单，演示怎么使用 OpenSSL 中的一个功能 |
| Include | 存放了使用 OpenSSL 的库时需要的头文件                        |
| Test    | 存放了 OpenSSL 自身功能测试程序的源码文件                    |



## 查看 OpenSSL 版本

```bash
# 查看当前系统使用的 OpenSSL 版本
$ openssl version
OpenSSL 1.0.2g  1 Mar 2016

# 获取完整的版本信息
$ openssl version -a 
...
OPENSSLDIR: "/usr/lib/ssl" # OpenSSL 默认查找和配置证书目录
```



## 构建特定版本的 OpenSSL

OpenSSL 官网：<https://www.openssl.org/>


```bash
# 下载 OpenSSL 二进制包
$ wget https://www.openssl.org/source/openssl-1.1.1.tar.gz

# 解压缩，先调用 gzip 解压缩，再解开文件。
$ tar -xzvf openssl-1.0.2o.tar.gz 

# 安装配置
$ cd openssl-1.0.2o/
$ ./config \
--prefix=/opt/openssl \
--openssldir=/opt/openssl \ # 设置安装目录
enable-ec_nistp_64_gcc_128  # 使用优化后的常用椭圆曲线算法

$ make depend       # 生成可执行文件
$ make              # 编译
$ sudo make install # 安装

# OpenSSL 安装在 /opt/openssl/ 目录下
$ cd /opt/openssl/
$ ls -l
total 40
drwxr-xr-x 2 root root  4096 Jun 15 08:08 bin
drwxr-xr-x 2 root root  4096 Jun 15 08:08 certs   # 根证书目录、可信证书库
drwxr-xr-x 3 root root  4096 Jun 15 08:08 include
drwxr-xr-x 4 root root  4096 Jun 15 08:08 lib
drwxr-xr-x 6 root root  4096 Jun 15 08:08 man
drwxr-xr-x 2 root root  4096 Jun 15 08:08 misc    # 补充脚本
-rw-r--r-- 1 root root 10835 Jun 15 08:08 openssl.cnf
drwxr-xr-x 2 root root  4096 Jun 15 08:08 private # 私钥目录
```



## 查看可用命令：`openssl help`

```bash
# ------ 所有可用工具 ------
Standard commands 
asn1parse         ca                ciphers           cms
crl               crl2pkcs7         dgst              dh
dhparam           dsa               dsaparam          ec
ecparam           enc               engine            errstr
gendh             gendsa            genpkey           genrsa
nseq              ocsp              passwd            pkcs12
pkcs7             pkcs8             pkey              pkeyparam
pkeyutl           prime             rand              req
rsa               rsautl            s_client          s_server
s_time            sess_id           smime             speed
spkac             srp               ts                verify
version           x509
# man + 工具名称，显示某一命令的详细信息
# man ciphers，显示如何配置密码套件

# ------ 消息摘要命令 ------
Message Digest commands (see the `dgst' command for more details)
md4               md5               rmd160            sha
sha1

# ------ 加密命令 ------
Cipher commands (see the `enc' command for more details)
aes-128-cbc       aes-128-ecb       aes-192-cbc       aes-192-ecb
aes-256-cbc       aes-256-ecb       base64            bf
bf-cbc            bf-cfb            bf-ecb            bf-ofb
camellia-128-cbc  camellia-128-ecb  camellia-192-cbc  camellia-192-ecb
camellia-256-cbc  camellia-256-ecb  cast              cast-cbc
cast5-cbc         cast5-cfb         cast5-ecb         cast5-ofb
des               des-cbc           des-cfb           des-ecb
des-ede           des-ede-cbc       des-ede-cfb       des-ede-ofb
des-ede3          des-ede3-cbc      des-ede3-cfb      des-ede3-ofb
des-ofb           des3              desx              rc2
rc2-40-cbc        rc2-64-cbc        rc2-cbc           rc2-cfb
rc2-ecb           rc2-ofb           rc4               rc4-40
seed              seed-cbc          seed-cfb          seed-ecb
seed-ofb
```



## 获取算法的帮助信息

```bash
# 示例：获取 RSA 算法的帮助信息
$ openssl rsa --help
```



# 证书格式及转换

## 证书格式

### X.509 标准

X.509 标准来自国际电信联盟电信标准（ITU-T）的 X.500 标准。

X.509 标准是国际互联网工程任务组（IETF）的 PKIX 小组用来建设互联网的 PKI 公钥基础设施的标准。

HTTPS 中使用 X.509 的 PKI 标准。

X.509 标准目前最通用的版本为 X.509 V3 版本，引入了证书扩展的概念。

X.509 标准主要内容：证书的作用、证书文件的结构、证书管理方式、证书校验方式、证书的撤销等。



### ASN.1 标准

ASN.1是国际电信联盟电信标准（ITU-T）定义的标准，用来**结构化描述证书**。

ASN.1是一种抽象的数据结构，描述了复杂的对象，以及对象之间的关系。



> 💡💡💡
>
> X.509 标准定义了证书应该包含的内容，而为了让机器和人更好地理解和组织 X.509 标准，可以采用 ASN.1标准来描述 X.509 标准（或者说证书），ASN.1 类似于伪代码，是一种可理解的数据结构。



### DER 编码、BER 编码、CER 编码、PEM 编码

* 为了方便证书在互联网中传输，需要**通过一个规则将 ASN.1转换为二进制文件**。在 X.509 中，使用的编码方式是 Distinguished Encoding Rules（**DER**）。ASN.1和 DER 的关系类似于**字符集**和**编码**的关系。

* Basic Encoding Rules（**BER**）是 DER 的一个子集。

* Canonical Encoding Rules（**CER**）是另外一种编码标准，用来编码 ASN.1 结构。

* DER 是一个二进制文件，为了方便传输，可以将 DER 转换为 **PEM**（Privacy-enhanced Electronic Mail）格式，**PEM 是 base64 编码方式**。

参考：

* 在线 Web 工具：[SSL Converter](https://www.sslshopper.com/ssl-converter.html) - 转换证书的各种格式
* [阿里云文档：主流字证书都有哪些格式？](https://help.aliyun.com/knowledge_detail/42214.html?spm=5176.2020520163.cas.58.2dba2b7aDehVwA&accounttraceid=ffd6c2ba-4d55-4849-8553-9964575343da)



## 证书转换

### PEM 和 DER 转换

使用 x509 子命令进行 PEM 和 DER 证书文件之间的格式转换：

```bash
# 从 PEM 转换到 DER：
$ openssl x509 -in cert.pem -inform PEM -out cert.der -outform DER

# 从 DER 转换到 PEM：
$ openssl x509 -in cert.der -inform DER -out cert.pem -outform PEM
```

参数：

* -in，输入文件。
* -out，输出文件。
* -inform，输入文件的原有编码方式。
* -outform，输出文件编码方式。



### PKCS#12

PKCS#12 格式可以将证书和密钥对打包成一个文件，还可以对文件进行加密保护。

PKCS#12 文件后缀一般是 .pkcs12、.pfx、.p12。

通过 OpenSSL pkcs12 子命令将密钥对（privkey.pem）、服务器实体证书（cert.pem）、中间证书（chain.pem）转换成一个文件，并使用口令进行保护：

```bash
$ openssl pkcs12 \
    -export -out cert.pfx \
    -inkey privkey.pem -in cert.pem -certfile chain.pem
```

当需要使用证书时，可以从 cert.pfx 导出密钥对和证书，需要输入口令：

```bash
# 导出密钥对
$ openssl pkcs12 -in cert.pfx -nodes -nocerts -out new_privkey.pem

# 导出服务器实体证书
$ openssl pkcs12 -in cert.pfx -nodes -clcerts -out new_cert.pem

# 导出中间证书
$ openssl pkcs12 -in cert.pfx -nodes -cacerts -out new_chain.pem
```



### PKCS#7

PKCS#7 格式主要用来进行**数字签名**和**数据加密**。

PKCS#7 文件后缀一般是 .p7b 或 .p7c。

使用 crl2pkcs7 子命令生成 cert.p7b 文件：

```bash
$ openssl crl2pkcs7 -nocrl -certfile cert.pem -certfile chain.pem -out cert.p7b
```

参数：

* -certfile 表示服务器证书。
* 另一个 -certfile 表示中间件证书，不包含根证书。
* -nocrl 表示不加载证书对应的 CRL 文件。

从 cert.p7b 文件中导出服务器证书文件和中间件证书文件：

```bash
# 导出完整的证书链文件，服务器实体证书在文件顶部，中间证书在文件底部
$ openssl pkcs7 -print_certs -in cert.p7b -out fullchain.cert
```



# 获取线上证书

使用 OpenSSL 获取服务器实体证书、中间证书、根证书。

在线 Web 工具：[SSL Certificate Checker](https://www.sslchecker.com/sslchecker) - 分析网站证书的工具，可以在线下载证书及证书链



## OpenSSL 获取线上证书

```bash
# 使用 s_client 获取线上证书，输出证书内容到控制台
$ openssl s_client -connect www.github.com:443 -showcerts 2>&1 </dev/null

# 下载服务器实体证书
$ openssl s_client -connect www.sina.com.cn:443 2>&1 < /dev/null | sed -n '/-----BEGIN/,/-----END/p' > www_cert.pem
```

输出内容如下：

```bash
CONNECTED(00000005)
# 以下描述的是证书关系链
# github.com -> DigiCert SHA2 Extended Validation Server CA -> DigiCert High Assurance EV Root CA
# 根证书：DigiCert High Assurance EV Root CA
depth=2 C = US, O = DigiCert Inc, OU = www.digicert.com, CN = DigiCert High Assurance EV Root CA
verify return:1
# 从编号 1 开始是中间证书：DigiCert SHA2 Extended Validation Server CA
depth=1 C = US, O = DigiCert Inc, OU = www.digicert.com, CN = DigiCert SHA2 Extended Validation Server CA
verify return:1
# 编号 0 服务器实体证书：github.com
depth=0 businessCategory = Private Organization, jurisdictionCountryName = US, jurisdictionStateOrProvinceName = Delaware, serialNumber = 5157550, C = US, ST = California, L = San Francisco, O = "GitHub, Inc.", CN = github.com
verify return:1
---
Certificate chain
# 编号 0 服务器实体证书：github.com
 0 s:/businessCategory=Private Organization/jurisdictionCountryName=US/jurisdictionStateOrProvinceName=Delaware/serialNumber=5157550/C=US/ST=California/L=San Francisco/O=GitHub, Inc./CN=github.com
   i:/C=US/O=DigiCert Inc/OU=www.digicert.com/CN=DigiCert SHA2 Extended Validation Server CA
-----BEGIN CERTIFICATE-----
MIIHQjCCBiqgAwIBAgIQCgYwQn9bvO1pVzllk7ZFHzANBgkqhkiG9w0BAQsFADB1
MQswCQYDVQQGEwJVUzEVMBMGA1UEChMMRGlnaUNlcnQgSW5jMRkwFwYDVQQLExB3
d3cuZGlnaWNlcnQuY29tMTQwMgYDVQQDEytEaWdpQ2VydCBTSEEyIEV4dGVuZGVk
IFZhbGlkYXRpb24gU2VydmVyIENBMB4XDTE4MDUwODAwMDAwMFoXDTIwMDYwMzEy
MDAwMFowgccxHTAbBgNVBA8MFFByaXZhdGUgT3JnYW5pemF0aW9uMRMwEQYLKwYB
BAGCNzwCAQMTAlVTMRkwFwYLKwYBBAGCNzwCAQITCERlbGF3YXJlMRAwDgYDVQQF
Ewc1MTU3NTUwMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQG
A1UEBxMNU2FuIEZyYW5jaXNjbzEVMBMGA1UEChMMR2l0SHViLCBJbmMuMRMwEQYD
VQQDEwpnaXRodWIuY29tMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA
xjyq8jyXDDrBTyitcnB90865tWBzpHSbindG/XqYQkzFMBlXmqkzC+FdTRBYyneZ
w5Pz+XWQvL+74JW6LsWNc2EF0xCEqLOJuC9zjPAqbr7uroNLghGxYf13YdqbG5oj
/4x+ogEG3dF/U5YIwVr658DKyESMV6eoYV9mDVfTuJastkqcwero+5ZAKfYVMLUE
sMwFtoTDJFmVf6JlkOWwsxp1WcQ/MRQK1cyqOoUFUgYylgdh3yeCDPeF22Ax8AlQ
xbcaI+GwfQL1FB7Jy+h+KjME9lE/UpgV6Qt2R1xNSmvFCBWu+NFX6epwFP/JRbkM
fLz0beYFUvmMgLtwVpEPSwIDAQABo4IDeTCCA3UwHwYDVR0jBBgwFoAUPdNQpdag
re7zSmAKZdMh1Pj41g8wHQYDVR0OBBYEFMnCU2FmnV+rJfQmzQ84mqhJ6kipMCUG
A1UdEQQeMByCCmdpdGh1Yi5jb22CDnd3dy5naXRodWIuY29tMA4GA1UdDwEB/wQE
AwIFoDAdBgNVHSUEFjAUBggrBgEFBQcDAQYIKwYBBQUHAwIwdQYDVR0fBG4wbDA0
oDKgMIYuaHR0cDovL2NybDMuZGlnaWNlcnQuY29tL3NoYTItZXYtc2VydmVyLWcy
LmNybDA0oDKgMIYuaHR0cDovL2NybDQuZGlnaWNlcnQuY29tL3NoYTItZXYtc2Vy
dmVyLWcyLmNybDBLBgNVHSAERDBCMDcGCWCGSAGG/WwCATAqMCgGCCsGAQUFBwIB
FhxodHRwczovL3d3dy5kaWdpY2VydC5jb20vQ1BTMAcGBWeBDAEBMIGIBggrBgEF
BQcBAQR8MHowJAYIKwYBBQUHMAGGGGh0dHA6Ly9vY3NwLmRpZ2ljZXJ0LmNvbTBS
BggrBgEFBQcwAoZGaHR0cDovL2NhY2VydHMuZGlnaWNlcnQuY29tL0RpZ2lDZXJ0
U0hBMkV4dGVuZGVkVmFsaWRhdGlvblNlcnZlckNBLmNydDAMBgNVHRMBAf8EAjAA
MIIBfgYKKwYBBAHWeQIEAgSCAW4EggFqAWgAdgCkuQmQtBhYFIe7E6LMZ3AKPDWY
BPkb37jjd80OyA3cEAAAAWNBYm0KAAAEAwBHMEUCIQDRZp38cTWsWH2GdBpe/uPT
Wnsu/m4BEC2+dIcvSykZYgIgCP5gGv6yzaazxBK2NwGdmmyuEFNSg2pARbMJlUFg
U5UAdgBWFAaaL9fC7NP14b1Esj7HRna5vJkRXMDvlJhV1onQ3QAAAWNBYm0tAAAE
AwBHMEUCIQCi7omUvYLm0b2LobtEeRAYnlIo7n6JxbYdrtYdmPUWJQIgVgw1AZ51
vK9ENinBg22FPxb82TvNDO05T17hxXRC2IYAdgC72d+8H4pxtZOUI5eqkntHOFeV
CqtS6BqQlmQ2jh7RhQAAAWNBYm3fAAAEAwBHMEUCIQChzdTKUU2N+XcqcK0OJYrN
8EYynloVxho4yPk6Dq3EPgIgdNH5u8rC3UcslQV4B9o0a0w204omDREGKTVuEpxG
eOQwDQYJKoZIhvcNAQELBQADggEBAHAPWpanWOW/ip2oJ5grAH8mqQfaunuCVE+v
ac+88lkDK/LVdFgl2B6kIHZiYClzKtfczG93hWvKbST4NRNHP9LiaQqdNC17e5vN
HnXVUGw+yxyjMLGqkgepOnZ2Rb14kcTOGp4i5AuJuuaMwXmCo7jUwPwfLe1NUlVB
Kqg6LK0Hcq4K0sZnxE8HFxiZ92WpV2AVWjRMEc/2z2shNoDvxvFUYyY1Oe67xINk
myQKc+ygSBZzyLnXSFVWmHr3u5dcaaQGGAR42v6Ydr4iL38Hd4dOiBma+FXsXBIq
WUjbST4VXmdaol7uzFMojA4zkxQDZAvF5XgJlAFadfySna/teik=
-----END CERTIFICATE-----
# 从编号 1 开始是中间证书
# 这里没有输出根证书，根证书集成在浏览器中。
 1 s:/C=US/O=DigiCert Inc/OU=www.digicert.com/CN=DigiCert SHA2 Extended Validation Server CA
   i:/C=US/O=DigiCert Inc/OU=www.digicert.com/CN=DigiCert High Assurance EV Root CA
-----BEGIN CERTIFICATE-----
MIIEtjCCA56gAwIBAgIQDHmpRLCMEZUgkmFf4msdgzANBgkqhkiG9w0BAQsFADBs
MQswCQYDVQQGEwJVUzEVMBMGA1UEChMMRGlnaUNlcnQgSW5jMRkwFwYDVQQLExB3
d3cuZGlnaWNlcnQuY29tMSswKQYDVQQDEyJEaWdpQ2VydCBIaWdoIEFzc3VyYW5j
ZSBFViBSb290IENBMB4XDTEzMTAyMjEyMDAwMFoXDTI4MTAyMjEyMDAwMFowdTEL
MAkGA1UEBhMCVVMxFTATBgNVBAoTDERpZ2lDZXJ0IEluYzEZMBcGA1UECxMQd3d3
LmRpZ2ljZXJ0LmNvbTE0MDIGA1UEAxMrRGlnaUNlcnQgU0hBMiBFeHRlbmRlZCBW
YWxpZGF0aW9uIFNlcnZlciBDQTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoC
ggEBANdTpARR+JmmFkhLZyeqk0nQOe0MsLAAh/FnKIaFjI5j2ryxQDji0/XspQUY
uD0+xZkXMuwYjPrxDKZkIYXLBxA0sFKIKx9om9KxjxKws9LniB8f7zh3VFNfgHk/
LhqqqB5LKw2rt2O5Nbd9FLxZS99RStKh4gzikIKHaq7q12TWmFXo/a8aUGxUvBHy
/Urynbt/DvTVvo4WiRJV2MBxNO723C3sxIclho3YIeSwTQyJ3DkmF93215SF2AQh
cJ1vb/9cuhnhRctWVyh+HA1BV6q3uCe7seT6Ku8hI3UarS2bhjWMnHe1c63YlC3k
8wyd7sFOYn4XwHGeLN7x+RAoGTMCAwEAAaOCAUkwggFFMBIGA1UdEwEB/wQIMAYB
Af8CAQAwDgYDVR0PAQH/BAQDAgGGMB0GA1UdJQQWMBQGCCsGAQUFBwMBBggrBgEF
BQcDAjA0BggrBgEFBQcBAQQoMCYwJAYIKwYBBQUHMAGGGGh0dHA6Ly9vY3NwLmRp
Z2ljZXJ0LmNvbTBLBgNVHR8ERDBCMECgPqA8hjpodHRwOi8vY3JsNC5kaWdpY2Vy
dC5jb20vRGlnaUNlcnRIaWdoQXNzdXJhbmNlRVZSb290Q0EuY3JsMD0GA1UdIAQ2
MDQwMgYEVR0gADAqMCgGCCsGAQUFBwIBFhxodHRwczovL3d3dy5kaWdpY2VydC5j
b20vQ1BTMB0GA1UdDgQWBBQ901Cl1qCt7vNKYApl0yHU+PjWDzAfBgNVHSMEGDAW
gBSxPsNpA/i/RwHUmCYaCALvY2QrwzANBgkqhkiG9w0BAQsFAAOCAQEAnbbQkIbh
hgLtxaDwNBx0wY12zIYKqPBKikLWP8ipTa18CK3mtlC4ohpNiAexKSHc59rGPCHg
4xFJcKx6HQGkyhE6V6t9VypAdP3THYUYUN9XR3WhfVUgLkc3UHKMf4Ib0mKPLQNa
2sPIoc4sUqIAY+tzunHISScjl2SFnjgOrWNoPLpSgVh5oywM395t6zHyuqB8bPEs
1OG9d4Q3A84ytciagRpKkk47RpqF/oOi+Z6Mo8wNXrM9zwR4jxQUezKcxwCmXMS1
oVWNWlZopCJwqjyBcdmdqEU79OX2olHdx3ti6G8MdOu42vi/hw15UJGQmxg7kVkn
8TUoE6smftX3eg==
-----END CERTIFICATE-----
---
# 服务器证书
Server certificate
subject=/businessCategory=Private Organization/jurisdictionCountryName=US/jurisdictionStateOrProvinceName=Delaware/serialNumber=5157550/C=US/ST=California/L=San Francisco/O=GitHub, Inc./CN=github.com
issuer=/C=US/O=DigiCert Inc/OU=www.digicert.com/CN=DigiCert SHA2 Extended Validation Server CA
---
No client certificate CA names sent
Server Temp Key: ECDH, X25519, 253 bits
---
SSL handshake has read 3541 bytes and written 285 bytes
---
New, TLSv1/SSLv3, Cipher is ECDHE-RSA-CHACHA20-POLY1305
Server public key is 2048 bit
Secure Renegotiation IS supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
SSL-Session:
    Protocol  : TLSv1.2
    Cipher    : ECDHE-RSA-CHACHA20-POLY1305
    Session-ID: BA6DB13E4B6CEC5B9AF3ABD52BFF096A05BC829EDF6A4D48075A42ACFC77EFC8
    Session-ID-ctx:
    Master-Key: D1B878609C84F45DDC44DCCE1DF2EEEFC61BF87593D7E60A8C776BAB3FB06CD6F3880BF691E075F8A6D5300CE906FE07
    Start Time: 1557454972
    Timeout   : 7200 (sec)
    Verify return code: 0 (ok)
---
poll error%
```



## 使用 Shell 命令拆分服务器实体证书和中间证书

使用 Shell 命令提取证书链中的服务器实体证书和中间证书：

```bash
$ openssl s_client -connect www.github.com:443 -shwocerts 2>&1 </dev/null \
  | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' \
  > www_fullchain.pem
```

完整的证书链文件保存在 www_fullchain.pem 文件中，然后将 www_fullchain.pem 拆分成各个文件：

❌ 注：这里拆分文件存在命令行语法错误，待解决。

```bash
$ cat www_fullchain.pem  | awk 'split_after==1{n++;split_after=0} \
    /-----END CERTIFICATE-----/ {split_after=1} \
    {print > "www_cert" n ".pem"}'

# 重命名中间证书，在本列中中间证书只有一个
$ mv www_cert1.pem www_chain.pem

# 查看生成的各个文件
$ tree
.
|—— www_cert.pem  # 服务器实体证书
|—— www_chain.pem # 中间证书
|—— www_fullchain.pem # 完整证书链文件
```



## 获取根证书

首先，找到证书链中最底部的一张证书（即根证书签发的第一张中间证书），然后通过证书中包含的 **CA Issures** 信息获取上一级证书（即根证书）的地址。

```bash
# 在本例中 www_chain.pem 是证书链中最底部的一张证书，它的上一级证书就是根证书
$ openssl x509 -in www_chain.pem -noout -text | grep "CA Issuers"

# 下载根证书文件
$ wget http://apps.identrust.com/roots/dstrootcax3.p7c -O DST_ROOT.p7c

# 转换成 PEM 格式
$ openssl pkcs7 -inform der -in DST_ROOT.p7c -print_certs -out DST_ROOT.pem

# 检查证书签发者
$ openssl x509 -in DST_ROOT.pem -issuer
```



# 导入证书到根证书库

如何更新系统的根证书？

### 使用 Mozilla 根证书库更新系统证书库

使用 Mozilla 根证书库创建可信证书库的两种方式：

1. 从 [Mozilla CA Certificate Store](https://www.mozilla.org/en-US/about/governance/policies/security-group/certs/) 获取开源的可信证书库：[Source file with all of the included root certificates](https://hg.mozilla.org/mozilla-central/raw-file/tip/security/nss/lib/ckfw/builtins/certdata.txt)。下载完成后，可以使用 Perl 脚本或者 Go 脚本将 certdata.txt 文件转换为 PEM 格式。

2. 使用 Curl 获取最新的 PEM 格式的可信证书库：[CA certificates extracted from Mozilla](https://curl.haxx.se/docs/caextract.html)。



在 Linux 系统中，可以使用 Mozilla 根证书库更新系统的根证书库。

1. 下载 Mozilla 根证书库。

   ```bash
   # 使用 Curl 的 mk-ca-bundle 工具可以从 Mozilla 下载根证书，并转换为各个 CA 机构的根证书文件
   $ wget https://raw.githubusercontent.com/curl/curl/master/lib/mk-ca-bundle.pl
   
   $ chmod 0777 mk-ca-bundle.pl
   
   $ ./mk-ca-bundle.pl
   ```

   CA 机构的根证书文件存放在 /usr/share/ca-certificates/mozilla 目录下。

   同时 /etc/ca-certificates.conf 文件也会更新，该文件包含了 Mozilla 各个根证书文件的列表。

2. 使用 **update-ca-certificates** 工具将 Mozilla 的各个根证书文件同步到系统的根证书库中。

   **update—certificates** 工具会读取 /etc/ca-certificates.conf 文件，找到所有 Mozilla 配置的根证书文件，然后将 /usr/share-certificates/mozilla 下的根证书文件复制到 /etc/ssl/certs 目录下，同时 /etc/ssl/certs-certificates.crt 文件也会更新，该文件比较大，包含了所有的根证书文件，相当于所有根证书文件的集合。

   ```bash
   # 更新系统的证书库
   $ update-ca-certificates
   ```

3. 将**自签名证书**同步到系统根证书库中。

   ```bash
   $ mkdir /usr/local/share/ca-certificates/extra
   
   # 拷贝自签名证书，文件后缀 crt 
   $ cp self-ertificate.crt /usr/local/share/ca-certificates/extra
   
   $ update-ca-certificates
   ```

   

# OpenSSL 管理 CSR

服务器实体为了证明自己的身份，需要向 CA 机构申请证书。

在申请证书之前，必须先生成一个 CSR 文件，CSR 文件是要求 CA 给证书签名的一种正式申请，该文件中包含申请证书的实体的公钥以及该实体的相关信息。然后再将 CSR 文件发送给 CA 机构。

CSR 文件标准：PKCS#10 标准。

推荐阅读：[花生壳：SSL 证书请求文件 (CSR) 生成指南](http://service.oray.com/question/4985.html)

## 生成 CSR 文件

### 交互式

```bash
# 1.使用 genrsa 命令生成密钥长度为 2048 比特的 RSA 密钥对
$ openssl genrsa -out mykey.pem 2048

# 2.使用私钥（mykey.pem）生成 CSR 文件（mycsr.pem）
$ sudo openssl req -new -key mykey.pem -out mycsr.pem
Enter pass phrase for fd.key:
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
# ⚠️ 输入一个点（.）,该字段为空。键入回车键，则设置为默认值。
-----
Country Name (2 letter code) [AU]:CN # 国家
State or Province Name (full name) [Some-State]:Shanghai # 省市
Locality Name (eg, city) []:Shanghai # 地区
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Shanghai Clumsiest Information Co., Ltd. # 组织机构
Organizational Unit Name (eg, section) []: # 单位部门
Common Name (e.g. server FQDN or YOUR name) []: # 通用名
Email Address []:clumsiest@outlook.com # 邮箱地址

Please enter the following 'extra' attributes # 质询密码，留空
to be sent with your certificate request
A challenge password []:.
An optional company name []:.
```



### 非交互式

使用 OpenSSL req 子命令，通过非交互式方式同时生成密钥对和 CSR 文件，命令如下：

```bash
# -------------- 示例一 --------------
$ openssl req \
    -new -sha256 -newkey rsa:2048 -nodes \
    -subj '/CN=www1.example.com,www2.example.com/O=Test, Inc./C=CN/ST=Beijing/L=Haidian' \
    -keyout example_key.pem -out example_csr.pem


# -------------- 示例二 --------------
# 1. 如果想自动生成 www.example.com 的CSR文件，可以先创建一个 fd.cnf 文件：
[req]
prompt = no
distinguished_name = dn
req_extensions = ext
input_password = PASSPHRASE

[dn]
CN = www.feistyduck.com
emailAddress = webmaster@feistyduck.com
0 = Feisty Duck Ltd
L = London
C = GB

[ext]
subjectAltName = DNS:www.example.com,NDS:example.com

# 2. 使用如下命令直接创建 CSR 文件
$ openssl req -new -config fd.cnf -key example_key.pem -out example_csr.csr
```

参数：

* -sha256 表示证书使用 sha256 算法生成摘要（Hash 值）然后计算签名。
* -newkey 表示生成一个 2048 比特的 RSA 密钥对文件。
* -subj 参数表示手动设置 CSR 请求信息，不用进行交互式输入。
* -keyout 表示输出密钥对文件。
* -out 表示输出 CSR 文件。



### 用当前证书生成 CSR 文件

更新一张证书，并且不想对里面的信息作任何更改：

```bash
$ openssl x509 -x509toreq -in example_cert.crt -out example_csr.pem -signkey example_mykey.pem
```



## 查看 CSR 文件

使用 OpenSSL req 子命令查看 CSR 文件内容：

```bash
$ openssl req -in example_csr.pem -noout -text
```

输出 CSR 文件示例：

```bash
Certificate Request: # 证书请求信息
    Data:
        Version: 0 (0x0) # PKCS#10 标准的版本号
        Subject: CN=www1.example.com,www2.example.com, O=Test, Inc., C=CN, ST=Beijing, L=Haidian # 服务器主体可分辨名称 DN
        Subject Public Key Info: # 服务器密钥对的公钥
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit) # 密钥长度 2048 比特
                Modulus:
                    00:d1:72:55:de:64:97:c8:8e:6e:e1:34:0c:52:d5:
                    68:03:83:84:61:52:4a:64:ed:a8:d0:47:35:1f:89:
                    66:4a:7d:82:66:37:55:3c:4a:26:6e:06:c0:da:56:
                    56:dc:36:fa:7c:df:70:22:c8:f2:25:47:46:ea:9c:
                    88:e9:9e:09:6b:98:e3:7e:59:a5:fb:44:47:2b:92:
                    c4:2e:ca:be:1c:2e:7b:c5:4a:cb:66:12:dd:34:81:
                    37:b1:21:d1:14:de:2c:e0:08:7d:cd:0a:98:1f:de:
                    ab:eb:77:5f:7e:bc:3e:84:cf:01:c7:c4:97:ee:e5:
                    00:ec:61:12:3e:93:76:cd:f8:0c:ac:92:77:52:01:
                    c4:d2:de:0a:44:ff:fb:59:92:4a:7e:66:32:4d:2c:
                    1c:17:c1:7e:36:0b:d8:97:2a:89:5e:d4:0c:a2:a8:
                    81:67:36:d3:59:93:e1:08:84:06:61:45:83:7e:8b:
                    7b:f6:1b:b9:e0:c3:d8:66:ba:a1:01:4f:f0:7c:8a:
                    02:ab:0e:4d:4b:9d:b5:07:1b:db:b4:1d:f4:85:9e:
                    a1:0e:84:22:e6:fc:48:cb:8b:34:49:1f:8e:d6:25:
                    b2:7a:6e:70:14:02:a3:13:f5:ad:31:af:f4:87:87:
                    44:89:31:16:d8:7b:79:1a:30:90:bf:fa:bd:2a:e1:
                    7f:89
                Exponent: 65537 (0x10001)
        Attributes: # 可选信息
            a0:00
    Signature Algorithm: sha256WithRSAEncryption # 签名算法和签名值
         70:73:53:1f:5e:47:82:ea:88:02:30:7a:d9:bc:86:3c:2c:77:
         fb:d0:ee:09:87:4d:e0:05:4e:b3:73:89:f1:00:77:b8:a8:31:
         c8:68:dd:73:45:b2:f8:9b:c6:3e:03:4a:b0:5f:fd:d4:cd:a5:
         7a:18:3b:0b:ac:99:14:76:a6:8d:3d:e7:43:56:a2:a4:0e:5b:
         a4:22:ab:69:1f:67:7b:ea:3d:ab:df:2b:c0:a6:8c:f0:9b:df:
         cd:11:18:88:bc:37:87:b9:b3:58:2c:de:17:3e:a9:a1:52:43:
         34:b1:2b:40:15:74:ef:4c:17:fb:23:ef:17:a1:5f:99:cd:fa:
         6f:6a:ef:8d:61:bc:23:2a:23:2b:68:9e:f5:ab:5e:cd:17:ef:
         d3:f3:6f:fc:ad:cb:4e:c6:36:4e:cb:1d:6c:3c:2d:ec:13:9f:
         dc:f1:86:50:66:7e:44:f7:70:27:6b:48:27:18:54:95:6a:39:
         47:32:10:7e:5d:07:4e:63:32:3a:16:c8:c6:d0:3e:fa:b8:56:
         78:fd:50:db:cd:44:79:9e:47:a5:05:98:3c:3d:90:4b:65:d7:
         1c:10:5c:8e:ec:25:3e:f1:a4:6e:03:33:12:65:f1:19:e6:ea:
         05:14:c5:55:e2:b9:d9:70:2f:54:8c:9a:b6:f3:71:70:fa:34:
         ee:8c:0f:8e
```

在线 Web 工具：[CSR Decoder](https://www.sslshopper.com/csr-decoder.html) - 解析 CSR 文件的一个小工具



## 校验 CSR 签名

CA 会使用服务器实体的公钥验证 CSR 文件的签名，确保 CSR 文件没有被篡改。

```bash
$ openssl req -in example_csr.pem -noout -verify -key example_key.pem
verify OK
```



## CSR 格式转换

```bash
# PEM 格式转换为 DER 格式
$ openssl req -in example_csr.pem -out example_csr.der -outform DER

# DER 格式转换为 PEM 格式
$ openssl req -in example_csr.der -inform DER -out example_csr.pem -outform PEM 
```



# OpenSSL 生成自签名证书

> 请勿在生产环境中使用自签名证书，自签名证书的唯一目的是测试。 

使用 OpenSSL 管理密钥和证书的步骤：

![image](http://upload-images.jianshu.io/upload_images/2648731-a015f3d5c5ccbe97.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 通常情况下，使用 2048 位的 RSA 算法，因为 DSA 算法因效率问题会被限制在 1024 位，而 ECDSA 算法则还没有被大部分 CA/客户端支持。

如何生成自签名证书：

![自签名证书生成步骤](http://upload-images.jianshu.io/upload_images/2648731-d011054c940d97d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## 方式一：同时生成密钥对和自签名证书

执行以下 OpenSSL 命令生成自签名证书：

```bash
$ openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 365 -nodes
...
# 这里会以交互式方式让你设置相关参数
```



## 方式二：通过密钥对创建自签名证书

### 使用密钥对交互式创建自签名证书

```bash
# 1.使用 genrsa 命令生成 2048 比特的 RSA 密钥对
$ openssl genrsa -out example_key.pem 2048

# 2.通过密钥对生成证书
$ openssl req -new -x509 -days 365 -key example_key.pem -out example_cert.pem
...
# 这里会以交互式方式让你设置相关参数
```

注：了解 RSA 算法的更多实践，参考之前写的博客：[密码学—密码算法与协议](https://andy0570.com/2018/12/04/%E5%AF%86%E7%A0%81%E5%AD%A6%E2%80%94%E5%AF%86%E7%A0%81%E7%AE%97%E6%B3%95%E4%B8%8E%E5%8D%8F%E8%AE%AE/)



### 使用密钥对非交互式创建自签名证书

```bash
# 1.使用 genrsa 命令生成 2048 比特的 RSA 密钥对
$ openssl genrsa -out example_key.pem 2048

# 2. 生成证书
$ openssl req -new -x509 -days 365 -key example_key.pem -out example_cert.pem -subj "/C=GB/L=London/O=Feisty Duck Ltd/CN=www.example.com"
```



## 方式三：通过 CSR 文件创建自签名证书

使用 OpenSSL x509 子命令生成自签名证书：

```bash
# 1.非交互式方式同时生成密钥对和 CSR 文件
$ openssl req \
    -new -sha256 -newkey rsa:2048 -nodes \
    -subj '/CN=*.example.com,example.com/O=Test, Inc./C=CN/ST=Beijing/L=Haidian' \
    -keyout example_key.pem -out example_csr.pem

# 2.创建扩展文件 certext.ext，以在一张证书中支持多个域名（可选）
# 默认情况下，OpenSSL 创建的证书只包含一个公用名而且只能设置一个主机名。
$ echo "subjectAltName=DNS:*.example.com,DNS:example.com" >>certext.ext 

# 3.生成自签名证书
$ openssl x509 -req -days 365 -in example_csr.pem \
   -signkey example_key.pem -out example_cert.pem \
   -extfile certext.ext
```

参数：

* -days 参数表示证书的有效期。
* -in 表示 CSR 文件。
* -signkey 表示密钥对的公钥。
* -out 表示输出的证书文件。
* -extfile 表示引用一个扩展文件。



# OpenSSL 查看证书

> 数字证书基本格式见文章末尾。

## 使用 x509 子命令查看证书内部结构

```bash
# 查看证书完整信息
$ openssl x509 -in cert.pem -text -noout

# 查看证书包含的公钥
$ openssl x509 -in cert.pem -pubkey

# 查看哪个 CA 机构签发了证书
$ openssl x509 -in cert.pem -issuer

# 查看证书的有效期
$ openssl x509 -in cert.pem -enddate
```

参数：

* -in 表示要查看的证书文件。
* -text 表示打印详细信息。

* -noout 表示不打印编码后的证书内容，以减少信息干扰。



## 使用 x509 子命令查看服务器实体证书

```bash
# 查看服务器实体证书
$ openssl x509 -in 2056942_www.andy0570.com.pem -text -noout

# 输出内容如下：
Certificate:
    Data:
    		# 证书版本
        Version: 3 (0x2)
        # 序列号
        Serial Number:
            0b:5b:23:58:48:f7:92:61:01:88:07:4b:2f:19:5e:4e
    Signature Algorithm: sha256WithRSAEncryption
    		# 证书签发者：Encryption Everywhere DV TLS CA
        Issuer: C=US, O=DigiCert Inc, OU=www.digicert.com, CN=Encryption Everywhere DV TLS CA - G1
        Validity
        		# 证书有效期
            Not Before: Apr 13 00:00:00 2019 GMT
            Not After : Apr 12 12:00:00 2020 GMT
        # 证书的域名
        Subject: CN=www.andy0570.com
        # 证书公钥信息
        Subject Public Key Info:
        		# 证书公钥算法：RSA
            Public Key Algorithm: rsaEncryption
                # 公钥长度：2048 比特
                Public-Key: (2048 bit)
                # 公钥 n 信息
                Modulus:
                    00:c7:1a:29:48:57:ed:de:11:b0:ff:8a:ac:36:d0:
                    fb:3f:23:cf:27:41:2b:99:cd:a8:7a:61:34:2e:b8:
                    0a:00:16:cc:b5:e0:7c:21:63:90:75:05:de:6f:1c:
                    1d:df:72:b7:0e:63:96:5d:e3:e1:cc:5b:38:87:9b:
                    bf:0f:6d:fd:a1:53:c4:03:19:b6:77:40:30:75:91:
                    09:a3:68:ff:1d:fc:8a:e3:b7:f7:e9:d5:8b:1b:56:
                    48:5a:c6:24:10:8e:53:9d:11:f2:22:8b:a5:c8:8c:
                    9e:02:3b:ff:fd:3e:90:93:8c:b8:d4:f0:7d:42:95:
                    9f:b6:3f:3a:f2:44:de:cd:7a:cd:0a:2a:83:a3:4b:
                    d6:e7:b9:6e:a2:05:fc:5e:7e:6d:12:ed:9f:db:3b:
                    96:52:58:06:eb:da:15:9b:21:ea:f1:8c:50:94:bc:
                    8c:58:e6:f7:d3:3a:06:8b:85:9d:b2:31:19:94:1e:
                    88:99:46:e8:88:58:2b:37:22:71:be:8a:c6:7f:76:
                    23:92:d9:60:4d:ba:c7:33:38:ac:38:e4:7b:d1:7d:
                    20:c5:8d:19:66:50:41:74:51:a5:d1:36:92:00:d2:
                    17:5c:ab:6b:e1:d6:bd:16:5a:7b:8f:41:dc:61:ec:
                    2e:88:c0:aa:ef:de:67:9f:9b:24:95:cd:7c:23:c9:
                    53:2f
                # 公钥 e 信息
                Exponent: 65537 (0x10001)
        # 证书扩展
        X509v3 extensions:
            # CA 密钥标识符
            X509v3 Authority Key Identifier:
                keyid:55:74:4F:B2:72:4F:F5:60:BA:50:D1:D7:E6:51:5C:9A:01:87:1A:D7
						# 使用者密钥标识符
            X509v3 Subject Key Identifier:
                6C:60:56:C3:E3:41:39:6C:F8:03:74:52:13:92:F6:1B:F0:52:0B:15
            # 使用者可选名称
            X509v3 Subject Alternative Name:
                DNS:www.andy0570.com, DNS:andy0570.com
            # 密钥使用扩展，证书密钥的用法
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment # 数字签名、密钥协商
            # 扩展密钥用法，表明该证书可以进行 HTTPS 网站服务器身份校验
            X509v3 Extended Key Usage:
                TLS Web Server Authentication, TLS Web Client Authentication
            # 证书策略
            X509v3 Certificate Policies:
                Policy: 2.16.840.1.114412.1.2
                  CPS: https://www.digicert.com/CPS
                Policy: 2.23.140.1.2.1
						# CA 信息，包括 CA 的 OCSP 地址、CA 官网地址
            Authority Information Access:
                OCSP - URI:http://ocsp.dcocsp.cn
                CA Issuers - URI:http://cacerts.digicert.com/EncryptionEverywhereDVTLSCA-G1.crt
						# 基本约束，该证书不是 CA 证书，不能签发其他证书
            X509v3 Basic Constraints:
                CA:FALSE
            1.3.6.1.4.1.11129.2.4.2:
                ......v.......X......gp
.....j..a/.....G0E. 0m.6.q....Os4.......8K.(.S.......!....2aP....".N..../..O.S#m%.;.\.m.v.^.s..V...6H}.I.2z.........u..qEX...j..\*.....G0E. ..EE...qu....K.......v...p<.Z....!....)..[...5...t..4eOR.qaV{.9...@
    # 签名算法（SHA256）和签名值
    Signature Algorithm: sha256WithRSAEncryption
         06:b1:75:bf:f1:64:6a:19:a0:fd:c9:05:d2:15:bd:f0:34:e8:
         2e:30:0b:01:d8:08:00:46:67:5e:63:67:3c:dd:02:54:c7:05:
         f1:f6:98:d6:38:62:c6:c9:73:fd:76:1d:47:63:db:11:79:82:
         d7:89:0e:4c:47:74:36:a3:37:05:7c:c5:9c:9e:10:12:6c:ab:
         ea:56:07:eb:a5:78:b3:73:df:fb:53:c8:1b:66:84:8a:42:3b:
         5e:f0:a4:3d:75:e4:c5:b6:af:cd:b4:e9:0d:40:f9:b9:dd:fb:
         3c:bf:01:19:b5:93:27:e1:7b:d7:6d:17:3f:11:b9:8e:22:12:
         ee:0c:78:d4:4f:26:07:b2:27:0c:4c:74:98:24:6e:f4:25:de:
         cd:29:dd:99:f7:19:de:43:74:4d:ae:2c:f9:25:e6:39:30:99:
         d2:b9:74:94:85:a7:c8:99:75:e6:f0:66:36:7f:f7:4f:8a:ea:
         92:dd:91:7d:fa:85:af:34:c1:5b:b9:76:74:af:0c:98:3e:20:
         aa:6c:3a:85:d4:03:7f:5b:ec:67:7b:45:90:a9:bb:11:81:94:
         86:f1:e8:f2:56:27:2e:40:86:30:59:83:9b:10:1d:c6:2c:3a:
         74:d8:88:6d:2b:84:14:3a:56:1d:03:33:24:15:b2:19:44:88:
         f5:1e:03:30
```



## 使用 x509 子命令查看中间证书

```bash
$ openssl x509 -in cert.pem -text -noout  
```



## 使用 x509 子命令查看根证书

```bash
$ openssl x509 -in  DST_ROOT.pem -noout -text   
```



# 校验 CRL

## 手动校验 CRL

下载 CRLs 文件，手动查看服务器实体证书的序列号是否存在于 CRLs 中，如果存在说明证书被吊销了，这种方式不校验 CRLs 的签名。
Let’ s Encrypt 认为 CRL 的作用已经不大，所以其签发的证书并不包含CRL分发点信息。

```bash
# 下载服务器实体证书
$ openssl s_client -connect www.sina.com.cn:443 2>&1 < /dev/null | sed -n '/-----BEGIN/,/-----END/p' > www_cert.pem

# 找到服务器实体证书的 CRL 分发点
$ openssl x509 -in www_cert.pem -noout -text | grep "crl"

# 输出 CRL 分发点地址
URI:http://cdp1.digicert.com/GeoTrustRSACA2018.crl

# 下载 CRLs 文件
$ wget "http://cdp1.digicert.com/GeoTrustRSACA2018.crl"

# 查看 CRLs 文件内容
$ openssl crl -inform DER -text -noout -in GeoTrustRSACA2018.crl
# 由于文件太长了，这里保存到 crl.text 文件中
$ openssl crl -inform DER -text -noout -in GeoTrustRSACA2018.crl > crl.text
```

查看 CRLS 文件内容：

```bash
Certificate Revocation List (CRL):
        # 版本号，必须是 V2 版本
        Version 2 (0x1)
    # 签名算法：SHA256
    Signature Algorithm: sha256WithRSAEncryption
        # CRL 签发者：GeoTrust RSA CA
        Issuer: /C=US/O=DigiCert Inc/OU=www.digicert.com/CN=GeoTrust RSA CA 2018
        # CRLs 本次更新时间
        Last Update: May  9 20:30:01 2019 GMT
        # CRLs 下次更新时间
        Next Update: May 16 20:30:01 2019 GMT
        # CRL 扩展
        CRL extensions:
            X509v3 Authority Key Identifier: 
                keyid:90:58:FF:B0:9C:75:A8:51:54:77:B1:ED:F2:A3:43:16:38:9E:6C:C5

            X509v3 CRL Number: 
                549
# 被吊销的证书列表
Revoked Certificates:
    # 服务器实体证书的序列号
    Serial Number: 0E0072EBF08A8C7E0429798AD5BA850D
        # 服务器实体证书的吊销时间
        Revocation Date: Nov 22 10:28:31 2017 GMT
        CRL entry extensions:
            # 证书被吊销的原因
            X509v3 CRL Reason Code: 
                Key Compromise
    Serial Number: 02E6BE4950D0BAE5205926AA3C350C92
        Revocation Date: Dec  1 00:26:42 2017 GMT
    # ...
    # (略去 n 张证书列表)
    # ...
    Serial Number: 0A6D5EF45E060221840A8A97FF36D1F5
        Revocation Date: May  9 20:06:16 2019 GMT
        CRL entry extensions:
            X509v3 CRL Reason Code: 
                Key Compromise
    # CRLs 签名算法：SHA256 和签名值
    Signature Algorithm: sha256WithRSAEncryption
         15:9a:56:e1:ff:de:63:bc:66:af:47:41:c9:07:83:cf:50:0c:
         24:16:04:0c:ad:e1:c2:25:83:c2:de:ef:36:26:3c:1d:04:07:
         b2:91:e5:3e:8e:39:16:4a:e4:0c:3f:cc:3d:48:16:db:2d:f4:
         01:cf:36:3a:68:c1:75:a7:d2:ab:59:88:63:d5:52:f9:b5:26:
         b9:06:c5:96:b0:c6:1a:94:23:9c:88:b0:75:89:1a:4b:54:2f:
         e2:32:70:ec:6e:11:e1:5d:dd:e6:90:52:b2:78:57:bf:fa:c1:
         ef:0b:6f:da:e3:45:5d:30:f6:60:40:b0:fe:82:f3:3b:b8:7b:
         18:2f:9e:ed:c7:58:5e:23:59:e0:81:a8:25:fb:27:d3:63:9c:
         56:e2:b0:c4:61:52:0c:08:a1:a1:c8:ae:55:44:71:0c:9a:c6:
         f5:7a:ad:0c:19:78:d2:50:2d:23:d6:7d:1c:6a:74:2d:c6:8f:
         7f:d5:9a:2a:e9:07:47:f1:84:a6:5e:80:0f:06:cb:a3:1f:2a:
         1b:16:bf:29:c1:48:36:10:bf:50:ea:60:db:fc:d0:25:74:f2:
         60:e9:2f:d8:4a:28:0a:46:6a:a3:b0:f2:5c:bc:18:c9:36:e6:
         be:e9:cb:45:c3:b9:21:07:65:f9:92:21:3f:6e:e0:a4:e0:68:
         21:9f:17:2b
```



## 自动校验 CRL

通过 OpenSSL verify 子命令自动校验证书的吊销状态：

```bash
# 下载证书链文件
$ openssl s_client -connect www.sina.com.cn:443 -showcerts  2>&1 </dev/null \
   | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p'    \
   > www_fullchain.pem

# 拆分证书文件，www_cert.pem 是服务器实体证书，www_chain1.pem 是中间证书
$ cat www_fullchain.pem  | awk 'split_after==1{n++;split_after=0} \
    /-----END CERTIFICATE-----/ {split_after=1} \
    {print > "www_cert" n ".pem"}'

# 重命名中间证书，在该例中，中间证书只有一个。
$ mv www_cert1.pem www_chain.pem

# 找到服务器实体证书的 CRL 分发点
$ openssl x509 -in www_cert.pem -noout -text | grep "crl"

# 输出 CRL 分发点地址
URI:http://gn.symcb.com/gn.crl

# 下载 CRLs 文件
$ wget "http://gn.symcb.com/gn.crl"

# 将 CRLs 文件转换为 PEM 格式
$ openssl crl -inform DER -in gn.crl -outform PEM -out crl.pem

# 合并中间证书和 CRLs 文件
$ cat www_chain.pem crl.pem > crl_chain.pem

# 校验，-CAfile 表示完整证书链
$ openssl verify -crl_check -CAfile crl_chain.pem www_cert.pem

# 输出 ok 表示服务器实体证书没有被吊销
www_cert.pem: OK
```



## 比较服务器实体证书的签发者和 CRLs 的签发者

```bash
# 查询服务器实体证书的签发者
$ openssl x509 -in www_cert.pem -issuer -pubkey
# 输出
issuer=C = US, O = GeoTrust Inc., CN = GeoTrust SSL CA - G3

# 查询 CRLs 的签发者
$ openssl crl -inform DER  -noout -in gn.crl  -issuer   
# 输出
issuer=C = US, O = GeoTrust Inc., CN = GeoTrust SSL CA - G3
```



# 校验 OCSP

## 校验 Let‘s Encrypt 的 OCSP 服务

```bash
# 下载服务器实体证书：letsencrypt.org
$ openssl s_client -connect www.letsencrypt.org:443 2>&1 < /dev/null | sed -n '/-----BEGIN/,/-----END/p' > www_cert.pem

# 从服务器实体证书中获取 OCSP 的地址
$ openssl x509 -in www_cert.pem -noout -ocsp_uri
# 输出 OCSP URL 地址
http://ocsp.int-x3.letsencrypt.org

# 校验 OCSP  
$ openssl ocsp -issuer chain.pem -cert www_cert.pem -CAfile chain.pem \
   -no_nonce --text -url  http://ocsp.int-x3.letsencrypt.org \
   -header Host=ocsp.int-x3.letsencrypt.org
```



## 校验 GeoTrust 的 OCSP 响应

Let‘s Encrypt 的 OCSP 响应不包含 Certificate 信息，而 GeoTrust 的 OCSP 响应包含 Certificate 信息：

```bash
# 1.下载新浪网服务器实体证书，www.sina.com.cn
$ openssl s_client -connect www.sina.com.cn:443 2>&1 < /dev/null | sed -n '/-----BEGIN/,/-----END/p' > www_cert.pem

# 2. 下载中间证书
# 2.1 找到该服务器实体证书的中间证书的 URL
$ openssl x509 -in www_cert.pem -noout -text | grep "CA Issuers"
CA Issuers - URI:http://cacerts.geotrust.com/GeoTrustRSACA2018.crt
# 2.2下载该中间证书文件
$ wget http://cacerts.geotrust.com/GeoTrustRSACA2018.crt -O GeoTrustRSACA2018.crt
# 2.3 从 DER 转换到 PEM
$ openssl x509 -in GeoTrustRSACA2018.crt -inform DER -out GeoTrustRSACA2018.pem -outform PEM

# 3. 校验 GeoTrust 的 OCSP 响应
$ openssl ocsp -issuer GeoTrustRSACA2018.pem -cert www_cert.pem  \
   -url  http://ocsp.digicert.com -CAfile GeoTrustRSACA2018.pem --text -no_nonce
```

> 以上方法实测存在问题。




# 校验 OCSP 封套

OCSP 封套相比标准 OCSP 来说，不是由浏览器发出 OCSP 请求，而是**由证书部署者即服务器负责发出 OCSP 请求**。

说明，并不是所有的 HTTPS 网站都支持 OCSP 封套。

## 使用不支持 OCSP 封套的 HTTPS 网站进行演示

```bash
$ openssl s_client -connect www.baidu.com:443 -status -tlsextdebug < /dev/null 2>&1 \
   | grep -i "OCSP response"
# 如果服务器不支持 OCSP 封套，则输出
OCSP response: no response sent
```



## 使用支持 OCSP 封套的 HTTPS 网站进行演示

```bash
$ openssl s_client -connect letsencrypt.org:443 -status -tlsextdebug < /dev/null 2>&1
```

输出内容如下：

```bash
CONNECTED(00000005)
TLS server extension "renegotiation info" (id=65281), len=1
0001 - <SPACES/NULS>
TLS server extension "EC point formats" (id=11), len=4
0000 - 03 00 01 02                                       ....
TLS server extension "session ticket" (id=35), len=0
TLS server extension "status request" (id=5), len=0
depth=2 O = Digital Signature Trust Co., CN = DST Root CA X3
verify return:1
depth=1 C = US, O = Let's Encrypt, CN = Let's Encrypt Authority X3
verify return:1
depth=0 CN = www.letsencrypt.org
verify return:1
OCSP response:
======================================
# 重点关注以下内容
OCSP Response Data:
    OCSP Response Status: successful (0x0)
    Response Type: Basic OCSP Response
    Version: 1 (0x0)
    Responder Id: C = US, O = Let's Encrypt, CN = Let's Encrypt Authority X3
    Produced At: May  3 22:10:00 2019 GMT
    Responses:
    Certificate ID:
      Hash Algorithm: sha1
      Issuer Name Hash: 7EE66AE7729AB3FCF8A220646C16A12D6071085D
      Issuer Key Hash: A84A6A63047DDDBAE6D139B7A64565EFF3A8ECA1
      Serial Number: 03E1CE2C0324F9CA93417FC8886F87F34857
    Cert Status: good
    This Update: May  3 22:00:00 2019 GMT
    Next Update: May 10 22:00:00 2019 GMT

    Signature Algorithm: sha256WithRSAEncryption
         0d:de:d1:a8:f7:d2:20:19:76:dd:29:47:19:c1:07:ed:4e:8f:
         fb:4f:9f:10:2f:b7:c7:74:43:17:27:61:9f:b8:f5:d9:76:f1:
         49:b0:ee:b3:14:a9:a9:e0:9b:78:86:79:db:47:d6:21:04:e5:
         ef:d4:9d:a9:98:0c:e0:7c:3d:08:4c:34:7b:ba:59:0e:f9:29:
         81:c8:dc:ec:76:f4:29:e3:9f:56:27:bb:0b:8d:4e:a1:7e:75:
         51:55:b2:04:79:0f:4e:be:f1:9d:69:d8:60:49:90:4f:de:d6:
         33:e0:45:e9:cd:0b:97:01:d8:ee:cf:2f:d1:4c:40:bb:b0:26:
         cf:b4:bf:fb:02:2e:7a:8f:f1:87:a4:29:ef:6e:0f:df:e2:78:
         cd:3a:b6:8d:c7:8c:d4:31:83:eb:63:28:98:1f:bd:ee:8f:03:
         fe:42:97:79:3a:20:4e:d4:9b:f6:e3:b4:2e:ad:df:83:6f:3a:
         d4:53:e0:e3:a1:0d:a5:79:4c:4d:b0:3e:03:e6:7e:9d:2c:4c:
         83:65:e7:08:b0:86:71:c7:d0:57:41:3c:3d:6a:83:00:e5:57:
         51:f8:13:50:8a:21:5a:69:68:c7:6b:c4:96:e9:6c:b1:b9:82:
         c1:a1:c7:04:3f:c2:d0:dd:4e:20:1c:51:b3:55:8e:11:d4:a8:
         e3:c4:7c:d0
======================================
---
Certificate chain
 0 s:/CN=www.letsencrypt.org
   i:/C=US/O=Let's Encrypt/CN=Let's Encrypt Authority X3
 1 s:/C=US/O=Let's Encrypt/CN=Let's Encrypt Authority X3
   i:/O=Digital Signature Trust Co./CN=DST Root CA X3
---
Server certificate
-----BEGIN CERTIFICATE-----
MIIHMjCCBhqgAwIBAgISA+HOLAMk+cqTQX/IiG+H80hXMA0GCSqGSIb3DQEBCwUA
MEoxCzAJBgNVBAYTAlVTMRYwFAYDVQQKEw1MZXQncyBFbmNyeXB0MSMwIQYDVQQD
ExpMZXQncyBFbmNyeXB0IEF1dGhvcml0eSBYMzAeFw0xOTA1MDMyMTEwMjZaFw0x
OTA4MDEyMTEwMjZaMB4xHDAaBgNVBAMTE3d3dy5sZXRzZW5jcnlwdC5vcmcwggEi
MA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCiJVoMxjBUFBa/qCfgulvNK8kP
9HcXYlgOi7K81iUQW6Pe8aGVfTD7e3HpWKFGR9BgKUL+3K9s1Ig5L0VkzGh1JPfi
+Ug+9oEq2Cy7hDDQwV0hEmORyv1dm2Q9UTh2D6L564YD0JxtYxJrWRrKTprrK1jQ
ogsHKWa1NGDOI1w2zvGNUF6XsRme8dJwC4SNUiNiScovQ2R9w6OafQNs+7CbgDgA
KmPa/xSnK14x9pXeim2RS8GObJunPxBRaOyfRHwO6WIvxE89G2ZQFQBi8MK1Q28y
sVKm5R9/y4AH5eGuedGCOyXUTu9pdHreqcaYNSDgaIh8lLacJ4AJhYwpYrDNAgMB
AAGjggQ8MIIEODAOBgNVHQ8BAf8EBAMCBaAwHQYDVR0lBBYwFAYIKwYBBQUHAwEG
CCsGAQUFBwMCMAwGA1UdEwEB/wQCMAAwHQYDVR0OBBYEFMuUbU8b5LCmNdHbve0D
mMr0c724MB8GA1UdIwQYMBaAFKhKamMEfd265tE5t6ZFZe/zqOyhMG8GCCsGAQUF
BwEBBGMwYTAuBggrBgEFBQcwAYYiaHR0cDovL29jc3AuaW50LXgzLmxldHNlbmNy
eXB0Lm9yZzAvBggrBgEFBQcwAoYjaHR0cDovL2NlcnQuaW50LXgzLmxldHNlbmNy
eXB0Lm9yZy8wggHxBgNVHREEggHoMIIB5IIbY2VydC5pbnQteDEubGV0c2VuY3J5
cHQub3JnghtjZXJ0LmludC14Mi5sZXRzZW5jcnlwdC5vcmeCG2NlcnQuaW50LXgz
LmxldHNlbmNyeXB0Lm9yZ4IbY2VydC5pbnQteDQubGV0c2VuY3J5cHQub3Jnghxj
ZXJ0LnJvb3QteDEubGV0c2VuY3J5cHQub3Jngh9jZXJ0LnN0YWdpbmcteDEubGV0
c2VuY3J5cHQub3Jngh9jZXJ0LnN0Zy1pbnQteDEubGV0c2VuY3J5cHQub3JngiBj
ZXJ0LnN0Zy1yb290LXgxLmxldHNlbmNyeXB0Lm9yZ4ISY3AubGV0c2VuY3J5cHQu
b3JnghpjcC5yb290LXgxLmxldHNlbmNyeXB0Lm9yZ4ITY3BzLmxldHNlbmNyeXB0
Lm9yZ4IbY3BzLnJvb3QteDEubGV0c2VuY3J5cHQub3Jnghtjcmwucm9vdC14MS5s
ZXRzZW5jcnlwdC5vcmeCD2xldHNlbmNyeXB0Lm9yZ4IWb3JpZ2luLmxldHNlbmNy
eXB0Lm9yZ4IXb3JpZ2luMi5sZXRzZW5jcnlwdC5vcmeCFnN0YXR1cy5sZXRzZW5j
cnlwdC5vcmeCE3d3dy5sZXRzZW5jcnlwdC5vcmcwTAYDVR0gBEUwQzAIBgZngQwB
AgEwNwYLKwYBBAGC3xMBAQEwKDAmBggrBgEFBQcCARYaaHR0cDovL2Nwcy5sZXRz
ZW5jcnlwdC5vcmcwggEDBgorBgEEAdZ5AgQCBIH0BIHxAO8AdgDiaUuuJujpQAno
hhu2O4PUPuf+dIj7pI8okwGd3fHb/gAAAWp/v6MgAAAEAwBHMEUCIGrZoFnKmmYt
Omx+B0sKmyRBbeiSJQwGFYDETJswjpVAAiEA0BkcBEGR/r6787vEDHwuhCmOuavs
7YybVaoj8lmVx1YAdQApPFGWVMg5ZbqqUPxYB9S3b79Yeily3KTDDPTlRUf0eAAA
AWp/v6U0AAAEAwBGMEQCIFIxbMPE6RDnputd6t3Z1lthJ2vWRjIxNkPw5BkhlVOj
AiB4rr/jnUUdquBrNbL2jUghUktMi59oIGFv6HSgXVkweDANBgkqhkiG9w0BAQsF
AAOCAQEAUzzOcatp5xJBPnSm5Wa/d7JAM8fV/LBvAmLTdNb0Udk4w3QXdTMCN06K
EooTZFoOBe2ae1SIbqDDFFW19OEt0veSlLdJGE7CZgTW7mxdvERXuhhKw4dYtSmd
YOz/ukuNt/xaQxOD2B+4NRYkmr1kxvApZVOJSCduLXmYCw7EFWNXAojeeuDT3dOG
/9/GpOFVOywu7JpgvZwUgeymSU206Z7igxVvCTFN9Hwl2ddeXqT061efa4a9v62H
75sbpxaBKztrZMJdWukmtuyND1MV2+zhVUF6he87nVtrpzmvyfwCdnCH+N7h2LlB
cJLo338k0DUgi+b4PSIxUQIn5NBTGg==
-----END CERTIFICATE-----
subject=/CN=www.letsencrypt.org
issuer=/C=US/O=Let's Encrypt/CN=Let's Encrypt Authority X3
---
No client certificate CA names sent
Server Temp Key: ECDH, P-256, 256 bits
---
SSL handshake has read 4237 bytes and written 335 bytes
---
New, TLSv1/SSLv3, Cipher is ECDHE-RSA-AES256-GCM-SHA384
Server public key is 2048 bit
Secure Renegotiation IS supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
SSL-Session:
    Protocol  : TLSv1.2
    Cipher    : ECDHE-RSA-AES256-GCM-SHA384
    Session-ID: EFDCAF717B3A840A2E3A9808F90028B29F2B8CD6CEEF52AC5CAE92E67D7C14ED
    Session-ID-ctx:
    Master-Key: C758A1EEA29D5051A00E74FDA649AE7A3DC84370563763B41FAAD3136D22C5F2BED802065E4FB3664C95EE35D0D4172D
    TLS session ticket lifetime hint: 7200 (seconds)
    TLS session ticket:
    0000 - 00 00 0c e0 5e 86 4b e0-d7 0f a8 8e 1f f3 89 41   ....^.K........A
    0010 - 8a 16 c0 33 f1 69 c7 38-d7 9a f7 93 bc 15 2f 8a   ...3.i.8....../.
    0020 - d2 56 85 27 3d 0a 98 1c-04 91 ed d8 0c f4 87 23   .V.'=..........#
    0030 - 2c 1f 1f 61 fc d3 63 57-34 19 33 bb 2a 17 0f a0   ,..a..cW4.3.*...
    0040 - 30 51 7d ef db fc e7 b3-57 64 64 f8 6b 36 0b f5   0Q}.....Wdd.k6..
    0050 - 38 93 77 9e bb 12 f0 1a-75 d6 47 6b dc 8b 49 de   8.w.....u.Gk..I.
    0060 - 51 9a d1 bd e0 00 5f e8-8c f7 48 0e b9 03 07 73   Q....._...H....s
    0070 - b6 33 a8 9b 35 3e a1 43-06 7e 63 be 26 16 35 c1   .3..5>.C.~c.&.5.
    0080 - 99 56 d5 15 f1 47 c9 e9-3d c9 89 d9 d4 1c 00 6b   .V...G..=......k
    0090 - f8 e6 0f e6 96 8c 4e ee-94 2e 6d 9a f6 04 e1 8f   ......N...m.....
    00a0 - c3 5e c9 6a f0 2d bc e3-84 21 47 c5 b1 65 cb ff   .^.j.-...!G..e..

    Start Time: 1557485338
    Timeout   : 7200 (sec)
    Verify return code: 0 (ok)
---
```



# 证书透明度

通过证书透明度机制，CA 机构、服务器实体、客户端能够监控、审计证书的签发、使用，确保证书是被正确使用的。

查看 github 的 SCT：

```bash
$ openssl x509 -in github.cer -noout -text
```

在线查看支持 OCSP 封套的网站：

```bash
$ openssl s_client -connect www.example.com:443 -status -tlsextdebug < /dev/null 2>&1
```



# 创建私有证书颁发机构

## 运行私有CA的最大挑战

如何保证基础结构的安全？

1. 根密钥必须离线保存。
2. CRL 和 OSCP 响应程序证书必须定期进行更新，而这会要求根密钥保持联机。



## 创建全新 CA 的步骤

创建全新 CA 的步骤：

1. 根 CA 配置；
2. 创建根 CA 的目录结构和初始化密钥文件；
3. 生成根 CA 的密钥和证书；

```shell
# CA 中心生成自身私钥，为了保证CA机构私钥的安全，需要把私钥文件权限设置为077
root@ip-172-31-8-201:/opt/openssl# (umask 077; openssl genrsa -out private/cakey.pem 2048)
Generating RSA private key, 2048 bit long modulus
...............+++
...............................................................+++
e is 65537 (0x10001)

# CA 签发自身公钥
root@ip-172-31-8-201:/opt/openssl# openssl req -new -x509 -key private/cakey.pem -out cacert.pem -days 365
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:CN
State or Province Name (full name) [Some-State]:Shanghai
Locality Name (eg, city) []:Shanghai
Organization Name (eg, company) [Internet Widgits Pty Ltd]:shanghai Clumsiest Information Co., Ltd.
Organizational Unit Name (eg, section) []:Development Team
Common Name (e.g. server FQDN or YOUR name) []:arlingbc.com
Email Address []:clumsiest@outlook.com

# 创建数据库文件及证书序列文件
root@ip-172-31-8-201:/opt/openssl# ls -l
total 52
drwxr-xr-x 2 root root  4096 Jun 15 08:08 bin
-rw-r--r-- 1 root root  1558 Aug 24 03:21 cacert.pem # CA 自身证书文件
drwxr-xr-x 2 root root  4096 Jun 15 09:24 certs      # 客户端证书存放目录
drwxr-xr-x 2 root root  4096 Aug 24 05:54 crl        # CA 吊销的客户端证书存放目录*
drwxr-xr-x 3 root root  4096 Jun 15 08:08 include
-rw-r--r-- 1 root root     0 Aug 24 05:56 index.txt  # 存放客户端证书信息*
drwxr-xr-x 4 root root  4096 Jun 15 08:08 lib
drwxr-xr-x 6 root root  4096 Jun 15 08:08 man
drwxr-xr-x 2 root root  4096 Jun 15 08:08 misc
drwxr-xr-x 2 root root  4096 Aug 24 05:55 newcerts   # 生成新证书存放目录*
-rw-r--r-- 1 root root 10835 Jun 15 08:08 openssl.cnf
drwxr-xr-x 2 root root  4096 Aug 24 03:11 private    # 存放 CA 自身私钥的目录
-rw-r--r-- 1 root root     0 Aug 24 05:57 serial     # 客户端证书编号（编号可以自定义），用于识别客户端证书*
```





# 附：数字证书格式

[RFC 3280](https://www.ietf.org/rfc/rfc3280.txt) 规定了 X.509 数字证书的基本格式



## X.509 数字证书结构

### X.509 证书域组成

| 分类               | 标识符             | 说明                                                     |
| ------------------ | ------------------ | -------------------------------------------------------- |
| 证书内容（待签名） | tbsCertificate     | 包含持有者公钥、持有者信息、签发者信息等                 |
| 签名算法           | signatureAlgorithm | 包含摘要算法和公钥算法                                   |
| 签名值             | signatureValue     | 使用签名算法，对证书内容 tbsCertificate 进行签名后的结果 |



### X.509 证书内容

| 分类           | 标识符               | 说明                                             |
| -------------- | -------------------- | ------------------------------------------------ |
| 版本号         | version              | 用于区分证书格式版本，最新版本为 v3，缺省值为 v1 |
| 序列号         | serialNumber         | 证书唯一标识                                     |
| 签名算法       | signature            | 必须与证书域名中的签名算法相同                   |
| 证书签发者     | issure               | 用于区分证书签发者，包含证书签发者身份信息       |
| 证书有效期     | validity             | 由生效日期和失效日期组成                         |
| 证书持有者     | subject              | 用于区分证书持有者，包含证书持有者身份信息       |
| 证书持有者公钥 | subjectPublicKeyInfo | 包含证书持有者公钥信息                           |
| 证书签发者ID   | issuerUniqueID       | 表示证书签发者唯一标识                           |
| 证书持有者ID   | subjectUniqueID      | 表示证书持有者唯一标识                           |
| 扩展项         | extension            | 包含其他可扩展信息                               |



### issure 和 subject 包含的主要属性类型

| 分类                         | OID      | 说明                           |
| ---------------------------- | -------- | ------------------------------ |
| country                      | id-at 6  | 国家，C                        |
| organization                 | id-at10  | 单位，O                        |
| organizational-unit          | id-at 11 | 部门，OU                       |
| distinguished name qualifier | id-at 46 | DN 限定符                      |
| state or province name       | id-at 8  | 省份或州，ST                   |
| common name                  | id-at 3  | 通用名称，CN                   |
| serial number                | id-at 5  | 序列号，SN                     |
| locality                     | id-at 7  | 城市，L                        |
| domain component             |          | 域名组件，等同于 DNS，DC       |
| title                        | id-at 12 | 头衔                           |
| surname                      | id-at 4  | 姓                             |
| given name                   | id-at 42 | 名                             |
| initials                     | id-at 43 | 首字母缩写                     |
| pseudonym                    | id-at 65 | 假名                           |
| generation qualifier         | id-at 44 | 时代限定符，如老、小、第四代等 |
| email address                | pkcs-9 1 | 电子邮箱                       |



# 附：OpenSSL 配置文件

## openssl.conf 文件

```bash
#
# OpenSSL example configuration file.
# This is mostly being used for generation of certificate requests.
#

# This definition stops the following lines choking if HOME isn't
# defined.
HOME			= .
RANDFILE		= $ENV::HOME/.rnd

# Extra OBJECT IDENTIFIER info:
#oid_file		= $ENV::HOME/.oid
oid_section		= new_oids

# To use this configuration file with the "-extfile" option of the
# "openssl x509" utility, name here the section containing the
# X.509v3 extensions to use:
# extensions		=
# (Alternatively, use a configuration file that has only
# X.509v3 extensions in its main [= default] section.)

[ new_oids ]

# We can add new OIDs in here for use by 'ca', 'req' and 'ts'.
# Add a simple OID like this:
# testoid1=1.2.3.4
# Or use config file substitution like this:
# testoid2=${testoid1}.5.6

# Policies used by the TSA examples.
tsa_policy1 = 1.2.3.4.1
tsa_policy2 = 1.2.3.4.5.6
tsa_policy3 = 1.2.3.4.5.7

####################################################################
[ ca ]
default_ca	= CA_default		# The default ca section

####################################################################
[ CA_default ]

dir		= ./demoCA		# Where everything is kept
certs		= $dir/certs		# Where the issued certs are kept
crl_dir		= $dir/crl		# Where the issued crl are kept
database	= $dir/index.txt	# database index file.
#unique_subject	= no			# Set to 'no' to allow creation of
					# several ctificates with same subject.
new_certs_dir	= $dir/newcerts		# default place for new certs.

certificate	= $dir/cacert.pem 	# The CA certificate
serial		= $dir/serial 		# The current serial number
crlnumber	= $dir/crlnumber	# the current crl number
					# must be commented out to leave a V1 CRL
crl		= $dir/crl.pem 		# The current CRL
private_key	= $dir/private/cakey.pem# The private key
RANDFILE	= $dir/private/.rand	# private random number file

x509_extensions	= usr_cert		# The extentions to add to the cert

# Comment out the following two lines for the "traditional"
# (and highly broken) format.
name_opt 	= ca_default		# Subject Name options
cert_opt 	= ca_default		# Certificate field options

# Extension copying option: use with caution.
# copy_extensions = copy

# Extensions to add to a CRL. Note: Netscape communicator chokes on V2 CRLs
# so this is commented out by default to leave a V1 CRL.
# crlnumber must also be commented out to leave a V1 CRL.
# crl_extensions	= crl_ext

default_days	= 365			# how long to certify for
default_crl_days= 30			# how long before next CRL
default_md	= default		# use public key default MD
preserve	= no			# keep passed DN ordering

# A few difference way of specifying how similar the request should look
# For type CA, the listed attributes must be the same, and the optional
# and supplied fields are just that :-)
policy		= policy_match

# For the CA policy
[ policy_match ]
countryName		= match
stateOrProvinceName	= match
organizationName	= match
organizationalUnitName	= optional
commonName		= supplied
emailAddress		= optional

# For the 'anything' policy
# At this point in time, you must list all acceptable 'object'
# types.
[ policy_anything ]
countryName		= optional
stateOrProvinceName	= optional
localityName		= optional
organizationName	= optional
organizationalUnitName	= optional
commonName		= supplied
emailAddress		= optional

####################################################################
[ req ]
default_bits		= 2048
default_keyfile 	= privkey.pem
distinguished_name	= req_distinguished_name
attributes		= req_attributes
x509_extensions	= v3_ca	# The extentions to add to the self signed cert

# Passwords for private keys if not present they will be prompted for
# input_password = secret
# output_password = secret

# This sets a mask for permitted string types. There are several options.
# default: PrintableString, T61String, BMPString.
# pkix	 : PrintableString, BMPString (PKIX recommendation before 2004)
# utf8only: only UTF8Strings (PKIX recommendation after 2004).
# nombstr : PrintableString, T61String (no BMPStrings or UTF8Strings).
# MASK:XXXX a literal mask value.
# WARNING: ancient versions of Netscape crash on BMPStrings or UTF8Strings.
string_mask = utf8only

# req_extensions = v3_req # The extensions to add to a certificate request

[ req_distinguished_name ]
countryName			= Country Name (2 letter code)
countryName_default		= AU
countryName_min			= 2
countryName_max			= 2

stateOrProvinceName		= State or Province Name (full name)
stateOrProvinceName_default	= Some-State

localityName			= Locality Name (eg, city)

0.organizationName		= Organization Name (eg, company)
0.organizationName_default	= Internet Widgits Pty Ltd

# we can do this but it is not needed normally :-)
#1.organizationName		= Second Organization Name (eg, company)
#1.organizationName_default	= World Wide Web Pty Ltd

organizationalUnitName		= Organizational Unit Name (eg, section)
#organizationalUnitName_default	=

commonName			= Common Name (e.g. server FQDN or YOUR name)
commonName_max			= 64

emailAddress			= Email Address
emailAddress_max		= 64

# SET-ex3			= SET extension number 3

[ req_attributes ]
challengePassword		= A challenge password
challengePassword_min		= 4
challengePassword_max		= 20

unstructuredName		= An optional company name

[ usr_cert ]

# These extensions are added when 'ca' signs a request.

# This goes against PKIX guidelines but some CAs do it and some software
# requires this to avoid interpreting an end user certificate as a CA.

basicConstraints=CA:FALSE

# Here are some examples of the usage of nsCertType. If it is omitted
# the certificate can be used for anything *except* object signing.

# This is OK for an SSL server.
# nsCertType			= server

# For an object signing certificate this would be used.
# nsCertType = objsign

# For normal client use this is typical
# nsCertType = client, email

# and for everything including object signing:
# nsCertType = client, email, objsign

# This is typical in keyUsage for a client certificate.
# keyUsage = nonRepudiation, digitalSignature, keyEncipherment

# This will be displayed in Netscape's comment listbox.
nsComment			= "OpenSSL Generated Certificate"

# PKIX recommendations harmless if included in all certificates.
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid,issuer

# This stuff is for subjectAltName and issuerAltname.
# Import the email address.
# subjectAltName=email:copy
# An alternative to produce certificates that aren't
# deprecated according to PKIX.
# subjectAltName=email:move

# Copy subject details
# issuerAltName=issuer:copy

#nsCaRevocationUrl		= http://www.domain.dom/ca-crl.pem
#nsBaseUrl
#nsRevocationUrl
#nsRenewalUrl
#nsCaPolicyUrl
#nsSslServerName

# This is required for TSA certificates.
# extendedKeyUsage = critical,timeStamping

[ v3_req ]

# Extensions to add to a certificate request

basicConstraints = CA:FALSE
keyUsage = nonRepudiation, digitalSignature, keyEncipherment

[ v3_ca ]


# Extensions for a typical CA


# PKIX recommendation.

subjectKeyIdentifier=hash

authorityKeyIdentifier=keyid:always,issuer

# This is what PKIX recommends but some broken software chokes on critical
# extensions.
#basicConstraints = critical,CA:true
# So we do this instead.
basicConstraints = CA:true

# Key usage: this is typical for a CA certificate. However since it will
# prevent it being used as an test self-signed certificate it is best
# left out by default.
# keyUsage = cRLSign, keyCertSign

# Some might want this also
# nsCertType = sslCA, emailCA

# Include email address in subject alt name: another PKIX recommendation
# subjectAltName=email:copy
# Copy issuer details
# issuerAltName=issuer:copy

# DER hex encoding of an extension: beware experts only!
# obj=DER:02:03
# Where 'obj' is a standard or added object
# You can even override a supported extension:
# basicConstraints= critical, DER:30:03:01:01:FF

[ crl_ext ]

# CRL extensions.
# Only issuerAltName and authorityKeyIdentifier make any sense in a CRL.

# issuerAltName=issuer:copy
authorityKeyIdentifier=keyid:always

[ proxy_cert_ext ]
# These extensions should be added when creating a proxy certificate

# This goes against PKIX guidelines but some CAs do it and some software
# requires this to avoid interpreting an end user certificate as a CA.

basicConstraints=CA:FALSE

# Here are some examples of the usage of nsCertType. If it is omitted
# the certificate can be used for anything *except* object signing.

# This is OK for an SSL server.
# nsCertType			= server

# For an object signing certificate this would be used.
# nsCertType = objsign

# For normal client use this is typical
# nsCertType = client, email

# and for everything including object signing:
# nsCertType = client, email, objsign

# This is typical in keyUsage for a client certificate.
# keyUsage = nonRepudiation, digitalSignature, keyEncipherment

# This will be displayed in Netscape's comment listbox.
nsComment			= "OpenSSL Generated Certificate"

# PKIX recommendations harmless if included in all certificates.
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid,issuer

# This stuff is for subjectAltName and issuerAltname.
# Import the email address.
# subjectAltName=email:copy
# An alternative to produce certificates that aren't
# deprecated according to PKIX.
# subjectAltName=email:move

# Copy subject details
# issuerAltName=issuer:copy

#nsCaRevocationUrl		= http://www.domain.dom/ca-crl.pem
#nsBaseUrl
#nsRevocationUrl
#nsRenewalUrl
#nsCaPolicyUrl
#nsSslServerName

# This really needs to be in place for it to be a proxy certificate.
proxyCertInfo=critical,language:id-ppl-anyLanguage,pathlen:3,policy:foo

####################################################################
[ tsa ]

default_tsa = tsa_config1	# the default TSA section

[ tsa_config1 ]

# These are used by the TSA reply generation only.
dir		= ./demoCA		# TSA root directory
serial		= $dir/tsaserial	# The current serial number (mandatory)
crypto_device	= builtin		# OpenSSL engine to use for signing
signer_cert	= $dir/tsacert.pem 	# The TSA signing certificate
					# (optional)
certs		= $dir/cacert.pem	# Certificate chain to include in reply
					# (optional)
signer_key	= $dir/private/tsakey.pem # The TSA private key (optional)

default_policy	= tsa_policy1		# Policy if request did not specify it
					# (optional)
other_policies	= tsa_policy2, tsa_policy3	# acceptable policies (optional)
digests		= md5, sha1		# Acceptable message digests (mandatory)
accuracy	= secs:1, millisecs:500, microsecs:100	# (optional)
clock_precision_digits  = 0	# number of digits after dot. (optional)
ordering		= yes	# Is ordering defined for timestamps?
				# (optional, default: no)
tsa_name		= yes	# Must the TSA name be included in the reply?
				# (optional, default: no)
ess_cert_id_chain	= no	# Must the ESS cert id chain be included?
				# (optional, default: no)
```



## root-ca.conf 文件

```bash
# 配置文件第一部分包括了CA的名称、基础URL和CA可分辨名称等基本信息。
[default]
name                    = root-ca
domain_suffix           = example.com
aia_url                 = http://$name.$domain_suffix/$name.crt
crl_url                 = http://$name.$domain_suffix/$name.crl
ocsp_url                = http://ocsp.$name.$domain_suffix:9080
default_ca              = ca_default
name_opt                = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
countryName             = "GB"
organizationName        = "Example"
commonName              = "Root CA"

# 第二部分直接控制了CA的操作。
[ca_default]
home                    = .
database                = $home/db/index
serial                  = $home/db/serial
crlnumber               = $home/db/crlnumber
certificate             = $home/$name.crt
private_key             = $home/private/$name.key
RANDFILE                = $home/private/random
new_certs_dir           = $home/certs
unique_subject          = no
copy_extensions         = none
default_days            = 3650 # 有效期10年
default_crl_days        = 365
default_md              = sha256 # 使用SHA256作为签名算法
policy                  = policy_c_o_match

[policy_c_o_match] # 默认策略，限制了这张CA签发的证书的国家名和组织名会与CA本身一样。
countryName             = match
stateOrProvinceName     = optional
organizationName        = match
organizationalUnitName  = optional
commonName              = supplied
emailAddress            = optional

# 第三部包含了req命令的配置，req命令只会在创建自签发根证书的时候用到一次。最重要的部分是扩展:
[req]
default_bits            = 4096
encrypt_key             = yes
default_md              = sha256
utf8                    = yes
string_mask             = utf8only
prompt                  = no
distinguished_name      = ca_dn
req_extensions          = ca_ext

[ca_ext]
# 基本限制(basicContraints)扩展表明这个证书是一张CA
basicConstraints        = critical,CA:true
# 密钥用法(keyUsage) 扩展用来说明这个CA的用处
keyUsage                = critical,keyCertSign,cRLSign
subjectKeyIdentifier    = hash

# 配置的第四部分包括了根CA创建证书所需要的信息。
# 因为基本限制(basicContraints)扩展的设置，所有的证书都将成为CA，但是我们需要把pathlen设置为0，表示这些CA无法再签发新的CA了。
[sub_ca_ext]
authorityInfoAccess     = @issuer_info
authorityKeyIdentifier  = keyid:always
basicConstraints        = critical,CA:true,pathlen:0
crlDistributionPoints   = @crl_info
# 第一，扩展密钥用法(extendedKeyUsage)扩展限制了只能进行客户端验证 (clientAuth)和服务器验证(serverAuth)，也就是TLS的客户端和服务器验证。
extendedKeyUsage        = clientAuth,serverAuth
keyUsage                = critical,keyCertSign,cRLSign
# 第二，名称限制(nameContraints)扩展限制了允许签发的域名只有example.com和example.org。
nameConstraints         = @name_constraints
subjectKeyIdentifier    = hash

[crl_info]
URI.0                   = $crl_url

[issuer_info]
caIssuers;URI.0         = $aia_url
OCSP;URI.0              = $ocsp_url

[name_constraints]
permitted;DNS.0=example.com
permitted;DNS.1=example.org
excluded;IP.0=0.0.0.0/0.0.0.0
excluded;IP.1=0:0:0:0:0:0:0:0/0:0:0:0:0:0:0:0

# 最后两部分的配置表示有了这个扩展的证书可以对OCSP响应进行签名。为了能够运行OCSP 响应程序，我们生成一个特别的证书，并且将OCSP的签名能力赋予这张证书。从扩展可以看出 这张证书不是一个CA
[ocsp_ext]
authorityKeyIdentifier  = keyid:always
basicConstraints        = critical,CA:false
extendedKeyUsage        = OCSPSigning
noCheck                 = yes
keyUsage                = critical,digitalSignature
subjectKeyIdentifier    = hash
```



# 参考

* 《深入浅出 HTTPS》
* [《HTTPS 权威指南：在服务器和 Web 应用上部署 SSL/TLS 和 PKI》](http://www.ituring.com.cn/book/1734)
* [OpenSSL Cookbook](https://www.feistyduck.com/library/openssl-cookbook/online/)
* [OpenSSL 隐形战友](http://www.jiemian.com/article/231843.html)
* [知乎：为什么 SSL 证书那么贵?](https://www.zhihu.com/question/21838720/answer/62574832)
* [知乎：SSL 证书服务，大家用哪家的？](https://www.zhihu.com/question/19578422)
* [openSSL 命令、PKI、CA、SSL 证书原理](https://www.cnblogs.com/LittleHann/p/3738141.html)
* [OpenSSL 中文手册 之一 OpenSSL 简介－证书](https://blog.csdn.net/xiyuan1999/article/details/6416948)
* [Let's Encrypt，免费好用的 HTTPS 证书](https://imququ.com/post/letsencrypt-certificate.html)
* [主流浏览器直接信任 Let’s Encrypt 根证书，宣告他成为顶级 CA](https://www.jianshu.com/p/3a2c274b61d8)

