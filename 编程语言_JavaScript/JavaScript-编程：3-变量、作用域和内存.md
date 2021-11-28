JavaScript 变量**松散类型**的本质，决定了它只是在特定时间用于保存特定值的一个名字而已。

## 基本类型和引用类型

ECMAScript 变量的两种数据类型：

| 数据类型 | 基本类型     | 引用类型      |
| -------- | :-------- | ------------- |
| 含义     | 简单的数据段，5种基本数据类型：`Undefined`、`Null`、`Boolean`、`Number`、`String`。 | 由多个值构成的对象。|
| 访问方式 | 按值访问，可以操作保存在变量中的实际的值。 | 按引用访问，不能直接操作对象的内存空间。 |
| 保存区域 | 栈内存  | 堆内存   |
| 复制    | 开辟新的内存空间   | 两个指针引用同一个对象 |

### 动态的属性

可以为引用类型添加、改变、删除属性和方法：
```JavaScript
var person = new Object();
person.name = "Nicholas";
console.log(person.name);    // Nicholas
```

不能给基本类型的值添加属性：
```JavaScript
var name = "Nicholas";
name.age = 27;
console.log(name.age);    // undefined
```


### 复制变量值

* 基本类型：会开辟新的内存，两个值互不影响。
* 引用类型：指针，指向存储在堆中的同一个对象。

从一个变量向另一个变量复制**基本类型**的值，会在变量对象上创建一个新值，然后把该值复制到为新变量分配的位置上。

从一个变量向另一个变量复制**引用类型**的值，同样也会将存储在变量对象中的值复制一份放到为新变量分配的空间中。不同的是，这个值的副本实际上是一个指针，而这个指针指向存储在堆中的一个对象。复制操作结束后，两个变量实际上将引用同一个对象。因此，改变其中一个变量，就会影响另一个变量。
 
### 传递参数

ECMAScript 中所有函数的参数都是**按值传递**的。也就是说，把函数外部的值复制给函数内部的参数，就和把值从一个变量复制到另一个变量一样。基本类型值的传递如同基本类型变量的复制一样，而引用类型值的传递，则如同引用类型变量的复制一样。

在向参数传递**基本类型**的值时，被传递的值会被复制给一个局部变量（即命名参数，或者用 ECMAScript 的概念来说，就是 `arguments` 对象中的一个元素）。

在向参数传递**引用类型**的值时，会把这个值在内存中的地址复制给一个局部变量，因此这个局部变量的变化会反映在函数的外部。

```javascript
// 基本类型传递参数
function addTen(num){
  num += 10;
  return num;
}

var count = 20;
var result = addTen(count);
console.log(count); // 20，原来的基本数据类型值没有变化。
console.log(result); // 30

// 引用类型传递参数
function setName(obj) {
    obj.name = "Nicholas";
}

var person = new Object();
setName(person);
alert(person.name);    //"Nicholas"，原来的对象属性也跟着改变了
```

### 检测类型

* `typeof` 操作符是确定一个变量是字符串、数值、布尔值，还是 `undefined` 的最佳工具（检测基本类型）。
* `instanceof` 操作符用于确定一个值是哪种引用类型。如果变量是给定引用类型（根据它的原型链来识别）的实例，`instanceof` 操作符返回 `true`。
* 因为所有引用类型的值都是 `Object` 的实例。因此，在检测一个引用类型值和 `Object` 构造函数时，`instanceof` 操作符始终会返回 `true`。
* 使用 `instanceof` 操作符检测基本类型的值，则该操作符始终会返回 `false`，因为基本类型不是对象。

> 💡 确定一个值是哪种基本类型可以使用 `typeof` 操作符，而确定一个值是哪种引用类型可以使用 `instanceof` 操作符。


```javascript
let person = new Object();
console.log(person instanceof Object); // true
console.log(person instanceof Array); // false

let colors = ['blue', 'green', 'yellow'];
console.log(colors instanceof Array); // true
```

### 执行环境及作用域

* 全局执行环境；
* 函数执行环境；
* 全局环境只能访问在全局环境中定义的变量和函数，而不能直接访问局部环境中的任何数据; 
* 内部环境可以通过作用域链访问所有的外部环境，但外部环境不能访问内部环境中的任何变量和函数。
* 作用域链的用途：保证对「执行环境有权访问的所有变量和函数」的有序访问。


![执行环境及作用域](https://upload-images.jianshu.io/upload_images/2648731-401cb850eed10412.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

查询标识符的搜寻过程：从作用域链的前端开始，向上逐级查询与给定名字匹配的标识符。有则返回，无则继续沿作用域链向上搜索。

### 延长作用域链

有些语句可以在作用域链的前端临时增加一个变量对象，该变量对象会在代码执行后被移除。

当执行流进入下列任何一个语句时，作用域链就会得到加长:
* try-catch 语句的 catch 块；
* with 语句。



### 没有块级作用域 & 作用域链

在 JavaScript 中，if 语句中使用 `var` 声明的变量会将变量添加到当前的执行环境中：
```javascript
if(true) {
    var color = "blue"; // 变量会被添加到当前执行环境（这里是全局环境）。
} // if语句结束后，变量不会被销毁。

// if{} 外部依然可以访问到内部声明的变量
alert(color); // "blue"
```

for 语句：
```JavaScript
// 使用 var 关键字声明的变量没有块级作用域
for (var i = 0; i < 10; i++) {
  console.log(i);
}
console.log(i); // 10

// 使用 let 关键字声明的变量有块级作用域
for (let i = 0; i < 10; i++) {
  console.log(i);
}
console.log(i); // ReferenceError: i is not defined
```

💡 ES6 中的 `let`、`const` 添加了**块级作用域**的特性。

## 垃圾收集

JavaScript 具有**自动垃圾收集机制**，也就是说，**执行环境**会负责管理代码执行过程中使用的内存。

原理：找出不再继续使用的变量，释放其占用的内存。垃圾收集器会按照固定的时间间隔（或代码执行中预定的收集时间），周期性地执行垃圾收集和释放操作。


JavaScript 的两种垃圾收集策略：
1. 标记清除（主流方法）：进入环境-离开环境；给当前不使用的值加上标记，然后再回收其内存。
2. 引用记数（不再使用）；存在循环引用问题。


### 管理内存

JavaScript 在进行内存管理及垃圾收集时面临的最主要的一个问题：分配给 Web 浏览器的可用内存数量通常要比分配给桌面应用程序的少。

因此，确保占用最少的内存可以让页面获得更好的性能。

**解除引用**：为执行中的代码只保存必要的数据。一旦数据不再有用，最好通过将其值设置为 `null` 来释放其引用。

解除引用的真正作用是让值脱离执行环境，以便垃圾收集器下次运行时将其回收。

![手工解除引用](https://upload-images.jianshu.io/upload_images/2648731-89883dd38cdc87e4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)
