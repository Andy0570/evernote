## 异步编程的背景知识

JavaScript 引擎是基于*单线程（Single-threaded）事件循环*的概念构建的。

JavaScript 引擎同一时刻只能执行一个代码块，每当一段代码准备执行时，都会被添加到一个*任务队列*（job queue）中，以跟踪即将运行的代码。每当 JavaScript 引擎中的一段代码结束执行，*事件循环*（event loop）会执行队列中的下一个任务，它是 JavaScript 引擎中的一段程序，负责监控代码执行并管理任务队列。请记住，队列中的任务会从第一个一直执行到最后一个。

### 事件模型

事件模型会向任务队列添加一个新任务来响应用户的操作，当指定的事件被触发时，执行事件处理程序。

```js
// 将 onclick() 函数添加到任务队列中，单击 button 后，就会执行相应的代码！
let button = document.getElementById('my-btn');
button.onclick = function (event) {
  console.log('Clicked');
};
```

必须要保证事件在添加事件处理程序之后才被触发。（即，必须先将事件添加到任务队列中，才能执行该事件）。


### 回调模式

回调模式与事件模型类似，异步代码会在未来的某个时间点被执行，二者的区别是：**回调模式中被调用的函数是作为参数传入的**。

相比之下，回调模式比事件模型更灵活，因为它可以嵌套多个回调函数，但也同时会产生回调地狱问题。

Node.js 中的*错误优先*（error-first）风格示例代码：

```js
readFile('example.txt', function (err, contents) {
  if (err) {
    throw err;
  }
  console.log(contents);
});

console.log('Hi!');
```

## Promise 的基础知识

Promise 相当于异步操作结果的占位符，它会让函数返回一个 `Promise`。


### Promise 的生命周期

每个 `Promise` 都会经历一个短暂的生命周期：先是处于*进行中*（pending）的状态，此时操作尚未完成，所以它也是*未处理*（unsettled）的；一旦异步操作执行结束， `Promise` 则变为*已处理*（ settled）的状态。操作结束后， Promise 可能会进入到以下两个状态中的其中一个：

* **Fulfilled**：`Promise` 异步操作成功完成。
* **Rejected**：由于程序错误或一些其他原因， `Promise` 异步操作未能成功完成。

内部属性 `[[Promisestate]]` 被用来表示 `Promise` 的 3 种状态:＂**pending**＂、＂**fulfilled**＂及＂**rejected**＂。这个属性不暴露在 `Promise` 对象上，所以不能以编程的方式检测 `Promise` 的状态，只有当 `Promise` 的状态改变时，通过 `then()` 方法来采取特定的行动。

所有 `Promise` 都有 `then()` 方法，它接受两个**可选参数**：

* 参数一：当 `Promise` 的状态变为 **fulfilled** 时要调用的函数，与异步操作相关的附加数据都会传递给这个完成函数（fulfilled function）;
* 参数二：当 `Promise` 的状态变为 **rejected** 时要调用的函数，其与完成时调用的函数类似，所有与失败状态相关的附加数据都会传递给这个拒绝函数（rejected function）。

`then()` 的两个参数都是可选的，所以你可以按照任意组合的方式来监听 `Promise`，执行完成或被拒绝都会被响应。

```js
let promise = readFile('example.txt');

// 组合方式一：同时监听【执行完成】和【执行拒绝】
promise.then(function (contents) {
  // 完成
  console.log(contents);
}, function (err) {
  // 拒绝
  console.error(err.message);
});

// 组合方式二：只监听【执行完成】
promise.then(function (contents) {
  // 完成
  console.log(contents);
});

// 组合方式三：只监听【执行拒绝】
promise.then(null, function (err) {
  // 拒绝
  console.error(err.message);
});
```

`Promise` 的 `catch()` 方法相当于只给其传入拒绝处理程序的 `then()` 方法。

```js
promise.catch(function (err) {
  console.error(err.message);
});

// 与以下调用相同
promise.then(null, function(err) {
  // 拒绝
  console.error(err.message);
});
```

### 创建未完成的 Promise

用 `Promise` 构造函数可以创建新的 `Promise`，构造函数只接受一个参数：包含初始化 `Promise` 代码的执行器（executor）函数。执行器接受两个参数，分别是 `resolve()` 函数和 `reject()` 函数。执行器成功完成时调用 `resolve()` 函数，反之，失败时则调用 `reject()` 函数。

```js
let fs = require('fs');

function readFile(filename) {
  return new Promise(function(resolve, reject) {
    // 触发异步操作
    fs.readFile(filename, { encoding: 'utf8' }, function(err, contents) {
      // 检查是否有错误
      if (err) {
        reject(err);
        return;
      }

      // 成功读取文件
      resolve(contents);
    });
  });
}

let promise = readFile('example.txt');

// 同时监听【执行完成】和【执行拒绝】
promise.then(
  function(contents) {
    // 完成
    console.log(contents);
  },
  function(err) {
    // 拒绝
    console.error(err.message);
  }
);
```

`readFile()` 方法被调用时执行器会立刻执行，在执行器中，无论是调用 `resolve()` 函数和 `reject()` 函数，都会向任务队列中添加一个任务来解决这个 `Promise`。


```js
let promise = new Promise(function (resolve, reject) {
  console.log('Promise'); // 1
  resolve();
  // 调用 resolve() 方法后会触发一个异步操作，
  // 传入 then() 和 catch() 方法的函数会被添加到任务队列中并异步执行。
})

// 3
promise.then((result) => {
  console.log(result);
}).catch((err) => {
  console.error(err.message);
});

console.log('Hi'); // 2

/*
输出内容：

Promise
Hi
result 或者 err.message
*/
```

### 创建已处理的 Promise

如何用 `Promise` 来表示一个已知值？？？

使用 `Promise.resolve()` 和 `Promise.reject()` 方法来根据特定的值来创建已解决 `Promise`。

#### 使用 `Promise.resolve()`

`Promise.resolve()` 方法只接受一个参数并返回一个完成态的 `Promise`，也就是说不会有任务编排的过程，而且需要向 `Promise` 添加一至多个完成处理程序来获取值。

```js
let promise = Promise.resolve(42);

promise.then(function (value) {
  console.log(value); // 42
});
```

这段代码创建了一个已完成 `Promise`，完成处理程序的形参 value 接受了传入值 42，由于该 `Promise` 永远不会存在拒绝状态，因而该 `Promise` 的拒绝处理程序永远不会被调用。

#### 使用 `Promise.reject()`

也可以通过 `Promise.reject()` 方法来创建已拒绝 `Promise`，它与 `Promise.resolve()` 很像，唯一的区别是：创建出来的是拒绝状态的 `Promise`。

```js
let promise = Promise.reject(42);

promise.catch(function (value) {
  console.log(value); // 42
})
```

任何附加到这个 `Promise` 的拒绝处理程序都将被调用，但却不会调用完成处理程序。

#### 非 Promise 的 Thenable 对象

`Promise.resolve()` 方法和 `Promise.reject()` 方法都可以接受「非 Promise 的 Thenable 对象」作为参数。如果传入一个「非 Promise 的 Thenable 对象」，则这些方法会创建一个新的 Promise，并在 `then()` 函数中被调用。
【拥有 `then()` 方法并且接受 `resolve` 和 `reject` 这两个参数的普通对象】就是「非 Promise 的 Thenable 对象」，例如：

```js
let thenable = {
  then: function (resolve, reject) {
    resolve(42);
  }
};
```

在此示例中，thenable 对象和 Promise 对象之间只有 `then()` 方法这一个相似之处，可以调用 `Promise.resolve()` 方法将 thenable 对象转换成一个已完成 Promise：

```js
let thenable = {
  then: function(resolve, reject) {
    resolve(42);
  }
};

// Promise.resolve() 调用的是 thenable.then()，所以 Promise 的状态可以被检测到。
let p1 = Promise.resolve(thenable);
p1.then(function (value) {
  console.log(value); // 42
})
```

有了 `Promise.resolve()` 方法和 `Promise.reject()` 方法，我们可以更轻松地处理「非 Promise 的 Thenable 对象」。

在 ECMAScript 6 引入 `Promise` 对象之前，许多库都使用了 Thenable 对象，所以如果要向后兼容之前已有的库，则将 Thenable 对象转换为正式 `Promise` 的能力就显得至关重要了。

**如果不确定某个对象是不是 `Promise` 对象，那么可以根据预期的结果将其传入 `Promise.resolve()` 方法中或 `Promise.reject()` 方法中，如果它是 `Promise` 对象，则不会有任何变化。**


### 执行器错误

如果执行器内部抛出了一个错误，则 `Promise` 的拒绝处理程序就会被调用。

每个执行器内部都隐含一个 `try-catch` 块，所以错误会被捕获并传入拒绝处理程序。

```js
let promise = new Promise(function(resolve, reject) {
  throw new Error('Explosion!')
});

promise
  .then(result => {
    console.log(result);
  })
  .catch(err => {
    console.log(err.message); // Explosion!
  });
```

默认情况下，执行器会捕获所有抛出的错误，但只有当错误处理程序存在时才会记录执行器中抛出的错误，否则错误会被忽略掉。

JavaScript 环境提供了一些捕获已拒绝 `Promise` 的钩子函数来解决这个问题！

## 全局的 Promise 拒绝处理

`Promise` 的特性决定了很难检测一个 `Promise` 是否被处理过：

```js
let reject = Promise.reject(42);

// 此时，reject 还没有被处理

// 过了一会儿...
reject.catch(function (value) {
  // 现在，reject 已经被处理了
  console.log(value);
})
```

任何时候都可以调用 `then()` 方法或 `catch()` 方法，无论 `Promise` 是否已解决这两个方法都可以正常运行，但这样就很难知道一个 `Promise` 何时被处理。在此示例中， `Promise` 被立即拒绝，但是稍后才被处理。

尽管这个问题在未来版本的 ECMAScript 中可能会被解决，但是 Node.js 和浏览器环境都已分别做出了一些改变来解决开发者的这个痛点，这些改变不是 ECMAScript 6 标准的一部分，不过当你使用 `Promise` 的时候它们确实是非常有价值的工具。

### Node.js 环境的拒绝处理

在 Node.js 中，处理 `Promise` 拒绝时会触发 **process** 对象上的两个事件：

* **unhandledRejection**：在一个事件循环中，当 `Promise` 被拒绝，并且没有提供拒绝处理程序时，触发该事件。
* **rejectionHandled**：在一个事件循环后，当 `Promise` 被拒绝时，若拒绝处理程序被调用，触发该事件。

设计这些事件是用来识别那些**被拒绝却又没有被处理过的 `Promise`** 的。

拒绝原因（通常是一个错误对象）及被拒绝的 `Promise` 作为参数被传入到 **unhandledRejection** 事件中。

```js
let reject;

process.on('unhandledRejection', function (reason, promise) {
  console.log(reason.message); // Explosion!
  console.log(rejected === promise); // true
});

reject = Promise.reject(new Error('Explosion!'))
```

**rejectionHandled** 事件处理程序只有一个参数，即被拒绝的 `Promise`。

```js
let reject;

// 当  reject.catch() 处理过后才会监听到 rejectionHandled 事件并被触发
process.on('rejectionHandled', function(promise) {
  console.log(rejected === promise); // true
});

reject = Promise.reject(new Error('Explosion!'));

// 等待添加拒绝处理程序
setTimeout(function () {
  reject.catch(function (value) {
    console.log(value); // Explosin!
  })
}, 1000)
```

通过事件 **rejectionHandled** 和事件 **unhandledRejection** 将潜在未处理的拒绝存储为一个列表，等待一段时间后检查列表便能够正确地跟踪潜在的未处理拒绝。例如下面这个简单的未处理拒绝跟踪器：

```js
let possiblyUnhandledRejections = new Map();

// 如果一个拒绝没有被处理，则将它添加到 Map 集合中
process.on('unhandledRejection', function(reason, promise) {
  possiblyUnhandledRejections.set(promise, reason);
});

// 已处理的 Promise 会从列表中划掉！
process.on('rejectionHandled', function(promise) {
  possiblyUnhandledRejections.delete(promise);
});

setInterval(function () {
  possiblyUnhandledRejections.forEach(function (reason, promise) {
    console.log(reason.message ? reason.message: reason);

    // 处理没有被及时处理的 Promise
    handleRejection(promise, reason);
  });
  
  // 所有拒绝被处理后，清空列表！
  possiblyUnhandledRejections.clear();
}, 60000);
```


### 浏览器环境的拒绝处理

浏览器也是通过触发两个事件来识别未处理的拒绝的，只不过这些事件是在 `window` 对象上被触发的。

* **unhandledrejection** 事件，在一个事件循环中，当 `Promise` 被拒绝，并且没有提供拒绝处理程序时，触发该事件。
* **rejectionhandled** 事件，在一个事件循环后，当 `Promise` 被拒绝时，若拒绝处理程序被调用，触发该事件。

...


### 串联 Promise

每次调用 `then()` 方法或 `catch()` 方法时，实际上创建并返回了另一个 `Promise`，只有当第一个 `Promise` 完成或被拒绝后，第二个才会被解决。

```js
let p1 = new Promise(function (resolve, reject) {
  resolve(42);
});

p1.then(function (value) {
  console.log(value);
}).then(function () {
  console.log("Finish!");
})
```
这段代码输出以下内容：

```text
42
finish!
```

### 捕获错误

「完成处理程序」或「拒绝处理程序」中可能会发生错误，而 `Promise` 链可以用来捕获这些错误：

```js
let promise = new Promise(function(resolve, reject) {
  resolve(42);
});

promise.then((result) => {
  throw new Error('Boom!')
}).catch((err) => {
  console.log(err.message); // Boom!
});
```

### Promise 链的返回值

Promise 链的另一个重要特性是：**可以给下游 Promise 传递数据**。

```js
let promise = new Promise(function(resolve, reject) {
  resolve(42);
});

promise
  .then(result => {
    console.log(result); // 42
    return result + 1;
  })
  .then(result => {
    console.log(result); // 43
  });
```

### 在 Promise 链中返回 Promise

...

## 响应多个 Promise

如果想要监听多个 Promise 来决定下一步的操作，则可以使用 `Promise.all()` 和 `Promise.race()` 方法。

### `Promise.all()` 方法

`Promise.al1()` 方法只接受一个参数并返回一个 `Promise`，该参数是一个含有多个受监视 `Promise` 的可迭代对象（例如，一个数组），只有当可迭代对象中所有 `Promise` 都被解决后返回的 `Promise` 才会被解决，只有当可迭代对象中所有 `Promise` 都被完成后返回的 `Promise` 才会被完成。

所有传入 `Promise.al1()` 方法的 `Promise` 只要有一个被拒绝，那么返回的 `Promise` 没等所有 `Promise` 都完成就立即被拒绝。

```js
let p1 = new Promise(function(resolve, reject) {
  resolve(42);
});

let p2 = new Promise(function(resolve, reject) {
  resolve(43);
});

let p3 = new Promise(function(resolve, reject) {
  resolve(44);
});

// 只有当 p1、p2、p3  都处于完成状态后 p4 才被完成。
let p4 = Promise.all([p1, p2, p3]);

p4.then(function (value) {
  console.log(Array.isArray(value)); // true
  console.log(value[0]); // 42
  console.log(value[1]); // 43
  console.log(value[2]); // 44
});
```

### `Promise.race()` 方法

`Promise.race()` 方法监听多个 Promise 的方法稍有不同：它也接受含多个受监视 `Promise` 的可迭代对象作为唯一参数并返回一个 `Promise`，但**只要有一个 `Promise` 被解决返回的 `Promise` 就被解决**，无须等到所有 `Promise` 都被完成。一旦数组中的某个 `Promise` 被完成，`Promise.race()` 方法也会像 `Promise.all()` 方法一样返回一个特定的 `Promise`:

```js
let p1 = Promise.resolve(42);

let p2 = new Promise(function(resolve, reject) {
  resolve(43);
});

let p3 = new Promise(function(resolve, reject) {
  resolve(44);
});

let p4 = Promise.race([p1, p2, p3]);

p4.then(function (value) {
  console.log(value); // 42
});
```

实际上，传递给 `Promise.race()` 方法的 `Promise` 会进行竞选，哪个先完成就先返回哪个，不管这个 `Promise` 返回的是已完成的还是已拒绝的。


### 自 Promise 继承

`Promise` 与其他内建类型一样，也可以作为基类派生其他类，所以你可以定义自己的 `Promise` 变量来扩展内建 `Promise` 的功能。

...



### 基于 Promise 的异步任务执行

将生成器与 `Promise` 结合。







