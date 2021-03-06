> ð¡ð¡ð¡
>
> æ¬ææ¯éè¿ Node.js å®ç°å¯ç å­¦ä¸­ææ¶åå°ç**åå¸ç®æ³**ã**å¯¹ç§°å å¯ç®æ³**ã**éå¯¹ç§°å å¯ç®æ³**ã**å¯é¥äº¤æ¢ç®æ³**ã**æ°å­è¯ä¹¦çæç®æ³**ç­å®ç°æ¹å¼çæ¨¡çä»£ç ã
> æ¶åå°ç npm æ¨¡åä¸ºï¼**crypt**ã**scrypt**ã**bcrypt**ã**forge** ç­ã
>
> è¿é¨åæ¯æä¹åæ´çå¨ SnippetsLab ä¸çæ¨¡çä»£ç ï¼å¸æå¯¹ä½ ææå¸®å©ï¼


### å¯ç åå¸å½æ° - bcrypt

```javascript
const bcrypt = require('bcrypt');

// å°è£ hash å½æ°
function bcrypt_encrypt(username, password) {
  // 1. bcrypt åç½®äºçæçå¼çæ¹æ³ï¼bcrypt.genSalt()
  // ç¬¬ä¸ä¸ªåæ°æ¯å¾ªç¯æ¬¡æ°ï¼æèç§°å å­ï¼é»è®¤ä¸º 10ï¼
  bcrypt.genSalt(10, (err, salt) => {
    if (err) throw err;
      
    // 2. çæåå¸å¼ï¼bcrypt.hash()
    bcrypt.hash(password, salt, (err, key) => {
      if (err) throw err;
      
      // 3. æç¨æ·åãå¯ç åå¸å¼åçå¼å­å¥æ°æ®åº
      // usernameãkeyãsalt
    });
  });
}

// è°ç¨ç¤ºä¾
bcrypt_encrypt('zhangsan', '123456');

// å¯¹æ¯åå¸å¼ï¼éªè¯å¯ç ï¼
bcrypt.compare(password, hash, (err, same) => {
  // è¿å true æ false
});
```



### å¯ç åå¸å½æ° - egg-bcrypt

```javascript
// config.default.js
exports.bcrypt = {
  saltRounds: 10 // å¾ªç¯æ¬¡æ°ï¼é»è®¤ä¸º 10
}


// {app_root}/config/plugin.js
exports.bcrypt = {
  enable: true,
  package: 'egg-bcrypt'
}


// {app_root}/app/contoller/user.js
async generate() {
  // çæåå¸å¼ï¼ctx.genHash(plainText)
  const hash = await this.ctx.genHash(this.ctx.request.body.plainText);
  // Store hash in your password DB

}

async compare() {
  const { hash, plainText } = this.ctx.request.body;
  // æ¯è¾åå¸å¼ååå§å­ç¬¦ä¸²ï¼ctx.compare(plainText, hash)
  const checked = await this.ctx.compare(plainText, hash);
  this.ctx.body = { checked };
} 
```



### å¯ç åå¸å½æ° - PBKDF2

```javascript
/*
 * PBKDF2 æ¯ Node.js ç crypto æ¨¡ååçæ¯æçæ åæ¹æ³ã
 */
 
// ãå å¯ç¤ºä¾ã
// PBKDF2 ç®æ³
function pbkdf2_encrypt(username, password) {
  // 1. crypto.randomBytes(ï¼æ¹æ³çæ 32 å­èçéæºçå¼
  crypto.randomBytes(32, (err, salt) => {
    if (err) throw err;

    // 2. åæ°åè¡¨ï¼ï¼å¯ç ï¼çå¼ï¼è¿­ä»£æ¬¡æ°ï¼è¾åºå¯é¥é¿åº¦ï¼æè¦ç®æ³ï¼
    crypto.pbkdf2(password, salt, 4096, 512, 'sha256', (err, key) => {
      if (err) throw err;
      
      // 3. å°ç¨æ·åãå¯ç åå¸å¼åçå¼å­å¥æ°æ®åº
      // Salt çå¼æ¯ææä¿å­çï¼ä¸è¬ä¸åæç»çæçå¯é¥ä¿å­å¨ä¸èµ·ã
      console.log(username, key.toString('hex'), salt.toString('hex'));
    });
  });
}

// è°ç¨ç¤ºä¾
pbkdf2_encrypt('zhangSan', '123456');

// å¯¹æ¯åå¸å¼ï¼éªè¯å¯ç 

const dbsalt = 'USER RECORD SALT FROM YOUR DATABASE';
const dbhash = 'USER RECORD KEY FROM YOUR DATABASE';

crypto.pbkdf2(password, dbsalt, 4096, 512, 'sha256', (err, comparsehash) => {
  if (err) throw err;

  // æ¯è¾
  if (dbhash.toString('hex') === comparsehash.toString('hex')) {
    // å¯ç å¹é
  } else {
    // å¯ç ä¸å¹é
  }
});
```





### å¯ç åå¸å½æ° - scrypt

```javascript
const scrypt = require('scrypt');
const crypto = require('crypto');

function scrypt_encrypt(username, password) {
  // 1. ä½¿ç¨ crypto ç crypto.randomBytes(...) æ¹æ³çæçå¼ã
  crypto.randomBytes(32, (err, salt) => {
    if (err) throw err;

    // 2. scrypt.hash(...) çæ 64 ä½çåå¸å¼
    // - N: scrypt æå¤ä½¿ç¨å¤é¿æ¶é´ï¼ç§æ°ï¼è®¡ç®å¯é¥ï¼å¶æ°ï¼ã
    // - r: è®¡ç®å¯é¥æ¶æå¤ä½¿ç¨å¤å°å­è RAMï¼æ´æ°ï¼ãé»è®¤ä¸º0ã
    // - p: è®¡ç®å¯é¥æ¶æç¨ RAM å å¯ç¨å¼çæ¯ä¾ï¼0-1ï¼æ¢ç®æç¾åæ¯ï¼é»è®¤ä¸º0.5ã
    scrypt.hash(password, {"N":16384,"r":8,"p":1}, 64, salt, (err, key) => {
      if (err) throw err;

      // 3. æç¨æ·åãå¯ç åå¸å¼åçå¼å­å¥æ°æ®åº
      console.log(`key is ${key}`);
    });
  });
}
```



### crypto æ¨¡å - çæéæºæ°

```javascript
const crypto = require('crypto');

// 1.å¼æ­¥æ¹æ³çæçå¼
// crypto.randomBytes() çæ 16 ä½å¼ºå å¯çä¼ªéæºæ°
crypto.randomBytes(16, (err, buf) => {
  if (err) throw err;
  console.log(`${buf.length} bytes of random data: ${buf.toString('hex')}`);
  console.log(`${buf.length} bytes of random data: ${buf.toString('base64')}`);
});

// 2.åæ­¥æ¹æ³çæçå¼
const buf = crypto.randomBytes(256);
```



### crypto æ¨¡å - aes-256-gcm ç¤ºä¾1

```javascript
/* 
 * AES-256-GCM å¯¹ç§°å å¯ç®æ³ï¼å å¯ãè§£å¯ç¤ºä¾
 */
const crypto = require('crypto');

// åå§ååæ°
const text = 'Encryption Testing AES GCM mode'; // è¦å å¯åè§£å¯çæ°æ®
const key = crypto.randomBytes(32); // 256 ä½çå±äº«å¯é¥
const iv = crypto.randomBytes(16); // åå§åéï¼16 å­è
const algorithm = 'aes-256-gcm'; // å å¯ç®æ³åæä½æ¨¡å¼

// å å¯
const cipher = crypto.createCipheriv(algorithm, key, iv); // åå§åå å¯ç®æ³
let encrypted = cipher.update(text, 'utf8', 'hex');
encrypted += cipher.final('hex');
const tag = cipher.getAuthTag(); // çææ ç­¾ï¼ç¨äºéªè¯å¯æçæ¥æº

// è§£å¯
const decipher = crypto.createDecipheriv(algorithm, key, iv); // åå§åè§£å¯ç®æ³
decipher.setAuthTag(tag); // ä¼ å¥éªè¯æ ç­¾ï¼éªè¯å¯æçæ¥æº
let decrypted = decipher.update(encrypted, 'hex', 'utf8');
decrypted += decipher.final('utf8');

console.log(decrypted); // Encryption Testing AES GCM mode
```



### crypto æ¨¡å - aes-256-gcm ç¤ºä¾2

```javascript
const crypto = require('crypto');

const aes256gcm = key => {
  const ALGO = 'aes-256-gcm'; // å å¯ç®æ³åæä½æ¨¡å¼

  // encrypt returns base64-encoded ciphertext
  const encrypt = str => {
    // Hint: the 'iv' should be unique (but not necessarily random).
    // 'randomBytes' here are (relatively) slow but convenient for demonstration
    const iv = new Buffer.from(crypto.randomBytes(16), 'utf8'); // åå§åéï¼16 å­è
    const cipher = crypto.createCipheriv(ALGO, key, iv); // åå§åå å¯ç®æ³

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

// å å¯å¯é¥
// Note: a key size of 16 bytes will use AES-128, 24 => AES-192, 32 => AES-256
const KEY = new Buffer.from(crypto.randomBytes(32), 'utf8');

const aesCipher = aes256gcm(KEY);

// å å¯
const [ encrypted, iv, authTag ] = aesCipher.encrypt('hello world');
// è§£å¯
const decrypted = aesCipher.decrpt(encrypted, iv, authTag);

console.log(decrypted); // hello world
```



### crypto æ¨¡å - DH å¯é¥äº¤æ¢

```javascript
/*
 DiffieHellman ç±»æ¯ä¸ä¸ªç¨æ¥åå»º Diffie-Hellman é®äº¤æ¢çå·¥å·ã 
 DiffieHellman ç±»çå®ä¾å¯ä»¥ä½¿ç¨ crypto.createDiffieHellman() æ¹æ³ã
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



### crypto æ¨¡å - ECDH å¯é¥äº¤æ¢

```javascript
/*
 ECDH ç±»æ¯åå»ºæ¤­åæ²çº¿ Diffie-Hellmanï¼Elliptic Curve Diffie-Hellman (ECDH)ï¼é®äº¤æ¢çå®ç¨å·¥å·ã 
 ECDH ç±»çå®ä¾å¯ä»¥ä½¿ç¨ crypto.createECDH() æ¹æ³ã

 crypto.getCurves() æ¹æ³ä¼è¿åä¸ä¸ªæ°ç»ï¼å¶ä¸­åå«æ¯æçæ¤­åæ²çº¿çåç§°ã
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



### crypto æ¨¡å - Hmac

```javascript
const crypto = require('crypto');
const key = crypto.randomBytes(16); // å¯é¥

const hmac = crypto.createHmac('sha1', key.toString('ascii'));
hmac.update('hello world');
console.log(hmac.digest('hex'));
// 4e1926fdb2992c927574cc9cbdc2f9d167191e89
```



### crypto æ¨¡å - MD5

```javascript
const crypto = require('crypto');
const md5 = crypto.createHash('md5');

// 1. ç´æ¥å¯¹ææè¿è¡ Hash
md5.update('123456');
console.log(md5.digest('hex'));

// 2. å ç Hash
let password = '123456';
let salt = 'hhug6dcKyCNBQ5sUC0i6hja5dCTqdSzV'; // çå¼
// å°å¯ç æ¼æ¥ä¸ä»»æé¿åº¦çéæºå­ç¬¦ä¸²åï¼åè¿è¡ Hash
md5.update(password+salt);
console.log(md5.digest('hex'));
```



### crypto æ¨¡å - RSA çå å¯åè§£å¯

```javascript
const crypto = require('crypto');
const fs = require('fs');
const path = require('path');
const root = __dirname;

const publicKey = fs.readFileSync(path.join(root, './mypubkey.pem')).toString('ascii');
const privateKey = fs.readFileSync(path.join(root, './ mykey.pem')).toString('ascii');
const data = 'data to crypt';

// å¬é¥å å¯
const encryptData = crypto.publicEncrypt(publicKey, Buffer.from(data)).toString('base64');
console.log('encode', encryptData);

// ç§é¥è§£å¯
const decryptData = crypto.privateDecrypt(privateKey, Buffer.from(encryptData.toString('base64'), 'base64'));
console.log('decode', decryptData.toString());
```



### crypto æ¨¡å - RSA çç­¾ååéªè¯

```javascript
const crypto = require('crypto');
const fs = require('fs');
const path = require('path');
const root = __dirname;

// å è½½ç§é¥æä»¶
const pem = fs.readFileSync(path.join(root, './rsa_private_key.pem'));
const key = pem.toString('ascii');
// å è½½å¬é¥æä»¶
const publicKey = fs.readFileSync(path.join(root, './rsa_public_key.pem')).toString('ascii');

// ç­¾å
const sign = crypto.createSign('RSA-SHA256'); // åå»ºç­¾åç®æ³
sign.update('data to sign'); // æ´æ°å¾ç­¾ååå®¹
const result = sign.sign(key, 'hex'); // sign.sign() æ¹æ³ç¨äºçæå¹¶è¿åç­¾å
console.log('result:', result);

// éªè¯
const verify = crypto.createVerify('RSA-SHA256'); // åå»ºéªè¯ç®æ³
verify.update('data to sign');
console.log(verify.verify(publicKey, result, 'hex')); // true
```



### crypto æ¨¡å - RSA å¯é¥å¯¹çæ

```javascript
/*
crypto.generateKeyPair(type, options, callback)

åæ°ï¼
* `type`ï¼<string> ç±»åï¼è¦çæçç®æ³åç§°ï¼å¿é¡»æ¯ `'rsa'`ã`'dsa'` æè `'ec'` ä¹ä¸ï¼
* `options`ï¼<Object> ç±»åï¼
    * `modulusLength`ï¼<number> ç±»åï¼å¯é¥é¿åº¦ï¼RSAï¼DSAï¼ï¼
    * `publicExponent`ï¼<number> ç±»åï¼å¬å±ææ°ï¼RSAï¼ï¼é»è®¤å¼ï¼`0x10001`ï¼
    * `divisorLength`ï¼<number> ç±»åï¼ä»¥æ¯ç¹ä¸ºåä½ç q çå¤§å°ï¼DSAï¼ï¼
    * `namedCurve`ï¼<string> ç±»åï¼è¦ä½¿ç¨çæ¤­åæ²çº¿åç§°ï¼ECï¼ï¼
    * `publicKeyEncoding`ï¼<Object> ç±»åï¼å¬é¥ç¼ç æ ¼å¼ï¼
        * `type`ï¼<string> ç±»åï¼å¿é¡»æ¯ `'pkcs1'`ï¼ä»é RSAï¼æ `'spki'` ä¹ä¸ï¼
        * `format`ï¼<string> ç±»åï¼å¬é¥è¾åºæ ¼å¼ï¼å¿é¡»æ¯ `'pem'` æ `'der'` ä¹ä¸ï¼
    * `privateKeyEncoding`ï¼<Object> ç±»åï¼ç§é¥ç¼ç æ ¼å¼ï¼
        * `type`ï¼<string> ç±»åï¼å¿é¡»æ¯ `'pkcs1'`ï¼ä»é RSAï¼æ `'pkcs8'` æ `'sec1'`(ä»é EC) ä¹ä¸ï¼
        * `format`ï¼<string> ç±»åï¼å¬é¥è¾åºæ ¼å¼ï¼å¿é¡»æ¯ `'pem'` æ `'der'` ä¹ä¸ï¼
        * `cipher`ï¼<string> ç±»åï¼å¦ææå®ç®æ³ï¼å°ä½¿ç¨åºäº PKCSï¼5 v2.0 è§èçç®æ³åå¯é¥å å¯ï¼ä½¿ç¨ç»å®çç®æ³åå¯é¥å å¯ç§é¥ï¼
        * `passphrase`ï¼<string> ç±»åï¼ç¨äºå å¯çå¯é¥ï¼
* `callback`ï¼<Function> ç±»åï¼
    * `err`ï¼<Error> ç±»å
    * `publicKey`ï¼<string> ç±»å | <Buffer> ç±»åï¼è®¾ç½®ä¸º PEM ç¼ç æ¶è¿å String ç±»åï¼DER ç¼ç åè¿å Buffer ç±»å
    * `privateKey`ï¼<string> ç±»å | <Buffer> ç±»å
 */


// å»ºè®®å°å¬é¥ç¼ç ä¸º 'spki' æ ¼å¼ï¼å°ç§é¥ç¼ç ä¸º 'pkcs8' æ ¼å¼å¹¶å å¯ä¿å­ï¼
const { generateKeyPair } = require('crypto');
generateKeyPair('rsa', {
  modulusLength: 4096,
  publicKeyEncoding: {
    type: 'spki', // pkcs1ãspki
    format: 'pem'
  },
  privateKeyEncoding: {
    type: 'pkcs8', // pkcs1ãpkcs8ãsec1
    format: 'pem',
    cipher: 'aes-256-cbc',
    passphrase: 'top secret'
  }
}, (err, publicKey, privateKey) => {
  // Handle errors and use the generated key pair.
});
```



### crypto æ¨¡å - sha256

```javascript
const crypto = require('crypto');

// åå»ºåå¸å½æ° sha256
const hash = crypto.createHash('sha256'); 

// è¾å¥æµç¼ç ï¼utf8ãasciiãbinaryï¼é»è®¤ï¼
hash.update('some data to hash', 'utf8');
// è¾åºç¼ç ï¼hexãbinaryãbase64
console.log(hash.digest('hex'));
```



### forge æ¨¡å - çæèªç­¾åè¯ä¹¦

```javascript
const forge = require('node-forge');

// çæ RSA å¯é¥å¯¹ï¼ä»¥åå»ºèªç­¾åè¯ä¹¦ï¼å¹¶ä¿å­å° MongoDB æ°æ®åº
const cert = forge.pki.createCertificate();
const keys = forge.pki.rsa.generateKeyPair(2048);
cert.publicKey = keys.publicKey;
cert.serialNumber = '01';
// è¯ä¹¦æææ
cert.validity.notBefore = new Date();
cert.validity.notAfter = new Date();
cert.validity.notAfter.setFullYear(
    cert.validity.notBefore.getFullYear() + 1
);
const attrs = [
    {
    name: 'commonName',
    value: payload.username, // é»è®¤åç§°ä¸ºç¨æ·å
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
// è¯ä¹¦æ©å±
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

// èªç­¾åè¯ä¹¦
cert.sign(keys.privateKey, forge.md.sha256.create());
this.logger.debug('è¯ä¹¦åå»ºæå');

// PEM-format keys and cert
const pem = {
    privateKey: forge.pki.privateKeyToPem(keys.privateKey),
    publicKey: forge.pki.publicKeyToPem(keys.publicKey),
    certificate: forge.pki.certificateToPem(cert),
};

// ä¿å­å° mongoDB æ°æ®åºï¼æ°å­è¯ä¹¦
const CertificateEntity = new ctx.model.Certificates({
    certificate: pem.certificate,
    privateKey: pem.privateKey,
});
const certificate = await CertificateEntity.save();
```



### Forge æ¨¡å - PKCS#5



```javascript
/*
 å®ç°åºäº PKCS#5 çåºäºå£ä»¤çå¯é¥æ´¾çåè½ã
 
 PBKDF2 ï¼ï¼ï¼
 */


// çæåºäºå£ä»¤ç 16 è¿å¶å¯é¥
// note an optional message digest can be passed as the final parameter
// æ³¨æï¼å¯ä»¥å°å¯éçæ¶æ¯æè¦ç®æ³ä½ä¸ºæç»åæ°ä¼ é
var salt = forge.random.getBytesSync(128);
var derivedKey = forge.pkcs5.pbkdf2('password', salt, numIterations, 16);

// å¼æ­¥çæå¯é¥
// note an optional message digest can be passed before the callback
forge.pkcs5.pbkdf2('password', salt, numIterations, 16, function(err, derivedKey) {
  // do something w/derivedKey
});

```





### forge æ¨¡å - RSA å å¯åè§£å¯

```javascript
const forge = require('node-forge');

// åæ­¥æ¹å¼çæ RSA-2048 å¯é¥å¯¹
const keyPair = forge.pki.rsa.generateKeyPair(2048);
const privateKey = keyPair.privateKey;
const publicKey = keyPair.publicKey;

// å å¯åè§£å¯ï¼é»è®¤ RSAES PKCS#1 v1.5
const encrypted = publicKey.encrypt(bytes);
const decrypted = privateKey.decrypt(encrypted);

// å å¯åè§£å¯ï¼ä½¿ç¨ RSAES PKCS#1 v1.5
const encrypted = publicKey.encrypt(bytes, 'RSAES-PKCS1-V1_5');
const decrypted = privateKey.decrypt(encrypted, 'RSAES-PKCS1-V1_5');

// å å¯åè§£å¯ï¼ä½¿ç¨ RSAES-OAEP
const encrypted = publicKey.encrypt(bytes, 'RSA-OAEP');
const decrypted = privateKey.decrypt(encrypted, 'RSA-OAEP');

// å å¯åè§£å¯ï¼ä½¿ç¨ RSAES-OAEP/SHA-256
const encrypted = publicKey.encrypt(bytes, 'RSA-OAEP', {
  md: forge.md.sha256.create(),
});
const decrypted = privateKey.decrypt(encrypted, 'RSA-OAEP', {
  md: forge.md.sha256.create(),
});

// å å¯åè§£å¯ï¼ä½¿ç¨ RSAES-OAEP/SHA-256/MGF1-SHA-1
// å¼å®¹ Java ç RSA/ECB/OAEPWithSHA-256AndMGF1Padding
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



### forge æ¨¡å - RSA ç­¾ååéªè¯

```javascript
const forge = require('node-forge');

// åæ­¥æ¹å¼çæ RSA-2048 å¯é¥å¯¹
const keyPair = forge.pki.rsa.generateKeyPair(2048);
const privateKey = keyPair.privateKey;
const publicKey = keyPair.publicKey;



// ------------------- RSASSA PKCS#1 v1.5 æ¨¡å¼ --------------------------------
// ä½¿ç¨ç§é¥å¯¹æ°æ®è¿è¡ç­¾åå¹¶è¾åº DER ç¼ç çå­è
// é»è®¤ä¸º RSASSA PKCS#1 v1.5 æ¨¡å¼
const md = forge.md.sha1.create();
md.update('sign this', 'utf8');
const signature = privateKey.sign(md);

// ä½¿ç¨å¬é¥éªè¯æ°æ®
const verified = publicKey.verify(md.digest().bytes(), signature);
console.log(verified); // true



// ------------------- RSASSA-PSS æ¨¡å¼ --------------------------------
// ç­¾ååéªè¯ï¼ä½¿ç¨ RSASSA-PSS æ¨¡å¼
// å¶ä¸­ï¼PSS ä½¿ç¨ SHA-1 ä½ä¸ºåå¸ç®æ³ï¼SHA-1 åºäº MGF1 å½æ°ï¼çå¼é¿åº¦ä¸º 20 byte
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

// éªè¯ RSASSA-PSS ç­¾å
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



### forge æ¨¡å - RSA ç®æ³

```javascript
const forge = require('node-forge');

// åæ­¥æ¹å¼çæ RSA-2048 å¯é¥å¯¹ï¼ä¸æ¨èï¼
const keyPair = forge.pki.rsa.generateKeyPair(2048);

// å¼æ­¥æ¹å¼çæ RSA-2048 å¯é¥å¯¹
rsa.generateKeyPair({bits: 2048, workers: 2}, function(err, keypair) {
  // keypair.privateKey, keypair.publicKey
});

// è½¬æ¢ä¸º PEM æ ¼å¼
const privateKey = forge.pki.privateKeyToPem(keyPair.privateKey);
const publicKey = forge.pki.publicKeyToPem(keyPair.publicKey);

// ä¿å­å°æ°æ®åº
const KeyPairEntity = new ctx.model.KeyPairs({
    publicKey: publicKey,
    privateKey: privateKey,
});
const result = await KeyPairEntity.save();
```



### forge æ¨¡å - SSH å·¥å·

```javascript
// å°ä¸ä¸ª RSA ç§é¥æä»¶ç¼ç ï¼å¯éå å¯ï¼ä¸º Putty PPK æä»¶
forge.ssh.privateKeyToPutty(privateKey, passphrase, comment);

// å°ä¸ä¸ª RSA å¬é¥æä»¶ç¼ç ä¸º OpenSSH æä»¶
forge.ssh.publicKeyToOpenSSH(key, comment);

// å°ä¸ä¸ª RSA ç§é¥æä»¶ç¼ç ä¸º OpenSSH æä»¶
forge.ssh.privateKeyToOpenSSH(privateKey, passphrase);

// ä»å­èç¼å²åºï¼byte bufferï¼ä¸­è·å SSH å¬é¥æçº¹
forge.ssh.getPublicKeyFingerprint(key);

// è·ååå­è¿å¶ç¼ç ï¼ä»¥åå·åéç SSH å¬é¥æçº¹
forge.ssh.getPublicKeyFingerprint(key, {encoding: 'hex', delimiter: ':'});
```



### Forge æ¨¡å - X.509

```javascript
var pki = forge.pki;

// å° PEM æ ¼å¼çå¬é¥è½¬æ¢ä¸º Forge å¬é¥
var publicKey = pki.publicKeyFromPem(pem);

// å° Forge å¬é¥è½¬æ¢ä¸º PEM æ ¼å¼çå¬é¥
var pem = pki.publicKeyToPem(publicKey);

// convert an ASN.1 SubjectPublicKeyInfo to a Forge public key
var publicKey = pki.publicKeyFromAsn1(subjectPublicKeyInfo);

// convert a Forge public key to an ASN.1 SubjectPublicKeyInfo
var subjectPublicKeyInfo = pki.publicKeyToAsn1(publicKey);

// gets a SHA-1 RSAPublicKey fingerprint a byte buffer
// è·å RSA å¬é¥ç SHA-1 æçº¹ï¼Byte buffer æ ¼å¼ 
pki.getPublicKeyFingerprint(key);

// gets a SHA-1 SubjectPublicKeyInfo fingerprint a byte buffer
pki.getPublicKeyFingerprint(key, {type: 'SubjectPublicKeyInfo'});

// gets a hex-encoded, colon-delimited SHA-1 RSAPublicKey public key fingerprint
// è·åä»¥åå­è¿å¶ç¼ç ï¼åå·åéç SHA-1 RSAPublicKey å¬é¥æçº¹
pki.getPublicKeyFingerprint(key, {encoding: 'hex', delimiter: ':'});

// gets a hex-encoded, colon-delimited SHA-1 SubjectPublicKeyInfo public key fingerprint
pki.getPublicKeyFingerprint(key, {
  type: 'SubjectPublicKeyInfo',
  encoding: 'hex',
  delimiter: ':'
});

// gets a hex-encoded, colon-delimited MD5 RSAPublicKey public key fingerprint
// è·ååå­è¿å¶ç¼ç ï¼åå·åéç MD5 RSAPublicKey å¬é¥æçº¹
pki.getPublicKeyFingerprint(key, {
  md: forge.md.md5.create(),
  encoding: 'hex',
  delimiter: ':'
});

// creates a CA store
// åå»ºä¸ä¸ª CA ä»åº
var caStore = pki.createCaStore([/* PEM-encoded cert */, ...]);

// add a certificate to the CA store
// æ·»å ä¸å¼ è¯ä¹¦å° CA ä»åº
caStore.addCertificate(certObjectOrPemString);

// gets the issuer (its certificate) for the given certificate
// è·åç»å®è¯ä¹¦çé¢åèï¼å¶è¯ä¹¦ï¼
var issuerCert = caStore.getIssuer(subjectCert);

// verifies a certificate chain against a CA store
// æ ¹æ® CA ä»åºéªè¯è¯ä¹¦é¾
pki.verifyCertificateChain(caStore, chain, customVerifyCallback);

// signs a certificate using the given private key
// ä½¿ç¨ç»å®çç§é¥ä¸ºè¯ä¹¦ç­¾å
cert.sign(privateKey);

// signs a certificate using SHA-256 instead of SHA-1
// ä½¿ç¨ SHA-256 èä¸æ¯ SHA-1 ä¸ºè¯ä¹¦ç­¾å
cert.sign(privateKey, forge.md.sha256.create());

// verifies an issued certificate using the certificates public key
// ä½¿ç¨è¯ä¹¦å¬é¥éªè¯å·²é¢åçè¯ä¹¦
var verified = issuer.verify(issued);

// generate a keypair and create an X.509v3 certificate
// çæå¯é¥å¯¹å¹¶åå»º X.509v3 è¯ä¹¦
var keys = pki.rsa.generateKeyPair(2048);
var cert = pki.createCertificate();
cert.publicKey = keys.publicKey;
// alternatively set public key from a csr
// æèéè¿ csr è®¾ç½®å¬é¥
//cert.publicKey = csr.publicKey;
// NOTE: serialNumber is the hex encoded value of an ASN.1 INTEGER.
// æ³¨æï¼serialNumber æ¯ ASN.1 INTEGER çåå­è¿å¶ç¼ç å¼ã
// Conforming CAs should ensure serialNumber is:
// ç¬¦åè¦æ±ç CA åºç¡®ä¿ serialNumber ä¸ºï¼
// - no more than 20 octets
// - ä¸è¶è¿20ä¸ªå«ä½ä½ç»
// - non-negative (prefix a '00' if your value starts with a '1' bit)
// - éè´æ°ï¼å¦ææ¨çå¼ä»¥â 1âå¼å¤´ï¼ååç¼ä¸ºâ 00âï¼
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
// æèéè¿ csr è®¾ç½®ä¸»é¢
//cert.setSubject(csr.subject.attributes);
cert.setIssuer(attrs);
// è¯ä¹¦æ©å±é¡¹
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
   æèéè¿ CSR è®¾ç½®æ©å±é¡¹
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
// èªç­¾åè¯ä¹¦
cert.sign(keys.privateKey);

// convert a Forge certificate to PEM
// å° Forge è¯ä¹¦è½¬æ¢ä¸º PEM æ ¼å¼
var pem = pki.certificateToPem(cert);

// convert a Forge certificate from PEM
// å° PEM æ ¼å¼è¯ä¹¦è½¬æ¢ä¸º Forge
var cert = pki.certificateFromPem(pem);

// convert an ASN.1 X.509x3 object to a Forge certificate
var cert = pki.certificateFromAsn1(obj);

// convert a Forge certificate to an ASN.1 X.509v3 object
var asn1Cert = pki.certificateToAsn1(cert);
```
