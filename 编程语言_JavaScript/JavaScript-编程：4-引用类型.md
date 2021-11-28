# 引用类型

* **引用类型**：将数据和功能组织在一起的数据结构。
* 引用类型的值（对象）是引用类型的一个实例（对象是某个特定引用类型的实例）。
* 尽管 ECMAScript 从技术上讲是一门面向对象的语言，但它不具备传统的面向对象语言所支持的类和接口等基本结构。
* 引用类型与传统面向对象程序设计中的类相似，但实现不同。

## Object 类型

Object 是一个基础类型，其他所有类型都从 Object 继承了基本的行为。

### 创建 Object 实例

#### 1. 使用 Object 构造函数创建对象

```javascript
// 使用 new 操作符后跟 Object 构造函数
var person = new Object();
person.name = "Nicholas";
person.age = 29;
```

#### 2. 对象字面量表示法

对象字面量表示法可以简化「创建包含大量属性的对象」的过程。
通过对象字面量定义对象时，不会调用 `Object` 构造函数。
推荐只在考虑「对象属性名的可读性」时使用「对象字面量语法」。

```javascript
// 2.1 对象字面量表示法
var person = {
    name : "Nicholas",
    age : 29   // 最后一个属性后面不能添加逗号
};
// 在最后一个属性后面添加逗号，会在 IE7 及更早版本和 Opera 中导致错误。

// 2.2，属性名也可以使用字符串
var persion = {
    "name" : "Nicholas",
    "age" : 29,
    5 : true // 这里的数值属性会自动转换为字符串
}

// 2.3
var person = {}; // 与 new Object() 相同
```

### 访问属性

#### 1. 点语法

```javascript
alert(person.name);
```

#### 2. 方括号表示法

通常，除非必须使用变量来访问属性，否则我们**建议使用点语法**。
```javascript
alert(person["name"]);

// 方括号语法的优点：可以通过变量访问属性
var propertyName = "name";   // 属性名是 name，把 name 赋值给变量
alert(person[propertyName]); // 通过变量访问属性

// 方括号的属性名中还可以包含会导致语法错误的字符、关键字、保留字。
person["first name"] = "Nicholas";
```


## Array 类型

ECMAScript 数组的特点：

* 数组的每一项可以保存任何类型的数据。
* 数组的大小可以动态调整（可以随着数据的添加自动增长以容纳新增数据）。

![Array 类型](https://upload-images.jianshu.io/upload_images/2648731-5c7977e9561b6a49.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

### 创建数组

#### 1. 使用 Array 构造函数创建数组
```JavaScript
var colors = new Array();

// 创建 length 值为20的数组
var colors = new Array(20);

// 创建一个包含3个字符串值的数组
var colors = new Array("red", "blue", "green");

// 给构造函数传递一个值来创建数组
// 1. 如果传入参数为数值，创建一个包含3项的数组
var colors = new Array(3);
// 2. 如果传入参数为字符串，创建一个包含 1 项，即字符串“Tom”的数组
var names = new Array("Tom"); 

// 可以省略 new 操作符
var colors = Array(3);
```

#### 2. 使用数组字面量表示法创建数组

使用数组字面量表示法时，不会调用 Array 构造函数。
```JavaScript
// 创建一个包含3个字符串的数组
var colors = ["red", "blue", "green"];

// 创建一个空数组
var name = [];
```


### 数组的 `length` 属性
数组的 `length` 属性特点：它不是只读的。因此，通过设置 `length` 属性的值，可以从数组的末尾移除项或向数组中添加新项。
💡 可以通过设置 `length = 0` 来移除所有项。
```javascript
// 通过设置 length 属性的值来移除最后一项
var colors = ["red", "blue", "green"];
colors.length = 2;
console.log(colors[2]); // undefined

// 利用 length 属性在末尾添加新项
var colors = ["red", "blue", "green"]; // 创建一个包含3个字符串的数组
colors[colors.length] = "black";       // 在位置3添加一种颜色
colors[colors.length] = "brown";       // 在位置4添加一种颜色
```

### 检测数组

确定某个对象是不是数组。

#### 方式一：`instanceof`

`instanceof` 操作符只假定单一的全局执行环境。
如果网页中包含多个框架，实际上就会存在两个以上不同的全局执行环境，从而存在两个以上不同版本的 Array 构造函数。如果你从一个框架向另一个框架传入一个数组，那么传入的数组与在第二个框架中原生创建的数组分别具有各自不同的构造函数。

```javascript
if(colors instanceof Array) {
    // 对数组执行某些操作
}
```

#### 方式二：`Array.isArray()` ✅

ECMAScript 5 新增。`Array.isArray()` 可以最终确定某个值到底是不是数组，而不管它是在哪个全局执行环境中创建的。
```JavaScript
// 判断 colors 这个实例是不是数组
if(Array.isArray(colors)) {
    // 对数组执行某些操作 
}
```

### 转换方法

数组的 `toString()` 方法：返回由数组中每个值的字符串形式拼接而成的一个以逗号分隔的字符串。

```JavaScript
alert(colors.toString()); //red,blue,green

// 调用 valueOf() 返回的还是数组
alert(colors.valueOf());  //red,blue,green
alert(colors);            //red,blue,green，会在后台会调用 toString() 方法
alert(colors.toLocaleString());
```

数组继承的 `toLocaleString()`、`toString()` 和 `valueOf()` 方法，在默认情况下都会以逗号分隔的字符串的形式返回数组项。而如果使用 `join()` 方法，则可以使用不同的分隔符来构建这个字符串。

`join()` 方法只接收一个参数，即用作分隔符的字符串，然后返回包含所有数组项的字符串。
```JavaScript
alert(colors.join("||")); //red||blue||green , join() 函数用于指定分隔符
```

> 如果数组中的某一项的值是 null 或者 undefined，那么该值在 `join()`、 `toLocaleString()`、`toString()` 和 `valueOf()` 方法返回的结果中以**空字符串**表示。

### 栈方法 LIFO

* 栈是一种 LIFO（Last-In-First-Out，**后进先出**）的数据结构，也就是**最新添加的项最早被移除**。   
* 而栈中项的插入（叫做推入）和移除（叫做弹出），只发生在一个位置——**栈的顶部**。
* ECMAScript 为数组专门提供了 `push()` 和 `pop()` 方法，以便实现类似栈的行为。
* `push()`：接收任意数量的参数，把它们逐个添加到数组末尾，并**返回修改后数组的长度**。
* `pop()`：从数组末尾移除最后一项，减少数组的 `length` 值，然后**返回移除的项**。

```JavaScript
colors.push("red"); // 在数组末尾推入新项，返回数组的长度
colors.pop();       // 从数组末尾移除最后一项，返回被移除的项
```

### 队列方法 FIFO

* 队列数据结构的访问规则是 FIFO（First-In-First-Out，**先进先出**）。队列在列表的末端添加项，从列表的前端移除项。
* `shift()`：移除数组中的第一项并返回该项，同时将数组长度减 1。
* `unshift()`: 在数组前端添加任意个项并返回新数组的长度。

```javascript
var item = colors.shift(); // 移除数组中的第一项并返回该项
var count= colors.unshift("red"); // 在数组前端添加任意个项并返回新数组的长度
```

模拟队列操作：
`shift()`：从数组前端移除一项。
`push()`：在数组末尾添加N项。


模拟反对列操作：
`unshift()`: 在数组前端添加N项。
`pop()`：从数组末端移除一项。

![队列操作和反队列操作](https://upload-images.jianshu.io/upload_images/2648731-9f596a2fd4a74f7c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)


### 重排序方法

* `reverse()`：反转数组项的顺序
* `sort()`：调用每个数组项的 `toString()` 转型方法，然后比较得到的**字符串**，以确定如何排序。

```JavaScript
var values = [1, 2, 3, 4, 5];
values.reverse(); // 反转数组项的顺序
values.sort();    // 默认字符串升序排序，最小的值位于最前面，最大的值排在最后面。

// sort() 方法可以接收一个比较函数作为参数，以便我们指定哪个值位于哪个值的前面。
// 1.构造比较函数（升序排序）
function compare(value1, value2) {
    // 如果第一个参数应该位于第二个参数之前，则返回负数。
    // 如果第一个参数应该位于第二个参数之后，则返回正数。
    // 如果两个参数相等，则返回0
    if(value1 < value2) {
        return -1; // 
    }else if (value1 > value2) {
        return 1;
    }else {
        return 0;
    }
}
// 2.将比较函数作为参数传递给 sort()
values.sort(compare);

// 对于数值类型或者其 valueOf() 方法会返回数值类型的对象类型，可以使用一个更简单的比较函数。
// 降序排序函数：
function compare(value1, value2) {
    return value2 - value1;
}
```

### 操作方法

1. `concat()`：基于当前数组中的所有项创建一个新数组（数组拼接）。

```JavaScript
var colors = ["red", "green", "blue"];
var colors2 = colors.concat["yellow", ["black", "brown"]];

alert(colors2); // red,green,blue,yellow,black,brown
```

2. `slice()`：基于当前数组中的一个或多个项创建一个新数组（数组截取）。
注：`slice()` 方法不会影响原始数组。

```JavaScript
var colors = ["red", "green", "blue", "yellow", "purple"];
// 指定一个参数，指定位置开始到结尾
var colors2 = colors.slice(1);    // green,blue,yellow,purple
// 指定两个参数，返回起始和结束位置之间的项，但不包括结束位置的项
var colors3 = colors.slice(1, 4); // green,blue,yellow
```


3. `splice()`：向数组的中部插入项。
`splice()` 方法始终会返回一个数组，该数组中包含从原始数组中删除的项

```JavaScript
// 3.1 删除，可以删除任意数量的项。指定两个参数，要删除的第一项位置和要删除的项数。
var colors4 = colors.splice(0, 2); // 删除数组中的前两项

// 3.2 插入，向指定位置插入任意数量的项。提供3个参数，起始位置、0（要删除的项数）和要插入的项...
var colors5 = colors.splice(2, 0 , "red", "green"); // 从当前数组的位置 2 开始插入字符串"red"和"green"。

// 3.3 替换，向指定位置插入任意数量的项，且同时删除任意数量的项
var colors6 = colors.splice(2, 1 , "red", "green"); // 删除当前数组位置 2 的项，然后再从位置 2 开始插入字符串 "red"和"green"
```

### 位置方法

* 位置方法，返回要查找的项在数组中的位置，全等操作符（`===`）查找


```JavaScript
// indexOf()：从数组的开头开始向后查找
// lastIndexOf()：从数组的末尾开始向前查找
// 如果未查到，返回-1
var numbers = [1,2,3,4,5,4,3,2,1];

alert(numbers.indexOf(4)); // 3
alert(numbers.lastIndexOf(4)); // 5

// 参数一：要查找的项；参数二：查找起点位置的索引
alert(numbers.indexOf(4,4)); // 5
alert(numbers.lastIndexOf(4,4)); // 3
```

### 迭代方法

每个方法都接收两个参数：要在每一项上运行的函数和（可选的）运行该函数的作用域对象——影响 `this` 的值。
传入这些方法中的函数会接收三个参数：数组项的值、该项在数组中的位置和数组对象本身。根据使用的方法不同，这个函数执行后的返回值可能会也可能不会影响方法的返回值。

* `every()`：对数组中的每一项运行给定函数，如果该函数对每一项返回true，则返回true。
* `filter()`：对数组中的每一项运行给定函数，返回该函数会返回true的项组成的数组。
* `forEach()`：对数组中的每一项运行给定函数，没有返回值。
* `map()`：对数组中的每一项运行给定函数，返回每次函数调用结果组成的数组。
* `some()`：对数组中的每一项运行给定函数，如果该函数对任一项返回true，则返回true。

```JavaScript
var numbers = [1,2,3,4,5,4,3,2,1];

var everyResult = numbers.every(function(item, index, array){
   return (item > 2); 
}); 
alert(everyResult); // false

var someResult = numbers.some(function(item, index, array){
   return (item > 2); 
}); 
alert(someResult); // true

var filterResult = numbers.filter(function(item, index, array){
    return (item > 2);
});
alert(filterResult); // [3,4,5,4,3],只返回满足条件的结果

var mapResult = numbers.map(function(item, index, array){
    return item * 2;
});
alert(mapResult); // [2,4,6,8,10,8,6,4,2]，返回每个调用结果
```

### 归并方法

这两个方法都会迭代数组的所有项，然后构建一个最终返回的值。

* `reduce()`，从数组的第一项开始，逐个遍历到最后。
* `reduceRight()`，从数组的最后一项开始，向前遍历到第一项。
* 这两个方法都接收两个参数，参数一：一个在每一项上调用的函数；参数二：作为归并基础的初始值。
* 传给它们的函数的接收四个参数：前一个值、当前值、项的索引和数组对象。
* 第一次迭代发生在数组的第二项上，因此第一个参数是数组的第一项，第二个参数就是数组的第二项。

```javascript
// 对数组求和
var values = [1, 2, 3, 4, 5];
var sum = values.reduce(function(prev, cur, index, array){
   return prev + cur; 
});

alert(sum); // 15
```

## Date 类型

![Date 类型](https://upload-images.jianshu.io/upload_images/2648731-1c72fd3e3e093db5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)


### 创建日期

1. 使用 `new` 操作符和 Date 构造函数创建日期：

```javascript
// 默认创建当前日期
var nowDate = new Date();
var result = nowDate.toLocaleDateString() + " "+ nowDate.toLocaleTimeString();
console.log(result); // 10/19/2018 5:10:38 PM
```

2. `Date.parse()` 方法
 
* `Date.parse()` 方法接收一个表示日期的字符串参数，然后尝试根据这个字符串返回相应日期的毫秒数。
* 如果传入 `Date.parse()` 方法的字符串不能表示日期，那么它会返回 `NaN`。

```JavaScript
var someDate = new Date(Date.parse("May 25, 2004"));

// 同上，直接传递日期字符串，后台会自动调用 Date.parse() 方法。
var someDate = new Date("May 25, 2004");
```

3. `Date.UTC()` 方法

`Date.UTC()` 的参数分别是年份、基于 0 的月份（一月是 0，二月是 1，以此类推）、月中的哪一天（1 到 31）、小时数（0 到 23）、分钟、秒以及毫秒数。
在这些参数中，只有前两个参数（年和月）是必需的。如果没有提供月中的天数，则假设天数为 1；如果省略其他参数，则统统假设为 0。

```JavaScript
// GTM 时间 2000年1月1日午夜零点
var y2k = new Date(Date.UTC(2000. 0)); 
// GTM 时间 2005年5月5日 下午 5:55:55
var allFives = new Date(Date.UTC(2005, 4, 5, 17, 55, 55)); 

// 使用 Date 构造函数时，默认基于系统本地时间
// 本地时间 2000年1月1日午夜零点
var y2k = new Date(2000. 0); 
// 本地时间 2005年5月5日 下午 5:55:55
var allFives = new Date(2005, 4, 5, 17, 55, 55); 
```

4. `Data.now()` 方法，返回表示调用这个方法时的日期和时间的毫秒数。

```JavaScript
var date = Date.now();
console.log(date); // 毫秒结果：1539938296542
console.log(date.toString()); // 1539939024850
console.log(date.toLocaleString()); // 1,539,939,024,850

// 调用函数
doSomething();
// 取得停止时间
var stop = Date.now(),
    result = stop - start;
```

### 继承的方法

* `toLocaleString()`：按照与浏览器设置的地区相适应的格式返回日期和时间。
* `toString()` 返回带有时区信息的日期和时间，其中时间一般以军用时间(即小时的范围是 0 到 23)表示。
* `valueOf()`：返回日期的毫秒数。

```JavaScript
// Data 类型的 valueOf() 方法返回日期的毫秒表示，
// 因此，可以方便的使用比较操作符（小于或大于）来比较日期值。
var date1 = new Date(2018, 10, 6);
var date2 = new Date(2018, 9, 6);
console.log(date1 < date2); // false
```

### 日期格式化方法


| 日期格式化方法 | 描述 |
| --- | --- |
| `toDateString()` | 以特定于实现的格式显示星期几、月、日和年 |
| `toTimeString()` | 以特定于实现的格式显示时、分、秒和时区 |
| `toLocalDateString()` | 以特定于地区的格式显示星期几、月、日和年 |
| `toLocalTimeString()` | 以特定于地区的格式显示时、分、秒 |
| `toUTCString()` | 以特定于实现的格式完整的 UTC 日期 |

其他参考：
* [npm 模块: moment.js](http://momentjs.com/docs/#/parsing/)
* [date-fns](https://date-fns.org/)


### 日期/时间组件方法

以下是直接取得和设置日期值中特定部分的方法：

> 注：UTC 日期指的是在没有时区偏差的情况下(将日期转换为 GMT 时间) 的日期值。

| 方法 | 说明 |
| --- | --- |
| `getTime` | 返回表示日期的毫秒数;与 `valueOf()` 方法返回的值相同 |
| `setTime(毫秒)` | 以毫秒数设置日期，会改变整个日期 |
| `getFullYear()` | 取得4位数的年份(如2007而非仅07) |
| `getUTCFullYear()` | 返回UTC日期的4位数年份 |
| `setFullYear(年)` | 设置日期的年份。传入的年份值必须是4位数字(如2007而非仅07) |
| `setUTCFullYear(年)` | 设置UTC日期的年份。传入的年份值必须是4位数字(如2007而非仅07) |
| `getMonth()` |  返回日期中的月份，其中0表示一月，11表示十二月 |
| `getUTCMonth()` | 返回UTC日期中的月份，其中0表示一月，11表示十二月 |
| `setMonth(月)` | 设置日期的月份。传入的月份值必须大于0，超过11则增加年份 |
| `setUTCMonth(月)` | 设置UTC日期的月份。传入的月份值必须大于0，超过11则增加年份 |
| `getDate()` | 返回日期月份中的天数(1到31) |
| `getUTCDate()` | 返回UTC日期月份中的天数(1到31) |
| `setDate(日)` | 设置日期月份中的天数。如果传入的值超过了该月中应有的天数，则增加月份 |
| `setUTCDate(日)` |设置UTC日期月份中的天数。如果传入的值超过了该月中应有的天数，则增加月份 |
| `getDay()` |返回日期中星期的星期几(其中0表示星期日，6表示星期六) |
| `getUTCDay()` | 返回UTC日期中星期的星期几(其中0表示星期日，6表示星期六) |
| `getHours()` | 返回日期中的小时数(0到23) |
| `getUTCHours()` | 返回UTC日期中的小时数(0到23) |
| `setHours(时)` | 设置日期中的小时数。传入的值超过了23则增加月份中的天数 |
| `setUTCHours(时)` | 设置UTC日期中的小时数。传入的值超过了23则增加月份中的天数 |
| `getMinutes()` | 返回日期中的分钟数(0到59) |
| `getUTCMinutes()` | 返回UTC日期中的分钟数(0到59) |
| `setMinutes(分)` | 设置日期中的分钟数。传入的值超过59则增加小时数 |
| `setUTCMinutes(分)` | 设置UTC日期中的分钟数。传入的值超过59则增加小时数 |
| `getSeconds()` |返回日期中的秒数(0到59) |
| `getUTCSeconds()` | 返回UTC日期中的秒数(0到59) |
| `setSeconds(秒)` | 设置日期中的秒数。传入的值超过了59会增加分钟数 |
| `setUTCSeconds(秒)` | 设置UTC日期中的秒数。传入的值超过了59会增加分钟数 |
| `getMilliseconds()` | 返回日期中的毫秒数 |
| `getUTCMilliseconds()` | 返回UTC日期中的毫秒数 |
| `setMilliseconds(毫秒)` | 设置日期中的毫秒数  |
| `setUTCMilliseconds(毫秒)`  | 设置UTC日期中的毫秒数 |
| `getTimezoneOffset()` | 返回本地时间与UTC时间相差的分钟数。|
 

## RegExp 类型

ECMAScript 通过 RegExp 类型来支持正则表达式。

![RegExp 类型](https://upload-images.jianshu.io/upload_images/2648731-172e6ba5aa629ddf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)


### 创建正则表达式

#### 格式
```javascript
/*
 * 模式（pattern）部分可以是任何简单或复杂的正则表达式，
 * 可以包含字符类、限定符、分组、向前查找以及反向引用。
 * 每个正则表达式都可带有一或多个标志(flags)，用以标明正则表达式的行为。
 */
var expression = /pattern / flags ;
```

| flags标志值 |    描述        |
| ----------- | ---------- |
| g           | 全局模式   |
| i           | 忽略大小写 |
| m           | 多行模式   |


#### 1. 字面量模式

```javascript
/*
 * 匹配字符串中所有“at”的实例
 */
var pattern1 = /at/g

/*
 * 匹配第一个“bat”或“cat”，不区分大小写
 */
var pattern2 = /[bc]at/i
```


#### 2. 构造函数模式

```javascript
/*
 * 构造函数的两个参数（字符串）
 * 参数一：要匹配的字符串模式。
 * 参数二：可选的标志字符串。
 */
var pattern3 = new RegExp("[bt]at", "i");
```

* 由于 RegExp 构造函数的模式参数是字符串，所以在某些情况下要对字符进行**双重转义**。所有元字符都必须双重转义，那些已经转义过的字符也是如此。
* 使用正则表达式字面量和使用 RegExp 构造函数创建的正则表达式不一样。在 ECMAScript 3 中，正则表达式字面量始终会共享同一个 RegExp 实例，而使用构造函数创建的每一个新 RegExp 实例都是一个新实例。
* ECMAScript 5 明确规定，使用正则表达式字面量必须像直接调用 RegExp 构造函数一样，每次都创建新的 RegExp 实例。IE9+、Firefox 4+和 Chrome 都据此做出了修改。


### RegExp 实例属性

RegExp 的每个实例属性都具有下列属性，通过这些属性可以取得有关模式的各种信息。

| RegExp 实例属性 | 描述 |
| --- | --- |
| `global` | 布尔值，表示是否设置了 g 标志。 |
| `ignoreCase` | 布尔值，表示是否设置了 i 标志。 |
| `lastIndex` | 整数，表示开始搜索下一个匹配项的字符位置，从 0 算起。 |
| `multiline` | 布尔值，表示是否设置了 m 标志。 |
| `source` | 正则表达式的字符串表示，按照字面量形式而非传入构造函数中的字符串模式返回。 |


### RegExp 实例方法

#### `exec()` 方法

* `exec()` 方法专门为**捕获组**而设计。
* `exec()` 接受一个参数，即要应用模式的字符串，然后返回包含第一个匹配项信息的数组；或者在没有匹配项的情况下返回 `null`。
* 返回的数组虽然是 Array 的实例，但包含两个额外的属性：`index` 和 `input`。
    * `index` 表示匹配项在字符串中的位置。
    * `input` 表示应用正则表达式的字符串。
* 在数组中，第一项是与整个模式匹配的字符串，其他项是与模式中的捕获组匹配的字符串(如果模式中没有捕获组，则该数组只包含一项)。

```javascript
var text = "mom and dad and baby";
var pattern = /mom( and dad( and baby)?)?/gi;

var maches  = pattern.exec(text);
console.log(maches.index); // 0
console.log(maches.input); // "mom and dad and baby"
console.log(maches[0]);    // "mom and dad and baby"
console.log(maches[1]);    // " and dad and baby"
console.log(maches[2]);    // " and baby"

/*
 * 说明：
 * 这个例子中的模式包含两个捕获组。最内部的捕获组匹配"and baby"，而包含它的捕获组匹配"and dad"或者"and dad and baby"。
 * 当把字符串传入 exec() 方法中之后，发现了一个匹配项。因为整个字符串本身与模式匹配，
 * 所以返回的数组 matchs 的 index 属性值为 0。
 * 数组中的第一项是匹配的整个字符串，第二项包含与第一个捕获组匹配的内容，第三项包含与第二个捕获组匹配的内容。
 */
```

对于 `exec()` 方法而言，即使在模式中设置了全局标志（g），它每次也只会返回一个匹配项。
* 在不设置全局标志的情况下，在同一个字符串上多次调用 `exec()` 将始终返回第一个匹配项的信息。
* 而在设置全局标志的情况下，每次调用 `exec()` 则都会在字符串中继续查找新匹配项。

```javascript
var text = "cat, bat, sat, fat";

// 模式一：不是全局模式
var pattern1 = /.at/; 

// 每次调用 exec() 返回的都是第一个匹配 项("cat")
// 非全局模式下，lastIndex 始终保持不变
var matches = pattern1.exec(text);        
alert(matches.index);    //0
alert(matches[0]);       //"cat"
alert(pattern1.lastIndex);//0

matches = pattern1.exec(text);        
alert(matches.index);    //0
alert(matches[0]);       //"cat"
alert(pattern1.lastIndex);//0 

// 模式二：全局模式
var pattern2 = /.at/g;

// 每次调用 exec() 都会返回字符串中的下一个匹配项，直至搜索到字符串末尾为止。
// 全局匹配模式下，lastIndex 的值在每次调用 exec() 后都会增加
var matches = pattern2.exec(text);        
alert(matches.index);    //0
alert(matches[0]);       //"cat"
alert(pattern2.lastIndex);//3

matches = pattern2.exec(text);        
alert(matches.index);    //5
alert(matches[0]);       //"bat"
alert(pattern2.lastIndex);//8
```


#### `test()` 方法

`test()` 方法接受一个字符串参数。在模式与该参数匹配的情况下返回 `true`；否则返回 `false`。

在只想知道目标字符串与某个模式是否匹配，但不需要知道其文本内容的情况下，使用这个方法非常方便。因此，`test()` 方法经常被用在 `if` 语句中。

#### `toLocaleString()` 和 `toString()` 方法

RegExp 实例继承的 `toLocaleString()` 和 `toString()` 方法都会返回正则表达式的字面量，与创建正则表达式的方式无关。

```JavaScript
var pattern = new RegExp("\\[bc\\]at", "gi");
alert(pattern.toString());    // /\[bc\]at/gi
alert(pattern.toLocaleString());    // /\[bc\]at/gi
```

> 正则表达式的 `valueOf()` 方法返回正则表达式本身。


### RexExp 构造函数属性

RexExp 构造函数属性适用于作用域中的所有正则表达式，并且基于所执行的最近一次正则表达式操作而变化。

| 长属性名 | 短属性名 | 说明 |
| --- | --- | --- |
| input | &#36;_ | 最近一次要匹配的字符串。Opera未实现此属性 |
| lastMatch | &#36;& | 最近一次的匹配项。Opera未实现此属性 |
| lastParen | &#36;+ | 最近一次匹配的捕获组。Opera未实现此属性 |
| leftContext | &#36;\` | input字符串中lastMatch之前的文本 |
| multiline | &#36;* | 布尔值，表示是否所有表达式都使用多行模式。IE和Opera未实现此属性 |
| rightContext | $&#39; | Input字符串中lastMatch之后的文本 |

使用这些属性可以从 `exec()` 或 `test()` 执行的操作中提取出更具体的信息。
（略）

### 模式的局限性

尽管 ECMAScript 中的正则表达式功能还是比较完备的，但仍然缺少某些语言(特别是 Perl)所支持的高级正则表达式特性。
参考：<https://www.regular-expressions.info/>


## Function 类型

**函数是对象，函数名是指针。**

每个函数都是 `Function` 类型的实例，而且都与其他引用类型一样具有属性和方法。由于函数是对象，因此函数名实际上也是一个指向函数对象的指针，不会与某个函数绑定。


![Function 类型](https://upload-images.jianshu.io/upload_images/2648731-2f9cb5fec80f85f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

### 函数声明&函数表达式

```javascript
// 1.函数声明语法
// 函数声明提升：解析器会率先读取函数声明，并使其在执行任何代码之前可用。
alert(sum(10,10));
function sum (num1, num2) {
    return num1 + num2;
}

// 2.函数表达式语法
// 使用函数表达式定义函数时，没有必要使用函数名
// 必须等到解析器执行到它所在的代码行，才会真正被解释执行。
var sum = function(num1, num2) {
    return num1 + num2;
}; // 末尾有分号

// 除了什么时候可以通过变量访问函数这一点区别之外，函数声明与函数表达式的语法其实是等价的。

// 3.Function 构造函数（不推荐）
// 最后一个参数会被看成函数体
var sum = new Function("num1", "num2", "return num1 + num2");
```

由于函数是**对象**，因此函数名实际上也是一个指向函数对象的指针，不会与某个函数绑定。

```JavaScript
function sum(num1, num2){
    return num1 + num2;
}        
alert(sum(10,10));    //20

// 声明变量 anotherSum，并将其设置为与 sum 相等(将 sum 的值赋给 anotherSum)。
// anotherSum 和 sum 都指向了同一个函数
// 💡 使用不带圆括号的函数名是访问函数指针，而非调用函数。
var anotherSum = sum;        
alert(anotherSum(10,10));  //20

sum = null;        
alert(anotherSum(10,10));  //20
```

### 没有重载

JavaScript 的函数没有函数签名机制，如果声明两个同名函数，后面的函数会覆盖前面的函数。

```JavaScript
function addSomeNumber (num) {
  return num + 100;
}

function addSomeNumber (num) {
  return num + 200;
}

var result = addSomeNumber(100); // 300
```

### 作为值的函数

函数可以作为值来使用。不仅可以像传递参数一样把一个函数传递给另一个函数，而且可以将一个函数作为另一个函数的结果返回。

```javascript
function callSomeFunction(someFunction, someArgument) {
  return someFunction(someArgument);
}
```

示例代码：

```javascript
// 根据某个对象属性对数组进行排序

// 该函数接收一个属性名，根据该属性名来创建一个比较函数
function createComparisonFunction(propertyName) {

    return function(object1, object2){
        // 使用方括号表示法取得给定属性的值
        var value1 = object1[propertyName];
        var value2 = object2[propertyName];

        if (value1 < value2){
            return -1;
        } else if (value1 > value2){
            return 1;
        } else {
            return 0;
        }
    };
}

var data = [{name: "Zachary", age: 28}, {name: "Nicholas", age: 29}];

// 按照每个对象的 name 属性值进行排序
data.sort(createComparisonFunction("name"));
alert(data[0].name);  //Nicholas

// 按照对象的 age 属性排序
data.sort(createComparisonFunction("age"));
alert(data[0].name);  //Zachary 
```

### 函数的内部属性

#### `arguments`、`arguments.callee`

* `arguments` 是一个类数组对象，包含着传入函数中的所有参数。
* `arguments` 对象有一个 `callee` 属性，该属性是一个指针，指向拥有这个 `arguments` 对象的函数。
* 严格模式下，访问 `arguments.callee` 会导致错误。

```javascript
// 示例：阶乘函数
function factorial(num) {
  if (num <= 1) {
    return 1;
  } else {
    return num * factorial(num - 1)
  }
}

// 使用 arguments.callee 属性解决函数名紧密耦合问题
// arguments.callee 属性，指向函数本身
function factorial(num) {
  if (num <= 1) {
    return 1;
  } else {
    return num * arguments.callee(num - 1)
  }
}
```

#### `this`

`this` 引用的是**函数据以执行的环境对象**。

```javascript
// 当在网页的全局作用域中调用函数时，this 对象引用的就是 window
window.color = "red";
var o = {color:"blue"}; // 新建了一个对象

function sayColor() {
    alert(this.color);
};

// 当在全局作用域中调用 sayColor() 时，this 引用的是全局对象 window
sayColor(); // "red"

// 把函数赋给对象 o 并调用 o.sayColor() 时，this 引用的是对象 o
o.sayColor = sayColor;
o.sayColor(); // "blue"        
```

#### `caller`

* `caller` 属性表示**调用当前函数的引用**，如果是在全局作用域中调用当前函数，它的值为 `null`。
* ECMAScript 5 还定义了 `arguments.caller` 属性，但在严格模式下访问它会导致错误，而在非严格模式下这个属性始终是 `undefined`。
* 严格模式还有一个限制：不能为函数的 `caller` 属性赋值，否则会导致错误。


```javascript
// ********* 示例一 *************
function outer(){
    inner();
}

// 因为 outer() 调用了 inter()，所以 inner.caller 就指向 outer()。
function inner(){
    alert(inner.caller);
}

outer();

// ********* 示例二 *************
// 为了实现更松散的耦合，也可以通过 arguments.callee.caller 来访问相同的信息。
function outer(){
    inner();
}

function inner(){
    alert(arguments.callee.caller);
}

outer();
```


### 函数属性和方法

ECMAScript 中的函数是**对象**，因此函数也有属性和方法。每个函数都包含两个属性：`length` 和 `prototype`。


#### `length` 属性：函数希望接收的命名参数的个数。

示例代码：
```JavaScript
function sayName(name){
    alert(name);
}      

function sum(num1, num2){
    return num1 + num2;
}

function sayHi(){
    alert("hi");
}

alert(sayName.length);  //1
alert(sum.length);      //2
alert(sayHi.length);    //0
```

#### `prototype` 属性

* `prototype` 保存了引用类型中的所有实例方法。
* 在 ECMAScript 5 中，`prototype` 属性是不可枚举的，因此使用 `for-in` 无法发现。


#### `apply()` 方法

每个函数都包含两个非继承而来的方法：`apply()` 和 `call()`。这两个方法的用途都是**在特定的作用域中调用函数**，实际上等于设置函数体内 `this` 对象的值。

`apply()` 方法接收两个参数：
* 参数一：在其中运行函数的作用域，
* 参数二：参数数组。其中，第二个参数可以是 Array 的实例，也可以是 `arguments` 对象。

```javascript
function sum(num1, num2) {
  return num1 + num2;
}

// callSum1() 在执行 sum() 函数时传入了 this 作为 this 值(因为是在全局作用域中调用的，所以传入的就是 window 对象)和 arguments 对象。
function callSum1(num1, num2) {
  return sum.apply(this, arguments); // 传入 arguments 对象
}

// callSum2 同样也调用了 sum() 函数，但它传入的则是 this 和一个参数数组。
function callSum2(num1, num2) {
  return sum.apply(this, [num1, num2]); // 传入数组
}

console.log(callSum1(10,10)); // 20
console.log(callSum2(10,10)); // 20
```

> 在严格模式下，未指定环境对象而调用函数，则 `this` 值不会转型为 window。
除非明确把函数添加到某个对象或者调用 `apply()` 或 `call()`，否则 `this` 值将是
`undefined`。

#### `call()` 方法

`call()` 方法与 `apply()` 方法的作用相同，它们的区别仅在于**接收参数的方式不同**。
对于 `call()` 方法而言，第一个参数是 `this` 值没有变化，变化的是其余参数都直接传递给函数。换句话说，在使用 `call()` 方法时，**传递给函数的参数必须逐个列举出来**。

```javascript
function sum(num1, num2) {
  return num1 + num2;
}

function callSum1(num1, num2) {
  return sum.call(this, num1, num2); // <--
}

console.log(callSum1(10,10)); // 20
```


事实上，传递参数并非 `apply()` 和 `call()` 真正的用武之地，它们真正强大的地方是能够**扩充函数赖以运行的作用域**。
使用 `call()` 或 `apply()` 来扩充作用域的最大好处，就是**对象不需要与方法有任何耦合关系**。

```javascript
window.color = "red";
var o = {color:"blue"};

function sayColor() {
  alert(this.color);
};

sayColor(); // "red"

sayColor.call(this); // red
sayColor.call(window); // red
sayColor.call(o); // blue
```

#### `bind()` 方法

`bind()` 方法会创建一个函数的实例，其 `this` 值会被绑定到传给 `bind()` 函数的值。

```javascript
window.color = "red";
var o = {color:"blue"};

function sayColor() {
  alert(this.color);
};

var objectSayColor = sayColor.bind(o);
objectSayColor(); //blue

/*
 * sayColor() 调用 bind() 并传入对象 o，创建了 objectSayColor() 函数。
 * objectSayColor() 函数的 this 值等于 o。
 * 因此即使是在全局作用域中调用这个函数，也会看到 "blue"。
 */
```

#### 继承的方法

* 每个函数继承的 `toLocaleString()` 和 `toString()` 方法始终都返回函数的代码。【返回格式因浏览器而异，通常用于**调试代码**】
* `valueOf()` 方法同样也只返回函数代码。


## 基本包装类型

![基本包装类型](https://upload-images.jianshu.io/upload_images/2648731-3e2b0875fc466c1e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

* 为了便于操作基本类型值，ECMAScript 还提供了 3 个特殊的引用类型：`Boolean`、`Number` 和
  `String`。
* 实际上，每当读取一个**基本类型值**，后台就会创建一个对应的**基本包装类型对象**，以便我们进行操作。

### 引用类型与基本包装类型

引用类型与基本包装类型的主要区别就是**对象的生存期**。使用 `new` 操作符创建的引用类型的实例， 在执行流离开当前作用域之前都一直保存在内存中。而自动创建的基本包装类型的对象，则只存在于一行代码的执行瞬间，然后立即被销毁。这意味着我们不能在运行时为基本类型值添加属性和方法。

示例代码：
```JavaScript
var s1 = "some text";
s1.color = "red";
alert(s1.color);   //undefined

/* 注释：
 * 第二行创建的 String 对象在执行第三行代码时已经被销毁了。 
 * 第三行代码又创建自己的 String 对象，而该对象没有 color 属性。
 */
```

### 创建基本包装类型


可以显式地调用 `Boolean`、`Number` 和 `String` 来创建基本包装类型的对象。不过，应该在绝对必要的情况下再这样做，因为这种做法很容易让人分不清自己是在处理基本类型还是引用类型的值。

对基本包装类型的实例调用 `typeof` 会返回 "object"，而且所有基本包装类型的对象都会被转换为布尔值 `true`。

> ⚠️ 
> 基本包装类型是**对象**。
> 所以对 `Boolean` 类型取布尔值也是 `true`，不管它包装的是 `true` 还是 `false`。

Object 构造函数也会像工厂方法一样，根据传入值的类型返回相应基本包装类型的实例。
  * 把字符串传给 Object 构造函数，就会创建 `String` 的实例；
  * 而传入数值参数会得到 `Number` 的实例。
  * 传入布尔值参数就会得到 `Boolean` 的实例。

```javascript
var obj = new Object("some text");
console.log(obj instanceof String); //true
```

使用 `new` 调用基本包装类型的构造函数，与直接调用同名的转型函数是不一样的：

```javascript
var value = "25";

// 变量 number 中保存的是基本类型的值 25
var number = Number(value); // 转型函数
console.log(typeof number); // "number"

// 变量 obj 中保存的是 Number 的实例
var obj = new Number(value) // 构造函数
console.log(typeof obj); // "object"
```

### `Boolean` 类型

`Boolean` 类型是与布尔值对应的**引用类型**。(`Boolean` 类型是对象！！！)

```JavaScript
// 使用构造函数创建 Boolean 类型
var booleanObject = new Boolean(true);
```

#### 💡 不建议使用 `Boolean` 对象

`Boolean` 对象在 ECMAScript 中的用处不大，因为它经常会造成人们的误解。其中最常见的问题就是在布尔表达式中使用 `Boolean` 对象（「对象」的布尔值是 `true`）：

```JavaScript
// 布尔表达式中的所有对象都会被转换为 true，因此 falseObject 对象在布尔表达式中代表的是 true
var falseObject = new Boolean(false);
var result = falseObject && true; // true && true = true
alert(result);  //true

var falseValue = false;
result = falseValue && true; // false && true = false
alert(result);  //false

alert(typeof falseObject);   //object
alert(typeof falseValue);    //boolean
alert(falseObject instanceof Boolean);  //true
alert(falseValue instanceof Boolean);   //false
```

> 💡💡💡 
> 基本类型与引用类型的布尔值有区别，建议永远不要使用 `Boolean` 对象。

### `Number` 类型

创建 `Number` 对象：
```JavaScript
var numberObject = new Number(10);
```

#### 继承的方法

* `valueOf()` 方法返回对象表示的**基本类型的数值**。
* `toLocaleString()` 和 `toString()` 返回**字符串形式的数值**。

可以为 `toString()` 方法传递一个表示基数的参数，告诉它返回几进制数值的字符串形式：
```JavaScript
alert(numberObject.toString());       //"10"
alert(numberObject.toString(2));      //"1010"
alert(numberObject.toString(8));      //"12"
alert(numberObject.toString(10));     //"10"
alert(numberObject.toString(16));     //"a"
```

#### 将数值格式化为字符串方法

* `toFixed()`：按照指定的小数位返回数值的字符串表示。
* `toExponential()`：返回以指数表示法（也称 e 表示法）表示的数值的字符串形式。
* `toPrecision()`：该方法接收一个参数，即表示数值的所有数字的位数(不包括指数部分)
* `toPrecision()` 会根据要处理的数值决定到底是调用 `toFixed()` 还是调用 `toExponential()`。

示例代码：
```javascript
// toFixed()
alert(numberObject.toFixed(2));    // "10.00"
// toFixed() 方法有自动舍入特性
var num = 10.005;
alert(num.toFixed(2));     //"10.01"

// toExponential()
var num = 10;
alert(num.toExponential(1)); //"1.0e+1"

// toPrecision()
var num = 99;
console.log(num.toPrecision(1)); // 1e+2
console.log(num.toPrecision(2)); // 99
console.log(num.toPrecision(3)); // 99.0
```

#### 💡 不建议使用 `Number` 对象

与 `Boolean` 对象类似，`Number` 对象也以后台方式为数值提供了重要的功能。
但与此同时，我们仍然不建议直接实例化 `Number` 类型，而原因与显式创建 `Boolean` 对象一样。
具体来讲，就是在使用 `typeof` 和 `instanceof` 操作符测试基本类型数值与引用类型数值时，得到的结果完全不同：

```javascript
var numberObject = new Number(10); // 基本包装类型，Number 对象
var numberValue = 99; // 基本类型

alert(typeof numberObject);   // object
alert(typeof numberValue);    // number
alert(numberObject instanceof Number);  // true
alert(numberValue instanceof Number);   // false
```


## `String` 类型

`String` 类型是字符串的对象包装类型：

```JavaScript
var stringObject = new String("hello world");
```

* `String` 类型继承的 `valueOf()`、`toLocaleString()` 和 `toString()` 方法，都返回对象所表示的基本字符串值。
* `String` 类型的每个实例都有一个 `length` 属性，表示字符串中包含多个字符。

### 1. 字符方法

两个用于访问字符串中特定字符的方法是：`charAt()` 和 `charCodeAt()`。
这两个方法都接收一个参数，即基于 0 的字符位置。

#### `charAt()` 方法

`charAt()` 方法以**单字符字符串**的形式返回给定位置的那个字符（ECMAScript 中没有字符类型）。

```JavaScript
var stringValue = "hello world";
console.log(stringValue.charAt(1)); // 返回字符："e"

// 使用方括号加数字索引来访问字符串中的特定字符
console.log(stringValue[1]); // 返回字符："e"
```

#### `charCodeAt()` 方法

`charCodeAt()` 方法返回**字符编码**：

```JavaScript
var stringValue = "hello world";
console.log(stringValue.charCodeAt(1)); // 返回字符编码："101"
```

### 2. 字符串操作符方法

#### `concat()` 方法

`concat()` 方法将一或多个字符串拼接起来，返回拼接得到的新字符串。

```JavaScript
var stringValue = "hello ";
var result = stringValue.concat("world"); alert(result); //"hello world" alert(stringValue); //"hello"
```

> 💡
> 实践中使用更多的还是加号操作符（+）。
> 而且，使用加号操作符在大多数情况下都比使用 `concat()` 方法要简便易行(特别是拼接多个字符串的情况)。

#### `slice()`、`substr()`、`substring()` 方法

基于子字符串创建新字符串。
返回被操作字符串的一个子字符串，接受一或两个参数。
* 参数一：指定子字符串的开始位置；
* 参数二：表示子字符串到哪里结束（在指定的情况下）；
* 如果没有传递第二个参数，则将字符串的长度作为结束位置。

```JavaScript
var stringValue = "hello world";
// ******** 参数为正值 ******** 
// 参数一：指定子字符串的开始位置
// 参数二：
//  * slice() 和 substring() 的第二个参数指定的是：子字符串最后一个字符后面的位置。
//  * substr() 的第二个参数指定的是：返回的字符个数。
alert(stringValue.slice(3)); //"lo world"
alert(stringValue.substring(3)); //"lo world"
alert(stringValue.substr(3)); //"lo world"
alert(stringValue.slice(3, 7)); //"lo w"
alert(stringValue.substring(3, 7)); //"lo w"
alert(stringValue.substr(3, 7)); //"lo worl"

// ******** 参数为负值 ******** 
// slice() 方法会将传入的负值与字符串的长度相加
// substring() 方法会把所有负值参数都转换为 0
// substr() 方法将负的第一个参数加上字符串的长度，而将负的第二个参数转换为 0。
alert(stringValue.slice(-3)); //"rld" ，slice(-3+11)
alert(stringValue.substring(-3)); //"hello world"，substring(0)
alert(stringValue.substr(-3)); //"rld"，substr(-3+11)
alert(stringValue.slice(3, -4)); //"lo w"
alert(stringValue.substring(3, -4)); //"hel"
alert(stringValue.substr(3, -4)); //"" (empty string)
```

### 3. 字符串位置方法

#### `indexOf()` 和 `lastIndexOf()` 方法。

从一个字符串中搜索给定的子字符串，然后返子字符串的位置（如果没有找到该子字符串，则返回-1）。
区别：
* `indexOf()`：从字符串的开头向后搜索子字符串。
* `lastIndexOf()`：从字符串的末尾向前搜索子字符串。
* `includes()` 🆕，如果在字符串中检测到指定文本则返回 true，否则返回 false。
* `startsWith()` 🆕，如果在字符串的**起始部分**检测到指定文本则返回 true，否则返回 false。
* `endsWith()` 🆕，如果在字符串的**结束部分**检测到指定文本则返回 true，否则返回 false。


```JavaScript
var stringValue = "hello world";

alert(stringValue.indexOf("o"));         //4
alert(stringValue.lastIndexOf("o"));     //7

// 可选的第二个参数，表示从字符串中的哪个位置开始搜索。
// indexOf() 会从该参数指定的位置向后搜索，忽略该位置之前的所有字符。
// lastIndexOf() 则会从指定的位置向前搜索，忽略该位置之后的所有字符。
alert(stringValue.indexOf("o", 6));         //7
alert(stringValue.lastIndexOf("o", 6));     //4      
```

在使用第二个参数的情况下，可以通过循环调用 `indexOf()` 或 `lastIndexOf()` 来找到所有匹配的子字符串：

```JavaScript
var stringValue = "Lorem ipsum dolor sit amet, consectetur adipisicing elit";
var positions = new Array();
// 在循环之外，首先找到了"e"在字符串中的初始位置
var pos = stringValue.indexOf("e"); 

while(pos > -1){
    positions.push(pos); // 每次搜索返回的位置依次被保存在数组 positions 中，以便将来使用。
    // 在循环内部，每次都给 indexOf() 传递上一次的位置加 1
    pos = stringValue.indexOf("e", pos + 1);
}

alert(positions);    //"3,24,32,35,52"
```

### 4. `trim()` 方法

* `trim()` 方法：创建一个字符串的副本，删除前置及后缀的所有空格，然后返回结果。
* `trimLeft()` 和 `trimRight()` 方法，分别用于删除字符串开头和末尾的空格。

```javascript
var stringValue = "     hello world    ";
var trimmedStringValue = stringValue.trim(); 
console.log(trimmedStringValue); // "hello world"
```

### 5. 字符串大小写转换方法

* `toLowerCase()`
* `toLocaleLowerCase()`
* `toUpperCase()`
* `toLocaleUpperCase()`

```JavaScript
var stringValue = "hello world";
alert(stringValue.toLocaleUpperCase());  //"HELLO WORLD"
alert(stringValue.toUpperCase());        //"HELLO WORLD"
alert(stringValue.toLocaleLowerCase());  //"hello world"
alert(stringValue.toLowerCase());        //"hello world"
```

### 6. 字符串的模式匹配方法

#### `match()` 方法

* `match()`：在字符串上调用这个方法，本质上与调用 RegExp 的 `exec()` 方法相同。 
* `match()` 方法只接受一个参数，要么是一个正则表达式，要么是一个 RegExp 对象。

```JavaScript
var text = "cat, bat, sat, fat"; 
var pattern = /.at/;

// match() 方法返回了一个数组
// 数组的第一项是与整个模式匹配的字符串，
// 之后的每一项（如果有）保存着与正则表达式中的捕获组匹配的字符串。
var matches = text.match(pattern);        
alert(matches.index);        //0
alert(matches[0]);           //"cat"
alert(pattern.lastIndex);    //0
```

#### `search()` 方法

* `search()` 方法返回字符串中第一个匹配项的索引；如果没有找到匹配项，则返回 -1。
* `search()` 方法始终是从字符串开头向后查找模式。
* `search()` 方法的参数：由字符串或 RegExp 对象指定的一个正则表达式。

```JavaScript
var text = "cat, bat, sat, fat"; 
var pos = text.search(/at/);
alert(pos);   //1
```

#### `replace()` 方法

`replace()`：替换子字符串方法。这个方法接受两个参数：
* 第一个参数可以是一个 RegExp 对象或者一个字符串（这个字符串不会被转换成正则表达式）。
* 第二个参数可以是一个字符串或者一个函数。
* 如果第一个参数是字符串，那么只会替换第一个子字符串。要想替换所有子字符串，唯一的办法就是提供一个正则表达式，而且要指定全局（g）标志。

```JavaScript
var text = "cat, bat, sat, fat";

var result = text.replace("at", "ond");
alert(result);    //"cond, bat, sat, fat"

result = text.replace(/at/g, "ond");
alert(result);    //"cond, bond, sond, fond"
```

`replace()` 方法的第二个参数也可以是一个函数。在只有一个匹配项（即与模式匹配的字符串）的情况下，会向这个函数传递 3 个参数：
* 模式的匹配项；
* 模式匹配项在字符串中的位置；
* 原始字符串；

```JavaScript
var text = "cat, bat, sat, fat";
function htmlEscape(text){
    return text.replace(/[<>"&]/g, function(match, pos, originalText){
        switch(match){
            case "<":
                return "&lt;";
            case ">":
                return "&gt;";
            case "&":
                return "&amp;";
            case "\"":
                return "&quot;";
        }             
    });
}

alert(htmlEscape("<p class=\"greeting\">Hello world!</p>")); //&lt;p class=&quot;greeting&quot;&gt;Hello world!&lt;/p&gt;
```

#### `split()` 方法

* `split()`：基于指定的分隔符将一个字符串分割成多个子字符串，并将结果放在一个数组中。
* 分隔符可以是字符串，也可以是一个 RegExp 对象（这个方法不会将字符串看成正则表达式）。
* `split()` 方法可以接受可选的第二个参数，用于指定数组的大小，以便确保返回的数组不会超过既定大小。

```JavaScript
var colorText = "red,blue,green,yellow";
var colors1 = colorText.split(",");      //["red", "blue", "green", "yellow"]
var colors2 = colorText.split(",", 2);   //["red", "blue"]
// 通过使用正则表达式，可以取得包含逗号字符的数组
// 第一项和最后一项是两个空字符串：因为通过正则表达式指定的分隔符出现在了字符串的开头(即子字符串"red")和末尾(即子字符串"yellow")
var colors3 = colorText.split(/[^\,]+/); //["", ",", ",", ",", ""]
```

### 7. `localeCompare()` 方法

`localeCompare()` 方法，比较两个字符串，并返回下列值中的一个:
* 如果字符串在字母表中应该排在字符串参数之前，则返回一个负数（大多数情况下是-1，具体的值要视实现而定）；
* 如果字符串等于字符串参数，则返回 0;
* 如果字符串在字母表中应该排在字符串参数之后，则返回一个正数（大多数情况下是 1，具体的值要视实现而定）。

示例：
```JavaScript
var stringValue = "yellow";       
alert(stringValue.localeCompare("brick"));  //1
alert(stringValue.localeCompare("yellow")); //0
alert(stringValue.localeCompare("zoo"));    //-1

// ⚠️ 因为 localeCompare() 返回的数值取决于实现，所以最好是像下面例子所示的这样使用这个方法。
function determineOrder(value) {
    var result = stringValue.localeCompare(value);
    if (result < 0){
        alert("The string 'yellow' comes before the string '" + value + "'.");
    } else if (result > 0) {
        alert("The string 'yellow' comes after the string '" + value + "'.");
    } else {
        alert("The string 'yellow' is equal to the string '" + value + "'.");
    }
}

determineOrder("brick");
determineOrder("yellow");
determineOrder("zoo");
```

### 8. `fromCharCode()` 方法

String 构造函数本身还有一个静态方法：`fromCharCode()`。
这个方法的任务是接收一或多个字符编码，然后将它们转换成一个字符串。从本质上来看，这个方法与实例方法 `charCodeAt()` 执行的是相反的操作。

```JavaScript
alert(String.fromCharCode(104, 101, 108, 108, 111)); //"hello"
```




### 9. HTML 方法

早期的 Web 浏览器提供商觉察到了使用 JavaScript 动态格式化 HTML 的需求。于是，这些提供商就扩展了标准，实现了一些专门用于简化常见 HTML 格式化任务的方法。

**现不推荐使用**。



## 单体内置对象

内置对象的定义：由 ECMAScript 实现提供的、不依赖于宿主环境的对象，这些对象在 ECMAScript 程序执行之前就已经存在了。

* 内置对象：例如 `Object`、`Array` 和 `String`。 
* ECMA-262 还定义了两个单体内置对象：`Global` 和 `Math`。 

![单体内置对象](https://upload-images.jianshu.io/upload_images/2648731-2d0c58ba2f175eb2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

### Global 对象

ECMAScript 中的 `Global` 对象在某种意义上是作为一个终极的 “兜底儿对象” 来定义的。换句话说，不属于任何其他对象的属性和方法，最终都是它的属性和方法。
事实上，没有全局变量或全局函数；所有在全局作用域中定义的属性和函数，都是 `Global` 对象的属性。

> 在大多数 ECMAScript 实现中都不能直接访问 `Global` 对象；
> 不过，Web 浏览器实现了承担该角色的 `window` 对象。

#### 1. URL 编码方法：`encodeURL()` 和 `encodeURIComponent()`

* `encodeURI()` 不会对本身属于 URI 的特殊字符进行编码，例如冒号、正斜杠、问号和井字号；
* `encodeURIComponent()` 则会对它发现的任何非标准字符进行编码。

```JavaScript
var uri = "http://www.wrox.com/illegal value.htm#start";

// 使用 encodeURL() 编码后的结果是除了空格之外的其他字符都原封不动，只有空格被替换成了 %20。
//"http://www.wrox.com/illegal%20value.htm#start"
alert(encodeURI(uri));

// encodeURIComponent() 方法则会使用对应的编码替换所有非字母数字字符。
//"http%3A%2F%2Fwww.wrox.com%2Fillegal%20value.htm%23start"
alert(encodeURIComponent(uri));
```

#### 2. URL 解码方法：`decodeURI()` 和 `decodeURIComponent()`

* `decodeURI()` 只能对使用 `encodeURI()` 替换的字符进行解码。例如，它可将 %20 替换成一个空格，但不会对 %23 作任何处理，因为 %23 表示井字号(#)，而井字号不是使用 `encodeURI()` 替换的。
* `decodeURIComponent()` 能够解码使用 `encodeURIComponent()` 编码的所有字符，即它可以解码任何特殊字符的编码。

```JavaScript
var uri = "http%3A%2F%2Fwww.wrox.com%2Fillegal%20value.htm%23start";

// 调用 decodeURI() 输出的结果中，只有 %20 被替换成了空格。
//http%3A%2F%2Fwww.wrox.com%2Fillegal value.htm%23start
alert(decodeURI(uri));

// 所有特殊字符的编码都被替换成了原来的字符，得到了一个未经转义的字符串
//http://www.wrox.com/illegal value.htm#start
alert(decodeURIComponent(uri));
```


#### 3. `eval()` 方法

`eval()` 方法就像是一个完整的 ECMAScript 解析器，它只接受一个参数，即要执行 的ECMAScript（或 JavaScript）字符串。

```JavaScript
eval("alert('hi')");
// 以上代码的作用等价于下面的代码：
alert("hi");
```

当解析器发现代码中调用 `eval()` 方法时，它会将传入的参数当作实际的 ECMAScript 语句来解析，然后把执行结果插入到原位置。
通过 `eval()` 执行的代码被认为是包含该次调用的执行环境的一部分，因此被执行的代码具有与该执行环境相同的作用域链。

```JavaScript
// 函数 sayHi() 是在 eval() 内部定义的。但由于对 eval() 的调用最终会被替换成定义函数的实际代码，因此可以在下一行调用 sayHi()。
eval("function sayHi() { alert('hi'); }");
sayHi();

// 变量也是这样
eval("var msg = 'hello world'; ");
alert(msg);     //"hello world"

// 严格模式下，在外部访问不到 eval() 中创建的任何变量或函数，因此前面两个例子都会导致错误。
// 同样，在严格模式下，为 eval() 赋值也会导致错误:
"use strict";
eval = "hi"; //causes error
```


#### 4. Global 对象的所有属性

![](https://upload-images.jianshu.io/upload_images/2648731-7b98b7a96a75712a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

#### 5. windows 对象

ECMAScript 虽然没有指出如何直接访问 `Global` 对象，但 Web 浏览器都是将这个全局对象作为 `window` 对象的一部分加以实现的。
因此，在全局作用域中声明的所有变量和函数，就都成为了 `window` 对象的属性。

```JavaScript
// 全局变量
var color = "red";

// 全局函数
function sayColor(){
    // 通过 window.color 来访问 color 变量，以说明全局变量是 window 对象的属性
    alert(window.color);
}

// 使用 window.sayColor() 来直接通过 window 对象调用这个函数
window.sayColor();  //"red"
```

### Math 对象

* ECMAScript 还为保存数学公式和信息提供了一个公共位置，即 `Math` 对象。
* 与我们在 JavaScript 直接编写的计算功能相比，`Math` 对象提供的计算功能执行起来要快得多。

#### 1. Math 对象的属性

Math 对象包含的属性大都是数学计算中可能会用到的一些特殊值。

![](https://upload-images.jianshu.io/upload_images/2648731-4fb959122ee188cd.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)


#### 2. `min()` 和 `max()` 方法

`min()` 和 `max()` 方法用于确定一组数值中的最小值和最大值。这两个方法都可以接收任意多个数值参数。

```JavaScript
var max = Math.max(3, 54, 32, 16);
alert(max); // 54

var min = Math.min(3, 54, 32, 16);
alert(min); // 3
```

找到数组中的最大值或者最小值：
```JavaScript
// 把 Math 对象作为 apply() 的第一个参数，从而正确地设置 this 值。然后，可以将任何数组作为第二个参数。
var values = [1, 2, 3, 4, 5, 6, 7, 8]
var max = Math.max.apply(Math, values);
```

#### 3. 舍入方法

将小数值舍入为整数的几个方法：

| 舍入方法 | 描述 |
| --- | --- |
| `Math.ceil()` | 执行向上舍入，即它总是将数值向上舍入为最接近的整数; |
| `Math.floor()` | 执行向下舍入，即它总是将数值向下舍入为最接近的整数; |
| `Math.round()` | 执行标准舍入，即它总是将数值四舍五入为最接近的整数；|

```JavaScript
// 取大值
alert(Math.ceil(25.9));     //26
alert(Math.ceil(25.5));     //26
alert(Math.ceil(25.1));     //26

// 四舍五入
alert(Math.round(25.9));    //26
alert(Math.round(25.5));    //26
alert(Math.round(25.1));    //25

// 取小值
alert(Math.floor(25.9));    //25
alert(Math.floor(25.5));    //25
alert(Math.floor(25.1));    //25
```


#### 4. `random()` 方法

`Math.random()` 方法返回大于等于 0 小于 1 的一个随机数。

示例代码一：返回一个1到10之间到随机数：
```JavaScript
var num = Math.floor(Math.random() * 10 + 1);
```

示例代码二：
```JavaScript
// 该函数表示返回 lowerValue 到 upperValue 之间的一个值
function selectFrom(lowerValue, upperValue) {
    // 用最大值减最小值再加 1 得到了可能值的总数
    var choices = upperValue - lowerValue + 1;
    return Math.floor(Math.random() * choices + lowerValue);
}

var num = selectFrom(2, 10);
alert(num);   // 介于2和10之间(包括2和10)的一个数值

var colors = ["red", "green", "blue", "yellow", "black", "purple", "brown"];
var color = colors[selectFrom(0, colors.length-1)];
alert(color);  //any of the strings in the array
```

#### 5. 其他方法

![](https://upload-images.jianshu.io/upload_images/2648731-3d0d98eb5b9fd43f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)




