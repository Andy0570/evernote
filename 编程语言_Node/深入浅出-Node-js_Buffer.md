在网络流和文件的操作中，需要处理大量的二进制数据，JavaScript自有的字符串数据类型无法满足需求（注：ECAMScript 2015 引入了 **[TypedArray](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)**），Buffer 对象应运而生，以提供对**二进制数据**的操作。

官方文档：[Node.js Buffer API](http://nodejs.cn/api/buffer.html)

## Buffer 结构

Buffer 是一个类似于 Array 的对象，但它主要用于操作**字节**。
Buffer 的运用场景：**文件I/O**、**网络I/O**。
Buffer 是二进制数据，字符串与Buffer之间存在编码关系。

### 模块结构

Buffer 中，性能相关部分，使用C++实现；非性能相关部分，使用JavaScript实现。

![](http://upload-images.jianshu.io/upload_images/2648731-05edc8947d38b4b5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* Buffer 所占用的内存不是通过 V8 分配的，属于==堆外内存==。
* Node 在进程启动时已经加载了 Buffer，并将其放在了全局对象（global）上。所以在使用 Buffer 时，无须通过 `require()` 即可直接使用。

### Buffer 对象

Buffer 对象类似于数组，它的元素为 16 进制的两位数，即0到255的数值。

```javascript
var str = '深入浅出 node.js';
var buf = Buffer.from(str, 'utf-8');
console.log(buf); // <Buffer e6 b7 b1 e5 85 a5 e6 b5 85 e5 87 ba 20 6e 6f 64 65 2e 6a 73>

var buf2 = Buffer.alloc(100);
// 通过 length 获取长度
console.log(buf2.length); // 100
// 通过下标访问元素、赋值
console.log(buf2[10]); // 0
```

给元素的赋值（0～255）规则：

* 赋值<0，逐次加256；
* 赋值>255，逐次减256；
* 有小数，舍弃小数部分；



### Buffer 内存分配

* Buffer 对象的内存分配是在 Node 的 C++ 层面上实现的内存申请。

* Node采用了 slab 分配机制，slab 是一种动态内存管理机制。slab 是一块申请好的固定大小的内存区域，slab的3种状态：
  * full：完全分配状态；
  * partial：部分分配状态；
  * empty：没有被分配状态；
* Node 以 8KB 来区分 Buffer 是大对象还是小对象。


简单而言，真正的内存是在 Node 的 C++ 层面提供的，JavaScript 层面只是使用它。

* 当进行小而频繁的 Buffer 操作时，采用 slab 的机制进行预先申请和事后分配，使得 JavaScript 到操作系统之间不必有过多的内存申请方面的系统调用。
* 对于大块的 Buffer 而言，则直接使用 C++ 层面提供的内存，而无需细腻的分配操作。



## Buffer 的转换

Buffer 对象可以与字符串之间相互转换。

支持的字符串编码类型有：ASCII、UTF-8、UTF-16LE/UCS-2、Base64、Latin1、Binary、Hex。

判断编码是否支持转换：

```javascript
Buffer.isEncoding(ecoding); // true/false
```

第三方模块：

* iconv：通过 C++ 调用 libiconv 库完成。
* iconv-lite：通过 JavaScript 实现。



### 宽字节中文乱码

* `buf.toString([encoding[, start[, end]]])` 
* 文件可读流在读取时会逐个读取Buffer。
* 宽字节字符串在UTF-8编码下是以3个字节的方式存储的。

```javascript
// Buffer 的拼接
var chunks = []; // 用数组来存储接收到的所有Buffer片段
var size = 0;    // 记录下所有片段的总长度
res.on('data', function(chunk) {
    chunks.push(chunk);
    size += chunk.length;
});
res.on('end', function() {
    // Buffer.concat() 生成一个合并的 Buffer 对象
    var buf = Buffer.concat(chunks, size);
    vr str = iconv.decode(buf, 'utf8');
    console.log(str);
});
```



## Buffer 与性能

* 通过预先转换静态内容为 Buffer 对象，可以有效地减少 CPU 的重复使用，节省服务器资源。
* 在文件读取时，**highWaterMark** 的设置对性能至关重要。该值越大，读取速度越快。


