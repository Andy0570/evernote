> 注：本文内容参考自 [Gnu 隐私卫士 (GnuPG) 袖珍 HOWTO (中文版)](https://www.gnupg.org/howtos/zh/index.html)。 

## 什么是 GnuPG

![](http://static.open-open.com/lib/uploadImg/20130714/20130714103241_634.png)

> 1991 年，程序员 Phil Zimmermann 开发了加密软件 [PGP](http://en.wikipedia.org/wiki/Pretty_Good_Privacy)。这个软件非常好用，迅速流传开来，成了许多程序员的必备工具。但是，它是商业软件，不能自由使用。所以，自由软件基金会决定，开发一个 PGP 的替代品，取名为 GnuPG。这就是 GPG 的由来。
>
> GPG 有许多用途，本文主要介绍文件加密。至于邮件的加密，不同的邮件客户端有不同的设置，请参考 Ubuntu 网站的[介绍](https://help.ubuntu.com/community/GnuPrivacyGuardHowto#Reading_OpenPGP_E-mail)。


## Linux 系统下安装 GnuPG

### 1. 源码安装

下载 GPG [源码](http://www.gnupg.org/download/index.en.html)，编译安装：

```bash
$ ./configure
$ make
$ make install
```

### 2. 软件包安装

Debian/Ubuntu 系统：

```bash
$ apt-get install gnupg
```

Fedora 系统：

```bash
$ yum install gnupg
```

## Mac OS 系统下安装

通过二进制源码安装 GunPG：<https://macgpg.sourceforge.io/docs/howto-build-gpg-osx.txt.asc>



## 查看帮助文档

```bash
$ gpg --help
gpg (GnuPG) 2.2.4
libgcrypt 1.8.1
Copyright (C) 2017 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <https://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

# 安装路径
Home: /root/.gnupg
Supported algorithms:
# 下面列出了本软件支持的算法
# 公钥算法
Pubkey: RSA, ELG, DSA, ECDH, ECDSA, EDDSA
# 加密算法
Cipher: IDEA, 3DES, CAST5, BLOWFISH, AES, AES192, AES256, TWOFISH,
        CAMELLIA128, CAMELLIA192, CAMELLIA256
# 哈希算法
Hash: SHA1, RIPEMD160, SHA256, SHA384, SHA512, SHA224
# 压缩算法
Compression: Uncompressed, ZIP, ZLIB, BZIP2

Syntax: gpg [options] [files]
Sign, check, encrypt or decrypt
Default operation depends on the input data
...
```

## 使用密钥对

生成密钥对：
```bash
$ gpg --generate-key 
```

输出密钥对：

```bash
gpg --export [UID]
```

导入钥匙（将其他人的公钥导入你自己的钥匙串）：

```bash
gpg --import [Filename]
```

取消钥匙：

```bash
gpg --gen-revoke
```

列出所有现有钥匙：

```bash
gpg --list-keys
```

显示钥匙的同时显示签名：

```bash
gpg --list-sigs 
```

显示钥匙的指纹：

```bash
gpg --fingerprint 
```

列出私钥：

```bash
gpg --list-secret-keys
```

要删除一把公钥，你可以敲：

```bash
gpg --delete-key UID
```


要删除一把密钥，你可以敲

```bash
gpg --delete-secret-key 
```

还有一个与钥匙有关的重要命令：

```bash
gpg --edit-key UID
```

用此命令你可以修改钥匙的失效日期，加进一个指纹，对钥匙签名等等。


## 加密和解密

加密命令：

```bash
gpg -e Recipient [Data]
gpg --encrypt Recipient [Data]
```


解密命令：

```bash
gpg [-d] [Data] 
gpg [--decrypt] [Data]
```

## 签名和校验数字签名

以下命令可用于以你的钥匙对数据签名：

```
gpg -s (--sign) [Data]
```
这样做的时候，同时数据也被压缩。也就是说，最终结果是无法直接读懂的。若你想要一个能直接读懂的结果，你可以用：
```
 gpg --clearsign [Data]
```
这样就能保证结果是清晰可读的。同时它也照样对数据签名。
用

```
 gpg -b (--detach-sign) [Data]
```
你可以将签名写进另一个文件。我们高度推荐这种用法，尤其是对二进制文件（如文档）签名的时候。另外， --armor 选项在这儿也非常有用。
经常你会发现有些数据既加了密又签了名。要这么做，完整的命令行大致如下：

```
 gpg [-u Sender] [-r Recipient] [--armor] --sign --encrypt [Data]
```
选项 -u (--local-user) 和 -r (--recipient) 的作用如前所述。

如果数据既加了密又签了名，签名是在解密过程中检验的。你可以用以下命令检验签名：

```
 gpg [--verify] [Data]
```
当然，只有当你有讯息发出者的公钥时，这才起作用。





## 参考

* [Gnu 隐私卫士 (GnuPG) 袖珍 HOWTO (中文版)](https://www.gnupg.org/howtos/zh/index.html) ⭐️⭐️⭐️
* [Build GnuPG on OS X](https://macgpg.sourceforge.io/docs/howto-build-gpg-osx.txt.asc)
* [GPG Quick Start](https://www.madboa.com/geek/gpg-quickstart/)
* <https://blog.csdn.net/xingzouagain/article/details/52511129>
* <https://blog.csdn.net/chengqiuming/article/details/83045160>
* <https://blog.csdn.net/chengqiuming/article/details/83047967>