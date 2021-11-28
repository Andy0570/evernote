# UUID 定义

UUID 是由一组 32 位的 16 进制数所构成，是故 UUID 理论上的总数为 16^32=2^128，约等于 3.4 x 10^38。也就是说若每[纳秒](https://www.wikiwand.com/zh-hans/%E7%BA%B3%E7%A7%92)产生 1 [兆](https://www.wikiwand.com/zh-hans/%E5%85%86)个 UUID，要花 100 亿年才会将所有 UUID 用完。

UUID 示例：74760410-f963-11e8-b2a3-1bb26e1e5b69

# UUID 版本

* "版本 1" UUID 是根据时间和节点 ID（通常是 MAC 地址）生成；
* "版本 2" UUID 是根据标识符（通常是组或用户 ID）、时间和节点 ID 生成；
* "版本 3" 和 "版本 5" 确定性 UUID 通过散列 (hashing) 命名空间 (namespace) 标识符和名称生成；
* "版本 4" UUID 使用[随机性](https://www.wikiwand.com/zh-hans/%E9%9A%8F%E6%9C%BA%E6%80%A7)或[伪随机性](https://www.wikiwand.com/zh-hans/%E4%BC%AA%E9%9A%8F%E6%9C%BA%E6%80%A7)生成。



# Node.js [uuid](https://github.com/kelektiv/node-uuid) 模块示例

在 JavaScript 中生成符合 RFC 规范的 UUID。



## 版本 1：基于时间的 UUID

```javascript
const uuidv1 = require('uuid/v1');
uuidv1() //  ⇨ 43d7e120-f963-11e8-999e-51f3e5aa256f

const formatedUUID = uuidv1().replace(/-/g, ''); // 去除横线-
```



## 版本 2：DCE 安全的 UUID

未实现。



## 版本 3：基于名字空间的 UUID（MD5）

```javascript
const uuidv3 = require('uuid/v3');

// ... using predefined DNS namespace (for domain names)
uuidv3('hello.example.com', uuidv3.DNS); // ⇨ '9125a8dc-52ee-365b-a5aa-81b0b3681cf6'

// ... using predefined URL namespace (for, well, URLs)
uuidv3('http://example.com/hello', uuidv3.URL); // ⇨ 'c6235813-3ba4-3801-ae84-e0a6ebb7d138'

// ... using a custom namespace
//
// Note: Custom namespaces should be a UUID string specific to your application!
// E.g. the one here was generated using this modules `uuid` CLI.
const MY_NAMESPACE = '1b671a64-40d5-491e-99b0-da01ff1f3341';
uuidv3('Hello, World!', MY_NAMESPACE); // ⇨ 'e8b5a51d-11c8-3310-a6ab-367563f20686'
```



## 版本 4：基于随机数的 UUID

```javascript
const uuidv4 = require('uuid/v4');
uuidv4(); // ⇨ 57751a52-db27-4b2f-8a08-7e02f9e94a42
```



## 版本 5：基于名字空间的 UUID（SHA1）

```javascript
const uuidv5 = require('uuid/v5');

// ... using predefined DNS namespace (for domain names)
uuidv5('hello.example.com', uuidv5.DNS); // ⇨ 'fdda765f-fc57-5604-a269-52a7df8164ec'

// ... using predefined URL namespace (for, well, URLs)
uuidv5('http://example.com/hello', uuidv5.URL); // ⇨ '3bbcee75-cecc-5b56-8031-b6641c1ed1f1'

// ... using a custom namespace
//
// Note: Custom namespaces should be a UUID string specific to your application!
// E.g. the one here was generated using this modules `uuid` CLI.
const MY_NAMESPACE = '1b671a64-40d5-491e-99b0-da01ff1f3341';
uuidv5('Hello, World!', MY_NAMESPACE); // ⇨ '630eb68f-e0fa-5ecc-887a-7c7a62614681'
```



# 参考

* [关于 UUID 的二三事](https://www.jianshu.com/p/d77f3ef0868a)
* [wikiwand: 通用唯一识别码](https://www.wikiwand.com/zh-hans/%E9%80%9A%E7%94%A8%E5%94%AF%E4%B8%80%E8%AF%86%E5%88%AB%E7%A0%81)





