# Node 的特点：事件驱动、非阻塞I/O

1. 异步I/O；
2. 事件（轻量级、松耦合、只关注事务点）与回调函数；
3. 单线程：child_process 子进程；
4. 跨平台：libuv；

# Node 的应用场景

* I/O密集型；
* CPU密集型？（充分利用CPU的方法：编写 C/C++ 扩展高效利用CPU、使用子进程）；



# 异步I/O

单线程同步编程模型会因阻塞I/O导致硬件资源得不到更优的使用。

多线程编程模型也因为编程中的死锁、状态同步等问题让开发人员头疼。

Node 在两者之间给出了它的方案：利用单线程，远离多线程死锁、状态同步等问题；利用异步I/O，让单线程远离阻塞，以更好地使用CPU。



## 轮询

* 轮询：重复调用判断操作是否完成。
* 轮询技术：read、select、poll、epoll、kqeue。
* 轮询技术满足了非阻塞I/O确保获取完整数据的需求。



## Node 的异步I/O

![image](http://upload-images.jianshu.io/upload_images/2648731-68deabb35c97cf87.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Node异步I/O模型的基本要素：事件循环、观察者、请求对象、I/O线程池。

异步实现的核心是**事件循环**。

事件驱动的本质：通过主循环加事件触发的方式来运行程序。



💡💡💡

* 在 Node 中，除了 JavaScript 是单线程外，Node自身其实是多线程的，只是 I/O 线程使用的 CPU 较少。
* 除了用户代码无法并行执行外，所有的 I/O（磁盘I/O和网络I/O等）则可以是并行起来的。



## 非I/O的异步API

* `setTimeout()`，单次执行定时任务。
* `setInterval()`，重复执行定时任务。
* `setImmediate()`，将回调函数延迟执行。
* `process.nextTick()`，将回调函数延迟执行。





# 异步编程

异步编程在流程控制中，业务表达并不太适合自然语言的线性思维习惯。



### 高阶函数

```javascript
// 通常的语言
// 函数的参数：基本数据类型、对象引用
// 函数的返回值：基本数据类型、对象引用
function functionName(params) {
    return params;
}

// 高阶函数：可以把函数作为参数或返回值
function functionName(params) {
    // 返回一个函数
    return function () {
        return params;
    }
}

// 后续传递风格
// 将业务的重点从返回值转移到了回调函数中
function functionName(x, bar) {
    return bar(x);
}
```

### 偏函数用法

```javascript
// 类型判断
var toString = Object.prototype.toString;

var isString = function (obj) {
    return toString.call(obj) == '[object String]';
}
var isFunction = function (obj) {
    return toString.call(obj) == '[object Function]';
}

// 偏函数用法
// 创建一个调用另外一个部分——参数或变量已经预置的函数——的函数
var isType = function (type) {
    return function (obj) {
        // 通过指定部分参数来产生一个新的定制函数
        return toString.call(obj) == '[object ' + type + ']';
    };
};

var isString = isType('String');
var isFunction = isType('Function');
```



### 难点1：异常处理

Node在处理异常时的约定：**将异常作为回调函数的第一个实参传回，如果为空值，则表明异步调用没有抛出异常。**

```javascript
async(function (err, results) {
    // TODO
});
```

编写异步方法需要遵循的原则：

1. 必须执行调用者传入的回调函数；
2. 正确传递回异常供调用者判断；

```javascript
var async = function (callback) {
    process.nextTick(function() {
        var results = something;
        if (error) {
            return callback(error); // 2. 正确传递回异常供调用者判断
        }
        callback(null, results); // 1. 必须执行调用者传入的回调函数
    });
};
```



### 难点2：函数嵌套过深

异步编程中，函数嵌套过深的问题是：没有充分利用异步I/O带来的并行优势。

```javascript
// 在Node中，事件中存在多个异步调用的场景，就会产生嵌套
// 遍历一个目录
fs.readdir(path.join(__dirname, '..'), function (err, files) {
    files.forEach(function (filename, index) {
        fs.readFile(filename, 'utf8', function (err, file) {
            // TODO
        });
    });
});
```



### 难点3：阻塞代码

遇到需要阻塞代码的需求时，在统一规划业务逻辑之后，调用 `setTimeout()` 的效果会更好。



### 难点4：多线程编程

对于服务器来说，如果服务器是多核CPU，单个Node进程实质上是没有充分利用多核CPU的。

Node的多线程实现：child_process 是其基础API，[cluster](http://nodejs.cn/api/cluster.html) 模块是更深层次的应用。



### 难点5：异步转同步



## 异步编程解决方案

* 事件发布/订阅模式；
* Promise/Deferred模式（Q、when）；
* 流程控制模式；
  * 尾触发与Next（Connetc）；
  * [async](https://github.com/caolan/async)；
  * Step；
  * wind；



### 事件发布/订阅模式

* 事件监听模式（events 模块）：实现一个事件与多个回调函数的关联。
* 广泛用于异步编程。
* 常用于解耦业务逻辑。

```javascript
const EventEmitter = require('events');

class MyEmitter extends EventEmitter {}
const myEmitter = new MyEmitter();
// on() 注册监听器
myEmitter.on('event', () => {
  console.log('触发事件');
});
myEmitter.emit('event'); // emit() 触发事件
```



1. 继承events模块。

   ```javascript
   // Node 中 Stream 对象继承 EventEmitter 的示例：
   var events = require('events');
   
   function Stream() {
       event.EventEmitter.call(this);
   }
   Uint8ClampedArray.inherits(Stream, events.EventEmitter);
   ```

2. 利用事件队列解决雪崩问题（高访问量、大并发的情况下缓存失效的情景）:

   ```javascript
   // 添加状态锁
   var proxy = new events.EventEmitter();
   var status = 'ready';
   var select = function (callback) {
       // once()，执行一次就会将监视器移除，保证每一个回调只会被执行一次。
       proxy.once('selected', callback);
       if (status === 'ready') {
           status = 'pending';
           db.select('SQL', function (results) {
               proxy.emit('selected', results);
               status = 'ready';
           });
       }
   };
   ```

3. 多异步之间的协作方案

   ```javascript
   // 一般而言，事件与侦听器的关系是一对多，
   // 但在异步编程中，也会出现事件与侦听器的关系是多对一的情况：
   var after = function (times, callback) {
       var count = 0, results = {};
       return function (key, value) {
           results[key] = value;
           count ++;
           // 哨兵变量：count 是用于检测次数的变量
           if (count === times) {
               callback(results);
           }
       };
   };
   
   var done = after(times, render);
   ```

   * 第三方模块：EventProxy。


### Promise/Deferred 模式

**先执行异步调用，延迟传递处理。**

#### Promises/A

![image](http://upload-images.jianshu.io/upload_images/2648731-edb21d67064e6d1d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* Promise 操作的3种状态：未完成态、完成态和失败态。
* Promise 的状态只会出现从未完成态向完成态或失败态转化，不能逆反。完成态和失败态不能互相转化。
* Promise 的状态一旦转化，将不能更改。
*  [A promise library for javascript：q](https://github.com/kriskowal/q)

示例代码：

```javascript
var Promise = function () {
    EventEmitter.call(this);
};
util.inherits(Promise, EventEmitter);

// then() 方法将回调函数存放起来
Promise.prototype.then = function (fulfilledHander, errorHandler, progressHandler) {
    if (typeof fulfilledHander === 'function') {
        // 利用 once() 方法，保证成功回调只执行一次
        this.once('success', fulfilledHander);
    }
    if (typeof errorHandler === 'function') {
        // 利用 once() 方法，保证异常回调只执行一次
        this.once('error', errorHandler);
    }
    if (typeof progressHandler === 'function') {
        this.once('progress', progressHandler);
    }
    return this;
};

// Deferred，延迟对象，触发执行回调
var Deferred = function() {
    this.state = 'unfulfilled';
    this.promise = new Promise();
};
Deferred.prototype.resolve = function (obj) {
    this.state = 'fulfilled';
    this.promise.emit('success', obj);
};
Deferred.prototype.reject = function (obj) {
    this.state = 'failed';
    this.promise.emit('error', obj);
};
Deferred.prototype.progress = function (obj) {
    this.promise.emit('progress', obj);
};
```



```javascript
// 对一个典型的响应对象进行封装
res.setEncoding('utf8');
res.on('data', function (chunk) {
    console.log('BODY: ' + chunk);
})
res.on('end', function () {
    // Done
})
res.on('error', function (error) {
    // Error
})

// 通过 Promises/A提议的模式可以转换为如下的简略形式：
res.then(function () {
    // Done
}, function (error) {
    // Error
}, function (chunk) {
    console.log('BODY: ' + chunk);
})
```

以上简写的改造方法：

```javascript
// 封装：
var promisify = function (res) {
    // *业务中不可变的部分封装在 Deferred 中。
    var deferred = new Deferred();
    var result = '';
    res.on('data', function (chunk) {
        result += chunk;
        deferred.progress(chunk);
    });
    res.on('end', function () {
        deferred.resolve(result);
    });
    res.on('error', function (error) {
        deferred.reject(error);
    });
    return deferred.promise; // 返回 deferred.promise，不让外部程序直接调用 reslove()、reject() 方法。
}

// 调用示例：
// *业务中可变的部分封装在 Promise 中。
promisify(res).then(function () {
    // Done
}, function (error) {
    // Error
}, function (chunk) {
    // progress
    console.log('BODY: ' + chunk);
})
```

* Deferred 主要用于内部，用于维护异步模型的状态。
* Promise 主要作用于外部，通过 `then()` 方法暴露给外部以添加自定义逻辑.

![Promise 和 Deferred 整体关系示意图](http://upload-images.jianshu.io/upload_images/2648731-e9dd3cca8f9a3516.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### Promise 中的多异步协作

## 流程控制库

### 尾触发与Next

尾触发：需要手工调用才能持续执行后续调用的方法。

尾触发目前应用最多的地方是 [Connect](https://www.npmjs.com/package/connect) 的中间件。

在 [Connect](https://www.npmjs.com/package/connect) 中，尾触发十分适合处理网络请求的场景。将复杂的处理逻辑拆解为简洁、单一的处理单元，逐次地处理请求对象和响应对象。



### [async](https://www.npmjs.com/package/async)

##### 异步的串行执行

```javascript
// 函数作用：读取2个文件
// async 提供了 series() 方法来实现一组任务的串行执行
// 每个callback() 执行时会将结果保存起来，然后执行下一个调用，直到结束所有调用。
async.series([
    function (callback) {
        fs.readFile('file1.txt', 'utf-8', callback);
    },
    function (callback) {
        fs.readFile('file2.txt', 'utf-8', callback);
    }
], function (err, results) {
    // results => [file1.txt, file2.txt]
})

// 以上代码等价于
fs.readFile('file1.txt', 'utf-8', function (err, content) {
    if (err) {
        return callback(err);
    }
    fs.readFile('file2.txt', 'utf-8', function (err, data) {
        if (err) {
            return callback(err);
        }
        callback(null, [content, data]);
    });
});
```



##### 异步的并行执行

`parallel()` 方法对于异常的判断依然是一旦某个异步调用产生了异常，就会将异常作为第一个参数传入给最终的回调函数。只有所有异步调用都正常完成时，才会将结果以数组的方式传入。

```javascript
// parallel() 方法，并行执行异步操作
// 读取两个文件的并行版本
async.parallel([
    function(callback) {
        fs.readFile('file1.txt', 'utf-8', callback);
    },
    function(callback) {
        fs.readFile('file2.txt', 'utf-8', callback);
    }
], function (err, results) {
    // results => [file1.txt, file2.txt]
});

// 以上代码等价于下面的代码
var counter = 2;
var results = [];
var done = function (index, value) {
    results[index] = value;
    counter--;
    if (counter === 0) {
        callback(null, results);
    }
};

// 只传递第一个异常
var hasErr = false;
var fail = function (err) {
    if (!hasErr) {
        hasErr = true;
        callback(err);
    }
};

fs.readFile('file1.txt', 'utf-8', function (err, content) {
    if (err) {
        return fail(err);
    }
    done(0, content);
});

fs.readFile('file2.txt', 'utf-8', function (err, data) {
    if (err) {
        return fail(err);
    }
    done(1, data);
});
```



##### 异步调用的依赖处理

`series()` 适合**无依赖的异步串行执行**，但当前一个结果是后一个调用的输入时，``series()`` 方法就无法满足需求了。async 提供了 `waterfall()`  方法来满足：

```javascript
async.waterfall([
    function (callback) {
        fs.readFile('file1.txt', 'utf-8', function (err, content) {
            callback(err, content);
        });
    },
    function (arg1, callback) {
        // arg1 => file2.txt
        fs.readFile(arg1, 'utf-8', function (err, content) {
            callback(err, content);
        });
    },
    function (arg1, callback) {
        // arg1 => file3.txt
        fs.readFile(arg1, 'utf-8', function (err, content) {
            callback(err, content);
        });
    }
], function (err, result) {
    // result => file4.txt
});

// 以上代码等价于
fs.readFile('file1.txt', 'utf-8', function (err, data1) {
    if (err) {
        return callback(err);
    }
    // data1作为下个函数的入参
    fs.readFile(data1, 'utf-8', function (err, data2) {
        if (err) {
            return callback(err);
        }
        // data2 作为下个函数的入参
        fs.readFile(data2, 'utf-8', function (err, data3) {
            if (err) {
                return callback(err);
            }
            callback(null, data3);
        });
    });
});
```



##### 自动依赖处理

`auto()` 实现复杂的业务处理，它可以根据依赖关系自动分析，以最佳的顺序执行业务流。



## 异步并发控制

* [bagpipe](https://github.com/JacksonTian/bagpipe) 的解决方案；
* async 的解决方案：`parallelLimit()` 方法；
* [wind](https://github.com/JeffreyZhao/wind)



```javascript
// 增加限制并发数量的参数，使得任务只能同时并发一定数量，而不是无限制并发。
async.parallelLimit([
    function (callback) {
        fs.readFile('file1.txt', 'utf-8', callback);
    },
    function (callback) {
        fs.readFile('file2.txt', 'utf-8', callback);
    }
], 1, function (err, results) {
    // TODO
});

// queue() 方法，解决 parallelLiment() 无法动态地增加并行任务。
var q = async.queue(function (file, callback) {
    fs.readFile(file, 'utf-8', callback);
}, 2);
q.drain = function () {
    // 完成了队列中的所有任务
};
fs.readdirSync('.').forEach(function (file) {
    q.push(file, function (err, data) {
        // TODO
    });
});
```


