# 语法

* ECMAScript 中的一切（变量、函数名和操作符）都**区分大小写**。
* JavaScript 语句结尾的分号（`;`）不是必须的，但是建议任何时候都不要省略它。
* 建议始终在控制语句中使用代码块，即使代码块中只有一条语句。

## 标识符

标识符的定义：变量、函数、属性的名字，或者函数的参数。

标识符命名规则：
1. 第一个字符，必须是任意 Unicode 字母（包括英文字母和其他语言的字母）、美元符号（`$`）或下划线（`_`）。
2. 第二个字符及后面的字符，除了 Unicode 字母、美元符号和下划线，还可以用数字 `0-9`。

> 最佳实践：ECMAScript 标识符采用**驼峰大小写格式**。

## 注释

单行注释：
```javascript
// 单行注释内容
```

块级注释/多行注释：
```javascript
/*
 * 这是一个多行
 * （块级）注释
 */
```

## 严格模式

严格模式为 JavaScript 定义了一种不同的解析与执行模型。在严格模式中，ECMAScript 3 中一些不确定的行为将得到处理，而且对某些不安全的操作也会抛出错误。

1. 整个脚本启用严格模式，在文件顶部添加：

```javascript
"use strict" 
```
该语法是一个编译指示，用于告诉支持的 JavaScript 引擎切换到严格模式。

2. 函数体内启用严格模式：

```js
function doSomething() {
    "use strict";
    // 函数体
}
```

## 关键字和保留字

不要使用关键字和保留字作为标识符、属性名或者函数名。

关键字：

```javascript
break
case
catch
continue
default
delete
debugger (第 5 版新增)
do
else
finally
for
function
if
in
instanceof
new
return
switch
this
throw
try
typeof
var
void
while
with
```

保留字：

```javascript
abstract
boolean
byte
char
class
const
debugger
double
enum
export
extends
final
float
goto
implements
import
int
interface
long
native
package
private
protected
public
short
static
super
synchronized
throws
transient
volatile
let
yield
```



# 变量

ECMAScript 的变量是**松散类型/动态类型**的，松散类型可以保存任何类型的数据。换句话说，每个变量仅仅是一个用于保存值的占位符而已。定义变量时要使用 `var` 操作符。

也就是说，变量的类型没有限制，变量可以随时更改类型。

* 使用 `var` 操作符定义的变量将成为定义该变量的作用域中的局部变量。
```javascript
function test() {
  var message = "hello"; // 局部变量
}
test();
console.log(message); // 错误！函数退出后，变量已经被销毁了！
```

* 省略 `var` 操作符会创建全局变量。
```javascript
/*
* 不推荐！
* 严格模式下会报错：ReferenceError: message is not defined
*/
function test() {
  message = "hello"; // 全局变量
}
test();
console.log(message); // "hello"
```
* 可以使用一条语句定义多个变量，用逗号分隔开。
```javascript
// 代码中的换行和变量缩进不是必须的，但这样做可以提高代码可读性。
var message = "hello",
      found = false,
        age = 24;
```

## 变量提升

JavaScript 引擎的工作方式是：先解析代码，获取所有被声明的变量，然后再一行一行地运行。这样的结果就是：所有的变量的声明语句，都会被提升到代码的头部，这就叫做**变量提升**（hoisting）。

```javascript
console.log(a); // undefined
var a = 1;
```

上面代码首先使用 `console.log` 方法，在控制台（console）显示变量 `a` 的值。这时变量 `a` 还没有声明和赋值，所以这是一种错误的做法，但是实际上不会报错。因为存在变量提升机制，真正运行的是下面的代码：

```javascript
var a;
console.log(a); // undefined
a = 1;
```

最后的结果是显示 `undefined`，表示变量 `a` 已声明，但还未赋值。



# 数据类型

![ECMAScript 数据类型](https://upload-images.jianshu.io/upload_images/2648731-bddd822c04358206.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

Object 本质上是由一组无序的键值对组成的。另外值得注意的是，JavaScript 中的函数是对象，函数名只是指向该对象的指针。


## typeof 操作符

`typeof` 操作符：检测并返回给定变量的数据类型。

| typeof 操作符返回的字符串    | 描述       |
| ------------------------- | ---------- |
| "undefined"            | 未定义     |
| "boolean"                | 布尔值     |
| "string"                 | 字符串     |
| "number"                 | 数值       |
| "object"                 | 对象或 null |
| "function"               | 函数       |

用 `typeof` 操作符检测变量的数据类型：

```javascript
var message = 'some string';
console.log(typeof message); // string

console.log(typeof 95); // number

// undefined 表示 value 变量没有声明，或者声明了但是没有赋初始化值。
console.log(typeof value); // undefined
```

💡 用 `typeof` 检查 `null` 类型返回 `object`：

```javascript
typeof null // "object"
```

`null` 的类型是 `object`，这是由于历史原因造成的。1995 年的 JavaScript 语言第一版，只设计了五种数据类型（对象、整数、浮点数、字符串和布尔值），没考虑 `null`，只把它当作 `object` 的一种特殊值。后来 `null` 独立出来，作为一种单独的数据类型，为了兼容以前的代码，`typeof null` 返回 `object` 就没法改变了。

### `typeof` 是一个操作符而不是函数

```javascript
var message = "some string";
console.log(typeof message); // string
console.log(typeof (message)); // string
```

* `typeof` 操作符的操作数可以是变量（message），也可以是数值字面量。
* `typeof` 是一个操作符而不是函数，所以圆括号尽管可以使用，但不是必需的。


### `typeof` 与 `instanceof` 的使用异同

* `typeof` 操作符是确定一个变量是字符串、数值、布尔值，还是 `undefined` 的最佳工具（检测基本类型）。
* 如果变量是给定引用类型（根据它的原型链来识别）的实例，`instanceof` 操作符返回 `true`，而所有基本类型返回 `false`（检测引用类型）。

> 确定一个值是哪种**基本类型**可以使用 `typeof` 操作符；
> 确定一个值是哪种**引用类型**可以使用 `instanceof` 操作符。


## Undefined 类型

* 在使用 `var` 声明变量但未对其进行初始化时，这个变量的默认值就是 `undefined`。
* `undefined` 是一个特殊的值，表示“**未定义**”。
* 对**未初始化**和**未声明**的变量执行 `typeof` 操作符都会返回 `undefined`。

### 未声明变量&未初始化变量的区别

```javascript
var message; // 变量声明后，如果未赋初始值，则默认值为 undefined

alert(message); // "undefined"
alert(age);     // 没有声明的变量直接使用会产生错误！

// ⚠️ 没有声明过的变量只能使用 typeof 操作符检测其数据类型
alert(typeof message); // "undefined"
alert(typeof age)      // "undefined"
```



### `undefined` 常用场景

```javascript
// 变量声明了，但没有赋值
var i;
i // undefined

// 调用函数时，应该提供的参数没有提供，该参数等于 undefined
function f(x) {
  return x;
}
f() // undefined

// 对象没有赋值的属性
var  o = new Object();
o.p // undefined

// 函数没有返回值时，默认返回 undefined
function f() {}
f() // undefined
```

> 💡
> 即便未初始化的变量会自动被赋予 `undefined` 值，但显式地初始化变量依然是明智的选择。如果能够做到这一点，那么当 `typeoft` 操作符返回 `undefined` 值时，我们就知道被检测的变量还没有被声明，而不是尚未初始化。

## Null 类型

* Null 类型只有一个值 `null`。`null` 值表示一个**空对象指针**。因此，`typeof null` 会返回 "object"。
* `null` 表示“**空值**”，即该处的值现在为空。调用函数时，某个参数未设置任何值，这时就可以传入 `null`，表示该参数为空。

### 空对象指针&未经初始化变量

```javascript
// 不推荐使用 undefined 显式初始化变量,因为它默认就是 undefined。
// 而且，字面值 undefined 的主要目的是用于比较。
var message = undefined; // ❎
var message;             // ✅

// 但是如果定义的变量是对象，那么最好将该变量初始化为 null 而不是其他值。
var car = null;    // ✅
alert(typeof car); // "object"

// 实际上，undefined 值派生自 null 值
alert(null == undefined); // true
```

### Null 类型 & Undefined 类型的区别
* Null 类型只有一个值 `null`，而 Undefined 类型只有一个值 `undefined`;
* `null` 是一个表示 “空” 的对象，转为数值时为 `0`；
* `undefined` 是一个表示 "此处无定义" 的原始值，转为数值时为 `NaN`。

参考：[InfoQ：如何在 JavaScript 中处理 null 和 undefined？](https://www.infoq.cn/article/tsfJmMq4sPa9kmsBKypY)

## Boolean 类型

* Boolean 类型只有两个字面值：`true` 和 `false`。
* 通常意义上来说，0 为假，非 0 为真。但是**在 Boolean 类型中，`true` 不一定等于 1，而 `false` 也不一定等于 0**。
* `true` 和 `false` 区分大小写。

  

### 转型函数：`Boolean()`

ECMAScript 中所有类型的值都有与这两个 Boolean 类型的值等价的值。要将一个值转换为其对应的 Boolean 值，可以调用转型函数 `Boolean()`。

| 数据类型  | 转换为 `true` 的值            | 转换为 `false` 的值       |
| --------- | --------------------------- | ------------------------ |
| Boolean   | `true`                     | `false`                  |
| String    | 任何非空字符串              | “” 或者 ''（空字符串）     |
| Number    | 任何非零数字值（包括无穷大）    | 0 和 NaN               |
| Object    | 任何对象                   | `null`                  |
| Undefined | ——                       | `undefined`              |

注意：空数组（`[]`）和空对象（`{}`）对应的布尔值，都是 `true`。


示例：将一个值转换为其对应的 Boolean 值：

```javascript
var message = "Hello World";
var messageAsBoolean = Boolean(message);
console.log(messageAsBoolean); // true

var message = null;
var messageAsBoolean = Boolean(message);
console.log(messageAsBoolean); // false
```

## Number 类型

* 与其他语言不同，ECMScript 没有为整数和浮点数值分别定义不同的数据类型，Number 类型可用于表示所有数值。 
* Number 类型使用 IEEE754 格式来表示整数和浮点数值。
* Number 类型支持的数值字面量格式：十进制、八进制、十六进制。
* Number 类型中，**八进制在严格模式下无效**。

```javascript
// 十进制
var intNum = 55; 

// 八进制，第一位必须为 0
var octalNum1 = 070 // 八进制的56
// 如果八进制的字面值超出了范围（0～7），前导零将会被忽略，后面的数值将被当作十进制数值解析。
var octNum2 = 079; // 79
var octNum3 = 08; // 8

// 十六进制，前两位必须为 0x
var hexNum =0xA // 十六进制的10

// 科学计数法（e 表示法）
var float Num = 3.125e7; // 31250000
var float Num = 3.125e-3; // 0.003125

// 自动转换为科学记数法的情况
// 【1】小数点前的数字多于 21 位。
1234567890123456789012
// 1.2345678901234568e+21

123456789012345678901
// 123456789012345680000

// 【2】小数点后紧跟6个以上的零，就自动转为科学计数法
0.0000003 // 3e-7
// 否则，就保持原来的字面形式
0.000003 // 0.000003
```

### 浮点数值

* 浮点数值中必须包含一个小数点，并且小数点后面必须至少有一位数字。
* 由于保存浮点数值需要的内存空间是保存整数值的两倍，因此 **ECMAScript 会不失时机地将浮点数值转换为整数值。**

```javascript
var floatNum1 = 1.1; // 浮点数 1.1
var floatNum2 = 1.;  // 小数点后面没有数字，自动解析为 1
var floatNum3 = 10.0 // 整数，自动解析为 10
```
* 浮点数值的最高精度是17位小数，但在进行算术计算时其精确度远远不如整数。
* 浮点数值计算会产生舍入误差问题，因此，**永远不要测试某个特定的浮点数值**。

```javascript
0.1 + 0.2 === 0.3 // false
// 实际上，0.1 + 0.2 = 0.30000000004

0.3 / 0.1
// 2.9999999999999996

(0.3 - 0.2) === (0.2 - 0.1)
// false
```

### 数值范围

* JavaScript 能够表示的数值范围为 2^1024^ 到 2^-1023^（开区间），超出这个范围的数无法表示。

```math
(2^{1024}, 2^{-1023})
```
* 如果某次计算的结果超出了 JavaScript 数值范围（`Number.MIN_VALUE`, `Number.MAX_VALUE`），那么这个数值会被自动转换为 `Infinity` 值。而 `Infinity` 值不是能够参与计算的数值。

⭐️⭐️⭐️ `isFinite()`：在参数位于最小与最大数值之间会返回 `true`。

JavaScript 内部，所有数字都是以 64 位浮点数形式储存，即使整数也是如此。所以，`1` 与 `1.0` 是相同的，是同一个数。

```javascript
1 === 1.0 // true
```

这就是说，JavaScript 语言的底层根本没有整数，所有数字都是小数（64 位浮点数）。容易造成混淆的是，某些运算只有整数才能完成，此时 JavaScript 会自动把 64 位浮点数，转成 32 位整数，然后再进行运算。

JavaScript 提供的有效数字最长为 53 个二进制位。精度最多只能到 53 个二进制位，这意味着，绝对值小于 2 的 53 次方的整数，即 - 2^53^ 到 2^53^，都可以精确表示。

所以，简单的法则就是：**JavaScript 对15位的十进制数都可以精确处理**。

```javascript
Math.pow(2, 53)
// 输出：9007199254740992

// 多出的三个有效数字（最后三位的 111）都会无法保存，变成 0。
9007199254740992111
// 9007199254740992000
```

### NaN

* `NaN`（Not a Number），非数值，表示 **本来要返回数值的操作数未返回数值的情况**（这样就不会抛出错误了）。
* 在 ECMAScript 中，任何数值除以 0 会返回 `NaN`，因此不会影响其他代码的执行。
* 任何涉及 `NaN` 的操作都会返回 `NaN`。
* `NaN` 与任何值都不相等，包括 `NaN` 本身。

⭐️⭐️⭐️ `isNaN()`：确定传入参数是否“**不是数值**”。无法被转换为数值的值会返回 `true`。

```javascript
// 不是数值/不能转换为数值，返回 true
console.log(isNaN(NaN)); // true
console.log(isNaN(10));  // false（10是一个数值）
console.log(isNaN("10")); // false （可以被转换为数值10）
console.log(isNaN("blue")); // true （不能被转换为数值）
console.log(isNaN(true)); // false （可以被转换为数值1）
console.log(isNaN("true")); // true

// 对于空数组和只有一个数值成员的数组，isNaN 返回 false。
// 因为内部会默认先用 Number() 函数进行一次转换再作判断。
isNaN([]) // false
isNaN([123]) // false
isNaN(['123']) // false
```

使用 `isNaN` 之前，最好判断一下数据类型：

```javascript
function myIsNaN(value) {
  return typeof value === 'number' && isNaN(value);
}
```

`NaN` 不是独立的数据类型，而是一个**特殊数值**，它的数据类型依然属于 `Number`，使用 `typeof` 运算符可以看得很清楚。

```javascript
typeof NaN // 'number'
NaN === NaN // false
Boolean(NaN) // false
```

`isNaN()` 也适用于对象。在基于对象调用 `isNaN()` 函数时，会首先调用对象的 `valueOf()` 方法，然后确定该方法返回的值是否可以转换为数值。如果不能，则基于这个返回值再调用 `toString()` 方法，再测试返回值。

### 数值转换

* `Number()` 可以用于任何数据类型。
* `parseInt()`、`parseFloat()` 专门用于把**字符串**转换成数值。

#### 1. `Number()` ，任何数据类型 —> 数值

```javascript
// Boolean
var num1 = Number(true); // 1
var num2 = Number(false); // 0

// 数字 —> 数字

// null -> 0
var num4 = Number(null); // 0

// undefined -> NaN
var num5 = Number(undefined); // NaN

// 字符串
var num6 = Number("Hello world!"); // NaN
var num7 = Number("000011"); // 11
var num8 = Number(""); // 空字符串返回 0

parseInt('42 cats') // 42
Number('42 cats') // NaN

// 对象 ——> 字符串 ——> 数字
// 1.自动调用对象的 valueOf() 方法，再转为数值；
// 2.自动调用对象的 toString() 方法，再转为数值；
```

#### 2. `parseInt()`，字符串 —> 整数

在处理**整数**的时候更常用的是 `parseInt()` 函数。

`parseInt()` 函数在转换字符串时，更多的是看其是否符合数值模式。它会忽略字符串前面的空格，直至找到第一个非空格字符。
如果第一个字符不是数字字符或者负号，`parseInt()` 就会返回 `NaN`；也就是说，用 `parseInt()` 转换空字符串会返回 `NaN`（`Number()` 对空字符返回 0）。
如果第一个字符是数字字符，`parseInt()`会继续解析第二个字符，直到解析完所有后续字符或者遇到了一个非数字字符。

```javascript
// 如果字符串头部有空格，空格会被自动去除。
parseInt('   81') // 81

// 转换字符串
var num1 = parseInt("1234abcd");   // 1234
var num2 = parseInt("1a2b3c4d5e"); // 1

// parseInt() 会忽略小数点，因为小数点是非数字字符。
var num3 = parseInt("22.5");       // 22
var num4 = parseInt("22.34.5");     // 22

// parseInt() 处理空字符串返回 NaN
var num5 = parseInt(""); // NaN
var num6 = Number(""); // 0

// 如果字符串以 0x 或 0X 开头，parseInt 会将其按照十六进制数解析。
parseInt('0x10') // 16

// 如果字符串以 0 开头，将其按照 10 进制解析。
parseInt('011') // 11

// 指定基数（即多少进制）
var num1 = parseInt("10", 2);         //2 – parsed as binary
var num2 = parseInt("10", 8);         //8 – parsed as octal
var num3 = parseInt("10", 10);        //10 – parsed as decimal
var num4 = parseInt("10", 16);        //16 – parsed as hexadecimal

// 对于那些会自动转为科学计数法的数字，parseInt 会将科学计数法的表示方法视为字符串，因此导致一些奇怪的结果。
parseInt(1000000000000000000000.5) // 1
// 等同于
parseInt('1e+21') // 1

parseInt(0.0000008) // 8
// 等同于
parseInt('8e-7') // 8

// 科学计数法应该使用 parseFloat()
parseFloat('314e-2') // 3.14
parseFloat('0.0314E+2') // 3.14
```


在 ECMAScript 5 JavaScript 引擎中，`parseInt()` 已经不具有解析八进制值的能力，因此前导的零会被认为无效，从而将这个值当成"70"，结果就得到十进制的 70。在 ECMAScript 5 中，即使是在非严格模式下也会如此。
```javascript
var num = parseInt("070"); // 70
```

为了避免错误的解析，建议无论在什么情况下都**明确指定基数**：
```javascript
var num = parseInt("070", 8); // 56
```

多数情况下，我们要解析的都是十进制数值，因此始终将 10 作为第二个参数是非常必要的。

#### 3. `parseFloat()`，字符串 —> 浮点数


```javascript
var num1 = parseFloat("12345blue"); // 12345
var num2 = parseFloat("0xA");       // 0
var num3 = parseFloat("22.5");      // 22.5

// 字符串中的第一个小数点是有效的，而第二个小数点是无效的。
var num4 = parseFloat("22.34.5");   // 22.34
var num5 = parseFloat("0980.5");    // 980.5
var num6 = parseFloat("3.125e7");   // 31250000

// parseFloat 方法会自动过滤字符串前导的空格。
parseFloat('\t\v\r12.34\n ') // 12.34

// 如果参数不是字符串，或者字符串的第一个字符不能转化为浮点数，则返回 NaN。
parseFloat([]) // NaN
parseFloat('FF2') // NaN
parseFloat('') // NaN
```

* `parseFloat()` 与 `parseInt()` 的区别：`parseFloat()` 中字符串中的第一个小数点是有效的。
* `parseFloat()` 与 `parseInt()` 的第二个区别： `parseFloat()` 始终都会忽略前导零。
* `parseFloat()` 只解析**十进制值**，因此十六进制格式的字符串则始终会被转换成 0。
* 如果字符串包含的是一个可解析为**整数**的数（没有小数点，或者小数点后都是零），`parseFloat()` 会返回整数。

#### 总结

* 比较两个数字是否相等（在指定的误差范围内）：`Number.EPSILON`
* 整数的安全范围：（`Number.MAX_SAFE_INTEGER`, `Number.MIN_SAFE_INTEGER`）
* 判断一个值是否位于最小数值与最大数值之间：`isFinite()`
* 检测一个值是否是整数：`Number.isInterger()`
* 检测一个值是否是安全的整数：`Number.isSafeInterger()`
* 判断一个值是否不是一个数字、或是一个无效的数值（`NaN`）：`Number.isNaN()`
* 无穷数：`Infinity`
* 判断两个值是否绝对相等：`Object.is()`
* 另外，如果需要对大数值进行数学运算，目前需要借助相关工具库。


## String 字符串

String 类型用于表示由零或多个 16 位 Unicode 字符组成的字符序列，即字符串。字符串可以用双引号（“）或者单引号（‘）表示。

由于 HTML 语言的属性值使用双引号，所以很多项目约定 JavaScript 语言的字符串只使用**单引号**。

ECMAScript 中的字符串是**不可变**的，也就是说，**字符串一旦创建，它们的值就不能改变**。

### Unicode 字符集

JavaScript 使用 Unicode 字符集。JavaScript 引擎内部所有字符都用 Unicode 表示。

每个字符在 JavaScript 内部都是以 16 位（即 2 个字节）的 UTF-16 格式储存。也就是说，JavaScript 的单位字符长度固定为 16 位长度，即 2 个字节。

JavaScript 对 UTF-16 的支持是不完整的，由于历史原因，只支持两字节的字符，不支持四字节的字符。

对于码点在 `U+10000` 到 `U+10FFFF` 之间的字符，JavaScript 总是认为它们是两个字符（`length` 属性为 2）。所以处理的时候，必须把这一点考虑在内，也就是说，JavaScript 返回的字符串长度可能是不正确的。


### 字符字面量/转义序列

字符字面量会被作为**一个字符**来解析。

![字符字面量](https://upload-images.jianshu.io/upload_images/2648731-a2c234d75aa98fbd.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)



### `toString（）` 方法

> ⚠️ `null` 和 `undefined` 值没有这个方法。

* 在调用**数值**的 `toString()` 方法时，可以传递一个参数：指定输出数值的基数，默认为十进制转换。
* 通过传递基数，`toString()` 可以输出以二进制、八进制、十六进制，乃至其他任意有效进制格式表示的字符串值。

```javascript
var num = 10;
alert(num.toString());       //"10"
alert(num.toString(2));      //"1010"
alert(num.toString(8));      //"12"
alert(num.toString(10));     //"10"
alert(num.toString(16));     //"a"
```

### `String ()` 方法

`String ()` 函数可以将任何类型的值转换为字符串。转换规则如下：
* 如果值有 `toString()` 方法，则调用该方法；
* 如果值是 `null`，则返回 `"null"`；
* 如果值是 `undefined` ，则返回 `"undefined"`。

```javascript
var value1 = 10;
var value2 = true;
var value3 = null;
var value4;

alert(String(value1));     //"10"
alert(String(value2));     //"true"
alert(String(value3));     //"null"
alert(String(value4));     //"undefined"
```



### `length` 属性

`length` 属性返回字符串的长度，该属性也是无法改变的。

```javascript
var s = 'hello';
s.length // 5

s.length = 3;
s.length // 5

s.length = 7;
s.length // 5
```

上面代码表示字符串的 `length` 属性无法改变，但是不会报错。



###  Base64 编码

JavaScript 原生提供两个 Base64 相关的方法。

* `btoa()`：任意值转为 Base64 编码；
* `atob()`：Base64 编码转为原来的值；

```javascript
var string = 'Hello World!';
btoa(string) // "SGVsbG8gV29ybGQh"
atob('SGVsbG8gV29ybGQh') // "Hello World!"
```

注意，这两个方法不适合非 ASCII 码的字符，会报错。

```
btoa('你好') // 报错
```

要将非 ASCII 码字符转为 Base64 编码，必须中间插入一个转码环节，再使用这两个方法。

```javascript
function b64Encode(str) {
  return btoa(encodeURIComponent(str));
}

function b64Decode(str) {
  return decodeURIComponent(atob(str));
}

b64Encode('你好') // "JUU0JUJEJUEwJUU1JUE1JUJE"
b64Decode('JUU0JUJEJUEwJUU1JUE1JUJE') // "你好"
```


## Object 类型

* ECMAScript 中的对象就是一组 “键值对”（key-value）的集合，是一种无序的复合数据集合。
* 创建自定义对象：创建 `Object` 类型的实例并为其添加属性和（或）方法。

```javascript
// 通过 new 操作符创建对象。
var o = new Object();

// 定义一个对象，赋值给一个变量 obj，对象内部包含两个键值对。
var obj = {
  foo: 'Hello',
  bar: 'World'
};
```

在 ECMAScript 中，Object 是所有对象的基础，因此所有对象都具有下面这些基本的属性和方法。

| 属性或方法名 | 描述 |
| --- | --- |
| `Constructor` | 保存着用于创建当前对象的函数。 |
| `hasOwnProperty(propertyName)` | 检查给定的属性在当前对象实例中是否存在（而不是在实例的原型中）。 |
| `isPrototypeOf(object)` | 检查当前对象是否是传入对象的原型。 |
| `propertyIsEnumerable(propertyName)` | 检查给定的属性是否能够使用 for-in 来枚举。 |
| `toLoacleString()` | 返回对象的字符串表示，该字符串与执行环境的地区对应。 |
| `toString()` | 返回对象的字符串表示。 |
| `valueOf()` | 返回对象的字符串、数值或布尔值表示。 |


### 使用 `for...in` 循环枚举对象的属性

* 它遍历的是对象所有可遍历（enumerable）的属性，会跳过不可遍历的属性。
* 它不仅遍历对象自身的属性，还遍历继承的属性。

```javascript
var person = { name: '老张' };

// 遍历对象的属性，也会遍历继承来的属性
for (var key in person) {
  // 判断某个属性是否为对象自身的属性
  if (person.hasOwnProperty(key)) {
    console.log(key);
  }
}
```



## 数组

数组（array）是按次序排列的一组值。每个值的位置都有编号（从 0 开始），整个数组用方括号表示。

```javascript
// 定义数组，同时赋初值
var arr = ['a', 'b', 'c'];

// 先定义，后赋值
var arr = [];
arr[0] = 'a';
arr[1] = 'b';
arr[2] = 'c';
```

本质上，数组属于一种特殊的对象。`typeof` 运算符会返回数组的类型是 `object`。

```javascript
typeof [1, 2, 3] // "object"
```



### 数组的 `length` 属性

数组的 `length` 属性，返回数组的成员数量：

```javascript
['a', 'b', 'c'].length // 3
```

数组的 `length` 属性是**可写**的。如果人为设置一个小于当前成员个数的值，该数组的成员会自动减少到 `length` 设置的值。

当 `length` 属性设为大于数组个数时，读取新增的位置都会返回 `undefined`。

清空数组的一个有效方法，就是将 `length` 属性设为 0。



### `for...in` 循环和数组的遍历

`for...in` 循环不仅可以遍历对象，也可以遍历数组，毕竟数组只是一种特殊对象。

```javascript
var a = [1, 2, 3];

for (var i in a) {
  console.log(a[i]);
}
// 1
// 2
// 3
```

但是，`for...in` 不仅会遍历数组所有的数字键，还会遍历非数字键。

```javascript
var a = [1, 2, 3];
a.foo = true;

for (var key in a) {
  console.log(key);
}
// 0
// 1
// 2
// foo
```

上面代码在遍历数组时，也遍历到了非整数键 `foo`。所以，不推荐使用 `for...in` 遍历数组。

数组的遍历可以考虑使用 `for` 循环或 `while` 循环。

```javascript
var a = [1, 2, 3];

// for循环
for(var i = 0; i < a.length; i++) {
  console.log(a[i]);
}

// while循环
var i = 0;
while (i < a.length) {
  console.log(a[i]);
  i++;
}

var l = a.length;
while (l--) {
  console.log(a[l]);
}
```

数组的 `forEach` 方法，也可以用来遍历数组：

```javascript
var colors = ['red', 'green', 'blue'];
colors.forEach(function (color) {
  console.log(color);
});
// red
// green
// blue
```

### 数组的空位

数组的空位是可以读取的，返回 `undefined`。

```javascript
var a = [, , ,];
a[1] // undefined
```

使用 `delete` 命令删除一个数组成员，会形成空位，并且不会影响 `length` 属性。

```javascript
var a = [1, 2, 3];
delete a[1];

a[1] // undefined
a.length // 3
```

如果是空位，使用数组的 `forEach` 方法、`for...in` 结构、以及 `Object.keys` 方法进行遍历，空位都会被跳过。



# 操作符

一元操作符：只能操作一个值的操作符。
![一元操作符](https://upload-images.jianshu.io/upload_images/2648731-dc05261dc59c3c34.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)



## 1⃣️ 递增和递减操作符

递增和递减操作符遵循的规则：
* 在应用于一个**包含有效数字字符的字符串**时，先将其转换为数字值，再执行加减 1 的操作。字符串变量变成数值变量。
* 在应用于一个**不包含有效数字字符的字符串**时，将变量的值设置为 NaN。 字符串变量变成数值变量。
* 在应用于布尔值 **false** 时，先将其转换为 0 再执行加减 1 的操作。布尔值变量变成数值变量。
* 在应用于布尔值 **true** 时，先将其转换为 1 再执行加减 1 的操作。布尔值变量变成数值变量。
* 在应用于**浮点数值**时，执行加减 1 的操作。
* 在应用于**对象**时，先调用对象的 `valueOf()`方法，以取得一个可供操作的值。然后对该值应用前述规则。如果结果是 NaN，则在调用 `toString()` 方法后再应用前述规则。对象变量变成数值变量。


### 前置型操作符

++i、--i

执行前置递增和递减操作时，变量的值都是在语句被求值以前改变的。

```javascript
var age = 29;
var anotherAge = --age + 2; // age 先执行了减法操作

console.log(age); // 28
console.log(anotherAge); // 30
```

### 后置型操作符

i++、i--

后置型递增和递减操作是在包含它们的语句被求值之后才执行的。

```javascript
var age = 29;
var anotherAge = age-- + 2; // age 后执行减法操作

console.log(age); // 28
console.log(anotherAge); // 31
```



## 2⃣️ 一元加和减操作符

一元加和减操作符主要用于基本的算术运算，也可以用于转换数据类型。

### 一元加操作符

* 一元加操作符以一个加号(+)表示，放在数值前面，对数值不会产生任何影响。
* 当一元加操作符作用于**非数值**时，该操作符会像 `Number()` 转型函数一样对这个值执行转换。

示例：`a = +a;`

### 一元减操作符

* 将一元减操作符应用于数值时，该值会变成负数。
* 当一元减操作符作用于**非数值**时，一元减操作符遵循与一元加操作符相同的规则，最后再将得到的数值转换为负数。

示例：`a = -a;`


## 3⃣️ 位操作符

* 位操作符按**内存中表示数值的位**来操作数值。
* 位操作符并不直接操作 64 位的值。而是先将 64 位的值转换成 32 位的整数，然后执行操作，最后再将结果转换回 64 位。
* 默认情况下，ECMAScript 中的所有整数都是**有符号整数**。
* 符号位：对于有符号的整数，32 位中的前 31 位用于表示整数的值。第 32 位用于表示数值的符号：**0 表示正数，1 表示负数**。
* 负数同样以二进制码存储，但使用的格式是**二进制补码**。
* 如果对**非数值**应用位操作符，会先使用 `Number()` 函数将该值转换为一个数值(自动完成)，然后再应用位操作。

> 在 ECMAScript 中，当对数值应用位操作符时，后台会发生如下转换过程：
> 64 位的数值被转换成 32 位数值，然后执行位操作，最后再将 32 位的结果转换回 64 位数值。
> 但这个转换过程也导致了一个严重的副效应，即在对特殊的 **NaN** 和 **Infinity** 值应用位操作时，这两个值都会被当成 0 来处理。


### 1. 按位非（NOT）

按位非操作的本质：操作数的负值减 1。

```JavaScript
var num1 = 25;             //binary 00000000000000000000000000011001
var num2 = ~num1;          //binary 11111111111111111111111111100110
alert(num2);               //-26
```

### 2. 按位与（AND）


| 第一个数值的位 | 第二个数值的位 | 结果 |
| --- | --- | --- |
| 1 | 1 | 1 |
| 1 | 0 | 0 |
| 0 | 1 | 0 |
| 0 | 0 | 0 |

按位与操作只在两个数值的对应位都是 1 时才返回 1，任何一位是 0，结果都是 0。

```JavaScript
var result = 25 & 3;
alert(result);    //outputs 1

// 按位与原理：11得1，01得0
//  25 = 0000 0000 0000 0000 0000 0000 0001 1001
//   3 = 0000 0000 0000 0000 0000 0000 0000 0011
// ----------------------------------------------
// AND = 0000 0000 0000 0000 0000 0000 0000 0001
```

### 3. 按位或（OR）

| 第一个数值的位 | 第二个数值的位 | 结果 |
| --- | --- | --- |
| 1 | 1 | 1 |
| 1 | 0 | 1 |
| 0 | 1 | 1 |
| 0 | 0 | 0 |

```JavaScript
var result = 25 | 3;
alert(result);       //27
```

### 4. 按位异或（XOR）

| 第一个数值的位 | 第二个数值的位 | 结果 |
| --- | --- | --- |
| 1 | 1 | 0 |
| 1 | 0 | 1 |
| 0 | 1 | 1 |
| 0 | 0 | 0 |

```JavaScript
var result = 25 ^ 3;
alert(result);    //26
```

### 5. 左移

左移（<<）：将数值的所有位向左移动指定的位数。
左移不会影响操作数的符号位。

### 6. 有符号右移
有符号右移（>>），保留符号位（即正负号标记）,用符号位的值来填充所有空位。


### 7. 无符号右移
无符号右移（>>>），用 0 来填充空位。对正数来说，无符号右移的结果与有符号右移相同。
无符号右移操作符会把负数的二进制码当成正数的二进制码。



## 4⃣️ 布尔操作符

布尔操作符一共有 3 个：非(NOT)、与（AND）和或（OR）。

### 逻辑非 ！
* 逻辑非操作符首先会将它的操作数转换为一个布尔值，然后再对其求反。
* 同时使用两个逻辑非操作符（!!），实际上就会模拟 `Boolean()` 转型函数的行为，即返回一个操作数的布尔值。

逻辑非操作符遵循下列规则：
* 如果操作数是一个对象，返回 false;
* 如果操作数是一个空字符串，返回 true;
* 如果操作数是一个非空字符串，返回 false;
* 如果操作数是数值 0，返回 true;
* 如果操作数是任意非 0 数值(包括 Infinity)，返回 false; 
* 如果操作数是 `null`，返回 true;
* 如果操作数是 `NaN`，返回 true;
* 如果操作数是 `undefined`，返回 true。

### 逻辑与 &&

逻辑与的真值表如下：

| 第一个操作数 | 第二个操作数 | 结果 |
| :---: | :---: | :---: |
| `true` | `true` | `true` |
| `true` | `false` | `false` |
| `false` | `true` | `false` |
| `false` | `false` | `false` |

逻辑与操作可以应用于任何类型的操作数，而不仅仅是布尔值。
在有一个操作数不是布尔值的情况下，逻辑与操作就不一定返回布尔值。此时，它遵循下列规则:
* 如果第一个操作数是对象，则返回第二个操作数;
* 如果第二个操作数是对象，则只有在第一个操作数的求值结果为 true 的情况下才会返回该对象;
* 如果两个操作数都是对象，则返回第二个操作数;
* 如果有一个操作数是 `null`，则返回 `null`;
* 如果有一个操作数是 `NaN`，则返回 `NaN`;
* 如果有一个操作数是 `undefined`，则返回 `undefined`。

逻辑与操作属于**短路操作**，即如果第一个操作数能够决定结果，那么就不会再对第二个操作数求值。
不能在逻辑与操作中使用未定义的值。


### 逻辑或 ||

逻辑或的真值表如下：

| 第一个操作数 | 第二个操作数 | 结果 |
| :---: | :---: | :---: |
| `true` | `true` | `true` |
| `true` | `false` | `true` |
| `false` | `true` | `true` |
| `false` | `false` | `false` |

与逻辑与操作相似，如果有一个操作数不是布尔值，逻辑或也不一定返回布尔值；此时，它遵循下列规则:
* 如果第一个操作数是对象，则返回第二个操作数;
* 如果第一个操作数的求值结果为 `false`，则返回第二个操作数; 
* 如果两个操作数都是对象，则返回第一个操作数;
* 如果两个操作数都是 `null`，则返回 `null`;
* 如果两个操作数都是 `NaN`，则返回 `NaN`;
* 如果两个操作数都是 `undefined`，则返回 `undefined`。

逻辑或操作符也是**短路操作符**。也就是说，如果第一个操作数的求值结果为 `true`，就不会对第二个操作数求值了。

#### 短路操作

逻辑与/逻辑或操作属于**短路操作**。
即：如果第一个操作数能够决定结果，那么就不会再对第二个操作数求值。

可以利用逻辑或的这一行为来**避免**为变量赋 `null` 或 `undefined` 值：
```javascript
var myObject = preferredObject || backupObject;

/*
 * 变量 myObject 将被赋予等号后面两个值中的一个。
 * 变量 preferredObject 中包含优先赋给变量 myObject 的值，
 * 变量 backupObject 负责在 preferredObject 中不包含有效值的情况下提供后备值。
 * 如果 preferredObject 的值不是 null，那么它的值将被赋给 myObject;
 * 如果是 null，则将 backupObject 的值赋给 myObject。
 */
```

node.js 示例代码：
```javascript
// 组装参数
const payload = ctx.request.body || {};
```


## 5⃣️ 乘性操作符

* ECMAScript 定义了 3 个乘性操作符：乘法、除法和求模。
* 如果参与乘性计算的某个操作数不是数值，后台会先使用 ` Number()` 转型函数将其转换为数值。


### 乘法 *

乘法用于计算两个数值的乘积。

在处理特殊值的情况下，乘法操作符遵循下列特殊的规则：
* 如果操作数都是数值，执行常规的乘法计算，即两个正数或两个负数相乘的结果还是正数，而如果只有一个操作数有符号，那么结果就是负数。如果乘积超过了 ECMAScript 数值的表示范围，则返回 `Infinity` 或 `-Infinity`;
* 如果有一个操作数是 `NaN`，则结果是 `NaN`;
* 如果是 `Infinity` 与 0 相乘，则结果是 `NaN`;
* 如果是 `Infinity` 与非 0 数值相乘，则结果是 `Infinity` 或 `-Infinity`，取决于有符号操作数的符号;
* `Infinity * Infinity = Infinity;`
* 如果有一个操作数不是数值，则在后台调用 `Number()` 将其转换为数值，然后再应用上面的规则。

### 除法 /

除法执行第二个操作数除第一个操作数的计算。

除法操作符对特殊的值也有特殊的处理规则。这些规则如下：
* 如果操作数都是数值，执行常规的除法计算，即两个正数或两个负数相除的结果还是正数，而如果只有一个操作数有符号，那么结果就是负数。如果商超过了 ECMAScript 数值的表示范围，则返回 `Infinity` 或 `-Infinity`;
* 如果有一个操作数是 `NaN`，则结果是 `NaN`;
* `Infinity / Infinity = NaN;`
* `0 / 0 = NaN;`
* 如果是非零的有限数被零除，则结果是 `Infinity` 或 `-Infinity`，取决于有符号操作数的符号;
* 如果是 `Infinity` 被任何非零数值除，则结果是 `Infinity` 或 `-Infinity`，取决于有符号操作数的符号;
* 如果有一个操作数不是数值，则在后台调用 `Number()` 将其转换为数值，然后再应用上面的规则。

### 求模（取余数） %

求模操作符会遵循下列特殊规则来处理特殊的值: 
* 如果操作数都是数值，执行常规的除法计算，返回除得的余数;
* 如果被除数是无穷大值而除数是有限大的数值，则结果是 `NaN`;
* 如果被除数是有限大的数值而除数是零，则结果是 `NaN`;
* `Infinity % Infinity = NaN;`
* 如果被除数是有限大的数值而除数是无穷大的数值，则结果是被除数;
* 如果被除数是零，则结果是零;
* 如果有一个操作数不是数值，则在后台调用 `Number()` 将其转换为数值，然后再应用上面的规则。


## 6⃣️ 加性操作符

### 加法 +

```JavaScript
(+0) + (+0) = (+0);
(-0) + (-0) = (-0);
(+0) + (-0) = (+0);
Infinity + Infinity = Infinity;
(-Infinity) + (-Infinity) = (-Infinity);
Infinity + (-Infinity) = NaN;
```

* 字符串 + 字符串 = 拼接字符串。
* 字符串 + 操作数 = 操作数转换为字符串，再拼接两个字符串。
* 如果有一个操作数是 `NaN`，则结果是 `NaN`;
* 如果有一个操作数是对象、数值或布尔值，则调用它们的 `toString()` 方法取得相应的字符串值，然后再应用前面关于字符串的规则。对于 `undefined` 和 `null`，则分别调用 `String()` 函数并取得字符串"undefined"和"null"。

```JavaScript
var result1 = 5 + 5;
console.log(result1); // 10

var result2 = 5 + "5";
console.log(result2); // 55

// 错误示例
var num1 = 5;
var num2 = 10;
var message1 = "The sum of 5 and 10 is " + num1 + num2;
console.log(message1);    // The sum of 5 and 10 is 510

// 正确示例
var num1 = 5;
var num2 = 10;
var message2 = "The sum of 5 and 10 is " + (num1 + num2);
console.log(message2);    // The sum of 5 and 10 is 15
```


### 减法 -

```JavaScript
(+0) - (+0) = (+0);
(+0) - (-0) = (-0); 
(-0) - (-0) = (+0);

Infinity - Infinity = NaN;
(-Infinity) - (-Infinity) = NaN;
Infinity - (-Infinity) = Infinity;
(-Infinity) - Infinity = (-Infinity);
```

ECMAScript 中的减法操作符在处理各种数据类型转换时，同样需要遵循一些特殊规则：
* 如果两个操作符都是数值，则执行常规的算术减法操作并返回结果;
* 如果有一个操作数是 `NaN`，则结果是 `NaN`;
* 如果有一个操作数是字符串、布尔值、`null` 或 `undefined`，则先在后台调用 `Number()` 函数将其转换为数值，然后再根据前面的规则执行减法计算。如果转换的结果是 `NaN`，则减法的结果就是 `NaN`;
* 如果有一个操作数是对象，则调用对象的 `valueOf()` 方法以取得表示该对象的数值。如果得到的值是 `NaN`，则减法的结果就是 `NaN`。如果对象没有 `valueOf()` 方法，则调用其 `toString()` 方法并将得到的字符串转换为数值。

## 7⃣️ 关系操作符

当关系操作符的操作数使用了非数值时，也要进行数据转换或完成某些奇怪的操作。以下就是相应的规则。
* 如果两个操作数都是数值，则执行数值比较。
* 如果两个操作数都是**字符串**，则比较两个字符串对应的字符编码值。
* 如果一个操作数是数值，则将另一个操作数转换为一个数值，然后执行数值比较。
* 如果一个操作数是对象，则调用这个对象的 `valueOf()` 方法，用得到的结果按照前面的规则执行比较。如果对象没有 `valueOf()` 方法，则调用 `toString()` 方法，并用得到的结果根据前面的规则执行比较。
* 如果一个操作数是布尔值，则先将其转换为数值，然后再执行比较。
* 任何操作数与 `NaN` 进行比较，结果都是 `false`。

```javascript
// 两个操作数都是字符串， 而字符串比较的是字符编码("2"的字符编码是 50，而"3"的字符编码是 51)。
var result = "23" < "3" // true

// 字符串"23"会被转换成数值 23，然后再与 3 进行比较
var result = "23" < 3   // false

// 字母"a"不能转换成合理的数值，因此就被转换成了 NaN。
var result = "a" < 3; // false
```


## 8⃣️ 相等操作符

> * 相等和不相等——先转换再比较;
> * 全等和不全等——仅比较而不转换;

### 相等（`==`）和不相等（`!=`）

在转换不同的数据类型时，相等和不相等操作符遵循下列基本规则:
* 如果有一个操作数是布尔值，则在比较相等性之前先将其转换为数值——`false` 转换为 0，而 `true` 转换为 1。
* 如果一个操作数是字符串，另一个操作数是数值，在比较相等性之前先将字符串转换为数值;
* 如果一个操作数是对象，另一个操作数不是，则调用对象的 `valueOf()` 方法，用得到的基本类型值按照前面的规则进行比较;
* `null` 和 `undefined` 是相等的。
* 要比较相等性之前，不能将 `null` 和 `undefined` 转换成其他任何值。
* 如果有一个操作数是 `NaN`，则相等操作符返回 `false`，而不相等操作符返回 `true`。重要提示：即使两个操作数都是 `NaN`，相等操作符也返回 `false`；因为按照规则，`NaN` 不等于 `NaN`。
* 如果两个操作数都是对象，则比较它们是不是同一个对象。如果两个操作数都指向同一个对象，则相等操作符返回 `true`；否则，返回 `false`。

```JavaScript
null == undefined // true，类似的值
null === undefined // false，不同类型的值
"NaN" == NaN    // false
5 == NaN        // false
NaN == NaN      // false
NaN != NaN      // true
false == 0      // true
true == 1       // true
true == 2       // false
undefined == 0  // false
null == 0       // false
"5" == 5        // true
```

### 全等（`===`）和不全等（`!==`）

* 相等（`==`）：如果两个操作数类型不同，会自动强制转换为相等类型的操作数，再进行比较（**强制转型**）。
* 全等（`===`）：两个操作数未经转换就相等的情况下才返回 `true`。

## 9⃣️ 条件操作符

条件操作符也常称为三元运算符：

```javascript
variable = boolean_expression ? true_value : false_value;
```

## 🔟 赋值操作符 =

复合赋值操作符：在等于号(=)前面再添加乘性操作符、加性操作符或位操作符。
*=、/=、%=、+=、-=、<<=、>>=、>>>=、

复合赋值操作符可以**简化赋值操作**，但不会带来任何性能的提升。


## 11. 逗号操作符

逗号操作符多用于声明多个变量。

```javascript
// 1.在一条语句中执行多个操作，声明多个变量
var num1=1, num2=2, num3=3;

// 2.赋值，总是返回表达式中的最后一项
var num = (5, 1, 4, 8, 0); // num 的值为 0
```



# 语句

![JavaScript 中的语句](https://upload-images.jianshu.io/upload_images/2648731-3624100068bbb442.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)


## if 语句

ECMAScript 会自动调用 `Boolean()` 转换函数将 `if` 表达式中的条件语句转换为一个布尔值。

```javascript
if (m === 0) {
  // ...
} else if (m === 1) {
  // ...
} else if (m === 2) {
  // ...
} else {
  // ...
}
```



## switch 语句

ECMAScript 中 `switch` 的特点：
* 可以在 `switch` 语句中使用**任何数据类型**。
* 每个 `case` 值可以是常量、变量、表达式。
* `switch` 语句在比较值时使用的是**全等操作符**（`===`），因此不会发生类型转换。



## while 语句

`while` 语句属于**前测试循环语句**，也就是说，在循环体内的代码被执行之前，就会对出口条件求值。因此，循环体内的代码有可能永远不会被执行。



## do-while 语句

`do-while` 语句是一种**后测试循环语句**，即只有在循环体中的代码执行之后，才会测试出口条件。换句话说，在对条件表达式求值之前，循环体内的代码至少会被执行一次。



## for 语句

`for` 语句也是一种**前测试循环语句**，但它具有在执行循环之前初始化变量和定义循环后要执行的代码的能力。

由于 ECMAScript 中不存在块级作用域，因此在循环内部定义的变量也可以在外部访问到。

```javascript
// 即使 i 是在循环内部定义的一个变量，但在循环外部仍然可以访问到它。
var count = 10;
for (var i = 0; i < count; i++) {
    console.log(i);
}
console.log(i); // 10
```


## for-in 语句

如果表示要迭代的对象的变量值为 `null` 或 `undefined`，`for-in` 语句会抛出错误，比如数组中间有一个对象为空。ECMAScript 5 更正了这一行为，对这种情况不再抛出错误，而只是不执行循环体。

💡 建议在使用 for-in 循环之前，先检测确认该对象的值不是 `null` 或 `undefined`。



## label 语句

* JavaScript 语言可以在代码中添加 `label` 标签，相当于定位符，用于跳转到程序的任意位置。
* 标签通常与 `break` 语句和 `continue` 语句配合使用，跳出特定的循环。
* 建议如果使用 `label` 语句，一定要使用描述性的标签，同时不要嵌套过多的循环。

```javascript
// 语法
label: statement
```



## with 语句

* with 语句的作用是将代码的作用域设置到一个特定的对象中。
* 主要目的：简化多次编写同一个对象的工作。

```javascript
// 语法
with (expression) statement;

// 示例：
// 把变量都包含在 location 对象中。
with(location) {
    var qs = search.substring(1);
    var hostName = hostname;
    var url = href;
}
```

> ⚠️
> 大量使用 `with` 语句会导致性能下降，同时也会给调试代码造成困难，因此在开发大型应用程序时，不建议使用 `with` 语句。



# 函数

通过函数可以封装任意多条语句，而且可以在任何地方、任何时候调用执行。

ECMAScript 中的函数在定义时不必指定是否返回值。

```javascript
// 定义
// 关键字 函数名(参数)
function functionName(arg0, arg1,...argN) {
    statments
}

// 函数声明
function sayHi(num1, num2) {
    alert("Hello " + arguments[0] + "," + arguments[1]); // 用类似数组的方式访问传入的参数
    alert(arguments.length); // 传入参数的个数

    if(arguments.length == 1) {
        alert(arguments[0] + 10);
    }else if {
        alert(arguments[0] + num2);
    }
}

// 函数体内通过 arguments 对象访问参数数组。
// arguments 与 命名参数的内存空间是相互独立的；
// ***特性：单向影响***：修改 arguments 的值会自动映射同步到命名参数上，反之则不能；
// arguments 对象的长度由传入的参数个数决定；
```

## 参数

* ECMAScript 函数的重要特点：**命名的参数只是提供便利，但不是必须的。**
* ECMAScript 函数中的参数在内部是用一个**数组**来表示的。在函数体内可以通过 `arguments` 对象来访问这个参数数组，从而获取传递给函数的每一个参数。
* `arguments` 对象的长度是由**传入的参数个数**决定的，不是由定义函数时的命名参数的个数决定的。
* `arguments` 对象可以与命名参数一起使用。而且 `arguments` 对象的值永远与对应命名参数的值保持同步，但它们的内存空间是独立的。
* 没有传递值的命名参数将自动被赋予 `undefined` 值。
* 由于不存在函数签名的特性，ECMAScript 函数不能重载。（就是函数名相同、参数的个数不同的函数）。
* 无须指定函数的返回值，因为任何 ECMAScript 函数都可以在任何时候返回任何值。实际上，未指定返回值的函数返回的是一个特殊的 `undefined` 值。
* 函数中 `arguments.length` 属性可以返回传入参数的个数，~~实际上，函数的 `length` 属性与实际传入的参数个数无关，只反映函数预期传入的参数个数~~。
* **严格模式**对如何使用 `arguments` 对象做出了一些限制。首先，在函数体内直接对 `arguments` 对象中的元素赋值是无效的。也就是说，即使把 arguments[1] 设置为 10，该函数的参数值仍然还是 `undefined`。其次，重写 `arguments` 的值会导致语法错误（代码将不会执行）。

```javascript
function howManyArgs () {
    console.log(arguments.length);
}

howManyArgs("string", 45);    //2
howManyArgs();                //0
howManyArgs(12);              //1
```


## 函数的传值

函数参数如果是原始类型的值（数值、字符串、布尔值），传递方式是**传值传递**（passes by value）。这意味着，在函数体内修改参数值，不会影响到函数外部。

如果函数参数是复合类型的值（数组、对象、其他函数），传递方式是**传址传递**（pass by reference）。也就是说，传入函数的原始值的地址，因此在函数内部修改参数，将会影响到原始值。



## 函数的作用域

函数执行时所在的作用域，是定义时的作用域，而不是调用时所在的作用域。

```javascript
var a = 1;
// 函数 x 的作用域绑定在外层
var x = function () {
  console.log(a);
};

function f() {
  var a = 2;
  x(); // x 函数执行时，所处的是定义时的作用域。
}

f() // 1
```

## 立即调用的函数表达式（IIFE）

在定义函数之后，立即调用该函数：

```javascript
(function(){ /* code */ }());
```
