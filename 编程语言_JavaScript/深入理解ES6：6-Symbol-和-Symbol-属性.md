
在 ECMAScript 5 及早期版本中，语言包含 5 种原始类型：字符串型、数字型、布尔型、`null` 和 `undefined`。

ECMAScript 6 引入了第 6 种原始类型: `Symbol`。起初，人们用它来**创建对象的私有成员**。在 `Symbol` 出现以前，人们一直通过属性名来访问所有属性，无论属性名由什么元素构成，全部通过一个字符串类型的名称来访问；**私有名称原本是为了让开发者们创建非字符串属性名称而设计的**，但是一般的技术无法检测这些属性的私有名称。

私有名称最终演变成了 ECMAScript 6 中的 `Symbol`。虽然通过 `Symbol` 可以为属性添加非字符串名称，但是其隐私性就被打破了。最终，新标准中将 Symbol 属性与对象中的其他属性分别分类。

### 创建 Symbol

可以通过全局的 `Symbol` 函数创建一个 `Symbol`。
```js
// 创建一个名为 firstName 的 Symbol。
let firstName = Symbol();
let person = {};

person[firstName] = 'Andy';
console.log(person[firstName]); // Andy
```

`Symbol` 函数接受一个可选参数，用于描述即将创建的 `Symbol`。该描述不可用于属性访问，用来便于阅读代码和调试 `Symbol` 程序。

```js
// 创建一个名为 firstName 的 Symbol。
let firstName = Symbol('first name');
let person = {};

person[firstName] = 'Andy';
console.log(person[firstName]); // Andy
console.log(firstName); // Symbol(first name)
```

`Symbol` 的描述被存储在内部的 `[[Description]]` 属性中，只有当调用 `Symbol` 的 `toString()` 方法时才可以读取这个属性。在执行 `console.log()` 时隐式调用了 `firstName` 的 `tostring()` 方法，所以它的描述会被打印到日志中，但不能直接在代码里访问 `[[Description]]` 属性。

### Symbol 的使用方法

所有使用可计算属性名的地方，都可以使用 `Symbol`。

`Symbol` 可以用于「可计算对象字面量属性名」、`Object.defineProperty()` 方法和 `Object.defineProperties()` 方法。

```js
// 创建一个名为 firstName 的 Symbol。
let firstName = Symbol('first name');

// 通过可计算对象字面量属性语法为 person 对象创建了一个 Symbol 属性 firstName。
let person = {
  [firstName]: 'Andy'
};

// 将 firstName 属性设置为只读
Object.defineProperty(person, firstName, { writable: false });

let lastName = Symbol('last name');

// 创建了一个只读的 Symbol 属性 lastName。
Object.defineProperties(person, {
  [lastName]: {
    value: 'blueSky',
    writable: false
  }
});

console.log(person[firstName]); // Andy
console.log(person[lastName]);  // blueSky
```

### Symbol 共享体系

* `Symbol.for()` 方法；
* `Symbol.keyFor()` 方法；

ECMAScript 6 提供了一个全局 `Symbol` 注册表支持在不同的代码中共享一个 `Symbol`。

```js
// 通过 Symbol.for() 方法创建可共享的 Symbol
let uid1 = Symbol.for('uid');

// 后续传入同样的键调用 Symbol.for() 方法返回的是同一个 Symbol
let uid2 = Symbol.for('uid');

console.log(uid1 === uid2); // true

// 使用 Symbol.keyFor() 方法在 Symbol 全局注册表中检索与 Symbol 有关的键
console.log(Symbol.keyFor(uid1)); // uid
```


### Symbol 与类型强制转换

⚠️ 不能将 `Symbol` 强制转换为字符串和数字类型。如果将 `Symbol` 与字符串拼接会导致程序抛出异常！

```js
let uid1 = Symbol.for('uid');

// String() 函数会调用 uid 的 toString() 方法
// 即调用 Symbol 的 toString() 方法
let desc = String(uid1);

console.log(desc); // Symbol(uid)
```

### Symbol 属性检索

检索对象中的所有属性名：

* `Object.keys()`：返回一个字符串数组，它包含一个对象的所有可枚举属性名。
* `Object.getOwnPropertyNames(Object.prototype)`：返回所有实例属性名，无论它是否可枚举。
* `Object.getOwnPropertySymbols()`：检索对象中的 `Symbol` 属性。

```js
let uid = Symbol.for('uid');
let object = {
  [uid]: '12345'
};

let symbols = Object.getOwnPropertySymbols(object);

console.log(symbols.length);     // 1
console.log(symbols[0]);         // Symbol(uid)
console.log(object[symbols[0]]); // 12345
```

### 通过 well-known Symbol 暴露内部操作

ECMAScript 6 在原型链上定义了与 Symbol 相关的属性来暴露更多的语言内部逻辑。

well-known Symbol 为标准对象定义了一些以前只在语言内部可见的功能。


* `Symbol.hasInstance`：一个在执行 `instanceof` 时调用的内部方法，用于检测对象的继承信息。
* `Symbol.isConcatSpreadable`：一个布尔值，用于表示当传递一个集合作为 `Array.prototype.concat()` 方法的参数时，是否应该将集合内的元素规整到同一层级。
* `Symbol.iterator()`：检查指定对象中是否存在默认的函数类型迭代器。
* `Symbol.match(regex)`：一个在调用 `String.prototype.match()` 方法时调用的方法，用于比较字符串。
* `Symbol.replace(regex, replacement)`：一个在调用 `String.prototype.replace()` 方法时调用的方法，用于替换字符串的子串。
* `Symbol.search(regex)`：一个在调用 `String.prototype.search()` 方法时调用的方法，用于在字符串中定位子串。
* `Symbol.species(regex)`：用于创建派生对象的构造函数。
* `Symbol.split`：一个在调用 `String.prototype.split()` 方法时调用的方法，用于分割字符串。
* `Symbol.toPrimitive`：一个返回对象原始值的方法。
* `Symbol.toStringTag`：一个在调用 `String.prototype.toString()` 方法时使用的字符串，用于创建对象描述。
* `Symbol.unscopables`：一个定义了一些不可被 with 语句引用的对象属性名称的对象集合。






