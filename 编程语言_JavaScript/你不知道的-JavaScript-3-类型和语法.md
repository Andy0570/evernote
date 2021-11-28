# 类型

ECMAScript 语言中所有的值都有一个对应的语言类型。ECMAScript 语言类型包括 `Undefined`、`Null`、`Boolean`、`String`、`Number` 和 `Object`。

对语言引擎和开发人员来说，类型是值的内部特征，它定义了值的行为，以使其区别于其他值。


## 内置类型

JavaScript 有七种内置类型：

* `null`
* `undefined`
* `boolean`
* `number`
* `string`
* `object`
* `symbol` -- added in ES6!

可以用 `typeof` 操作符来查看值的类型，它返回的是**类型的字符串值**。

```JavaScript
// null 是一个对象？？？
typeof null === "object"; // true
```

function（函数）是 object 的一个“子类型”，因此函数是对象。

函数不仅是对象，还可以拥有属性。

数组也是对象，它也是 object 的一个“子类型”。


## 值和类型

JavaScript 变量没有类型，但它们持有的值有类型。变量可以随时持有任何类型的值。类型定义了值的行为特征。

在对变量执行 `typeof` 操作时，得到的结果并不是该变量的类型，而是**该变量持有的值的类型**，因为 JavaScript 中的变量没有类型。

### `undefined` 和 `undeclared`

变量在未持有值的时候为 `undefined`。此时 `typeof` 返回 "undefined"。

已在作用域中声明但还没有赋值的变量，是 `undefined` 的。
还没有在作用域中声明过的变量，是 `undeclared` 的。

### typeof Undeclared

示例：在程序中使用全局变量 DEBUG 作为“调试模式”的开关。
问题是如何**在程序中检查全局变量 `DEBUG` 才不会出现 `ReferenceError` 错误**。这时 `typeof` 的安全防范机制就成了我们的好帮手

```JavaScript
// 这样会抛出错误
if (DEBUG) {
  console.log("Debugging is starting");
}

// 这样是安全的
if (typeof DEBUG !== "undefined") {
  console.log("Debugging is starting");
}
```

# 值

## 数组

* 在 JavaScript 中，**数组可以容纳任何类型的值**。
* 对数组声明后即可使用，不需要预先设定数组大小。
* 因为数组也是对象，也可以包含字符串键值和属性，但不推荐这么做。
* 如果字符串键值能够被强制类型转换为十进制数字的话，它就会被当作数字索引来处理。

### 类数组

将类数组（一组通过数字索引的值）转换为真正的数组：
* `slice()`
* `Array.from()`



## 字符串

JavaScript 中字符串是不可变的，而数组是可变的。

**字符串不可变**是指字符串的成员函数不会改变其原始值，而是创建并返回一个新的字符串。而数组的成员函数都是在其原始值上进行操作。


## 数字

JavaScript 只有一种数值类型：`number`（数字），包括“整数”和带小数的十进制数。

JavaScript 中的“整数”就是没有小数的十进制数。

比较两个数字是否相等（在指定的误差范围内）：`Number.EPSILON`

整数的安全范围：（`Number.MAX_SAFE_INTEGER`, `Number.MIN_SAFE_INTEGER`）

如果需要对大数值进行数学运算，目前需要借助相关工具库。

### 整数检测

检测一个值是否是整数：`Number.isInterger()`
检测一个值是否是安全的整数：`Number.isSafeInterger()`


## 特殊数值

### 不是值的值

* `undefined`：未定义，从未赋值。
* `null`：空值。
* void 运算符返回 `undefined`

### 特殊的数字

* `NaN`：不是一个数字、无效数值。—— “执行数学运算没有成功，这是失败后返回的结果。”
* 判断一个值是否是 `NaN`：`Number.isNaN()`
* 无穷数：`Infinity`
* 判断两个值是否绝对相等：`Object.is()`


## 值和引用

JavaScript 对值和引用的赋值/传递完全根据**值的类型**来决定。

简单值（即标量基本类型值，scalar primitive）总是通过**值复制**的方式来赋值/传递，包括 null、undefined、字符串、数字、布尔和 ES6 中的 symbol。

复合值（compound value）——对象（包括数组和封装对象）和函数，则总是通过**引用复制**的方式来赋值/传递。



# 原生函数

原生函数就是内建函数：

* `String()`
* `Number()`
* `Boolean()`
* `Array()`
* `Object()`
* `Function()`
* `RegExp()`
* `Date()`
* `Error()`
* `Symbol()` -- ES6 新增!

**一般不推荐直接使用封装对象**。

## 内部属性 [[class]]

所有 `typeof` 返回值为 "object" 的对象（如数组）都包含一个内部属性 `[[Class]]`。
这个属性无法直接访问，一般通过 `Object.prototype.toString(..)` 来查看。

```js
// 数组的内部 [[class]] 属性是 Array
Object.prototype.toString.call( [1,2,3] ); // "[object Array]"

// 正则表达式的内部 [[class]] 属性是 RegExp
Object.prototype.toString.call( /regex-literal/i ); // "[object RegExp]"

// 虽然 Null() 和 Undefined() 这样的原生构造函数并不存在，但是内部 [[Class]] 属性值仍然是 "Null" 和 "Undefined"
Object.prototype.toString.call(null) // "[object Null]"
Object.prototype.toString.call(undefined) // "[object Undefined]"

// 包装：基本类型值被各自的封装对象自动包装
Object.prototype.toString.call('abc') // "[object String]"
Object.prototype.toString.call(42) // "[object Number]"
Object.prototype.toString.call(true) // "[object Boolean]"
```

## 封装对象包装

JavaScript 会自动为**基本类型值**包装（box 或者 wrap）一个封装对象。

```js
var a = 'abc' // 基本类型

//  String 对象 才有 length 属性和 toUpperCase() 方法
a.length // 3
a.toUpperCase() // "ABC"
```

一般情况下，我们不需要直接使用封装对象。最好的办法是让 JavaScript 引擎自己决定什么时候应该使用封装对象。换句话说，就是应该优先考虑使用 "abc" 和 42 这样的基本类型值，而非 `new String("abc")` 和 `new Number(42)`。

## 拆封

获得封装对象中的基本类型值：`valueOf()`

## 原生函数作为构造函数

**应该尽量避免使用构造函数，除非十分必要**，因为它们经常会产生意想不到的结果。

* `Array()`
* `Object()`
* `Function()`
* `RegExp()`

强烈建议使用**常量形式**(如 `/^a*b+/g`)来定义正则表达式，这样不仅语法简单，执行效率也更高，因为 JavaScript 引擎在代码执行前会对它们进行预编译和缓存。

### `Date()` 和 `Error()`

创建日期：
* `new Date()`
* `Date.now()`


# 强制类型转换

JavaScript 中的**强制类型转换**总是返回标量基本类型值，如字符串、数字和布尔值，不会返回对象和函数。

## 值类型转换

**类型转换**：将值从一种类型转换为另一种类型。

* 显示类型转换；
* 强制类型转换；

我们能够从代码中看出哪些地方是**显式强制类型转换**，而**隐式强制类型转换**则不那么明显，通常是某些操作产生的副作用。

```js
var a = 42;

var b = a + ""; // 隐式强制类型转换

var c = String( a ); // 显式强制类型转换
```

## 抽象值操作

### `ToString()`：非字符串转换为字符串

...

#### JSON 字符串化

工具函数 `JSON.stringify(..)` 在将 JSON 对象序列化为字符串时只是用到了 `ToString()` 强制类型转换，但是 JSON 字符串化并非严格意义上的强制类型转换：
1. 字符串、数字、布尔值和 `null` 的 `JSON.stringify(..)` 规则与 `ToString()` 基本相同。
2. 如果传递给 `JSON.stringify(..)` 的对象中定义了 `toJSON()` 方法，那么该方法会在字符串化前调用，以便将对象转换为安全的 JSON 值。

`JSON.stringify(..)` 在对象中遇到 `undefined`、`function` 和 `symbol` 时会自动将其忽略，在数组中则会返回 `null`（以保证单元位置不变）。

```js
JSON.stringify( 42 );	// "42"
JSON.stringify( "42" );	// ""42"" (a string with a quoted string value in it)
JSON.stringify( null );	// "null"
JSON.stringify( true );	// "true"

// 不安全的 JSON 值
JSON.stringify( undefined );					// undefined
JSON.stringify( function(){} );					// undefined

JSON.stringify( [1,undefined,function(){},4] );	// "[1,null,null,4]"
JSON.stringify( { a:2, b:function(){} } );		// "{"a":2}"
```

可以向 `JSON.stringify(..)` 传递一个可选参数 replacer，它可以是数组或者函数，用来指定对象序列化过程中哪些属性应该被处理，哪些应该被排除，和 toJSON() 很像。

`JSON.stringify(..)` 还有一个可选参数 space，用来指定输出的缩进格式。space 为正整数时是指定每一级缩进的字符数，它还可以是字符串，此时最前面的十个字符被用于每一级的缩进



### `ToNumber()`：非数值转换为数值



### `ToBoolean()`：转换为布尔值

以下这些是假值，假值的布尔强制类型转换结果为 `false`。
* `undefined`
* `null`
* `false`
* `+0`, `-0`, and `NaN`
* `""`

真值就是假值列表之外的值。

## 显式强制类型转换

显式强制类型转换：显而易见的类型转换


### 字符串和数字之间的显式转换

* `String()`：将值转换为**字符串基本类型**。
* `Number()`：将值转换为**数字基本类型**。
* `toString()`
* 一元运算 + 被普遍认为是显式强制类型转换

我们不建议对日期类型使用强制类型转换，应该使用 `Date.now()` 来获得当前的时间戳，使用 `new Date(..).getTime()` 来获得指定时间的时间戳。

### 显式解析数字字符串

**解析字符串中的数字**和**将字符串强制类型转换为数字**的返回结果都是数字。但解析和转换两者之间还是有明显的差别。

解析允许字符串中含有非数字字符，解析按从左到右的顺序，如果遇到非数字字符就停止。而转换不允许出现非数字字符，否则会失败并返回 `NaN`。

* 解析字符串中的浮点数：`parseFloat()`
* `parseInt()`


### 显示转换为布尔值

* `Boolean()`，不常用。
* 显式强制类型转换为布尔值最常用的方法：`!!`


## 隐式强制类型转换

隐式强制类型转换指的是那些隐蔽的强制类型转换，副作用也不是很明显。
隐式强制类型转换的作用是减少冗余，让代码更简洁。



## 宽松相等和严格相等

== 允许在相等比较中进行强制类型转换，而 === 不允许。

JavaScript 中的相等比较：[JS Copmarison Table](https://dorey.github.io/JavaScript-Equality-Table/)


## 抽象关系比较



# 语法

JavaScript 语法定义了词法规则（syntax rule，如运算符和关键词等）是如何构成可运行的程序代码的。

## 语句和表达式

JavaScript 的语法中：语句相当于句子，表达式相当于短语，运算符则相当于标点符号和连接词。

## 运算符优先级

## 自动分号

## 错误

## 函数参数

## try...finally

## switch
