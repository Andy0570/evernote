[http-assert](https://github.com/jshttp/http-assert) 

assert with status codes.

## API

此模块的 API 被设计为和 Node.js 的 `assert` 模块相似。

当断言失败时，每个函数都会抛出一个 `http-errors` 模块下的 `HttpError` 实例。


### `assert(value, [status], [message], [properties])`

测试 `value` 值是否为真。如果 `value` 值为假，一个带有给定 `status`、 `message` 和 `properties` 属性的 `HttpError` 实例会被抛出。


### `assert.ok(value, [status], [message], [properties])`

测试 `value` 值是否为真。如果 `value` 值为假，一个带有给定 `status`、 `message` 和 `properties` 属性的 `HttpError` 实例会被抛出。


### `assert.deepEqual(a, b, [status], [message], [properties])`

测试 `a` 与 `b` 是否全等。原始值会通过相等操作符（ `==` ）进行比较。如果 `a` 与 `b` 不相等，一个带有给定 `status`、 `message` 和 `properties` 属性的 `HttpError` 实例会被抛出。


### `assert.notDeepEqual(a, b, [status], [message], [properties])`

测试 `a` 与 `b` 之间的全等性。原始值会通过相等操作符（ `==` ）进行比较。如果 `a` 与 `b` 相等，一个带有给定 `status`、 `message` 和 `properties` 属性的 `HttpError` 实例会被抛出。


### `assert.equal(a, b, [status], [message], [properties])`

使用相等操作符（ `==` ）测试 `a` 和 `b` 之间的浅的、强制性相等。如果 `a` 与 `b` 不相等，一个带有给定 `status`、 `message` 和 `properties` 属性的 `HttpError` 实例会被抛出。


### `assert.notEqual(a, b, [status], [message], [properties])`

使用相等操作符（ `==` ）测试 `a` 和 `b` 之间的浅的、强制性相等。如果 `a` 与 `b` 相等，一个带有给定 `status`、 `message` 和 `properties` 属性的 `HttpError` 实例会被抛出。


### `assert.strictEqual(a, b, [status], [message], [properties])`

使用全等操作符（ `===` ）测试 `a` 和 `b` 是否严格相等。如果 `a` 与 `b` 不相等，一个带有给定 `status`、 `message` 和 `properties` 属性的 `HttpError` 实例会被抛出。


### `assert.notStrictEqual(a, b, [status], [message], [properties])`

使用全等操作符（ `===` ）测试 `a` 和 `b` 是否严格相等。如果 `a` 与 `b` 相等，一个带有给定 `status`、 `message` 和 `properties` 属性的 `HttpError` 实例会被抛出。
