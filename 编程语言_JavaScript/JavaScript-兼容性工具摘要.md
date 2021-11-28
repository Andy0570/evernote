# transpiling

transpiling（transformation + compiling，转换+编译）：把 ES6 代码转化为等价（或近似）的可以在 ES5 环境下工作的代码。

* Facebook: [Regenerator](https://facebook.github.io/regenerator/)
* Google: Traceur
* Babel

## Traceur

Traceur 是 Google 维护的一个项目，该项目用来将 ES6 代码转换成兼容 ES6 之前的环境（大部分是 ES5，但不是全部）。TC39 委员会依赖这个工具（也有其他工具）来测试他们指定的语义化相关的功能。

如果你在 ES6 环境下写了这么一段代码：
```javascript
{
  let a = 2;
  console.log(a); // 2
}

console.log(a); // ReferenceError
```

Traceur 会将我们的代码片段转换成如下这样：

```javascript
{
  try {
    throw undefined;
  } catch (a) {
    a = 2;
    console.log(a);
  }
}

console.log(a);
```

通过使用这样的工具，我们就可以在使用块作用域时无需考虑目标平台是否是 ES6 环境，因为 `try/catch` 从 ES3 开始就存在了（并且一直是这样工作的）。


# shim/polyfill

shim/polyfill 模式：在可能的情况下，为新环境中的行为定义在旧环境中的等价行为。

[ES6 shim](https://github.com/paulmillr/es6-shim/) - 提供兼容性填充程序，以便旧版 JavaScript 引擎尽可能地与 ECMAScript 6（Harmony）保持一致。
