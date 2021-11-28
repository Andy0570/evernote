![面向对象的程序设计](https://upload-images.jianshu.io/upload_images/2648731-89fc0e8187c849d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

## 理解对象

在 ECMA-262 中，对象的定义：**无序属性的集合，其属性可以包含基本值、对象或者函数。**

我们可以把 ECMAScript 的对象想象成散列表/字典：无非就是一组键值对，其中值可以是数据或函数。

每个对象都是基于一个引用类型创建的。


### 属性类型

ECMA-262 第 5 版在定义只有内部才用的特性（attribute）时，描述了属性（property）的各种特征。ECMA-262 定义这些特性是为了实现 JavaScript 引擎用的，因此在 JavaScript 中不能直接访问它们。ECMAScript 中有两种属性：**数据属性**和**访问器属性**。

#### 1. 数据属性

数据属性包含一个数据值的位置。在这个位置可以读取和写入值。

| 数据属性 | 描述 |
| --- | --- |
| `[[Configurable]]` | 表示能否通过 `delete` 删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性。默认值为 `true`。 |
| `[[Enumerable]]` | 表示能否通过 for-in 循环返回属性。默认值为 `true`。 |
| `[[Writable]]` | 表示能否修改属性的值。默认值为 `true`。 |
| `[[Value]]` | 包含这个属性的数据值。默认值为 `undefined`。 |

要修改属性默认的特性，必须使用 ECMAScript 5 的 `Object.defineProperty()` 方法。

这个方法接收三个参数：属性所在的对象、属性的名字和一个描述符对象。

其中，描述符（descriptor）对象的属性必须是：`configurable`、`enumerable`、`writable` 和 `value`。设置其中的一或多个值，可以修改对应的特性值：

```javascript
var person = {};

// 修改属性默认的特性
Object.defineProperty(person, "name", {
    writable: false,
    value: "Nicholas"
});

alert(person.name); //Nicholas
person.name = "Greg";
alert(person.name); //Nicholas
```

* 一旦把属性定义为不可配置的（`configurable = false`），就不能再把它变回可配置了。也就是说，可以多次调用 `Object.defineProperty()` 方法修改同一个属性，但在把 `configurable` 特性设置为 `false` 之后就会有限制了。
* 在调用 `Object.defineProperty()` 方法时，如果不指定 `configurable`、`enumerable`、和 `writable` 特性的默认值都是 `false`。

#### 2. 访问器属性

访问器属性不包含数据值，它们包含一对 `getter` 和 `setter` 函数（不过，这两个函数都不是必需的）。在读取访问器属性时，会调用 `getter` 函数，这个函数负责返回有效的值。在写入访问器属性时，会调用 `setter` 函数并传入新值，这个函数负责决定如何处理数据。访问器属性有如下 4 个特性：

| 访问器属性 | 描述 |
| --- | --- |
| `[[Configurable]]` | 表示能否通过 `delete` 删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为数据属性。对于直接在对象上定义的属性，这个特性的默认值为 `true`。 |
| `[[Enumerable]]` | 表示能否通过 for-in 循环返回属性。对于直接在对象上定义的属性，这 5 个特性的默认值为 `true`。 |
| `[[Get]]` | 在读取属性时调用的函数。默认值为 `undefined`。 |
| `[[Set]]` | 在写入属性时调用的函数。默认值为 `undefined`。 |


访问器属性不能直接定义，必须使用 `Object.defineProperty()` 来定义。 

```javascript
var book = {
    _year: 2018, // _表示只能通过对象方法访问的属性
    edition: 1
};

Object.defineProperty(book, "year", {
    // getter 函数返回_year 的值，
    get: function() {
        return this._year;
    },
    // setter 函数通过计算来确定正确的版本。
    set: function(newValue) {
        if (newValue > 2018) {
            this._year = newValue;
            this.edition += newValue - 2018;
        }
    }
});

book.year = 2020;
console.log(book.edition); // 3
```



### 定义多个属性

`Object.defineProperties()` 方法可以同时为对象定义多个属性。
这个方法接收两个对象参数：
* 参数一：要添加和修改其属性的对象；
* 参数二：该属性与第一个对象中要添加或修改的属性一一对应。

```JavaScript
var book = {};
        
Object.defineProperties(book, {
    // 数据属性
    _year: {
        value: 2004
    },
    // 数据属性
    edition: {
        value: 1
    },
    // 访问器属性
    year: {            
        get: function(){
            return this._year;
        },

        set: function(newValue){
            if (newValue > 2004) {
                this._year = newValue;
                this.edition += newValue - 2004;
            }                  
        }            
    }        
});

book.year = 2005;
alert(book.edition);   //2
```


#### 读取属性的特性

ECMAScript 5 的 `Object.getOwnPropertyDescriptor()` 方法，可以获取给定属性的描述符。
这个方法接收两个参数：属性所在的对象和要读取其描述符的属性名称。

返回值是一个对象：
如果是**访问器属性**，这个对象的属性有 `configurable`、`enumerable`、`get` 和 `set`;
如果是**数据属性**，这个对象的属性有 `configurable`、`enumerable`、`writable` 和 `value`。

```javascript
var book = {};

// 同时定义多个属性
Object.defineProperties(book, {
    // 定义数据属性
    _year: {
        value: 2016
    },
    edition: {
        value: 1
    },
    // 定义访问器属性
    year: {
        get: function () {
            return this._year;
        },
        set: function () {
            if (newValue > 2018) {
                this._year = newValue;
                this.edition += newValue - 2018;
            }
        }
    }
});

// 获取数据属性
var descriptor = Object.getOwnPropertyDescriptor(book, "_year");
console.log(descriptor.value); // 2016
console.log(descriptor.configurable); // false
console.log(typeof descriptor.get); // undefined

// 获取访问器属性
var descriptor = Object.getOwnPropertyDescriptor(book, "year");
console.log(descriptor.value); // undefined
console.log(descriptor.enumerable); // false
console.log(typeof descriptor.get); // function
```

## 创建对象

创建对象的两种方式：
1. 构造函数模式
```javascript
var person = new Object();
person.name = "Tom";
person.age = 29;
person.job = "Software Engineer";

person.sayName = function() {
    alert(this.name); // this.name 将被解析为 person.name
};
```
2. 字面量模式
```js
var person = {
    name: "Tom",
    age : 29,
    job:"Software Engineer",

    sayName: function(){
        alert(this.name);
    }
};
```
缺点：使用同一个接口创建很多对象，会产生大量的重复代码。 

### 工厂模式

工厂模式抽象了创建具体对象的过程，考虑到在 ECMAScript 中无法创建类，开发人员就发明了一种函数，**用函数来封装以特定接口创建对象的细节**。

```javascript
// 把创建对象的所有过程封装在一个函数中
function createPerson(name, age, job) {

  // 把构造函数放在函数体中，并返回这个构造函数创建的实例对象
  var person = new Object();
  person.name = name;
  person.age = age;
  person.job = job;
  
  person.sayName = function() {
    console.log(this.name);
  };

  return person;
}

var person1 = createPerson('Andy', 25, 'Software Engineer');
var person2 = createPerson('Grey', 24, 'Teacher');
```
工厂模式虽然解决了创建多个相似对象的问题，但却没有解决**对象识别**的问题（无法识别一个对象的类型）。

### 构造函数模式

ECMAScript 中的构造函数可用来创建特定类型的对象。
原生构造函数：Object、Array ...

创建自定义的构造函数，从而定义自定义对象类型的属性和方法：

```javascript
function Person(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
  this.sayName = function() {
    console.log(this.name);
  };
}

var person1 = new Person('Andy', 25, 'Software Engineer');
var person2 = new Person('Grey', 24, 'Teacher');

// 检测对象的类型
console.log(person1 instanceof Object); // true，因为所有对象均继承自 Object
console.log(person1 instanceof Person); // true
```

#### 构造函数模式&工厂模式的区别

* 没有显式地创建对象；
* 直接将属性和方法赋给了 `this` 对象；
* 没有 `return` 语句。

💡 构造函数始终都应该以一个大写字母开头，而非构造函数则应该以一个小写字母开头。


```javascript
function Person(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = sayName;
}

// 把函数定义转移到构造函数外部
// 所有对象都共享同一个全局的 sayName 函数
function sayName(){
    alert(this.name);
}

var person1 = new Person("Andy", 23, "Software Engineer");
var person2 = new Person("Bob", 35, "Army");
```

#### 将构造函数当作函数

* 构造函数与其他函数的唯一区别，就在于调用它们的方式不同。
* 任何函数，只要通过 `new` 操作符来调用，那它就可以作为构造函数；
* 任何函数，如果不通过 `new` 操作符来调用，那它跟普通函数也不会有什么两样。

```JavaScript
// 当作构造函数使用
// 使用 new 操作符创建一个新对象
var person = new Person("Nicholas", 29, "Software Engineer");
person.sayName(); //"Nicholas"

// 作为普通函数使用，属性和方法都被会添加给 window 对象
// 当在全局作用域中调用一个函数时，this 对象总是指向 Global 对象（在浏览器中就是 window 对象）。
Person("Greg", 27, "Doctor"); // 添加到 window
window.sayName(); //"Greg"

// 在另一个对象的作用域中调用 Person() 函数
// 在对象 o 的作用域中调用，因此调用后 o 就拥有了所有属性和 sayName() 方法。
var o = new Object();
Person.call(o, "Kristen", 25, "Nurse");
o.sayName(); //"Kristen"
```

#### 构造函数的问题

**每个方法都要在每个实例上重新创建一遍**。（对象无法共用同名函数）
ECMAScript 中的函数是对象，因此每定义一个函数，也就是实例化了一个对象。因此，**不同实例上的同名函数是不相等的**。

```JavaScript
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    // 每个 Person 实例都包含一个不同的 Function 实例
    this.sayName = new Function("alert(this.name)"); // 与声明函数在逻辑上是等价的 
}

// 不同实例上的同名函数是不相等的
alert(person1.sayName == person2.sayName);  //false
```

### 原型模式

> `prototype`（原型）属性
> 该属性指向函数的原型对象。原型对象中包含 `constructor` （构造函数）属性、共享的属性和方法。

* 每个函数都有一个 `prototype` （原型）属性，这个属性是一个指针，指向一个对象，而这个对象的用途是包含可以由特定类型的所有实例共享的属性和方法。
* **`prototype` 就是「通过调用构造函数而创建的那个实例对象」的原型对象** 。使用原型对象的好处是让所有对象实例共享它所包含的属性和方法。

```javascript
// 构造函数变成了空函数
function Person(){
}

// 将 sayName() 方法和所有属性直接添加到了 Person 的 prototype 属性中。
Person.prototype.name = "andy";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function() {
    alert(this.name);
};

var person1 = new Person();
person1.sayName(); // "andy"

var person2 = new Person();
person2.sayName(); // "andy"

alert(person1.sayName == person2.sayName); // true，所有实例共享同一个方法

// 💡💡💡
// isPrototypeOf()：判断实例对象与原型对象之间的关系
// 因为它们内部都有一个指向 Person.prototype 的指针，因此都返回了 true。
alert(Person.prototype.isPrototypeOf(person1)); //Person 是不是 person1 实例的原型？true
alert(Person.prototype.isPrototypeOf(person2)); //true

// 💡💡💡
// ES 5 新增，Object.getPrototypeOf()：返回 [[Prototype]] 的值。
alert(Object.getPrototypeOf(person1) == Person.prototype); // true
alert(Object.getPrototypeOf(person1).name); // andy
```

#### 理解原型对象

无论什么时候，只要创建了一个新函数，就会根据一组特定的规则为该函数创建一个 `prototype` 属性，这个属性指向函数的原型对象。

在默认情况下，所有原型对象都会自动获得一个 `constructor` （构造函数）属性，这个属性包含一个指向 `prototype` 属性所在函数的指针（即：原型对象中的 `constructor` 属性是一个指针，这个指针指回了 `prototype` 属性所在的函数）。

![原型对象示意图](https://upload-images.jianshu.io/upload_images/2648731-851280b088199d7c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

图中：

* `Person.prototype` 指向了原型对象。
* `Person.prototype.constructor` 又指回了 Person 的构造函数。
* 实例的内部属性 `[[Prototype]]` 指针仅指向原型对象，而不指向构造函数。

每当代码读取某个对象的某个属性时，都会执行一次搜索，目标是具有给定名字的属性。搜索首先从对象实例本身开始。如果在实例中找到了具有给定名字的属性，则返回该属性的值；如果没有找到，则继续搜索指针指向的原型对象，在原型对象中查找具有给定名字的属性。如果在原型对象中找到了这个属性，则返回该属性的值。

**当为对象实例添加一个属性时，这个属性就会屏蔽原型对象中保存的同名属性；换句话说，添加这个属性只会阻止我们访问原型中的那个属性，但不会修改那个属性。**即使将这个属性设置为 `null`，也只会在实例中设置这个属性，而不会恢复其指向原型的连接。不过，使用 `delete` 操作符则可以完全删除实例属性，从而让我们能够重新访问原型中的属性：

```JavaScript
function Person(){
}

Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
    alert(this.name);
};

var person1 = new Person();
var person2 = new Person();

person1.name = "Greg";
alert(person1.name);   //"Greg" ——来自实例
alert(person2.name);   //"Nicholas" ——来自原型

delete person1.name;   // delete 删除实例属性
alert(person1.name);   //"Nicholas" ——来自原型
```
使用 `hasOwnProperty()` 方法可以检测一个属性是存在于实例中（返回 `true`），还是存在于原型中（返回 `false`）。

```JavaScript
function Person(){
}

Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
    alert(this.name);
};

var person1 = new Person();
var person2 = new Person();

alert(person1.hasOwnProperty("name"));  //false

person1.name = "Greg"; // 设置实例属性
alert(person1.name);   //"Greg" ——来自实例
alert(person1.hasOwnProperty("name"));  //true

alert(person2.name);   //"Nicholas" ——来自原型
alert(person2.hasOwnProperty("name"));  //false

delete person1.name;   // delete 删除实例属性
alert(person1.name);   //"Nicholas" ——来自原型
alert(person1.hasOwnProperty("name"));  //false
```


#### 原型与 `in` 操作符

* 在单独使用时，`in` 操作符会在通过对象能够访问给定属性时返回 `true`，无论该属性存在于实例中还是原型中。
* 只要 `in` 操作符返回 `true` 而 `hasOwnProperty()` 返回 `false`，就可以确定属性是**原型中的属性**。

```javascript
function Person(){
}

Person.prototype.name = "andy";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function() {
    alert(this.name);
};

var person1 = new Person();
var person2 = new Person();

// hasOwnProperty()：实例属性存在于原型中，返回 false
console.log(person1.hasOwnProperty("name")); // false

// in：不判断存在于原型中还是实例中，只要有就返回 true
console.log("name" in person1); // true

// 只要 in 操作符返回 true 而 hasOwnProperty() 返回 false，就可以确定属性是原型中的属性。
function hasPrototypeProperty(object, name) {
    return !object.hasOwnProperty(name) && (name in object);
}
```

#### 枚举对象属性

* 在使用 for-in 循环时，返回的是所有能够通过对象访问的、可枚举的（enumerated）属性，其中既包括存在于实例中的属性，也包括存在于原型中的属性。
* 要取得**对象**上所有可枚举的实例属性，可以使用 ECMAScript 5 的 `Object.keys()` 方法。这个方法接收一个对象作为参数，返回一个包含所有可枚举属性的字符串数组。
* 如果你想要得到所有**实例**属性，无论它是否可枚举，都可以使用 `Object.getOwnPropertyNames()` 方法。

```javascript
function Person(){
}

Person.prototype.name = "andy";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function() {
    alert(this.name);
};

// 原型属性
var keys = Object.keys(Person.prototype);
console.log(keys); //[ 'name', 'age', 'job', 'sayName' ]

var person1 = new Person();
person1.name = "Andy";
person1.age = 24;

// 实例属性
var person1Key = Object.keys(person1);
console.log(person1Key); //[ 'name', 'age' ]
```



#### 更简单的原型语法
用一个包含所有属性和方法的对象字面量来重写整个原型对象。

```javascript
function Person(){
}

// 本质上完全重写了默认的 prototype 对象
Person.prototype = {
    name : "Andy",
    age : 28,
    job : "Doctor",
    sayName : function () {
        alert(this.name);
    }
};

// 上面 constructor 属性不再指向 Person。
// 默认情况下，每创建一个函数，就会同时创建它的 prototype 对象，这个对象也会自动获得 constructor 属性。
// 而这里使用的语法，本质上完全重写了默认的 prototype 对象，因此 constructor 属性也就变成了新对象的 constructor 属性(指向 Object 构造函数)，不再指向 Person 函数。

// 解决方案
// 注意，以这种方式重设 constructor 属性会导致它的 [[Enumerable]] 特性被设置为 true。
// 默认情况下，原生的 constructor 属性是不可枚举的
function Person(){
}

Person.prototype = {
    constructor : Person, // 将它设置为适当的值
    name : "Andy",
    age : 28,
    job : "Doctor",
    sayName : function () {
        alert(this.name);
    }
};
```



#### 原型的动态性

* 对原型对象所做的任何修改都能够立即从实例上反映出来——即使是先创建了实例后修改原型也照样如此。
* 实例中的指针仅指向原型，而不指向构造函数。
* 调用构造函数时会为实例添加一个指向最初原型的 `[[Prototype]]` 指针，而把原型修改为另外一个对象就等于切断了构造函数与最初原型之间的联系。

```javascript
function Person() {

}
var friend = new Person();

Person.prototype = {
  constructor: Person,
  name: "Tom",
  age: 24,
  job: "Software Engineer",
  sayName: function () {
      console.log(this.name);
  }
};

friend.sayName(); // TypeError: friend.sayName is not a function
```

![重写整个原型对象](https://upload-images.jianshu.io/upload_images/2648731-058ddca5c54c3a74.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

#### 原生对象的原型

* 原型模式的重要性不仅体现在创建自定义类型方面，就连所有原生的引用类型，都是采用这种模式创建的。
* 所有原生引用类型(Object、Array、String，等等)都在其构造函数的原型上定义了方法。

```JavaScript
// 给基本包装类型 String 添加了一个名为 startsWith() 的方法
String.prototype.startsWith = function (text) {
    return this.indexOf(text) == 0;
};

var msg = "Hello world!";
alert(msg.startsWith("Hello"));   //true
```

> 不推荐在产品化的程序中修改原生对象的原型。

#### 原型对象的问题

1. 无法为构造函数传递初始化参数，默认情况下，所有实例的初始化属性值相同。
2. 所有属性、方法均共享。（多个实例共享同一个属性的问题！）

```JavaScript
function Person(){
}

Person.prototype = {
    constructor: Person,
    name : "Nicholas",
    age : 29,
    job : "Software Engineer",
    friends : ["Shelby", "Court"],
    sayName : function () {
        alert(this.name);
    }
};

var person1 = new Person();
var person2 = new Person();

// 修改 person1.friends 引用的数组，向数组中添加了一个字符串。
// 由于 friends 数组存在于 Person.prototype 而非 person1 中，
// 所以修改也会通过 person2.friends （与 person1.friends 指向同一个数组)）反映出来
person1.friends.push("Van");

alert(person1.friends);    //"Shelby,Court,Van"
alert(person2.friends);    //"Shelby,Court,Van"
alert(person1.friends === person2.friends);  //true
```


### 组合使用构造函数模式和原型模式

构造函数模式用于定义实例属性，而原型模式用于定义方法和共享的属性。
构造函数与原型混成的模式，是目前在 ECMAScript 中使用最广泛、认同度最高的一种创建自定义类型的方法。

```javascript
// 构造函数中定义实例属性
function Person(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
    this.friends = ["Andy", "Bob"];
}

// 原型中定义所有实例共享的属性和方法
Person.prototype = {
    constructor: Person,
    sayName: function () {
        alert(this.name);
    }
};

var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Grey", 27, "Doctor");

// 修改 person1.friends 并不会影响到 person2.friends
person1.friends.push("Van");
console.log(person1.friends); //[ 'Andy', 'Bob', 'Van' ]
console.log(person2.friends); //[ 'Andy', 'Bob' ]
console.log(person1.friends === person2.friends); // false
console.log(person1.sayName === person2.sayName); // true
```

### 动态原型模式

动态原型模式：把所有信息都封装在了构造函数中，而通过在构造函数中初始化原型（仅在必要的情况下），又保持了同时使用构造函数和原型的优点。换句话说，可以通过检查某个应该存在的方法是否有效，来决定是否需要初始化原型。

解释：使用「组合使用构造函数模式和原型模式」的方式创建一个自定义对象时，构造函数的代码和原型的代码是独立开来的。因此，为了解决这个“代码分散”的问题，通过在一个构造函数中加入 `if` 表达式进行判断，可以实现将所有代码汇集在一个方法中的目的。

```javascript
// 构造函数
function Person(name, age, job) {

    // 属性
    this.name = name;
    this.age = age;
    this.job = job;
    this.friends = ["Andy", "Bob"];

    // 方法
    // 只在 sayName() 方法不存在的情况下，才会将它添加到原型中
    if(typeof this.sayName != "function"){
        Person.prototype.sayName = function(){
            alert(this.name);
        }
    }
}

var friend = new Person("Andy", 24, "Doctor");
friend.sayName();
```

> ⚠️
>
> 使用动态原型模式时，不能使用对象字面量重写原型。如果在已经创建了实例的情况下重写原型，那么就会切断现有实例与新原型之间的联系。


### 寄生构造函数模式（不推荐使用）

寄生构造函数模式：创建一个函数，该函数的作用仅仅是封装创建对象的代码，然后再返回新创建的对象。

```javascript
// Person 函数创建了一个新对象，并以相应的属性和方法初始化该对象，然后又返回了这个对象
function Person(name, age, job){
    var object = new Object();
    object.name = name;
    object.age = age;
    object.job = job;
    object.sayName = function(){
        alert(this.name);
    }
    return object;
}

// 除了使用 new 操作符并把使用的包装函数叫做构造函数之外，这个模式跟工厂模式其实是一模一样的。
var friend = new Person("Andy", 24, "Doctor");
friend.sayName();
```

关于寄生构造函数模式，有一点需要说明：首先，返回的对象与构造函数或者与构造函数的原型属性之间没有关系；也就是说，构造函数返回的对象与在构造函数外部创建的对象没有什么不同。为此，不能依赖 `instanceof` 操作符来确定对象类型。由于存在上述问题，我们**建议在可以使用其他模式的情况下，不要使用这种模式。**



### 稳妥构造函数模式

所谓稳妥对象，指的是没有公共属性，而且其方法也不引用 `this` 的对象。
稳妥对象最适合在一些**安全的环境**中（这些环境中会禁止使用 `this` 和 `new`），或者在防止数据被其他应用程序（如 Mashup 程序）改动时使用。
稳妥构造函数遵循与寄生构造函数类似的模式，但有两点不同：
1. 新创建对象的实例方法不引用 `this`；
2. 不使用 `new` 操作符调用构造函数。

```javascript
function Person(name, age, job){

    // 创建要返回的对象
    var object = new Object();

    // 定义私有变量和函数
    object.name = name;
    object.age = age;
    object.job = job;

    // 添加方法
    object.sayName = function(){
        alert(name);
    }
    // 返回对象
    return object;
}

var friend = Person("Andy", 24, "Doctor");
// 除了调用 sayName() 方法外，没有别的方式可以访问其数据成员。
friend.sayName();
```

稳妥构造函数模式提供的这种安全性，使得它非常适合在某些安全执行环境——例如，ADsafe(www.adsafe.org)和 Caja(http://code.google.com/p/google-caja/)提供的环境下使用。


## 继承

许多 OO 语言都支持两种继承方式：
1. 接口继承：只继承方法签名；
2. 实现继承：继承实际的方法。

由于函数没有签名，在 ECMAScript 中无法实现接口继承。ECMAScript 只支持**实现继承**，而且其实现继承主要是依靠**原型链**来实现的。

### 原型链

基本思想：利用原型让一个引用类型继承另一个引用类型的属性和方法。

构造函数、原型和实例的关系：每个构造函数都有一个原型对象，原型对象包含一个指向构造函数的指针，而实例包含一个指向原型对象的内部指针。

假如我们让原型对象等于另一个类型的实例，结果会怎么样呢？显然，此时的原型对象将包含一个指向另一个原型的指针，相应地，另一个原型中也包含着一个指向另一个构造函数的指针。假如另一个原型又是另一个类型的实例，那么上述关系依然成立，如此层层递进，就构成了实例与原型的链条。

```javascript
// SuperType
function SuperType() {
    this.property = true;
}

SuperType.prototype.getSuperValue = function () {
    return this.property;
};

// SubType
function SubType() {
    this.subproperty = false;
}

/* 
 * SubType 继承了 SyperType
 * 继承是通过创建 SuperType 的实例，并将该实例赋给 SubType.prototype 实现的。
 * 这里 SubType 重写了 prototype 属性， [[prototype]] 的 constructor 属性指向 SuperType。
 * 即：让 SubType 的原型对象等于 SuperType 的实例
 */
SubType.prototype = new SuperType();

SubType.prototype.getSubValue = function () {
    return this.subproperty;
}

var instance = new SubType();
console.log(instance.getSuperValue()); // true
```

![继承关系示意图](https://upload-images.jianshu.io/upload_images/2648731-124f9c2659daa6ae.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

说明：
instance 实例指向 SubType 的原型，SubType 的原型又指向 SuperType 的原型。


#### 默认的原型

所有函数的默认原型都是 Object 的实例，因此默认原型都会包含一个内部指针，指向 `Object.prototype`。
这也正是所有自定义类型都会继承 `toString()`、`valueOf()` 等默认方法的根本原因。



#### 确定原型和实例的关系

##### 1. `instanceof` 操作符

通过 `instanceof` 操作符来测试实例与原型链中出现过的构造函数，结果就会返回 `true`。

```javascript
// instance 是 Object、SuperType 或 SubType 中任何一个类型的实例
alert(instance instanceof Object); // true
alert(instance instanceof SuperType); // true
alert(instance instanceof SubType); // true
```

##### 2. `isPrototypeOf()` 方法

只要是原型链中出现过的原型，都可以说是该原型链所派生的实例的原型，因此 `isPrototypeOf()` 方法也会返回 `true`。

```
alert(Object.prototype.isPrototypeOf(instance)); // true
alert(SuperType.prototype.isPrototypeOf(instance)); // true
alert(SubType.prototype.isPrototypeOf(instance)); // true
```

#### 谨慎地定义方法

子类型有时候需要重写超类型中的某个方法，或者需要添加超类型中不存在的某个方法。但不管怎样，**给原型添加方法的代码一定要放在替换原型的语句之后**。

```JavaScript
function SuperType(){
    this.property = true;
}

SuperType.prototype.getSuperValue = function(){
    return this.property;
};

function SubType(){
    this.subproperty = false;
}

// SubType 继承了 SuperType
SubType.prototype = new SuperType();

// SubType 添加新方法
SubType.prototype.getSubValue = function (){
    return this.subproperty;
};

// SubType 重写超类型中的方法
SubType.prototype.getSuperValue = function (){
    return false;
};

var instance = new SubType();
alert(instance.getSuperValue());   //false
```

**在通过原型链实现继承时，不能使用对象字面量创建原型方法**。因为这样做就会重写原型链：

```JavaScript
function SuperType(){
    this.property = true;
}

SuperType.prototype.getSuperValue = function(){
    return this.property;
};

function SubType(){
    this.subproperty = false;
}

// SubType 继承了 SuperType
SubType.prototype = new SuperType();

// 使用字面量添加新方法，会导致上一行代码无效
SubType.prototype = {

    // SubType 的新方法
    getSubValue : function (){
        return this.subproperty;
    },

    // SubType 的新方法
    someOtherMethod : function (){
        return false;
    }
};

var instance = new SubType();
alert(instance.getSuperValue());   //error!
```


#### 原型链的问题

* 问题一：在通过原型来实现继承时，原型实际上会变成另一个类型的实例。于是，原先的实例属性也就顺理成章地变成了现在的原型属性了。

```javascript
// 包含引用类型值的原型属性会被所有实例共享
function SuperType(){
  this.colors = ["red","green","blue"];
}

function SubType(){
}

// 继承了 SuperType
// 相当于 SubType.prototype 变成了 SuperType 的一个实例
SubType.prototype = new SuperType();

var instance1 = new SubType();
instance1.colors.push("black"); 
console.log(instance1.colors); //[ 'red', 'green', 'blue', 'black' ]

var instance2 = new SubType();
console.log(instance2.colors); // [ 'red', 'green', 'blue', 'black' ]
```

* 问题二：在创建子类型的实例时，不能向超类型的构造函数中传递参数。

> 💡💡💡
>
> **实践中很少会单独使用原型链。**



### 借用构造函数

基本思想：在子类型构造函数的内部调用超类型构造函数。

在子类型构造函数的内部调用超类型构造函数。这样就可以做到每个实例都具有自己的属性，同时还能保证只使用构造函数模式来定义类型。

```javascript
// SuperType
function SuperType() {
    this.colors = ["red", "blue", "green"];
}

// SubType
function SubType() {
    // 继承了 SuperTyoe
    // 在（未来将要）新创建的 SubType 实例的环境下调用了 SuperType 构造函数。
    SuperType.call(this);
}

var instance1 = new SubType();
instance1.colors.push("black");
console.log(instance1.colors); //[ 'red', 'blue', 'green', 'black' ]

var instance2 = new SubType();
console.log(instance2.colors); //[ 'red', 'blue', 'green' ]
```

#### 传递参数

相对于原型链而言，借用构造函数有一个很大的优势，即可以在子类型构造函数中向超类型构造函数**传递参数**。

```javascript
// SuperType
function SuperType(name) {
    this.name = name;
}

// SubType
function SubType() {
    // 继承了 SuperType，同时还传递了参数
    SuperType.call(this, "Bob");

    // 实例属性
    this.age = 34;
}

var instance1 = new SubType();
console.log(instance1.name); // Bob
console.log(instance1.age); //34
```



#### 借用构造函数的问题

如果仅仅是借用构造函数，那么也将无法避免构造函数模式存在的问题——方法都在构造函数中定义，因此**函数复用**就无从谈起了。而且，在超类型的原型中定义的方法，对子类型而言也是不可见的，结果所有类型都只能使用构造函数模式。考虑到这些问题，借用构造函数的技术也是很少单独使用的。



### 组合继承

将原型链和借用构造函数的技术组合到一块，从而发挥二者之长的一种继承模式。

基本思想：**使用原型链实现对原型属性和方法的继承，而通过借用构造函数来实现对实例属性的继承。**

```javascript
// SuperType 组合使用构造函数模式和原型模式
function SuperType(name){
    this.name = name;
    this.colors = ["red","green","blue"];
}

SuperType.prototype.sayName = function (){
    console.log(this.name);
};

// SubType 组合继承(原型链+借用构造函数)
function SubType(name, age){
    // 通过借用构造函数，继承属性
    SuperType.call(this, name);

    this.age = age;
}

// 通过原型链，继承方法
// 将 SuperType 的实例赋值给 SubType 的原型，然后又在该新原型上定义了方法 sayAge()。
SubType.prototype = new SuperType();
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function (){
    console.log(this.age);
};

var instance1 = new SubType("Andy", 23);
instance1.colors.push("black"); 
console.log(instance1.colors); //[ 'red', 'green', 'blue', 'black' ]
instance1.sayName(); // Andy
instance1.sayAge(); //23

var instance2 = new SubType("Bob", 24);
console.log(instance2.colors); //[ 'red', 'green', 'blue' ]
instance2.sayName(); // Bob
instance2.sayAge(); //24
```

### 原型式继承

借助原型可以基于已有的对象创建新对象，同时还不必因此创建自定义类型。

```javascript
function object(o) {
    function F() {} // 先创建一个临时性的构造函数
    F.prototype = o; // 然后将传入的对象作为这个构造函数的原型
    return new F(); // 返回这个临时类型的新实例
}
// 从本质上来说，object() 对传入其中的对象执行了一次浅复制。


// 前提条件：1.有一个对象可以作为另一个对象的基础。
var person = {
    name : "Andy",
    friends : ["Shelby", "Court", "Van"]
};

// 2.把基础对象 person 传递给 object() 函数，然后再根据具体需求对得到的对象加以修改
var anotherPerson = object(person);
anotherPerson.name = "Grey";
anotherPerson.friends.push("Rob");

var yetAnotherPerson = object(person);
anotherPerson.name = "Linda";
yetAnotherPerson.friends.push("Barbie");

console.log(person.friends); //[ 'Shelby', 'Court', 'Van', 'Rob', 'Barbie' ]
console.log(anotherPerson.friends); //[ 'Shelby', 'Court', 'Van', 'Rob', 'Barbie' ]
console.log(yetAnotherPerson.friends); //[ 'Shelby', 'Court', 'Van', 'Rob', 'Barbie' ]

console.log(anotherPerson.name); //Grey
console.log(yetAnotherPerson.name); //linda
```

#### `Object.create()` 方法

ECMAScript 5 通过新增 `Object.create()` 方法规范化了**原型式继承**。这个方法接收两个参数:

* 一个用作新对象原型的对象；
* 一个为新对象定义额外属性的对象(可选的)；

在传入一个参数的情况下，`Object.create()` 与 `object()` 方法的行为相同。

```javascript
var person = {
    name : "Andy",
    friends : ["Shelby", "Court", "Van"]
};

// 接收一个参数
var anotherPerson = Object.create(person);
anotherPerson.name = "Grey";
anotherPerson.friends.push("Rob");

// 接收两个参数
// 以这种方式指定的任何属性都会覆盖原型对象上的同名属性。
var anotherPerson = Object.create(person ,{
    name:{
        value: "Grey"
    }
});
```



### 寄生式继承

寄生式继承的思路与寄生构造函数和工厂模式类似，即创建一个仅用于封装继承过程的函数，该函数在内部以某种方式来增强对象，最后再像真的是它做了所有工作一样返回对象。

```javascript
function createAnother(original) {
    var clone =object(original); // 通过调用函数创建一个新对象
    clone.sayHi = function() {   // 以某种方式来增强这个对象
        console.log("Hi");
    };
    return clone; // 返回这个对象
}
```

使用寄生式继承来为对象添加函数，会由于不能做到函数复用而降低效率；这一点与构造函数模式类似。



### 寄生组合式继承

组合继承是 JavaScript 最常用的继承模式；不过，它也有自己的不足。组合继承最大的问题就是无论什么情况下，**都会调用两次超类型构造函数**：
* 一次是在创建子类型原型的时候；

* 另一次是在子类型构造函数内部。

没错，子类型最终会包含超类型对象的全部实例属性，但我们不得不在调用子类型构造函数时重写这些属性。

```javascript
// SuperType 组合使用构造函数模式和原型模式
function SuperType(name) {
    this.name = name;
    this.colors = ["red","blue","green"];
}

SuperType.prototype.sayName = function () {
    console.log(this.name);
};

// 当调用 SubType 构造函数时，又会调用一次 SuperType 构造函数，这一次又在新对象上创建了实例属性 name 和 colors。于是，这两个属性就屏蔽了原型中的两个同名属性。
function SubType(name, age) {
    SuperType.call(this, name); // 第二次调用 SuperType()

    this.age = age;
}

// 在第一次调用 SuperType 构造函数时， SubType.prototype 会得到两个属性：name 和 colors;
// 它们都是 SuperType 的实例属性，只不过现在位于 SubType 的原型中。
SubType.prototype = new SuperType(); // 第一次调用 SuperType()
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function () {
    console.log(this.age);
};
```



所谓寄生组合式继承，即**通过借用构造函数来继承属性**，**通过原型链的混成形式来继承方法**。其背后的基本思路是：不必为了指定子类型的原型而调用超类型的构造函数，我们所需要的无非就是超类型原型的一个副本而已。本质上，就是**使用寄生式继承来继承超类型的原型，然后再将结果指定给子类型的原型。**

```javascript
function inheritProtoptype(subType, superType) {
    var prototype = object(superType.prototype); // 创建对象
    prototype.constructor = subType; // 增强对象
    subType.prototype = prototype; // 指定对象
}

// 函数内部执行流程：
// 1. 创建超类型原型的一个副本;
// 2. 为创建的副本添加 constructor 属性，从而弥补因重写原型而失去的默认的 constructor 属性。
// 3. 将新创建的对象(即副本)赋值给子类型的原型；
```

这个例子的高效率体现在它只调用了一次 SuperType 构造函数，并且因此避免了在 `SubType.prototype` 上面创建不必要的、多余的属性。与此同时，原型链还能保持不变;因此，还能够正常使用`instanceof` 和 `isPrototypeOf()`。开发人员普遍认为**寄生组合式继承**是引用类型最理想的继承范式。

