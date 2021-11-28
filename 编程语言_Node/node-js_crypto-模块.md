`crypto` 模块提供了加密功能，实现了包括对 OpenSSL 的哈希、HMAC、加密、解密、签名、以及验证功能的一整套封装。

## Hash 算法

`Hash` 类是用于创建数据哈希值的工具类。

查看 crypto 模块支持的 hash 函数：`crypto.getHashes()`

```javascript
[ 'RSA-MD4',
  'RSA-MD5',
  'RSA-MDC2',
  'RSA-RIPEMD160',
  'RSA-SHA1',
  'RSA-SHA1-2',
  'RSA-SHA224',
  'RSA-SHA256',
  'RSA-SHA384',
  'RSA-SHA512',
  'blake2b512',
  'blake2s256',
  'md4',
  'md4WithRSAEncryption',
  'md5',
  'md5-sha1',
  'md5WithRSAEncryption',
  'mdc2',
  'mdc2WithRSA',
  'ripemd',
  'ripemd160',
  'ripemd160WithRSA',
  'rmd160',
  'sha1',
  'sha1WithRSAEncryption',
  'sha224',
  'sha224WithRSAEncryption',
  'sha256',
  'sha256WithRSAEncryption',
  'sha384',
  'sha384WithRSAEncryption',
  'sha512',
  'sha512WithRSAEncryption',
  'ssl3-md5',
  'ssl3-sha1',
  'whirlpool' ]
```


### SHA-256

使用 `hash.update()` 方法将要计算的数据以流（stream）的方式写入，流输入结束后，使用 `hash.digest()` 方法计算数据的 hash 值。

```javascript
const crypto = require('crypto');
// 创建哈希函数 sha256
const hash = crypto.createHash('sha256'); 

// 输入流编码：utf8、ascii、binary（默认）
hash.update('some data to hash', 'utf8');
// 输出编码：hex、binary、base64
console.log(hash.digest('hex'));

// 输出
// 6a2da20943931e9834fc12cfe5bb47bbd9ae43489a30726962b576f4e3993e50
```



### PBKDF2

PBKDF2 是 Node.js 的 **crypto** 模块原生支持的标准方法。

```javascript
const crypto = require('crypto');

function pbkdf2_encrypt(username, password) {
  // crypto.randomBytes(）方法生成 32 字节的随机数 - 这里作为盐值
  crypto.randomBytes(32, (err, salt) => {
    if (err) throw err;

    // 参数列表：（密码，盐值，迭代次数，密钥长度，摘要函数）
    crypto.pbkdf2(password, salt, 4096, 512, 'sha256', (err, key) => {
      if (err) throw err;
      // 将用户名、密码哈希值和盐值存入数据库
      console.log(username, key.toString('hex'), salt.toString('hex'));
    });
  });
}

pbkdf2_encrypt('zhangSan', '123456');
```

同步函数示例：

```javascript
const salt = crypto.randomBytes(32);
const result = crypto.pbkdf2Sync(password, salt, 4096, 512, 'sha256');
```



## Hmac 算法

`Hmac` 类是用于创建加密 Hmac 摘要的工具。

示例一：

```javascript
const crypto = require('crypto');
const key = 'BDvDYUmfdykkBLgX';
const hmac = crypto.createHmac('sha1', key.toString('ascii'));

hmac.update('data to crypt');
console.log(hmac.digest('hex')); 

// 输出
// a43bfb9f12b6f69ad9fcd4338a981efbed2569ae
```


示例二：

```JavaScript
const crypto = require('crypto');

const secret = 'abcdefg';
const hash = crypto.createHmac('sha256', secret)
                   .update('I love cupcakes')
                   .digest('hex');
console.log(hash);
```


## 加盐（salt）
> 盐值就是随机数值，用于在计算密码的哈希值时加强数据的安全性，可以有效抵御诸如字典攻击、彩虹表攻击等密码攻击媒介。

常见的 Hash 算法使用示例：

```javascript
const crypto = require('crypto');
const md5 = crypto.createHash('md5');

let password = '123456';
// 直接对密码原文进行 Hash
md5.update(password);
console.log(md5.digest('hex')); 

// 输出
// e10adc3949ba59abbe56e057f20f883e
// 可以通过 <https://www.cmd5.com/> 反向查询得到密码，不够安全。
```

加“盐”的 Hash 算法：

```javascript
const crypto = require('crypto');
const md5 = crypto.createHash('md5');

let password = '123456';
let salt = 'hhug6dcKyCNBQ5sUC0i6hja5dCTqdSzV'; // 盐值
// 将密码拼接上任意长度的随机字符串后，再进行 Hash
md5.update(password+salt);
console.log(md5.digest('hex'));

// 输出
// b2d23b0443c319e574f1ea3f8bddc6e0
```
`crypto.randomBytes()` 方法可以生成随机数，用于作为密钥或者盐值：
``` javascript
const crypto = require('crypto');

// crypto.randomBytes() 生成强加密的伪随机数
crypto.randomBytes(16, (err, buf) => {
  if (err) throw err;
  console.log(`${buf.length} bytes of random data: ${buf.toString('hex')}`);
});

// 打印
// 16 bytes of random data: 411fd0a79188f2fde58a91ea0302f148
```

使用 Node **crypto** 模块为哈希函数生成随机的盐值：
```javascript
const crypto = require('crypto');

// 1. 异步方法
crypto.randomBytes(32, (err, salt) => {
  if (err) throw err;

  // 记录盐值可读的字符串版本
  console.log('salt: ' + salt.toString('hex'));

  // 后续步骤：使用盐值
});

// 2. 同步方法
const buf = crypto.randomBytes(256);
```


## 加密（Cipher）和解密（Decipher）算法

### 对称加密算法

查看 Openssl 对称加密算法列表：

```bash
$ openssl list -cipher-algorithms
$ openssl list-cipher-algorithms # 旧版本命令
```
#### AES-256-GCM 示例1:
```javascript
'use strict';

const crypto = require('crypto');

// 初始化参数
const text = 'Encryption Testing AES GCM mode'; // 要加密和解密的数据
const key = crypto.randomBytes(32); // 256 位的共享密钥
const iv = crypto.randomBytes(16); // 初始向量，16 字节
const algorithm = 'aes-256-gcm'; // 加密算法和操作模式

// 加密
const cipher = crypto.createCipheriv(algorithm, key, iv); // 初始化加密算法
let encrypted = cipher.update(text, 'utf8', 'hex');
encrypted += cipher.final('hex');
const tag = cipher.getAuthTag(); // 生成标签，用于验证密文的来源

// 解密
const decipher = crypto.createDecipheriv(algorithm, key, iv); // 初始化解密算法
decipher.setAuthTag(tag); // 传入验证标签，验证密文的来源
let decrypted = decipher.update(encrypted, 'hex', 'utf8');
decrypted += decipher.final('utf8');

console.log(decrypted); // Encryption Testing AES GCM mode
```

#### AES-256-GCM 示例2:
* [Github - crypto-aes-256-gem-demo.js](https://gist.github.com/rjz/15baffeab434b8125ca4d783f4116d81)
* [GitHub: AES 简介](https://github.com/matt-wu/AES)

```javascript
'use strict';

const crypto = require('crypto');

const aes256gcm = key => {
  const ALGO = 'aes-256-gcm'; // 加密算法和操作模式

  // encrypt returns base64-encoded ciphertext
  const encrypt = str => {
    // Hint: the 'iv' should be unique (but not necessarily random).
    // 'randomBytes' here are (relatively) slow but convenient for demonstration
    const iv = new Buffer.from(crypto.randomBytes(16), 'utf8'); // 初始向量，16 字节
    const cipher = crypto.createCipheriv(ALGO, key, iv); // 初始化加密算法

    // Hint: Larger inputs (it's GCM, after all!) should use the stream API
    let enc = cipher.update(str, 'utf8', 'base64');
    enc += cipher.final('base64');
    return [ enc, iv, cipher.getAuthTag() ];
  };

  // decrypt decodes base64-encoded ciphertext into a utf8-encoded string
  const decrpt = (enc, iv, authTag) => {
    const decipher = crypto.createDecipheriv(ALGO, key, iv);
    decipher.setAuthTag(authTag);
    let str = decipher.update(enc, 'base64', 'utf8');
    str += decipher.final('utf8');
    return str;
  };

  return { encrypt, decrpt };
};

// 加密密钥
// Note: a key size of 16 bytes will use AES-128, 24 => AES-192, 32 => AES-256
const KEY = new Buffer.from(crypto.randomBytes(32), 'utf8');

const aesCipher = aes256gcm(KEY);

// 加密
const [ encrypted, iv, authTag ] = aesCipher.encrypt('hello world');
// 解密
const decrypted = aesCipher.decrpt(encrypted, iv, authTag);

console.log(decrypted); // hello world
```



### 非对称加密算法的使用，1. 公钥加密-私钥解密；2. 私钥加密-公钥解密。

* `crypto.publicEncrypt(key, buffer)`
* `crypto.publicDecrypt(key, buffer)`
* `crypto.privateEncrypt(privateKey, buffer)`
* `crypto.privateDecrypt(privateKey, buffer)`

#### 生成 RSA 密钥对

方法：`crypto.generateKeyPair(type, options, callback)` 生成给定类型的新非对称密钥对，目前仅支持的算法类型：RSA，DSA 和 EC。

参数：
* `type`：<string> 类型，要生成的算法名称，必须是 `'rsa'`、`'dsa'` 或者 `'ec'` 之一；
* `options`：<Object> 类型：
    * `modulusLength`：<number> 类型，密钥长度（RSA，DSA）；
    * `publicExponent`：<number> 类型，公共指数（RSA），默认值：`0x10001`；
    * `divisorLength`：<number> 类型，以比特为单位的 q 的大小（DSA）；
    * `namedCurve`：<string> 类型，要使用的椭圆曲线名称（EC）；
    * `publicKeyEncoding`：<Object> 类型，公钥编码格式：
        * `type`：<string> 类型，必须是 `'pkcs1'`（仅限 RSA）或 `'spki'` 之一；
        * `format`：<string> 类型，公钥输出格式，必须是 `'pem'` 或 `'der'` 之一；
    * `privateKeyEncoding`：<Object> 类型，私钥编码格式：
        * `type`：<string> 类型，必须是 `'pkcs1'`（仅限 RSA）或 `'pkcs8'` 或 `'sec1'`(仅限 EC) 之一；
        * `format`：<string> 类型，公钥输出格式，必须是 `'pem'` 或 `'der'` 之一；
        * `cipher`：<string> 类型，如果指定算法，将使用基于 PKCS＃5 v2.0 规范的算法和密钥加密，使用给定的算法和密钥加密私钥；
        * `passphrase`：<string> 类型，用于加密的密钥；
* `callback`：<Function> 类型：
    * `err`：<Error> 类型
    * `publicKey`：<string> 类型 | <Buffer> 类型，设置为 PEM 编码时返回 String 类型，DER 编码则返回 Buffer 类型
    * `privateKey`：<string> 类型 | <Buffer> 类型

建议将公钥编码为 'spki' 格式，将私钥编码为 'pkcs8' 格式并加密保存：
```JavaScript
const { generateKeyPair } = require('crypto');
generateKeyPair('rsa', {
  modulusLength: 4096,
  publicKeyEncoding: {
    type: 'spki', // pkcs1、spki
    format: 'pem'
  },
  privateKeyEncoding: {
    type: 'pkcs8', // pkcs1、pkcs8、sec1
    format: 'pem',
    cipher: 'aes-256-cbc', // 对私钥使用 aes-256-cbc 算法加密后返回
    passphrase: 'top secret' // aes-256-cbc 算法密钥
  }
}, (err, publicKey, privateKey) => {
  // Handle errors and use the generated key pair.
});
```


#### 使用 RSA 2048 进行公钥加密，私钥解密

使用 OpenSSL 生成 2048位 RSA 密钥：

```shell
# 使用 genrsa 命令生成密钥长度是 2048 比特的 RSA 密钥对
$ openssl genrsa -out mykey.pem 2048

# 从密钥对中分离出公钥
$ openssl rsa -in mykey.pem -pubout -out mypubkey.pem
```

使用公钥加密数据，再使用私钥解密数据：

```javascript
const crypto = require('crypto');
const fs = require('fs');
const path = require('path');
const root = __dirname;

const publicKey = fs.readFileSync(path.join(root, './mypubkey.pem')).toString('ascii');
const privateKey = fs.readFileSync(path.join(root, './ mykey.pem')).toString('ascii');
const data = 'data to crypt';

// 公钥加密
const encryptData = crypto.publicEncrypt(publicKey, Buffer.from(data)).toString('base64');
console.log('encode', encryptData);

// 私钥解密
const decryptData = crypto.privateDecrypt(privateKey, Buffer.from(encryptData.toString('base64'), 'base64'));
console.log('decode', decryptData.toString());
```





## 签名（Sign）和验证（Verify）算法

签名算法示例：

```javascript
const crypto = require('crypto');
const fs = require('fs');
const path = require('path');
const root = __dirname;

// 加载私钥文件
const pem = fs.readFileSync(path.join(root, './rsa_private_key.pem'));
const key = pem.toString('ascii');

const sign = crypto.createSign('RSA-SHA256'); // 创建签名算法
sign.update('data to sign'); // 更新待签名内容
const result = sign.sign(key, 'hex'); // 生成并返回签名
console.log('result:', result);

// 打印
// 3a26e0c3ec5ad1f517aad27f0f3a48cc0db156c2221800e4c2f995220f62a9077b4507a72ac6b0f1f50ee72386191d5ada4013945ff2afdb1ea6aa4e8d51a94f186089a67c2d6f4236150d59a1db134286fd7ba8deab0fc28f97eba7ad35820113f24dc6e30c6d11f0773527c4d81582c2b42016c9f972b5f4809e6d1f9115c2fb6db3168c68af3cfca986a458e3ea1c15606e7d33ee0bf5400ca460cf5371a51e800abe7ac943f16d9546239949bf7eec77488212d815b6a013e68fc2f456c4f2eb9d38c551e3f94168b076c3f761341afa3b724d8e73f486aacbace5a09412ef16c9b88182fcaeaaf97d96fdb402832530708a915d34c42131d7a26e4f04d4
```

验证算法示例：

```javascript
const crypto = require('crypto');
const fs = require('fs');
const path = require('path');
const root = __dirname;

// 加载公钥文件
const publicKey = fs.readFileSync(path.join(root, './rsa_public_key.pem')).toString('ascii');

const verify = crypto.createVerify('RSA-SHA256'); // 创建验证算法
verify.update('data to sign');
console.log(verify.verify(publicKey, result, 'hex'));

// 打印
// true
```

## DH 密钥交换算法

`DiffieHellman` 类是一个用来创建 Diffie-Hellman 键交换的工具。 `DiffieHellman` 类的实例可以使用 `crypto.createDiffieHellman()` 方法。

```JavaScript
const crypto = require('crypto');
const assert = require('assert');

// Generate Alice's keys...
const alice = crypto.createDiffieHellman(2048);
const aliceKey = alice.generateKeys();

// Generate Bob's keys...
const bob = crypto.createDiffieHellman(alice.getPrime(), alice.getGenerator());
const bobKey = bob.generateKeys();

// Exchange and generate the secret...
const aliceSecret = alice.computeSecret(bobKey);
const bobSecret = bob.computeSecret(aliceKey);

// OK
assert.strictEqual(aliceSecret.toString('hex'), bobSecret.toString('hex'));
```

## ECDH 密钥交换算法

`ECDH` 类是创建椭圆曲线 Diffie-Hellman（Elliptic Curve Diffie-Hellman (ECDH)）键交换的实用工具。 `ECDH` 类的实例可以使用 `crypto.createECDH()` 方法。

`crypto.getCurves()` 方法会返回一个数组，其中包含支持的椭圆曲线的名称。

```JavaScript
const crypto = require('crypto');
const assert = require('assert');

// Generate Alice's keys...
const alice = crypto.createECDH('secp521r1');
const aliceKey = alice.generateKeys();

// Generate Bob's keys...
const bob = crypto.createECDH('secp521r1');
const bobKey = bob.generateKeys();

// Exchange and generate the secret...
const aliceSecret = alice.computeSecret(bobKey);
const bobSecret = bob.computeSecret(aliceKey);

assert.strictEqual(aliceSecret.toString('hex'), bobSecret.toString('hex'));
// OK
```

## 参考

* [Node.js 的加密模块 crypto 之使用 Hash 类计算数据的哈希值](https://itbilu.com/nodejs/core/4koGTGNY.html)
* [Node.js 的加密模块 crypto 之使用 Hmac 类计算哈希密钥](https://itbilu.com/nodejs/core/VJKNIOVF.html)
* [Node.js 的加密模块 crypto 之使用 Cipher 类加密数据](https://itbilu.com/nodejs/core/EJOj6hBY.html)
* [Node.js 的加密模块 crypto 之使用 Decipher 类解密数据](https://itbilu.com/nodejs/core/4ySMqlUF.html)
* [Node.js 的加密模块 crypto 之使用 Sign 类生成数字签名并使用 Verify 类验证数字签名](https://itbilu.com/nodejs/core/NkQOETLK.html)
* [Node.js 的加密模块 crypto 之使用 DiffieHellman 类生成交换密钥](https://itbilu.com/nodejs/core/EknZWVKt.html)
* [Node.js 的加密模块 crypto 之使用 ECDH 类生成 EC Diffie-Hellman 交换密钥](https://itbilu.com/nodejs/core/41iXzVot.html)
* [Identity and Data Security for Web DevelopmentReport abuse](https://github.com/iddatasecuritybook)
* [Node.js 使用 RSA 加密 / 解密](https://blog.csdn.net/qq_39791705/article/details/79567410)

### 相关第三方库

* [bcrypt](https://www.npmjs.com/package/bcrypt)——密码哈希函数
* [bcrypt.js](https://www.npmjs.com/package/bcryptjs)
* [node-rsa](https://www.npmjs.com/package/node-rsa)
* [node-forge](https://www.npmjs.com/package/node-forge)


