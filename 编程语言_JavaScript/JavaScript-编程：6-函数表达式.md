# 定义函数的三种方式

1. 函数声明；
2. 函数表达式；
3. 构造函数；


## 函数声明

```javascript
// 函数声明语法
function functionName(arg0, arg1, arg2) {
    // 函数体
}

// Firefox、Safari、Chrome 和 Opera 都给函数定义了一个非标准的 name 属性，返回函数名
console.log(functionName.name); // "functionName"
```

### 函数声明提升

函数声明语法的重要特性：**函数声明提升**。

在执行代码之前会先读取函数声明，意味着可以把函数声明放在调用它的语句后面：

```javascript
sayHi();
function sayHi() {
    console.log("Hi!");
}
```


## 函数表达式

声明一个匿名函数，并将它赋值给变量。

```javascript
// 函数表达式语法
// 匿名函数/拉姆达函数/函数表达式
var functionName = function (arg0, arg1, arg2) {
    // 函数体
};

// 函数表达式与其他表达式一样，在使用前必须先赋值。
sayHi(); // 错误！！！
var sayHi = function sayHi() {
    console.log("Hi!");
};
```

## 构造函数

```javascript
var add = new Function(
  'x',
  'y',
  'return x + y'
);

// 等同于
function add(x, y) {
  return x + y;
}
```



## 函数声明&函数表达式的区别

* 函数表达式不同于函数声明。函数声明要求有名字，但函数表达式不需要。没有名字的函数表达式也叫做匿名函数。 
* 函数声明的特性：**函数提升**


```javascript
// 不要这样做
if (condition) {
    function sayHi() {
        alert("Hi");
    }
} else {
    function sayHi() {
        alert("Hi");
    }
}

// 可以这样做
var sayHi;
if (condition) {
    sayHi = function () {
        alert("Hi");
    }
} else {
    sayHi = function () {
        alert("Hi");
    }
}
```



# 递归

递归：函数调用自身。
* 在无法确定如何引用函数的情况下，递归函数就会变得比较复杂; 
* 递归函数应该始终使用 `arguments.callee` 来递归地调用自身，不要使用函数名——函数名可能会发生变化。 

```javascript
// 递归阶乘函数
function factorial(num) {
    if (num <= 1) {
        return 1;
    } else {
        return num * factorial(num - 1);
    }
}

var anotherFactorial = factorial;
factorial = null;
console.log(anotherFactorial(4)); // 出错！！！
```

使用 `arguments.callee` 解决以上问题：

```javascript
// arguments.callee 在严格模式下禁用
// arguments.callee 是一个指向正在执行的函数的指针
// 通过使用 arguments.callee 代替函数名，可以确保无论怎样调用函数都不会出问题。
function factorial(num) {
    if (num <= 1) {
        return 1;
    } else {
        return num * arguments.callee(num - 1);
    }
}
```

> `arguments.callee` 已被弃用，不推荐使用。

但在**严格模式**下，不能通过脚本访问 `arguments.callee`，访问这个属性会导致错误。不过，可以使用**命名函数表达式**来达成相同的结果。

```javascript
// 命名函数表达式
// 创建了一个名为 f() 的命名函数表达式，然后将它赋值给变量 factorial。
// 即便把函数赋值给了另一个变量，函数的名字 f 仍然有效，所以递归调用照样能正确完成。
var factorial = (function f(num) {
    if (num <= 1) {
        return 1;
    } else {
        return num *f(num - 1);
    }
});
```



# 闭包

当在函数内部定义了其他函数时，就创建了「闭包」。闭包有权访问包含函数内部的所有变量。
原理如下：
* 在后台执行环境中，闭包的作用域链包含着它自己的作用域、包含函数的作用域和全局作用域。
* 通常，函数的作用域及其所有变量都会在函数执行结束后被销毁。
* 但是，当函数返回了一个闭包时，这个函数的作用域将会一直在内存中保存到闭包不存在为止。

创建闭包的常见方式，就是**在一个函数内部创建另一个函数**。

示例一：

```javascript
function createComparisonFunction(propertyName) {
    
    return function(object1, object2) {
        
        // 这两行代码访问了外部函数中的变量 propertyName。
        // 即使这个内部函数被返回了，而且是在其他地方被调用了，但它仍然可以访问变量 propertyName。
        // 因为内部函数的作用域链中包含 createComparisonFunction()的作用域。
        var value1 = object[propertyName];
        var value2 = object[propertyName];

        if (value1 < value2) {
            return -1;
        } else if (value1 > value2) {
            return 1;
        } else {
            return 0;
        }
    };
}

// 在另一个函数内部定义的函数会将包含函数(即外部函数)的活动对象添加到它的作用域链中。
// 因此，在 createComparisonFunction() 函数内部定义的匿名函数的作用域链中，实际上将会包含外部函数 createComparisonFunction() 的活动对象。
```

示例二：

```javascript
// 默认在外部是无法读取函数 f1() 的内部属性的
function f1() {
  var n = 999;
  // 函数 f2() 就是闭包
  function f2() {
    // 在函数 f2() 内部，它是可以读取函数 f1() 的内部属性的
    console.log(n);
  }
  return f2; // 返回函数 f2()，利用f2()读取 f1() 的属性
}

var result = f1();
result(); // 999
```

作用域链：当某个函数被调用时，会创建一个执行环境(execution context)及相应的作用域链。然后，使用 arguments 和其他命名参数的值来初始化函数的活动对象(activation object)。但在作用域链中，外部函数的活动对象始终处于第二位，外部函数的外部函数的活动对象处于第三位，......直至作为作用域链终点的全局执行环境。



> 💡💡💡
>
> 由于闭包会携带包含它的函数的作用域，因此会比其他函数占用更多的内存。过度使用闭包可能会导致内存占用过多，我们建议读者只在绝对必要时再考虑使用闭包。虽然像 V8 等优化后的 JavaScript 引擎会尝试回收被闭包占用的内存，但请大家还是要慎重使用闭包。



## 闭包与变量

闭包只能取得包含函数中任何变量的最后一个值。

```JavaScript
function createFunctions(){
    var result = new Array();

    for (var index = 0; index < 10; index++) {
        result[index] = function() {
            result index;
        };
    }

    return result;
}
```

实际上，每个函数都返回 10。因为每个函数的作用域链中都保存着`createFunctions() `函数的活动对象，所以它们引用的都是同一个变量 index。当 `createFunctions() `函数返回后，变量 index 的值是 10，此时每个函数都引用着保存变量 index 的同一个变量对象，所以在每个函数内部 index 的值都是 10。

```JavaScript
// 通过创建另一个匿名函数强制让闭包的行为符合预期
function createFunctions(){
    var result = new Array();

    for (var index = 0; index < 10; index++) {
        result[index] = function(num) {
            return function(){
                return num;
            };
        }(i);
    }

    return result;
}
```



闭包的作用：

1. 可以读取函数内部的变量；
2. 让这些变量始终保持在内存中，即闭包可以使得它诞生环境一直存在。
3. 封装对象的私有属性和私有方法；



## this 对象

`this` 对象是在运行时基于函数的执行环境绑定的：

* 在全局函数中，`this` 等于 `window`，
* 而当函数被作为某个对象的方法调用时，`this` 等于那个对象。
* 匿名函数的执行环境具有全局性，因此其 `this` 对象通常指向 `window`。





# 模仿块级作用域

> Tips：ES6 新增了块级作用域。

JavaScript 没有块级作用域的概念。使用闭包可以在 JavaScript 中模仿块级作用域。

用作块级作用域(通常称为私有作用域)的匿名函数的语法:

```javascript
// 定义并立即调用一个匿名函数
// 将函数声明包含在一对圆括号中，表示它实际上是一个函数表达式。
// 紧随其后的另一对圆括号会立即调用这个函数。

(function(){
    // 这里是块级作用域
})();
```

示例代码：

```JavaScript
function outputNumbers(count){
    // 在块语句中定义的变量，实际上是在包含函数中而非语句中创建的	
    for (var i=0; i < count; i++){
        alert(i);
    }
    // 在 for 循环外仍然可以访问到变量 i 
    alert(i);   //count
}

// 模仿块级作用域
// 在匿名函数中定义的任何变量，都会在执行结束时被销毁。
function outputNumbers(count) {
    // 用作块级作用域的匿名函数
    (function() {
        for (var i = 0; i < count; i++) {
            alert(i);
        }
    })();

    alert(i); //causes an error
}
```


# 私有变量

严格来讲，JavaScript 中没有私有成员的概念；所有对象属性都是公有的。不过，倒是有一个私有变量的概念。**任何在函数中定义的变量，都可以认为是私有变量**，因为不能在函数的外部访问这些变量。私有变量包括函数的参数、局部变量和在函数内部定义的其他函数。

```javascript
function add(num1, num2) {
    // 函数内部的私有变量：num1,num2,sum
    var sum = num1 + num2;
    return sum;
}
```



## 创建用于访问私有变量的公有方法

* 在函数内部创建一个**闭包**，那么闭包通过自己的作用域链也可以访问这些变量（使用闭包在对象中创建私有变量）。
* **特权方法**：有权访问私有变量和私有函数的公有方法。

```javascript
// 1. 在构造函数中定义特权方法
function MyObject() {
    // 私有变量和私有函数
    var privateVariable = 10;

    function privateFunction() {
        return false;
    }

    // 特权方法作为闭包有权访问在构造函数中定义的 所有变量和函数。
    this.publicMethod = function() {
        privateVariable ++;
        return privateFunction();
    }
}
```



## 静态私有变量

通过在私有作用域中定义私有变量或函数，同样也可以创建特权方法。

```javascript

(function(){
    // 私有变量很私有函数
    var privateVariable = 10;

    function privateFunction() {
        return false;
    }

    // 构造函数
    MyObject = function(){
    };

    //共有/特权方法
    MyObject.prototype.publicMethod = function(){
        privateVariable ++;
        return privateFunction();
    };

})();
```



# 模块模式

模块模式：为**单例**创建私有变量和特权方法。所谓单例（singleton），指的就是只有一个实例的对象。

JavaScript 是以对象字面量的方式来创建单例对象的。

```JavaScript
var application = function(){

    // 私有变量和函数
    var components = new Array();

    // 初始化
    components.push(new BaseComponent());

    // 公共
    return {
        getComponentCount : function(){
            return components.length;
        },

        registerComponent : function(component){
            if (typeof component == "object"){
                components.push(component);
            }
        }
    };
}();
```

如果必须创建一个对象并以某些数据对其进行初始化，同时还要公开一些能够访问这些私有数据的方法，那么就可以使用模块模式。以这种模式创建的每个单例都是 Object 的实例，因为最终要通过一个对象字面量来表示它。事实上，这也没有什么;毕竟，单例通常都是作为全局对象存在的，我们不会将它传递给一个函数。因此，也就没有什么必要使用 `instanceof` 操作符来检查其对象类型了。


# 增强的模块模式

在返回对象之前加入对其增强的代码。这种增强的模块模式适合那些单例必须是某种类型的实例，同时还必须添加某些属性和(或)方法对其加以增强的情况。

```JavaScript
function BaseComponent(){
}

function OtherComponent(){
}

var application = function(){

    // 私有变量和函数
    var components = new Array();

    // 初始化
    components.push(new BaseComponent());

    // 创建 application 的一个局部副本
    var app = new BaseComponent();

    // 公共接口
    app.getComponentCount = function(){
        return components.length;
    };

    app.registerComponent = function(component){
        if (typeof component == "object"){
            components.push(component);
        }
    };

    //return it
    return app;
}();

alert(application instanceof BaseComponent);
application.registerComponent(new OtherComponent());
alert(application.getComponentCount());  //2
```

