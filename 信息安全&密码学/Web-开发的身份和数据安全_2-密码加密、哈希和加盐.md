本文主要内容：探讨密码加密和安全性。

![](http://upload-images.jianshu.io/upload_images/2648731-855039fe06e81156.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 静态数据&动态数据

**静态数据**：不活动的（或静止的）数字数据，存储在服务器中，例如用于存储密码、个人资料、或其他应用所需数据的数据库。

**动态数据**：传输中的数据，在应用和数据库之间来回发送，或者在网站和 API 或外部数据源之间来回传送。

## 静态数据

* 数据库加密是绝对有必要的，尽管 99% 的团体没有这么做。
* 加密数据库应该使用强加密、受 NIST 认可的算法，如：SHA-256、AES、RSA。
* 遵循标准做法：1. 分开访问控制（用户登录）和数据加密；2. 定期更新加密数据库的密钥；3. 分开存储加密密钥和数据。
* 对全球覆盖的应用来说，可以使用**数据联合**避免恶意访问数据存储器，在需要个人信息的不同区域维护不同的数据库系统。
* 根据运行应用、网站或服务的需要，**应该尽量少存储敏感的用户数据**。
* 敏感的财务信息（例如信用卡数据）可以交由他方（支付提供商）处理。


## 动态数据

动态数据的使用场景：

* 用户填写的注册信息，用于访问账户和认证身份。
* 把个人档案信息传输给API服务，或从中获取。
* 应用或网站收集的其他数据，传给数据库存储起来。


## 密码攻击媒介

* 钓鱼；
* 社会工程；
* 暴力攻击；（抵抗方式：密钥延伸技术）
  * 登录失败后显示验证码（[Google reCAPTCHA](https://www.google.com/recaptcha/intro/v3.html)、[易盾 - 智能无感知验证码](http://dun.163.com/trial/sense)），增加登录难度。
  * 添加 2FA （双因素身份认证）机制。
* 字典攻击；（抵抗方式：加盐）
* 彩虹表；（抵抗方式：加盐）
* 恶意软件；（抵抗方式：短信验证、n因素认证）
* 离线破解；


# 加盐

盐值是一种随机数据，计算密码的哈希值时用于加强数据，抵御多种攻击媒介尤其是字典攻击和彩虹表。这个随机数据（特别长）能确保生成的哈希值是唯一的，即使多个用户使用相同的密码（确实有这种情况），添加唯一的盐值后能确保得到的哈希值仍是唯一的。就因为得到的哈希值是唯一的，我们才得以免受彩虹表和字典攻击的危害。

盐值的特点：盐值需要足够长、不可预测、高度随机、需要使用安全的伪随机函数生成，另外，还要避免使用全局的盐值。


## 生成随机盐值

使用 Node 原生支持的的 [crypto](http://nodejs.cn/api/crypto.html#crypto_crypto_randombytes_size_callback) 库生成随机盐值。

```javascript
const crypto = require('crypto');

// 1.异步方法生成盐值
// crypto.randomBytes() 生成 16 位强加密的伪随机数
crypto.randomBytes(16, (err, buf) => {
  if (err) throw err;
  console.log(`${buf.length} bytes of random data: ${buf.toString('hex')}`);
  console.log(`${buf.length} bytes of random data: ${buf.toString('base64')}`);
});

// 2.同步方法生成盐值
const buf = crypto.randomBytes(256);
```

## 重用盐值

用户注册账户或修改密码时应该生成并存储新的盐值和哈希值。


## 盐值的长度

* 根据经验，盐值的长度应该与哈希函数的输出长度一致。
*  PBKDF2 标准建议至少应该使用 **64 位（8字节）**长度的盐值。通常，多数情况下使用的是2的7次方，即 **128 位（16字节）**。

## 把盐值存储在哪？

~~盐值可以和哈希值一起以明文形式存储在数据库中。~~

salt 不能和哈希值存储在一起，不然就和没有 salt 一样，因为攻击者拖库后，构建字典非常简单，因为它能够直接看到 salt，所以一定要分开存储（比如在不同的机器上），这样即使口令密文表被拖库了，而 salt 表没有被拖库，也是相对安全的。



# 撒胡椒

* 胡椒是在计算哈希值时随盐值和密码一起传入的值。
* 使用胡椒的简单公式：hash ( salt + pepper + password ) = password hash
* 胡椒在代码层计算，而不是使用存储的值。
* 使用胡椒的原因：利用额外的字符和符号加强密码的强度。



# 选择正确的密码哈希函数

## bcrypt ⭐️⭐️⭐️

GitHub 源码：[node.bcrypt.js](https://github.com/kelektiv/node.bcrypt.js#readme)

特性：专为加密密码设计，底层基于 Blowfish 密码法，有异步方法和同步方法、密码哈希函数、校验密码函数、带 promise 特性...

```javascript
const bcrypt = require('bcrypt');

// 封装 hash 函数
function bcrypt_encrypt(username, password) {
  // 1. bcrypt 内置了生成盐值的方法：bcrypt.genSalt()
  bcrypt.genSalt(10, (err, salt) => {
    if (err) throw err;
      
    // 2. 生成哈希值：hash()
    bcrypt.hash(password, salt, (err, key) => {
      if (err) throw err;
      
      // 3. 把用户名、密码哈希值和盐值存入数据库
    });
  });
}

// 调用示例
bcrypt_encrypt('zhangsan', '123456');

// 对比哈希值，验证密码：
bcrypt.compare(password, hash, (err, same) => {
  // 返回 true 或 false
});
```



## PBKDF2

* 1Password、LastPass 等密码管理系统采用的算法；
* Node.js 中的 crypt 模块原生支持的标准算法；
* 通过该算法可以实现基于口令的加密（PBE），即**基于口令生成密钥**。

加密示例：

```javascript
// PBKDF2 算法
function pbkdf2_encrypt(username, password) {
  // 1. crypto.randomBytes(）方法生成 32 字节的随机盐值
  crypto.randomBytes(32, (err, salt) => {
    if (err) throw err;

    // 2. 参数列表：（密码，盐值，迭代次数，输出密钥长度，摘要算法）
    crypto.pbkdf2(password, salt, 4096, 512, 'sha256', (err, key) => {
      if (err) throw err;
      
      // 3. 将用户名、密码哈希值和盐值存入数据库
      // Salt 盐值是明文保存的，一般不和最终生成的密钥保存在一起。
      console.log(username, key.toString('hex'), salt.toString('hex'));
    });
  });
}

// 调用示例
pbkdf2_encrypt('zhangSan', '123456');
```

对比哈希值，验证密码：

```javascript
const dbsalt = 'USER RECORD SALT FROM YOUR DATABASE';
const dbhash = 'USER RECORD KEY FROM YOUR DATABASE';

crypto.pbkdf2(password, dbsalt, 4096, 512, 'sha256', (err, comparsehash) => {
  if (err) throw err;

  // 比较
  if (dbhash.toString('hex') === comparsehash.toString('hex')) {
    // 密码匹配
  } else {
    // 密码不匹配
  }
});
```



## scrypt

GitHub 源码：[node-scrypt]((https://github.com/barrysteyn/node-scrypt))

scrypt 的优势和实现如下：

* 做了特殊设计，是硬件和内存密集型算法，攻击者要实施大型攻击，想要破解需要耗费异常多的硬件和内存。
* 是加密数字货币莱特币和狗狗币背后采用的算法。

```javascript
'use strict';

const scrypt = require('scrypt');
const crypto = require('crypto');

function scrypt_encrypt(username, password) {
  // 1. 使用 crypto 的 crypto.randomBytes(...) 方法生成盐值。
  crypto.randomBytes(32, (err, salt) => {
    if (err) throw err;

    // 2. scrypt.hash(...) 生成 64 位的哈希值
    // - N: scrypt 最多使用多长时间（秒数）计算密钥（偶数）。
    // - r:计算密钥时最多使用多少字节 RAM（整数）。默认为0。
    // - p:计算密钥时所用 RAM 占可用值的比例（0-1，换算成百分比）默认为0.5。
    scrypt.hash(password, {"N":16384,"r":8,"p":1}, 64, salt, (err, key) => {
      if (err) throw err;

      // 3. 把用户名、密码哈希值和盐值存入数据库
      console.log(`key is ${key}`);
    });
  });
}
```



## 密钥延伸

bcrypt、scrypt 和 PBKDF2 行之有效涉及到的底层概念——密钥延伸（Key Derivation Function，KDF）。
**密钥延伸**：把弱密码变成特别复杂的长密码，致使暴力攻击等攻击媒介不再可行。

对加密哈希函数来说，密钥延伸体现在不断循环应用哈希函数（哈希函数迭代），直到得到所需长度和复杂度的哈希值为止。



# 重新计算哈希值

有时可能需要为用户生成新的密码哈希值。比如说:

* 根据摩尔定律，硬件更新了，要修改加密算法使用的权重/工作因子。
* 算法变了，或者有更好的算法出现，目前使用的算法不安全。
* 觉得现有哈希值不再安全。

遇到这些情况，符合常规的做法是为用户生成新哈希值，存储在系统中。用户提供用户名和密码请求登录时，正常比较根据输入值计算的哈希值和存储的哈希值，而不是拒绝登录。随后，为用户生成新的哈希值，替换用户记录中的旧值。



# 参考

* [设计安全的账号系统的正确姿势](https://www.cnblogs.com/lobby/p/5361789.html)
* [如何安全存储口令？了解下 Hash 加盐的原理](https://mp.weixin.qq.com/s/1X_aOoadk1CCBzGFC-EM3w)


