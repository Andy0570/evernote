# this 全面解析

* `this` 提供了一种更优雅的方式来隐式“传递”一个对象引用，因此可以将 API 设计得更加简洁并且易于复用。
* `this` 既不指向**函数自身**也不指向函数的**词法作用域**。
* `this` 是在函数被调用时发生的绑定，它指向什么完全取决于**函数在哪里被调用**（函数的调用方法）。
* `this` 是在运行时进行绑定的，并不是在编写时绑定，它的上下文取决于函数调用时的各种条件。`this` 的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式。
* 当一个函数被调用时，会创建一个活动记录（有时候也称为执行上下文）。这个记录会包含函数在哪里被调用（调用栈）、函数的调用方法、传入的参数等信息。`this` 就是记录的其中一个属性，会在函数执行的过程中用到。

## 调用位置

**调用位置**：函数在代码中被**调用**的位置（而不是声明的位置）。
**调用栈**：为了到达当前执行位置所调用的所有函数。

```JavaScript
function baz() {
    // 当前调用栈是:baz
    // 因此，当前调用位置是全局作用域
    console.log( "baz" );
    bar(); // <-- bar 的调用位置
}

function bar() {
    // 当前调用栈是 baz -> bar
    // 因此，当前调用位置在 baz 中
    console.log( "bar" );
    foo(); // <-- foo 的调用位置
}

function foo() {
    // 当前调用栈是 baz -> bar -> foo
    // 因此，当前调用位置在 bar 中
    console.log( "foo" );
}

baz(); // <-- baz 的调用位置
```

## 绑定规则

函数的执行过程中调用位置如何决定 `this` 的绑定对象？

### 1⃣️ 默认绑定

非严格模式下，函数调用类型为「独立函数调用」时，执行默认绑定。

对于默认绑定来说，决定 `this` 绑定对象的并不是**调用位置**是否处于严格模式，而是**函数体**是否处于严格模式。如果函数体处于严格模式，`this` 会被绑定到 `undefined`，否则 `this` 会被绑定到全局对象。

```JavaScript
function foo () {
  // 函数调用时应用了 this 的默认绑定，因此 this 指向全局对象。
  console.log(this.a);
}

// 声明在全局作用域中的变量（比如 var a = 2）就是全局对象的一个同名属性。
var a = 2;

// foo() 直接使用不带任何修饰的函数引用进行调用，因此只能使用默认绑定，无法应用其他规则。
// 注意：只有 foo() 运行在非严格模式下时，默认绑定才能绑定到全局对象。
foo(); // 2
```

如果使用严格模式（`strict mode`），那么全局对象将无法使用默认绑定，因此 `this` 会绑定到 `undefined`:

```JavaScript
function foo () {
  "use strict"; // 函数体处于严格模式
  console.log(this.a);
}

var a = 2;

foo(); // TypeError: `this` is `undefined`
```

### 2⃣️ 隐式绑定

调用位置是否有上下文对象？或者说是否被某个对象拥有或者包含？

当函数引用有上下文对象时，隐式绑定规则会把函数调用中的 `this` 绑定到这个上下文对象。

隐式绑定方式：在一个对象内部包含一个指向函数的属性，并通过这个属性间接引用函数，从而把 `this` 间接（隐式）绑定到这个对象上。

```JavaScript
function foo () {
  // 因为调用 foo() 时 this 被绑定到 obj，因此 this.a 和 obj.a 是一样的。
  console.log(this.a);
}

var obj = {
  a: 2,
  foo: foo // foo 被当作引用属性添加到 obj 中
};

// 调用位置使用 obj 上下文来引用函数，因此你可以说函数被调用时 obj 对象“拥有”或者“包含”它。
obj.foo(); // 2
```

对象属性引用链中只有最顶层或者说最后一层会影响调用位置。

```JavaScript
function foo () {
  console.log(this.a);
}

var obj2 = {
  a: 42,
  foo: foo
};

var obj1 = {
  a: 2,
  obj2: obj2
};

obj1.obj2.foo(); // 42
```

#### 隐式丢失

一个最常见的 `this` 绑定问题就是**被隐式绑定的函数会丢失绑定对象**，也就是说它会应用**默认绑定**，从而把 `this` 绑定到全局对象或者 `undefined` 上，取决于是否是**严格模式**。

```JavaScript
function foo () {
  console.log(this.a);
}

var obj = {
  a: 2,
  foo: foo
};

var bar = obj.foo; // 函数别名

var a = "oops, global"; // a 是全局对象的属性

// 虽然 bar 是 obj.foo 的一个引用，但是实际上，它引用的是 foo 函数本身，
// 因此此时的 bar() 其实是一个不带任何修饰的函数调用，因此应用了默认绑定。
bar(); // "oops, global"
```

另外，调用回调函数的函数可能会修改 `this`。

```js
function foo () {
  console.log(this.a);
}

function doFoo (fn) {
  // fn 其实引用的是 foo
  fn(); // <-- 调用位置!
}

var obj = {
  a: 2,
  foo: foo
};

var a = "oops, global"; // a 是全局对象的属性

// 参数传递其实就是一种隐式赋值，因此我们传入函数时也会被隐式赋值
// 回调函数丢失 this 绑定
doFoo(obj.foo); // "oops, global"
```



### 3⃣️ 显式绑定

如果我们不想在对象内部包含函数引用，而想**在某个对象上强制调用函数**，则可以使用函数的 `call(..)` 和 `apply(..)` 方法。

它们的第一个参数是一个对象，它们会把这个对象绑定到 `this`，接着在调用函数时指定这个 `this`。因为你可以直接指定 `this` 的绑定对象，因此我们称之为**显式绑定**。

```JavaScript
function foo () {
  console.log(this.a);
}

var obj = {
  a: 2
};

// 调用 foo 时强制把它的 this 绑定到 obj 上
foo.call(obj); // 2
```

#### 硬绑定：在函数内部调用显式绑定

硬绑定可以解决绑定丢失问题。

```JavaScript
function foo () {
  console.log(this.a);
}

var obj = {
  a: 2
};

var bar = function () {
  // 硬绑定：在函数内部强制把 foo 的 this 绑定到 obj
  foo.call(obj); 
};

bar(); // 2
setTimeout(bar, 100); // 2

// 硬绑定的 bar 不可能再修改它的 this
bar.call(window); // 2
```


硬绑定的典型应用场景就是创建一个**包裹函数**，传入所有的参数并返回接收到的所有值：

```js
function foo(something) {
	console.log( this.a, something );
	return this.a + something;
}

var obj = {
	a: 2
};

var bar = function() {
	return foo.apply( obj, arguments );
};

var b = bar( 3 ); // 2 3
console.log( b ); // 5
```


另一种使用方法是创建一个可以重复使用的**辅助函数**:

```js
function foo (something) {
  console.log(this.a, something);
  return this.a + something;
}

// 简单的辅助绑定函数
function bind (fn, obj) {
  return function () {
    return fn.apply(obj, arguments);
  };
}

var obj = {
  a: 2
};

var bar = bind(foo, obj);

var b = bar(3); // 2 3
console.log(b); // 5
```

由于硬绑定是一种非常常用的模式，所以在 ES5 中提供了内置的方法 `Function.prototype.bind`，它的用法如下:

```JavaScript
function foo (something) {
  console.log(this.a, something);
  return this.a + something;
}

var obj = {
  a: 2
};

// bind(..) 会返回一个硬编码的新函数，它会把参数设置为 this 的上下文并调用原始函数。
var bar = foo.bind(obj);

var b = bar(3); // 2 3
console.log(b); // 5
```

#### API 调用的上下文

第三方库的许多函数，以及 JavaScript 语言和宿主环境中许多新的内置函数，都提供了一个可选的参数，通常被称为“上下文”(context)，其作用和 `bind(..)` 一样，确保你的回调函数使用指定的 `this`。

### 4⃣️ `new` 绑定

> JavaScript 中的构造函数：
> 
> 在 JavaScript 中，构造函数只是一些使用 `new` 操作符时被调用的函数。它们并不会属于某个类，也不会实例化一个类。实际上，它们甚至都不能说是一种特殊的函数类型，它们只是被 `new` 操作符调用的普通函数而已。
> 
> 实际上并不存在所谓的“构造函数”，只有对于函数的“构造调用”。


使用 `new` 来调用函数，或者说发生构造函数调用时，会自动执行下面的操作：
1. 创建（或者说构造）一个全新的对象。
2. 这个新对象会被执行[[原型]]连接。
3. 这个新对象会绑定到函数调用的 `this`。
4. 如果函数没有返回其他对象，那么 `new` 表达式中的函数调用会自动返回这个新对象。

```js
function foo(a) {
    this.a = a;
}

// 构造一个新对象并把它绑定到 foo(..) 调用中的 this 上。
var bar = new foo( 2 );
console.log( bar.a ); // 2
```

## 绑定优先级

`new` 绑定 > 显式绑定 > 隐式绑定 > 默认绑定

### 判断 `this`

根据优先级来判断函数在某个调用位置应用的是哪条规则。可以按照下面的顺序来进行判断:
1. 函数是否在 `new` 中调用（new 绑定）？如果是的话 `this` 绑定的是新创建的对象。
     `var bar = new foo()`
2. 函数是否通过 `call`、`apply`（显式绑定）或者硬绑定调用？如果是的话，`this` 绑定的是指定的对象。
     `var bar = foo.call(obj2)`
3. 函数是否在某个上下文对象中调用（隐式绑定）？如果是的话，`this` 绑定的是那个上下文对象。
     `var bar = obj1.foo()`
4. 如果都不是的话，使用默认绑定。如果在严格模式下，就绑定到 `undefined`，否则绑定到全局对象。
     `var bar = foo()`

## 绑定例外

### 被忽略的 `this`

如果你把 `null` 或者 `undefined` 作为 `this` 的绑定对象传入 `call`、`apply` 或者 `bind`，这些值在调用时会被忽略，实际应用的是**默认绑定规则**:

```JavaScript
function foo () {
  console.log(this.a);
}

var a = 2;

// 把 null 作为参数传入 call()，执行的是默认绑定
foo.call(null); // 2
```

传入 `null` 的常见场景：
```JavaScript
/* 
 * 以下两种方法都需要传入一个参数当作 this 的绑定对象，
 * 如果函数不关心 this，仍然需要传入一个占位符，因此传入 null
 * 导致的副作用：默认绑定规则会把 this 绑定到全局对象。
 */

function foo (a, b) {
  console.log("a:" + a + ", b:" + b);
}

// 使用 `apply(..)` 来“展开”一个数组，并当作参数传入一个函数。
foo.apply(null, [2, 3]); // a:2, b:3

// `bind(..)` 可以对参数进行柯里化(预先设置一些参数)
var bar = foo.bind(null, 2);
bar(3); // a:2, b:3
```


#### 更安全的 `this`

一种“更安全”的做法是传入一个特殊的对象，把 `this` 绑定到这个对象不会对你的程序产生任何副作用。就像网络(以及军队)一样，我们可以创建一个“DMZ”（demilitarized zone，非军事区）对象——它就是一个空的非委托的对象。

在 JavaScript 中创建一个空对象最简单的方法都是 `Object.create(null)`

一定要注意，有些调用可能在无意中使用默认绑定规则。如果想“更安全”地忽略 `this` 绑定，你可以使用一个 DMZ 对象，比如 `ø = Object.create(null)`，以保护全局对象。

```JavaScript
function foo(a,b) {
console.log( "a:" + a + ", b:" + b );
}
// 我们的 DMZ 空对象
//  ø 表示 “我希望 this 是空”
var ø = Object.create( null ); 

// 把数组展开成参数
foo.apply( ø, [2, 3] ); // a:2, b:3

// 使用 bind(..) 进行柯里化 
var bar = foo.bind( ø, 2 ); 
bar( 3 ); // a:2, b:3
```

### 间接引用

你有可能（有意或者无意地）创建一个函数的“间接引用”，在这种情况下，调用这个函数会应用默认绑定规则。

间接引用最容易在**赋值**时发生：

```js
function foo() {
    console.log( this.a );
}

var a = 2;
var o = { a: 3, foo: foo };
var p = { a: 4 };

o.foo(); // 3

// 赋值表达式 p.foo = o.foo 的返回值是目标函数的引用，因此调用位置是 foo() 而不是 p.foo() 或者 o.foo()。
// (p.foo = o.foo) 是一个函数体，它处于全局作用域下。
(p.foo = o.foo)(); // 2
```

### 软绑定

硬绑定会大大降低函数的灵活性，使用硬绑定之后就无法使用隐式绑定或者显式绑定来修改 `this`。

如果可以给默认绑定指定一个全局对象和 `undefined` 以外的值，那就可以实现和硬绑定相同的效果，同时保留隐式绑定或者显式绑定修改 `this` 的能力。

```JavaScript
// 它会对指定的函数进行封装，首先检查调用时的 this，
// 如果 this 绑定到全局对象或者 undefined，那就把指定的默认对象 obj 绑定到 this，否则不会修改 this。
if (!Function.prototype.softBind) {
  Function.prototype.softBind = function (obj) {
    var fn = this,
      curried = [].slice.call(arguments, 1),
      bound = function bound () {
        return fn.apply(
          (!this ||
            (typeof window !== "undefined" &&
              this === window) ||
            (typeof global !== "undefined" &&
              this === global)
          ) ? obj : this,
          curried.concat.apply(curried, arguments)
        );
      };
    bound.prototype = Object.create(fn.prototype);
    return bound;
  };
}
```

## 箭头函数

箭头函数不使用 `this` 的四种标准规则，而是根据**外层（函数或者全局）作用域**来决定 `this`。

具体来说，箭头函数会继承外层函数调用的 `this` 绑定（无论 `this` 绑定到什么）。这其实和 ES6 之前代码中的 `self = this` 机制一样。

```JavaScript
function foo () {
  // 返回一个箭头函数
  return (a) => {
    // this 继承自 foo()
    console.log(this.a);
  };
}

var obj1 = {
  a: 2
};

var obj2 = {
  a: 3
};

// foo() 内部创建的箭头函数会捕获调用时 foo() 的 this。
// 由于 foo() 的 this 绑定到 obj1，bar(引用箭头函数)的 this 也会绑定到 obj1。
// 注意：箭头函数的绑定无法被修改。
var bar = foo.call(obj1);
bar.call(obj2); // 2, 不是 3 !
```

箭头函数可以像 `bind(..)` 一样确保函数的 `this` 被绑定到指定对象，此外，其重要性还体现在它用更常见的词法作用域取代了传统的 `this` 机制。


# 对象

## 语法

对象可以通过两种形式定义：
1. 字面量语法；
2. 构造函数。

区别：在字面量语法中，你可以添加多个键/值对，但是在构造函数中你必须逐个添加属性。

## 类型

在 JavaScript 中一共有六种语言类型：
* `string`
* `number`
* `boolean`
* `null`
* `undefined`
* `object`

注意，简单基本类型（`string`、`boolean`、`number`、`null` 和 `undefined`）本身并不是对象。 `null` 有时会被当作一种对象类型，但是这其实只是语言本身的一个 bug，即对 `null` 执行 `typeof null` 时会返回字符串 "object"。实际上，`null` 本身是基本类型。

JavaScript 中有许多特殊的对象子类型，我们可以称之为**复杂基本类型**：
* 函数
* 数组

## 内置对象

JavaScript 中还有一些对象子类型，通常被称为**内置对象**。

* `String`
* `Number`
* `Boolean`
* `Object`
* `Function`
* `Array`
* `Date`
* `RegExp`
* `Error`

对于 `Object`、`Array`、`Function` 和 `RegExp`（正则表达式）来说，无论使用文字形式还是构造形式，它们都是**对象**，不是字面量。

```js
var strPrimitive = "I am a string";

// 引擎自动把字面量转换成 String 对象，所以可以访问属性和方法。
console.log( strPrimitive.length );  // 13
console.log( strPrimitive.charAt( 3 ) );  // "m"
```

`null` 和 `undefined` 没有对应的构造形式，它们只有文字形式。相反，`Date` 只有构造，没有文字形式。

建议只在需要那些额外选项时使用**构造形式**。

## 内容

对象的内容是由一些存储在特定命名位置的（任意类型的）值组成的，我们称之为**属性**。

访问对象属性的方式：
* 点语法；
* [“a”] 键访问方式。

### 可计算属性名

通过表达式来计算属性名。

ES6 增加了可计算属性名，可以在文字形式中使用 [] 包裹一个表达式来当作属性名:

```js
var prefix = "foo";

var myObject = {
    [prefix + "bar"]: "hello",
    [prefix + "baz"]: "world"
};

myObject["foobar"]; // hello
myObject["foobaz"]; // world
```

可计算属性名最常用的场景可能是 ES6 的符号(Symbol)。

### 属性与方法

在其他语言中，属于对象（也被称为“类”）的**函数**通常被称为“方法”，但是 JavaSCript 中有区分。

**从技术角度来说，函数永远不会“属于”一个对象。**

无论返回值是什么类型，每次访问对象的属性就是**属性访问**。如果属性访问返回的是一个函数，那它也并不是一个“方法”。属性访问返回的函数和其他函数没有任何区别（除了可能发生的隐式绑定 `this`）。

即使你在对象的文字形式中声明一个函数表达式，这个函数也不会“属于”这个对象——它们只是对于相同函数对象的多个引用。


### 数组

数组有一套更加结构化的值存储机制（不过仍然不限制值的类型）。数组期望的是**数值下标**，也就是说值存储的位置（通常被称为索引）是整数。

数组也是**对象**，所以虽然每个下标都是整数，你仍然可以给数组添加**属性**。

最好只用对象来存储键/值对，只用数组来存储数值下标/值对。

如果你试图向数组添加一个属性，但是属性名“看起来”像一个数字，那它会变成一个数值下标（因此会修改数组的内容而不是添加一个属性）。

### 复制对象

JavaScript 没有默认的复制算法来复制对象，对象只能引用。

#### 浅复制

ES6 定义了 `Object.assign(..)` 方法来实现浅复制。

`Object.assign(..)` 方法的第一个参数是目标对象，之后还可以跟一个或多个源对象。它会遍历一个或多个源对象的所有可枚举（enumerable）的自有键（owned key）并把它们复制（使用 = 操作符赋值）到目标对象，最后返回目标对象。

```js
function anotherFunction () { /*..*/ }

var anotherObject = {
  c: true
};

var anotherArray = [];

var myObject = {
  a: 2,
  b: anotherObject,	// 这是引用，而不是复制
  c: anotherArray,	// 引用
  d: anotherFunction
};

var newObj = Object.assign( {}, myObject );

newObj.a;	 // 2
newObj.b === anotherObject;	// true
newObj.c === anotherArray;	// true
newObj.d === anotherFunction;   // true
```

### 属性描述符

从 ES5 开始，所有的属性都具备了属性描述符。

在创建普通属性时属性描述符会使用默认值，我们也可以使用 `Object.defineProperty(..)` 来添加一个新属性或者修改一个已有属性（如果它是 `configurable`）并对特性进行设置。

```js
var myObject = {};

Object.defineProperty( myObject, "a", {
    value: 2,
    writable: true, // 是否可以修改属性的值
    configurable: true, // 可配置，单向操作，不可撤回。
    enumerable: true    // 属性是否可以被枚举
} );

myObject.a; // 2
```

* 除了无法修改，`configurable:false` 还会禁止删除这个属性。
* 不要把 `delete` 看作一个释放内存的工具（就像 C/C++ 中那样），它就是一个删除对象属性的操作，仅此而已。

### 不变性

在 ES5 中可以通过很多种方法来实现让属性或者对象是不可改变的。

所有的方法创建的都是**浅不变形**，也就是说，它们只会影响目标对象和它的直接属性。如果目标对象引用了其他对象（数组、对象、函数，等），其他对象的内容不受影响，仍然是可变的。

```js
myImmutableObject.foo; // [1,2,3]
myImmutableObject.foo.push( 4 );
myImmutableObject.foo; // [1,2,3,4]
```

#### 1. 对象常量

结合 `writable:false` 和 `configurable:false` 就可以创建一个真正的常量属性（不可修改、 重定义或者删除）：

```js
var myObject = {};

Object.defineProperty( myObject, "FAVORITE_NUMBER", {
    value: 42,
    writable: false,
    configurable: false
} );
```

#### 2. 禁止扩展

**禁止一个对象添加新属性并且保留已有属性，可以使用 `Object.preventExtensions(..)`**:

```js
var myObject = {
	a: 2
};

Object.preventExtensions( myObject );

myObject.b = 3;
myObject.b; // undefined
```

将一个对象设置为禁止扩展后，在非严格模式下，创建属性 `b` 会静默失败。在严格模式下，将会抛出 `TypeError` 错误。


#### 3. 密封

`Object.seal(..) `会创建一个“密封”的对象，这个方法实际上会在一个现有对象上调用 `Object.preventExtensions(..)` 并把所有现有属性标记为 `configurable:false`。

所以，密封之后不仅不能添加新属性，也不能重新配置或者删除任何现有属性（虽然可以修改属性的值）。

#### 4. 冻结

`Object.freeze(..)` 会创建一个冻结对象，这个方法实际上会在一个现有对象上调用 `Object.seal(..)` 并把所有“数据访问”属性标记为 `writable:false`，这样就无法修改它们的值。

### [[get]]

1. 对象默认的内置 `[[Get]]` 操作首先在对象中查找是否有名称相同的属性，如果找到就会返回这个属性的值。
2. 如果没有找到名称相同的属性，就遍历可能存在的 `[[Prototype]]` 链，也就是原型链。
3. 如果无论如何都没有找到名称相同的属性，那 `[[Get]]` 操作会返回值 `undefined`。


### [[put]]

[[Put]] 被触发时，实际的行为取决于许多因素，包括对象中是否已经存在这个属性（最重要的因素）。
如果已经存在这个属性，[[Put]] 算法大致会检查下面这些内容：
1. 属性是否是访问描述符？如果是并且存在 `setter` 就调用 `setter`。
2. 属性的数据描述符中 `writable` 是否是 `false` ？如果是，在非严格模式下静默失败，在
严格模式下抛出 `TypeError` 异常。
3. 如果都不是，将该值设置为属性的值。

如果对象中不存在这个属性，[[Put]] 操作会更加复杂。

### Getter 和 Setter

对象默认的 [[Put]] 和 [[Get]] 操作分别可以控制属性值的设置和获取。

在 ES5 中可以使用 `getter` 和 `setter` 部分改写默认操作，但是只能应用在**单个属性**上，无法 应用在**整个对象**上。

`getter` 是一个隐藏函数，会在获取属性值时调用。

`setter` 也是一个隐藏函数，会在设置属性值时调用。

`setter` 会覆盖单个属性默认的 [[Put]] （也被称为赋值）操作。通常来说 `getter` 和 `setter` 是成对出现的（只定义一个的话通常会产生意料之外的行为）：

```JavaScript
var myObject = {
  // define a getter for `a`
  get a () {
    return this._a_;
  },

  // define a setter for `a`
  set a (val) {
    this._a_ = val * 2;
  }
};

myObject.a = 2;

myObject.a; // 4
```

### 存在性

```JavaScript
var myObject = {
    a: undefined
};

myObject.a; // undefined
myObject.b; // undefined
```

如 myObject.a 的属性访问返回值可能是 `undefined`，但是这个值有可能是属性中存储的值为 `undefined`，也可能是因为属性不存在所以返回 `undefined`。那么如何区分这两种情况呢?

**在不访问属性值的情况下判断对象中是否存在这个属性**:

```JavaScript
var myObject = {
  a: 2
};

// in 操作符会检查属性是否在对象及其 [[Prototype]] 原型链中
("a" in myObject);  // true
("b" in myObject);  // false

// hasOwnProperty(..) 只会检查属性是否在 myObject 对象中，不会检查 [[Prototype]] 链。
myObject.hasOwnProperty("a");	// true
myObject.hasOwnProperty("b");	// false
```

所有的普通对象都可以通过对于 `Object.prototype` 的委托来访问 `hasOwnProperty(..)`， 但是 有的对象可能没有连接到 `Object.prototype`（通过 `Object.create(null)` 来创建）。在这种情况下，形如 `myObejct.hasOwnProperty(..)` 就会失败。
`Object.prototype.hasOwnProperty.call(myObject,"a")`，它借用基础的 `hasOwnProperty(..)` 方法并把它显式绑定到 myObject 上。

#### 枚举

在**数组**上应用 `for..in` 循环有时会产生出人意料的结果，因为这种枚举不仅会包含所有**数值索引**，还会包含所有**可枚举属性**。最好只在**对象**上应用 `for..in` 循环，如果要遍历**数组**就使用传统的 `for` 循环来遍历数值索引。

* `propertyIsEnumerable(..)` 会检查给定的属性名是否直接存在于对象中（而不是在原型链上）并且满足 `enumerable:true`。
* `Object.keys(..)` 会返回一个数组，包含所有可枚举属性。
* `Object.getOwnPropertyNames(..)` 会返回一个数组，包含所有属性，无论它们是否可枚举。

```JavaScript
var myObject = {};

Object.defineProperty(
  myObject,
  "a",
  // 让 a 像普通属性一样可以枚举
  { enumerable: true, value: 2 }
);

Object.defineProperty(
  myObject,
  "b",
  // 让 b 不可枚举
  { enumerable: false, value: 3 }
);

myObject.propertyIsEnumerable("a"); // true
myObject.propertyIsEnumerable("b"); // false

Object.keys(myObject); // ["a"] 返回可枚举属性
Object.getOwnPropertyNames(myObject); // ["a", "b"] 返回所有属性
```

`in` 和 `hasOwnProperty(..)` 的区别在于是否查找 [[Prototype]] 链。
然而，`Object.keys(..)` 和 `Object.getOwnPropertyNames(..)` 都只会查找对象直接包含的属性。

## 遍历

* `for..in` 循环可以用来遍历对象的可枚举属性列表（包括 [[Prototype]] 链）。
* 对于数值索引的数组来说，可以使用标准的 `for` 循环来遍历值（遍历属性的值）。
* `forEach(..)` 会遍历数组中的所有值并忽略回调函数的返回值。
* `every(..)` 会一直运行直到回调函数返回 `false`（或者“假”值）。
* `some(..)` 会一直运行直到回调函数返回 `true`（或者“真”值）。

如何直接遍历**值**而不是数组下标？ES6 增加了一种用来遍历数组的 `for..of` 循环语法(如果对象本身定义了迭代器的话也可以遍历对象)：

```js
var myArray = [ 1, 2, 3 ];

for (var v of myArray) {
    console.log( v );
}
// 1
// 2
// 3
```

`for..of` 循环首先会向被访问对象请求一个迭代器对象，然后通过调用迭代器对象的 `next()` 方法来遍历所有返回值。

你可以使用 ES6 的 `for..of` 语法来遍历数据结构（数组、对象，等等）中的值，`for..of` 会寻找内置或者自定义的 `@@iterator` 对象并调用它的 `next()` 方法来遍历数据值。


# 混合对象——“类”

在软件设计中，类是一种可选的设计模式，JavaScript 提供了一些近似类的语法。


## 构造函数

类实例是由一个特殊的类方法构造的，这个方法名通常和类名相同，被称为**构造函数**。这个方法的任务就是初始化实例需要的所有信息（状态）。

类构造函数属于类，而且通常和类同名。此外，构造函数大多需要用 `new` 来调，这样语言引擎才知道你想要构造一个新的类实例。

## 类的继承

在面向类的语言中，你可以先定义一个类，然后定义一个继承前者的类。后者通常被称为“子类”，前者通常被称为“父类”。

定义好一个子类之后，相对于父类来说它就是一个独立并且完全不同的类。子类会包含父类行为的原始副本，但是也可以重写所有继承的行为甚至定义新行为。

## 多态

多态：父类的通用行为可以被子类用更特殊的行为重写。

相对多态：任何方法都可以引用继承层次中高层的方法（无论高层的方法名和当前方法名是否相同）。

多态的另一个方面是，在继承链的不同层次中一个方法名可以被多次定义，当调用方法时会自动选择合适的定义。


## 多重继承

JavaScript 不提供多重继承的功能。

## 混入：模拟类的复制行为

在继承或者实例化时，JavaScript 的对象机制并不会自动执行复制行为。简单来说，JavaScript 中只有对象，并不存在可以被实例化的“类”。一个对象并不会被复制到其他对象，它们会被关联起来。

由于在其他语言中类表现出来的都是复制行为，因此 JavaScript 开发者也想出了一个方法来**模拟类的复制行为**，这个方法就是**混入**。

两种类型的混入：
1. 显式
2. 隐式

### 1⃣️ 显式混入

手动实现复制功能。这个功能在许多库和框架中被称为 `extend(..)`，但是为了方便理解我们称之为 `mixin(..)`。

```JavaScript
// 非常简单的 mixin(..) 例子 :
function mixin (sourceObj, targetObj) {
  // 遍历源对象中的 key
  for (var key in sourceObj) {
    // 只会在不存在的情况下复制
    if (!(key in targetObj)) {
      targetObj[key] = sourceObj[key];
    }
  }

  return targetObj;
}

var Vehicle = {
  engines: 1,

  ignition: function () {
    console.log("Turning on my engine.");
  },

  drive: function () {
    this.ignition();
    console.log("Steering and moving forward!");
  }
};

/*
 * 从技术角度来说，函数实际上没有被复制，复制的是函数引用。
 * 所以，Car 中的属性 ignition 只是从 Vehicle 中复制过来的对于 ignition() 函数的引用。
 * 相反，属性 engines 就是直接从 Vehicle 中复制了值 1。
 */
var Car = mixin(Vehicle, {
  wheels: 4,

  drive: function () {
    // 显式多态，使用 .call(this) 来确保 drive() 在 Car 对象的上下文中执行
    Vehicle.drive.call(this);
    console.log("Rolling on all " + this.wheels + " wheels!");
  }
});
```

#### 寄生式继承

```JavaSCript
// "Traditional JS Class" `Vehicle`
function Vehicle () {
  this.engines = 1;
}
Vehicle.prototype.ignition = function () {
  console.log("Turning on my engine.");
};
Vehicle.prototype.drive = function () {
  this.ignition();
  console.log("Steering and moving forward!");
};

// "Parasitic Class" `Car`
function Car () {
  // first, `car` is a `Vehicle`
  var car = new Vehicle();

  // now, let's modify our `car` to specialize it
  car.wheels = 4;

  // save a privileged reference to `Vehicle::drive()`
  var vehDrive = car.drive;

  // override `Vehicle::drive()`
  car.drive = function () {
    vehDrive.call(this);
    console.log("Rolling on all " + this.wheels + " wheels!");
  };

  return car;
}

var myCar = new Car();

myCar.drive();
// Turning on my engine.
// Steering and moving forward!
// Rolling on all 4 wheels!
```


### 2⃣️ 隐式混入

```JavaScript
var Something = {
  cool: function () {
    this.greeting = "Hello World";
    this.count = this.count ? this.count + 1 : 1;
  }
};

Something.cool();
Something.greeting; // "Hello World"
Something.count; // 1

var Another = {
  cool: function () {
    // 隐式把 Something 混入 Another
    Something.cool.call(this);
  }
};

Another.cool();
Another.greeting; // "Hello World"
Another.count; // 1 (not shared state with `Something`)
```

# 原型

## [[Prototype]]

`[[Prototype]]` 机制就是指对象中的一个内部链接引用另一个对象。

如果在第一个对象上没有找到需要的属性或者方法引用，引擎就会继续在 `[[Prototype]]` 关联的对象上进行查找。同理，如果在后者中也没有找到需要的引用就会继续查找它的 `[[Prototype]]`，以此类推。这一系列对象的链接被称为“原型链”。

JavaScript 中的对象有一个特殊的 `[[Prototype]]` 内置属性，其实就是**对于其他对象的引用**。几乎所有的对象在创建时 `[[Prototype]]` 属性都会被赋予一个非空的值。

对于默认的 `[[Get]]` 操作来说，如果无法在对象本身找到需要的属性，就会继续访问对象的 `[[Prototype]]` 链：

```JavaScript
var anotherObject = {
  a: 2
};

// 创建一个关联到 anotherObject 的对象
// myObject 对象的 [[Prototype]] 关联到了 anotherObject
var myObject = Object.create(anotherObject);

myObject.a; // 2
```

使用 `in` 操作符来检查属性在对象中是否存在时，同样会查找对象的整条原型链（无论属性是否可枚举）。

```JavaScript
var anotherObject = {
  a: 2
};

// 创建一个关联到 anotherObject 的对象
var myObject = Object.create(anotherObject);

for (var k in myObject) {
  console.log("found: " + k);
}
// found: a

("a" in myObject); // true
```

### Object.prototype

所有普通的 `[[Prototype]]` 链最终都会指向内置的 `Object.prototype`。


### 属性设置和屏蔽

```JavaScript
myObject.foo = "bar";
```

1. 如果 `myObject` 对象中包含名为 ``foo`` 的普通数据访问属性，这条赋值语句只会修改已有的属值。
2. 如果 `foo` 不是直接存在于 `myObject` 中，[[Prototype]] 链就会被遍历，类似 [[Get]] 操作。如果原型链上找不到 `foo`，`foo` 就会被直接添加到 `myObject` 上。
3. 如果属性名 `foo` 既出现在 `myObject` 中也出现在 `myObject` 的 [[Prototype]] 链上层，那么就会发生**屏蔽**。`myObject` 中包含的 `foo` 属性会屏蔽原型链上层的所有 `foo` 属性，因为 `myObject.foo` 总是会选择原型链中最底层的 `foo` 属性。

如果 `foo` 不直接存在于 `myObject` 中而是存在于原型链上层时, `myObject.foo = "bar"` 会出现的三种情况。

1. 如果在[[Prototype]]链上层存在名为foo的普通数据访问属性并且没有被标记为只读(`writable:false`)，那就会直接在 myObject 中添加一个名为 foo 的新属性，它是**屏蔽属性**。
2. 如果在[[Prototype]]链上层存在foo，但是它被标记为只读(`writable:false`)，那么无法修改已有属性或者在 myObject 上创建屏蔽属性。如果运行在严格模式下，代码会抛出一个错误。否则，这条赋值语句会被忽略。总之，**不会发生屏蔽**。
3. 如果在[[Prototype]]链上层存在foo并且它是一个setter，那就一定会调用这个 setter。foo 不会被添加到(或者说屏蔽于)myObject，也不会重新定义 foo 这个 setter。

## 类

JavaScript 中只有**对象**。

### “类”函数

所有的函数默认都会拥有一个名为 ``prototype`` 的公有并且不可枚举的属性，它会指向另一个对象。

继承意味着复制操作，JavaScript（默认）并不会复制对象属性。相反，JavaScript 会在两个对象之间创建一个关联，这样一个对象就可以通过委托访问另一个对象的属性和函数。 委托这个术语可以更加准确地描述 JavaScript 中对象的关联机制。

### 构造函数

```js
function Foo() {
    // ...
}

// constructor，这个属性引用的是对象关联的函数
Foo.prototype.constructor === Foo; // true

// 通过“构造函数”调用 new Foo() 创建的对象也有一个 .constructor 属性，指向 “创建这个对象的函数”。
var a = new Foo();
a.constructor === Foo; // true
```

在 JavaScript 中对于“构造函数”最准确的解释是，所有带 `new` 的函数调用。 

函数不是构造函数，但是当且仅当使用 `new` 时，函数调用会变成“构造函数调用”。

## 原型继承


## 对象关联


# 行为委托

行为委托认为对象之间是兄弟关系，互相委托，而不是父类和子类的关系。JavaScript 的 [[Prototype]] 机制本质上就是行为委托机制。也就是说，我们可以选择在 JavaScript 中努力实现类机制，也可以拥抱更自然的 [[Prototype]] 委托机制。

## 面向委托的设计

```JavaScript
// Task 对象，它不是类，也不是函数
// 它包含所有任务都可以使用（写作使用，读作委托）的具体行为
var Task = {
  setID: function (ID) { this.id = ID; },
  outputID: function () { console.log(this.id); }
};

// XYZ 是对象，它的 [[Prototype]] 委托了 Task 对象
// 让 XYZ 委托 Task
var XYZ = Object.create(Task);

XYZ.prepareTask = function (ID, Label) {
  this.setID(ID);
  this.label = Label;
};

XYZ.outputTaskDetails = function () {
  this.outputID();
  console.log(this.label);
};

// ABC = Object.create( Task );
// ABC ... = ...
```

* 委托行为意味着某些对象(XYZ)在找不到属性或者方法引用时会把这个请求委托给另一个对象(Task)。
* 在 API 接口的设计中，委托最好在内部实现，不要直接暴露出去。


## 类与对象


# ES6 中的 Class

`class` 基本上只是现有 `[[Prototype]]` (委托) 机制的一种语法糖。

也就是说，class 并不会像传统面向类的语言一样在声明时静态复制所有行为。如果你（有意或无意）修改或者替换了父“类”中的一个方法，那子“类”和所有实例都会受到影响，因为它们在定义时并没有进行复制，只是使用基于 `[[Prototype]]` 的实时委托。
