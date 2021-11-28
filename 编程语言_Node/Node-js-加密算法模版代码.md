> 💡💡💡
>
> 本文是通过 Node.js 实现密码学中所涉及到的**哈希算法**、**对称加密算法**、**非对称加密算法**、**密钥交换算法**、**数字证书生成算法**等实现方式的模版代码。
> 涉及到的 npm 模块为：**crypt**、**scrypt**、**bcrypt**、**forge** 等。
>
> 这部分是我之前整理在 SnippetsLab 上的模版代码，希望对你有所帮助！


### 密码哈希函数 - bcrypt

```javascript
const bcrypt = require('bcrypt');

// 封装 hash 函数
function bcrypt_encrypt(username, password) {
  // 1. bcrypt 内置了生成盐值的方法：bcrypt.genSalt()
  // 第一个参数是循环次数（或者称因子，默认为 10）
  bcrypt.genSalt(10, (err, salt) => {
    if (err) throw err;
      
    // 2. 生成哈希值：bcrypt.hash()
    bcrypt.hash(password, salt, (err, key) => {
      if (err) throw err;
      
      // 3. 把用户名、密码哈希值和盐值存入数据库
      // username、key、salt
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



### 密码哈希函数 - egg-bcrypt

```javascript
// config.default.js
exports.bcrypt = {
  saltRounds: 10 // 循环次数，默认为 10
}


// {app_root}/config/plugin.js
exports.bcrypt = {
  enable: true,
  package: 'egg-bcrypt'
}


// {app_root}/app/contoller/user.js
async generate() {
  // 生成哈希值：ctx.genHash(plainText)
  const hash = await this.ctx.genHash(this.ctx.request.body.plainText);
  // Store hash in your password DB

}

async compare() {
  const { hash, plainText } = this.ctx.request.body;
  // 比较哈希值和原始字符串：ctx.compare(plainText, hash)
  const checked = await this.ctx.compare(plainText, hash);
  this.ctx.body = { checked };
} 
```



### 密码哈希函数 - PBKDF2

```javascript
/*
 * PBKDF2 是 Node.js 的 crypto 模块原生支持的标准方法。
 */
 
// 【加密示例】
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

// 对比哈希值，验证密码

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





### 密码哈希函数 - scrypt

```javascript
const scrypt = require('scrypt');
const crypto = require('crypto');

function scrypt_encrypt(username, password) {
  // 1. 使用 crypto 的 crypto.randomBytes(...) 方法生成盐值。
  crypto.randomBytes(32, (err, salt) => {
    if (err) throw err;

    // 2. scrypt.hash(...) 生成 64 位的哈希值
    // - N: scrypt 最多使用多长时间（秒数）计算密钥（偶数）。
    // - r: 计算密钥时最多使用多少字节 RAM（整数）。默认为0。
    // - p: 计算密钥时所用 RAM 占可用值的比例（0-1，换算成百分比）默认为0.5。
    scrypt.hash(password, {"N":16384,"r":8,"p":1}, 64, salt, (err, key) => {
      if (err) throw err;

      // 3. 把用户名、密码哈希值和盐值存入数据库
      console.log(`key is ${key}`);
    });
  });
}
```



### crypto 模块 - 生成随机数

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



### crypto 模块 - aes-256-gcm 示例1

```javascript
/* 
 * AES-256-GCM 对称加密算法，加密、解密示例
 */
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



### crypto 模块 - aes-256-gcm 示例2

```javascript
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



### crypto 模块 - DH 密钥交换

```javascript
/*
 DiffieHellman 类是一个用来创建 Diffie-Hellman 键交换的工具。 
 DiffieHellman 类的实例可以使用 crypto.createDiffieHellman() 方法。
 */
 
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



### crypto 模块 - ECDH 密钥交换

```javascript
/*
 ECDH 类是创建椭圆曲线 Diffie-Hellman（Elliptic Curve Diffie-Hellman (ECDH)）键交换的实用工具。 
 ECDH 类的实例可以使用 crypto.createECDH() 方法。

 crypto.getCurves() 方法会返回一个数组，其中包含支持的椭圆曲线的名称。
 */
 
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



### crypto 模块 - Hmac

```javascript
const crypto = require('crypto');
const key = crypto.randomBytes(16); // 密钥

const hmac = crypto.createHmac('sha1', key.toString('ascii'));
hmac.update('hello world');
console.log(hmac.digest('hex'));
// 4e1926fdb2992c927574cc9cbdc2f9d167191e89
```



### crypto 模块 - MD5

```javascript
const crypto = require('crypto');
const md5 = crypto.createHash('md5');

// 1. 直接对明文进行 Hash
md5.update('123456');
console.log(md5.digest('hex'));

// 2. 加盐 Hash
let password = '123456';
let salt = 'hhug6dcKyCNBQ5sUC0i6hja5dCTqdSzV'; // 盐值
// 将密码拼接上任意长度的随机字符串后，再进行 Hash
md5.update(password+salt);
console.log(md5.digest('hex'));
```



### crypto 模块 - RSA 的加密和解密

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



### crypto 模块 - RSA 的签名和验证

```javascript
const crypto = require('crypto');
const fs = require('fs');
const path = require('path');
const root = __dirname;

// 加载私钥文件
const pem = fs.readFileSync(path.join(root, './rsa_private_key.pem'));
const key = pem.toString('ascii');
// 加载公钥文件
const publicKey = fs.readFileSync(path.join(root, './rsa_public_key.pem')).toString('ascii');

// 签名
const sign = crypto.createSign('RSA-SHA256'); // 创建签名算法
sign.update('data to sign'); // 更新待签名内容
const result = sign.sign(key, 'hex'); // sign.sign() 方法用于生成并返回签名
console.log('result:', result);

// 验证
const verify = crypto.createVerify('RSA-SHA256'); // 创建验证算法
verify.update('data to sign');
console.log(verify.verify(publicKey, result, 'hex')); // true
```



### crypto 模块 - RSA 密钥对生成

```javascript
/*
crypto.generateKeyPair(type, options, callback)

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
 */


// 建议将公钥编码为 'spki' 格式，将私钥编码为 'pkcs8' 格式并加密保存：
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
    cipher: 'aes-256-cbc',
    passphrase: 'top secret'
  }
}, (err, publicKey, privateKey) => {
  // Handle errors and use the generated key pair.
});
```



### crypto 模块 - sha256

```javascript
const crypto = require('crypto');

// 创建哈希函数 sha256
const hash = crypto.createHash('sha256'); 

// 输入流编码：utf8、ascii、binary（默认）
hash.update('some data to hash', 'utf8');
// 输出编码：hex、binary、base64
console.log(hash.digest('hex'));
```



### forge 模块 - 生成自签名证书

```javascript
const forge = require('node-forge');

// 生成 RSA 密钥对，以创建自签名证书，并保存到 MongoDB 数据库
const cert = forge.pki.createCertificate();
const keys = forge.pki.rsa.generateKeyPair(2048);
cert.publicKey = keys.publicKey;
cert.serialNumber = '01';
// 证书有效期
cert.validity.notBefore = new Date();
cert.validity.notAfter = new Date();
cert.validity.notAfter.setFullYear(
    cert.validity.notBefore.getFullYear() + 1
);
const attrs = [
    {
    name: 'commonName',
    value: payload.username, // 默认名称为用户名
    },
    {
    name: 'countryName',
    value: 'china',
    },
    {
    shortName: 'ST',
    value: 'Virginia',
    },
    {
    name: 'localityName',
    value: 'Blacksbury',
    },
    {
    name: 'organizationName',
    value: 'tonintech',
    },
    {
    shortName: 'OU',
    value: 'test',
    },
];
cert.setSubject(attrs);
cert.setIssuer(attrs);
// 证书扩展
cert.setExtensions([
    {
    name: 'basicConstraints',
    cA: true,
    pathLenConstraint: 4,
    },
    {
    name: 'keyUsage',
    keyCertSign: true,
    digitalSignature: true,
    nonRepudiation: true,
    keyEncipherment: true,
    dataEncipherment: true,
    },
    {
    name: 'extKeyUsage',
    serverAuth: true,
    clientAuth: true,
    codeSigning: true,
    emailProtection: true,
    timeStamping: true,
    },
    {
    name: 'nsCertType',
    client: true,
    server: true,
    email: true,
    objsign: true,
    sslCA: true,
    emailCA: true,
    objCA: true,
    },
    {
    name: 'subjectAltName',
    altNames: [
        {
        type: 6, // URI
        value: 'http://example.org/webid#me',
        },
        {
        type: 7, // IP
        ip: '127.0.0.1',
        },
    ],
    },
    {
    name: 'subjectKeyIdentifier',
    },
]);

// TODO: add authorityKeyIdentifier extension

// 自签名证书
cert.sign(keys.privateKey, forge.md.sha256.create());
this.logger.debug('证书创建成功');

// PEM-format keys and cert
const pem = {
    privateKey: forge.pki.privateKeyToPem(keys.privateKey),
    publicKey: forge.pki.publicKeyToPem(keys.publicKey),
    certificate: forge.pki.certificateToPem(cert),
};

// 保存到 mongoDB 数据库：数字证书
const CertificateEntity = new ctx.model.Certificates({
    certificate: pem.certificate,
    privateKey: pem.privateKey,
});
const certificate = await CertificateEntity.save();
```



### Forge 模块 - PKCS#5



```javascript
/*
 实现基于 PKCS#5 的基于口令的密钥派生功能。
 
 PBKDF2 ！！！
 */


// 生成基于口令的 16 进制密钥
// note an optional message digest can be passed as the final parameter
// 注意，可以将可选的消息摘要算法作为最终参数传递
var salt = forge.random.getBytesSync(128);
var derivedKey = forge.pkcs5.pbkdf2('password', salt, numIterations, 16);

// 异步生成密钥
// note an optional message digest can be passed before the callback
forge.pkcs5.pbkdf2('password', salt, numIterations, 16, function(err, derivedKey) {
  // do something w/derivedKey
});

```





### forge 模块 - RSA 加密和解密

```javascript
const forge = require('node-forge');

// 同步方式生成 RSA-2048 密钥对
const keyPair = forge.pki.rsa.generateKeyPair(2048);
const privateKey = keyPair.privateKey;
const publicKey = keyPair.publicKey;

// 加密和解密，默认 RSAES PKCS#1 v1.5
const encrypted = publicKey.encrypt(bytes);
const decrypted = privateKey.decrypt(encrypted);

// 加密和解密，使用 RSAES PKCS#1 v1.5
const encrypted = publicKey.encrypt(bytes, 'RSAES-PKCS1-V1_5');
const decrypted = privateKey.decrypt(encrypted, 'RSAES-PKCS1-V1_5');

// 加密和解密，使用 RSAES-OAEP
const encrypted = publicKey.encrypt(bytes, 'RSA-OAEP');
const decrypted = privateKey.decrypt(encrypted, 'RSA-OAEP');

// 加密和解密，使用 RSAES-OAEP/SHA-256
const encrypted = publicKey.encrypt(bytes, 'RSA-OAEP', {
  md: forge.md.sha256.create(),
});
const decrypted = privateKey.decrypt(encrypted, 'RSA-OAEP', {
  md: forge.md.sha256.create(),
});

// 加密和解密，使用 RSAES-OAEP/SHA-256/MGF1-SHA-1
// 兼容 Java 的 RSA/ECB/OAEPWithSHA-256AndMGF1Padding
var encrypted = publicKey.encrypt(bytes, 'RSA-OAEP', {
  md: forge.md.sha256.create(),
  mgf1: {
    md: forge.md.sha1.create(),
  },
});
var decrypted = privateKey.decrypt(encrypted, 'RSA-OAEP', {
  md: forge.md.sha256.create(),
  mgf1: {
    md: forge.md.sha1.create(),
  },
});
```



### forge 模块 - RSA 签名和验证

```javascript
const forge = require('node-forge');

// 同步方式生成 RSA-2048 密钥对
const keyPair = forge.pki.rsa.generateKeyPair(2048);
const privateKey = keyPair.privateKey;
const publicKey = keyPair.publicKey;



// ------------------- RSASSA PKCS#1 v1.5 模式 --------------------------------
// 使用私钥对数据进行签名并输出 DER 编码的字节
// 默认为 RSASSA PKCS#1 v1.5 模式
const md = forge.md.sha1.create();
md.update('sign this', 'utf8');
const signature = privateKey.sign(md);

// 使用公钥验证数据
const verified = publicKey.verify(md.digest().bytes(), signature);
console.log(verified); // true



// ------------------- RSASSA-PSS 模式 --------------------------------
// 签名和验证，使用 RSASSA-PSS 模式
// 其中，PSS 使用 SHA-1 作为哈希算法，SHA-1 基于 MGF1 函数，盐值长度为 20 byte
var md = forge.md.sha1.create();
md.update('sign this', 'utf8');
var pss = forge.pss.create({
  md: forge.md.sha1.create(),
  mgf: forge.mgf.mgf1.create(forge.md.sha1.create()),
  saltLength: 20,
  // optionally pass 'prng' with a custom PRNG implementation
  // optionalls pass 'salt' with a forge.util.ByteBuffer w/custom salt
});
var signature = privateKey.sign(md, pss);

// 验证 RSASSA-PSS 签名
var pss = forge.pss.create({
  md: forge.md.sha1.create(),
  mgf: forge.mgf.mgf1.create(forge.md.sha1.create()),
  saltLength: 20,
  // optionally pass 'prng' with a custom PRNG implementation
});
var md = forge.md.sha1.create();
md.update('sign this', 'utf8');
publicKey.verify(md.digest().getBytes(), signature, pss);
```



### forge 模块 - RSA 算法

```javascript
const forge = require('node-forge');

// 同步方式生成 RSA-2048 密钥对（不推荐）
const keyPair = forge.pki.rsa.generateKeyPair(2048);

// 异步方式生成 RSA-2048 密钥对
rsa.generateKeyPair({bits: 2048, workers: 2}, function(err, keypair) {
  // keypair.privateKey, keypair.publicKey
});

// 转换为 PEM 格式
const privateKey = forge.pki.privateKeyToPem(keyPair.privateKey);
const publicKey = forge.pki.publicKeyToPem(keyPair.publicKey);

// 保存到数据库
const KeyPairEntity = new ctx.model.KeyPairs({
    publicKey: publicKey,
    privateKey: privateKey,
});
const result = await KeyPairEntity.save();
```



### forge 模块 - SSH 工具

```javascript
// 将一个 RSA 私钥文件编码（可选加密）为 Putty PPK 文件
forge.ssh.privateKeyToPutty(privateKey, passphrase, comment);

// 将一个 RSA 公钥文件编码为 OpenSSH 文件
forge.ssh.publicKeyToOpenSSH(key, comment);

// 将一个 RSA 私钥文件编码为 OpenSSH 文件
forge.ssh.privateKeyToOpenSSH(privateKey, passphrase);

// 从字节缓冲区（byte buffer）中获取 SSH 公钥指纹
forge.ssh.getPublicKeyFingerprint(key);

// 获取十六进制编码，以冒号分隔的 SSH 公钥指纹
forge.ssh.getPublicKeyFingerprint(key, {encoding: 'hex', delimiter: ':'});
```



### Forge 模块 - X.509

```javascript
var pki = forge.pki;

// 将 PEM 格式的公钥转换为 Forge 公钥
var publicKey = pki.publicKeyFromPem(pem);

// 将 Forge 公钥转换为 PEM 格式的公钥
var pem = pki.publicKeyToPem(publicKey);

// convert an ASN.1 SubjectPublicKeyInfo to a Forge public key
var publicKey = pki.publicKeyFromAsn1(subjectPublicKeyInfo);

// convert a Forge public key to an ASN.1 SubjectPublicKeyInfo
var subjectPublicKeyInfo = pki.publicKeyToAsn1(publicKey);

// gets a SHA-1 RSAPublicKey fingerprint a byte buffer
// 获取 RSA 公钥的 SHA-1 指纹，Byte buffer 格式 
pki.getPublicKeyFingerprint(key);

// gets a SHA-1 SubjectPublicKeyInfo fingerprint a byte buffer
pki.getPublicKeyFingerprint(key, {type: 'SubjectPublicKeyInfo'});

// gets a hex-encoded, colon-delimited SHA-1 RSAPublicKey public key fingerprint
// 获取以十六进制编码，冒号分隔的 SHA-1 RSAPublicKey 公钥指纹
pki.getPublicKeyFingerprint(key, {encoding: 'hex', delimiter: ':'});

// gets a hex-encoded, colon-delimited SHA-1 SubjectPublicKeyInfo public key fingerprint
pki.getPublicKeyFingerprint(key, {
  type: 'SubjectPublicKeyInfo',
  encoding: 'hex',
  delimiter: ':'
});

// gets a hex-encoded, colon-delimited MD5 RSAPublicKey public key fingerprint
// 获取十六进制编码，冒号分隔的 MD5 RSAPublicKey 公钥指纹
pki.getPublicKeyFingerprint(key, {
  md: forge.md.md5.create(),
  encoding: 'hex',
  delimiter: ':'
});

// creates a CA store
// 创建一个 CA 仓库
var caStore = pki.createCaStore([/* PEM-encoded cert */, ...]);

// add a certificate to the CA store
// 添加一张证书到 CA 仓库
caStore.addCertificate(certObjectOrPemString);

// gets the issuer (its certificate) for the given certificate
// 获取给定证书的颁发者（其证书）
var issuerCert = caStore.getIssuer(subjectCert);

// verifies a certificate chain against a CA store
// 根据 CA 仓库验证证书链
pki.verifyCertificateChain(caStore, chain, customVerifyCallback);

// signs a certificate using the given private key
// 使用给定的私钥为证书签名
cert.sign(privateKey);

// signs a certificate using SHA-256 instead of SHA-1
// 使用 SHA-256 而不是 SHA-1 为证书签名
cert.sign(privateKey, forge.md.sha256.create());

// verifies an issued certificate using the certificates public key
// 使用证书公钥验证已颁发的证书
var verified = issuer.verify(issued);

// generate a keypair and create an X.509v3 certificate
// 生成密钥对并创建 X.509v3 证书
var keys = pki.rsa.generateKeyPair(2048);
var cert = pki.createCertificate();
cert.publicKey = keys.publicKey;
// alternatively set public key from a csr
// 或者通过 csr 设置公钥
//cert.publicKey = csr.publicKey;
// NOTE: serialNumber is the hex encoded value of an ASN.1 INTEGER.
// 注意：serialNumber 是 ASN.1 INTEGER 的十六进制编码值。
// Conforming CAs should ensure serialNumber is:
// 符合要求的 CA 应确保 serialNumber 为：
// - no more than 20 octets
// - 不超过20个八位位组
// - non-negative (prefix a '00' if your value starts with a '1' bit)
// - 非负数（如果您的值以“ 1”开头，则前缀为“ 00”）
cert.serialNumber = '01';
cert.validity.notBefore = new Date();
cert.validity.notAfter = new Date();
cert.validity.notAfter.setFullYear(cert.validity.notBefore.getFullYear() + 1);
var attrs = [{
  name: 'commonName',
  value: 'example.org'
}, {
  name: 'countryName',
  value: 'US'
}, {
  shortName: 'ST',
  value: 'Virginia'
}, {
  name: 'localityName',
  value: 'Blacksburg'
}, {
  name: 'organizationName',
  value: 'Test'
}, {
  shortName: 'OU',
  value: 'Test'
}];
cert.setSubject(attrs);
// alternatively set subject from a csr
// 或者通过 csr 设置主题
//cert.setSubject(csr.subject.attributes);
cert.setIssuer(attrs);
// 证书扩展项
cert.setExtensions([{
  name: 'basicConstraints',
  cA: true
}, {
  name: 'keyUsage',
  keyCertSign: true,
  digitalSignature: true,
  nonRepudiation: true,
  keyEncipherment: true,
  dataEncipherment: true
}, {
  name: 'extKeyUsage',
  serverAuth: true,
  clientAuth: true,
  codeSigning: true,
  emailProtection: true,
  timeStamping: true
}, {
  name: 'nsCertType',
  client: true,
  server: true,
  email: true,
  objsign: true,
  sslCA: true,
  emailCA: true,
  objCA: true
}, {
  name: 'subjectAltName',
  altNames: [{
    type: 6, // URI
    value: 'http://example.org/webid#me'
  }, {
    type: 7, // IP
    ip: '127.0.0.1'
  }]
}, {
  name: 'subjectKeyIdentifier'
}]);
/* alternatively set extensions from a csr
   或者通过 CSR 设置扩展项
var extensions = csr.getAttribute({name: 'extensionRequest'}).extensions;
// optionally add more extensions
extensions.push.apply(extensions, [{
  name: 'basicConstraints',
  cA: true
}, {
  name: 'keyUsage',
  keyCertSign: true,
  digitalSignature: true,
  nonRepudiation: true,
  keyEncipherment: true,
  dataEncipherment: true
}]);
cert.setExtensions(extensions);
*/
// self-sign certificate
// 自签名证书
cert.sign(keys.privateKey);

// convert a Forge certificate to PEM
// 将 Forge 证书转换为 PEM 格式
var pem = pki.certificateToPem(cert);

// convert a Forge certificate from PEM
// 将 PEM 格式证书转换为 Forge
var cert = pki.certificateFromPem(pem);

// convert an ASN.1 X.509x3 object to a Forge certificate
var cert = pki.certificateFromAsn1(obj);

// convert a Forge certificate to an ASN.1 X.509v3 object
var asn1Cert = pki.certificateToAsn1(cert);
```
