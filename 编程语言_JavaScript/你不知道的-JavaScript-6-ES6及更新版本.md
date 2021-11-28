# transpiling

transpiling（transformation + compiling，转换+编译）：把 ES6 代码转化为等价（或近似）的可以在 ES5 环境下工作的代码。

* Facebook: [Regenerator](https://facebook.github.io/regenerator/)
* Google: Traceur
* Babel


# shim/polyfill

shim/polyfill 模式：在可能的情况下，为新环境中的行为定义在旧环境中的等价行为。

[ES6 shim](https://github.com/paulmillr/es6-shim/) - 提供兼容性填充程序，以便旧版 JavaScript 引擎尽可能地与 ECMAScript 6（Harmony）保持一致。
