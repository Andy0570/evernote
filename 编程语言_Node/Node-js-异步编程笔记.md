# 从 Callback Hell 到 Promise 到 Async/Await

### [【知乎 Live】狼叔：如何正确的学习 Node.js](https://i5ting.github.io/How-to-learn-node-correctly/)

Node.js 异步流程控制的学习重点：

1. API 写法：Error-first Callback 和 EventEmitter；
2. 中流砥柱：[Promise](https://github.com/then/promise#readme)；
3. 终极解决方案：Async/Await

#### 回调地狱（Callback Hell ）

Node.js 默认使用回调函数（callback）风格语法来实现异步编程：

```javascript
// Node.js 默认的回调函数风格写法，Callback Hell
step1(function(value1) {
  step2(value1, function(value2) {
    step3(value2, function(value3) {
      step4(value3, function(value4) {
        // Do something with value 4.
      });
    });
  });
});

// ES6 箭头函数语法：
step1(value1 => {
  step2(value1, value2 => {
    step3(value2, value3 => {
      step4(value3, value4 => {
        // Do something with value4.
      });
    });
  });
});
```

#### Promise 语法

```javascript
return step1().then(step2).then(step3).then(step4).catch(function(err) {
  // do something with error.
});
```



### [InfoQ：异步 JavaScript 的演化史：从回调到 Promise 再到 Async/Await](https://www.infoq.cn/article/JQxzWt7FqDc9p7jJ_1hs) ⭐️⭐️⭐️⭐️



#### 什么是回调函数（callback）？

> 首先，就像可以将字符串或数字以参数的形式传递给函数一样，我们也可以将函数的引用作为参数进行传递。但我们这样做的时候，作为参数传递的函数被称为回调函数（callback function），而接收回调函数传入的那个函数则被称为高阶函数（higher order function）。

```javascript
function add(x, y) {
  return x + y;
}

function higherOrderFunction(x, callback) {
  return callback(x, 5);
}

higherOrderFunction(10, add);
```

回调的使用场景：将函数的执行延迟至一个特定的时间。



#### Promise

`Promise`会处于如下三种状态中的某一种： `pending`、`fulfilled`或`rejected`。

如果异步请求依然还在进行，那么`Promise`的状态会是`pending`。如果异步请求成功完成的话，那么`Promise`会将状态转换为`fulfilled`。如果异步请求失败的话，`Promise`会将状态转换为`rejected`。

---

`Promise`的构造函数会接收一个参数，这个参数是一个（回调）函数。该函数会被传入两个参数`resolve`和`reject`。

`resolve`：一个能将 Promise 状态变为`fulfilled`的函数；

`reject`：一个能将 Promise 状态变为`rejected`的函数；

```javascript
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(); // 将状态变为"fulfilled"
  }, 2000);
});
```

当 Promise 的状态变为`fulfilled`的时候，传递给`.then`的函数将会被调用。如果 Promise 的状态变为`rejected`，传递给`.catch`的函数将会被调用。

```javascript
function onSuccess() {
  console.log('Success');
}

function onError() {
  console.log('Error');
}

const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(); // 将状态变为"fulfilled"
  }, 2000);
});

promise.then(onSuccess);
promise.catch(onError);
```

Promise 的链式语法。



#### Async/Await

如何完全按照编写同步代码的方式来编写异步代码？

1. 首先，只要你为函数添加`async`，它就会隐式的返回一个 Promise 对象：

```javascript
async function getPromise() {}

const promise = getPromise();
```

2. 其次，如果`async`函数有返回值的话，它也将会包装到一个 Promise 中。这意味着，你必须要使用`.then`来访问它。

```javascript
async function add(x, y) {
  return x + y;
}

add(2, 3).then(result => {
  console.log(result); // 5
});

```

3. 不能将 `await` 用到非 `async` 的函数中。

   当你将 `await` 添加到一个函数上的时候：首先，它会让这个函数本身返回一个 Promise（或者将返回的内容包装到 Promise 中）；其次，它会确保你能够在这个函数中使用`await`。




### [Node.js 异步最佳实践 & 避免回调地狱 | @RisingStack](https://juejin.im/entry/589401b68fd9c57277376470)

>  要成为一个高效的 Node.js 开发者，你必须避免不断增加的缩进级别，书写简洁且易读的代码，以及处理复杂的流程。
>
> 尽可能使用异步 API 而不是同步 API。因为非阻塞的方式比同步的方式性能更好。



### [InfoQ：JavaScript 异步编程的 Promise 模式](https://www.infoq.cn/article/2011%2F09%2Fjs-promise) 

关键字：2011 年 9 月 15 日、JavaScript、Ajax 示例。



## Promise

* [Node.js 最新技术栈之 Promise 篇](https://cnodejs.org/topic/560dbc826a1ed28204a1e7de) ⭐️⭐️⭐️
* [理解 Promise 的工作原理](https://cnodejs.org/topic/569c8226adf526da2aeb23fd) ⭐️
* [Promise 迷你书](http://liubin.github.io/promises-book/) ⭐️
* [promise模块：bluebird](https://github.com/petkaantonov/bluebird)
* [promise模块: q](https://github.com/kriskowal/q)



```javascript
// 回调地狱
asyncOperation(function(data1) {
  // 处理 data1
  anotherAsync(function(data2) {
    // 处理 data2
    yetAnotherAsync(function() {
      // 完成
    });
  });
});

// Promise 语法
promiseSomething()
  .then(function(data1) {
    // 处理 data1
    return anotherAsync();
  })
  .then(function(data2) {
    // 处理 data2
    return yetAnotherAsync();
  })
  .then(function() {
    // 完成
  });
```



### Promise/A 规范

promise 表示一个最终值，该值由一个操作完成时返回。

* promise 有3种状态：**未完成（Pending）**、**完成（Fulfilled）**、**失败（Rejected）**。
* promise 的状态转换：
  * 未完成 ➡️ 完成。
  * 未完成 ➡️ 失败。
* promise 的状态转换只发生一次。

promise 的 `then()` 方法可以接受3个函数作为参数：

```javascript
promiseSomething().then(function(fulfilled) {
// 当 promise 状态变成 fulfilled 时，调用此函数。
}, function(rejected) {
  // 当 promise 状态变成 rejected 时，调用此函数
}, function(progress) {
  //当返回进度信息时，调用此函数（可选）
});
```



## Async
* [async](https://github.com/caolan/async)
* [Document: async](https://caolan.github.io/async/index.html)



### [async 函数的含义和用法 @阮一峰](http://es6.ruanyifeng.com/#docs/async)

```javascript
const asyncMethod = async () => {  // async 用于声明一个函数是异步的。
  const f1 = await firstMethod();  // await 用于等待一个异步方法执行完成。
  const f2 = await anotherMethod();
  console.log(f1, f2);
}
```

> async 函数就是 Generator 函数的语法糖。
>
> async 函数就是将 Generator 函数的星号（`*`）替换成 async，将 yield 替换成 await，仅此而已。
>
> await 命令后面的 Promise 对象，运行结果可能是 rejected，所以最好把 await 命令放在 try...catch 代码块中。
>
> async 函数返回的是一个 Promise 对象。
>
> await 命令只能用在 async 函数之中，如果用在普通函数，就会报错。



### [理解 JavaScript 的 async/await](https://segmentfault.com/a/1190000007535316) ⭐️⭐️⭐️

```javascript
async function testAsync() {
  return 'Hello World';
}

const result = testAsync();
console.log(result); // Promise { 'Hello World' }
```

> async 函数（包含函数语句、函数表达式、Lambda 表达式）会返回一个 Promise 对象，如果在函数中 `return` 一个直接量，async 会把这个直接量通过 `Promise.resolve()` 封装成 Promise 对象。

```javascript
async function testAsync() {
  return 'Hello World';
}

testAsync().then(result => {
  console.log(result); // Hello World
});
```



#### 用 `setTimeout` 模拟耗时操作的示例

Promise 语法：

```javascript
function takeLongTime() {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve('long_time_value');
    }, 1000);
  });
}

takeLongTime().then(result => {
  console.log(result); // long_time_value
});
```

async/await 语法：

```javascript
function takeLongTime() {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve('long_time_value');
    }, 1000);
  });
}

async function test() {
  const result = await takeLongTime();
  console.log(result);
}

test();
```

#### async/await 的优势在于处理 then 链。

假设一个业务，分多个步骤完成，每个步骤都是异步的，而且依赖于上一个步骤的结果。我们仍然用 `setTimeout` 来模拟异步操作：

```javascript
// 返回值：time+200
function takeLongTime(time) {
  return new Promise(resolve => {
    setTimeout(() => resolve(time + 200), time);
  });
}

function step1(time) {
  console.log(`step1 with ${time}`);
  return takeLongTime(time);
}

function step2(time) {
  console.log(`step2 with ${time}`);
  return takeLongTime(time);
}

function step3(time) {
  console.log(`step3 with ${time}`);
  return takeLongTime(time);
}
```

promise 语法：

```javascript
function promiseMethod() {
  const time = 300;
  step1(time)
    .then(time2 => step2(time2))
    .then(time3 => step3(time3))
    .then(result => {
      console.log(`result is ${result}`);
    })
    .catch(err => {
      // 任何一个步骤有错误，程序跳转到这里执行。
      console.log(err);
    });
}

promiseMethod();
```

async/await 语法：

```javascript
async function asyncMethod() {
  const time1 = 300;
  const time2 = await step1(time1);
  const time3 = await step2(time2);
  const result = await step3(time3);
  console.log(`result is ${result}`);
}

asyncMethod();
```


