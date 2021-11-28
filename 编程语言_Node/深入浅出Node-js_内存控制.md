内存控制是在**海量请求**和**长时间运行**的前提下进行探讨的。
在Node中如何高效地使用内存？

## V8 的垃圾回收机制与内存限制

### V8 的内存限制

* Node通过 JavaScript 使用内存的限制：64位系统下约为 1.4 GB，32位系统下约为 0.7 GB。
* Node 基于 V8 构建，所以在 Node 中使用的 JavaScript 对象基本上都是通过 V8 自己的方式来进行管理和分配的。

### V8 的对象分配

* 在 V8 中，所有的 JavaScript 对象都是通过==堆==来分配的。
* V8 限制堆内存，是基于垃圾回收机制与应用性能的考量（垃圾回收会引起 JS 线程暂停执行）。

#### 查看进程的内存占用：process.memoryUsage() 

```shell
$ node
> process.memoryUsage();
{ rss: 23175168,      # 进程的常驻内存部分
  heapTotal: 9682944, # 已申请到的堆内存
  heapUsed: 5283000,  # 堆内存当前使用量
  external: 11777 }
>

# 堆中的内存总量<进程的常驻内存用量
# 堆外内存：不是通过V8分配的内存。
# Buffer 对象不经过 V8 的内存分配机制。

# Node 的内存构成：通过 V8 分配的部分 + Node 自行分配的部分。
# 因此，受到 V8 的垃圾回收机制限制的主要是 V8 的堆内存。
```

#### 查看系统的内存占用：os.totalmem() 和 freemem()

查看操作系统的内存使用情况：

```shell
$ node
> os.totalmem() # 返回系统总内存
17179869184
> os.freemem() # 返回系统闲置内存
4516331520  
```



Node 启动时可以调整内存使用量：

```
node --max-old-space-size=1700 test.js // 单位为MB，设置老生代内存空间最大值
node --max-new-space-size=1024 test.js // 单位为KB，设置新生代内存空间大小
```



### V8 的垃圾回收机制

* V8 的垃圾回收策略主要基于==分代式垃圾回收机制==。

* V8 的内存分代：

  * V8 堆内存：新生代内存+老生代内存。
  * 新生代的内存空间中的对象：存活时间短。
  * 老生代的内存空间中的对象：存活时间长、常驻内存对象。

  | V8引擎下的堆内存分配 | 64位系统           | 32位系统         |
  | -------------------- | ------------------ | ---------------- |
  | 默认老生代内存最大值 | 1400 MB            | 700 MB           |
  | 默认新生代内存最大值 | 32 MB              | 16 MB            |
  | V8 堆内存的最大值    | 1464 MB （1.4 GB） | 732 MB（0.7 GB） |

* 新生代中的对象主要通过 Scavenge 算法进行垃圾回收，Scavenge 算法主要采用 Cheney 算法（采用复制的方式实现垃圾回收From-To）实现。

* 老生代中的对象主要采用了Mark-Sweep（标记清除）和Marl-Compact（标记整理）相结合的方式进行垃圾回收。

  ![image](http://upload-images.jianshu.io/upload_images/2648731-f57e3b94ac479cf2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 查看垃圾回收日志：`--trach_gc`

```shell
# 在 gc.log 文件中得到所有的垃圾回收信息。
node --trach_gc -e "var a = [];for (var i = 0; i < 1000000; i++) a.push(new Array(100));" > gc.log
```

### 查看性能分析数据：`--prof`

```shell
node --prof test.js
```

V8 提供了 linux-tick-processor 工具（Node路径：deps/v8/tools）用于统计日志信息。

```shell
# 将该工具的目录添加到环境变量 PATH 中，执行分析日志命令：
linux-tick-processor v8.log
```



## 高效使用内存

如何让垃圾回收机制更高效地工作？



### 作用域

* JavaScript 中能形成作用域的方式：函数调用、with、全局作用域。

* 标识符查找，变量只能向外访问，而不能向内访问；

* 作用域链；

* 变量的主动释放：delete 操作、将变量重新赋值。

  ```javascript
  // 全局变量，进程退出才会释放
  global.foo = 'I am global object';
  console.log(global.foo); // => 'I am global object'
  
  // 1. delete 操作删除全局变量
  delete global.foo; 
  
  // 2. 重新赋值
  global.foo = undefined; // or null
  console.log(global.foo); // => 'undefined'
  ```

  > 💡 在 V8 中通过 delete 删除对象的属性有可能干扰 V8 的优化，所以**通过赋值方式解除引用**更好。



### 闭包：实现外部作用域访问内部作用域中变量的方法

```javascript
var foo = function () {
    var bar = function () {
        var local = '局部变量';
        return function () { // 函数作为返回值，且该匿名函数可以访问local
            return local; // 内部作用域可以访问外部对象
        };
    };
    var baz = bar(); // bar() 函数返回的是一个匿名函数，且可以访问local
    console.log(baz());
}
```



在正常的 JavaScript 执行中，无法立即回收的内存：

1. 闭包；
2. 全局变量；



### 慎将内存当缓存

* 在Node中，任何试图拿内存当缓存的行为都应当被限制。
* [Iru cache](https://github.com/isaacs/node-lru-cache)



#### 缓存的解决方案

进程之间无法共享内存，使用大量缓存的解决方案：采用进程外的缓存，进程自身不存储状态。

解决方案：

* [redis](https://github.com/NodeRedis/node_redis)
* [Memcached](https://github.com/3rd-Eden/memcached)



### 关注队列状态

消费速度 < 生产速度时，将会形成堆积：

日志收集时，选择更高效的文件写入日志，而不是数据库写入日志。

深度的解决方案：1. 监控队列的长度；2.任意异步调用都应该包含超时机制。

Bagpipe 的超时模式和拒绝模式。



### 内存泄漏排查

Node内存泄漏检测工具：

* ~~v8-profiler。由Danny Coastes提供，它可以用于对V8堆内存抓取快照和对CPU进行分析，该项目已有3年没维护了~~
* node-heapdump。这是Node核心贡献者之一Ben Noordhuis编写的模块，它允许对V8堆内存抓取快照，用于事后分析。
* node-mtrace。由Jimb Esser提供，它使用了GCC的mtrace工具来分析堆的使用。
* dtace。在Joyent的SmartOS系统上，有完善的dtrace工具用来分析内存泄漏。
* node-memwatch。来自Mozilla的Lloyd Hilaiel贡献的模块，采用WTFPL许可发布。



### 大内存应用

Node 提供了 **stream** 模块用于处理大文件。

fs 模块的`createReadStream()` 和 `createWriteStream()` 方法可以分别用于创建文件的可读流与可写流。

process 模块中的 stdin 和 stdout 分别是可读流和可写流的示例。

```javascript
const fs = require('fs');

// 读取一个文件，然后将数据写入到另一个文件中
// 流处理的方式不会受到V8内存限制的影响。
var reader = fs.createReadStream('in.txt');
var writer = fs.createWriteStream('out.txt');
reader.on('data', function (chunk) {
    writer.write(chunk);
});
reader.on('end', function () {
    writer.end();
});

// 上述代码的简写方法：
var reader = fs.createReadStream('in.txt');
var writer = fs.createWriteStream('out.txt');
reader.pipe(writer);
```

>  💡
>
> 如果不需要进行字符串层面的操作，则不需要借助 V8 来处理，可以尝试进行纯粹的 ==Buffer== 操作，这不会受到 V8 堆内存的限制。

