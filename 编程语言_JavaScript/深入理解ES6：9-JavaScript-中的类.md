### ECMAScript 5 中的近类结构

在 ECMAScript 5 及其早期版本中并没有类的概念，最相近的思路是创建一个自定义类型：首先创建一个构造函数，然后定义另一个方法并赋值给构造函数的原型。

```js
// PersonType 是构造函数
function PersonType(name) {
  this.name = name;
}

// 给 PersonType 的原型添加 sayName() 方法，所有 PersonType 的实例都将共享这个方法。
PersonType.prototype.sayName = function() {
  console.log(this.name);
};

// 使用 new 操作符创建一个 PersonType 的实例 person
var person = new PersonType('Andy'); 
person.sayName(); // Andy

console.log(person instanceof PersonType); // true
console.log(person instanceof Object);     // true
```

### 类的声明

实际上，ECMAScript 6 中类声明仅仅是基于已有自定义类型声明的语法糖。

```js
class PersonClass {
  // 等价于 PersonType 构造函数
  // 建议在构造函数中创建自有属性
  constructor(name) {
    this.name = name;
  }

  // 等价于 PersonType.prototype.sayName
  sayName() {
    console.log(this.name);
  }
}

var person = new PersonClass('Andy');
person.sayName(); // Andy

console.log(person instanceof PersonClass); // true
console.log(person instanceof Object); // true

console.log(typeof PersonClass); // function
console.log(typeof PersonClass.prototype.sayName); // function
```

### 为何使用类语法

* 函数声明可以被提升，而类声明不能被提升。
* 类声明中的所有代码将自动运行在严格模式下。
* 在自定义类型中，需要通过 `Object.defineProperty()` 方法手工指定某个方法为不可枚举。而在类中，所有方法都是不可枚举的。
* 每个类都有一个名为 `[[Construct]]` 的内部方法，通过关键字 `new` 调用那些不含 `[[Construct]]` 的方法会导致程序抛出错误。
* 使用除了 `new` 关键字以外的方式调用类的构造函数会导致程序抛出错误。
* 在类中修改累吗会导致程序报错。


### 继承与派生

在 ECMAScript 6 之前实现继承的方式：

```js
function Rectangle(length, width) {
  this.length = length;
  this.width = width;
}

Rectangle.prototype.getArea = function() {
  return this.length * this.width;
};

function Square(length) {
  Rectangle.call(this, length, length);
}

Square.prototype = Object.create(Rectangle.prototype, {
  constructor: {
    value: Square,
    enumerable: true,
    writable: true,
    configurable: true
  }
});

var square = new Square(3);

console.log(square.getArea()); // 9
console.log(square instanceof Square); // true
console.log(square instanceof Rectangle); // true
```

ECMAScript 6 的等价版本如下：

```js
class Rectangle {
  constructor(length, width) {
    this.length = length;
    this.width = width;
  }

  getArea() {
    return this.length * this.width;
  }
}

// Square 通过关键字 extends 继承父类
class Square extends Rectangle {
  constructor(length) {
    // 等价于 Rectangle.call(this, length, length);
    super(length, length);
  }
}

var square = new Square(3);

console.log(square.getArea()); // 9
console.log(square instanceof Square); // true
console.log(square instanceof Rectangle); // true
```

继承自其他类的类被称为**派生类**。

### 类方法遮蔽

派生类中的方法总会覆盖基类中的同名方法。


```js
class Square extends Rectangle {
  constructor(length) {
    // 等价于 Rectangle.call(this, length, length);
    super(length, length);
  }

  // 覆盖并遮蔽 Rectangle.prototype.getArea() 方法
  getArea() {
    return this.length * this.width;
  }
}
```

### 静态成员

如果基类中有静态成员，那么这些静态成员在派生类中也可用。
