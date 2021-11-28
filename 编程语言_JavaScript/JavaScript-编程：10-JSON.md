# JSON 是一种数据格式，不是一种编程语言

* JSON 是 JavaScript 的一个严格的子集，利用了 JavaScript 中的一些模式来表示结构化数据。
* JSON 只是一种数据格式。很多编程语言都有针对 JSON 的解析器和序列化器。


JSON 的语法支持 3 种类型的值：
* **简单值**：可以在 JSON 中表示字符串、数值、布尔值和 null。不支持 undefined。
* **对象**：无序的键值对。
* **数组**：有序的值的列表。

JSON 不支持变量、函数或对象实例，它就是一种表示结构化数据的格式，虽然与 JavaScript 中表示数据的某些语法相同，但它并不局限于 JavaScript 的范畴。

推荐阅读：[JSON 介绍](http://www.json.org/json-zh.html)

## 简单值

* JSON 字符串必须使用双引号。
* 布尔值和 null 也是有效的 JSON 形式。


## 对象
* 与 JavaScript 不同，JSON 中对象的属性名任何时候都必须加双引号。


## 数组
* JSON 数组采用的就是 JavaScript 中的数组字面量形式。


## 解析与序列化

可以把 JSON 数据结构解析为有用的 JavaScript 对象。


* `stringify()`：把 JavaScript 对象序列化为 JSON 字符串。
* `parse`：把 JSON 字符串解析为原生 JavaScript 值。

```javascript
var book = {
  title: 'Professional JavaScript',
  authors: ['Nicholas C. Zakas'],
  edition: 3,
  year: 2011
};

var jsonText = JSON.stringify(book);

console.log(jsonText);
// {"title":"Professional JavaScript","authors":["Nicholas C. Zakas"],"edition":3,"year":2011}
```


### 序列化选项

实际上，`JSON.stringify()` 除了要序列化的 JavaScript 对象外，还可以接收另外两个参数，这两个参数用于指定以不同的方式序列化 JavaScript 对象：
* 第一个参数是个过滤器，可以是一个数组，也可以是一个函数；
* 第二个参数是一个选项，表示是否在 JSON 字符串中保留缩进。

单独或组合使用这两个参数，可以更全面深入地控制 JSON 的序列化。

#### 1.过滤结果

如果过滤器参数是数组，那么 `JSON.stringify()` 的结果中将只包含数组中列出的属性。

```javascript
var book = {
  title: 'Professional JavaScript',
  authors: ['Nicholas C. Zakas'],
  edition: 3,
  year: 2011
};

// 过滤器参数是数组
var jsonText = JSON.stringify(book, ['title', 'edition']);

console.log(jsonText);
// {"title":"Professional JavaScript","edition":3}
```

如果第二个参数是函数，行为会稍有不同。传入的函数接收两个参数，属性(键)名和属性值。根据属性(键)名可以知道应该如何处理要序列化的对象中的属性。属性名只能是字符串，而在值并非键值对结构的值时，键名可以是空字符串。

```javascript
var book = {
  title: 'Professional JavaScript',
  authors: ['Nicholas C. Zakas'],
  edition: 3,
  year: 2011
};

var jsonText = JSON.stringify(book, function(key, value) {
  switch (key) {
    case 'authors':
      return value.join(','); // 将数组连成字符串返回
    case 'year':
      return 5000;
    case 'edition':
      return undefined; // 返回 undefined，则相应的属性会被忽略
    default:
      return value; // 函数返回的值就是相应键的值
  }
});

console.log(jsonText);
// {"title":"Professional JavaScript","authors":"Nicholas C. Zakas","year":5000}
```

#### 2.字符串缩进

`JSON.stringify()` 方法的第三个参数用于控制结果中的缩进和空白符。如果这个参数是一个数值，那它表示的是每个级别缩进的空格数。

```javascript
var book = {
  title: 'Professional JavaScript',
  authors: ['Nicholas C. Zakas'],
  edition: 3,
  year: 2011
};

// 每个级别缩进 4 个空格
var jsonText = JSON.stringify(book, null, 4);

console.log(jsonText);
// {
//    "title": "Professional JavaScript",
//    "authors": [
//        "Nicholas C. Zakas"
//    ],
//    "edition": 3,
//    "year": 2011
//}
```

`JSON.stringify()` 也在结果字符串中插入了换行符以提高可读性。只要传入有效的控制缩进的参数值，结果字符串就会包含换行符。（只缩进而不换行意义不大。）最大缩进空格数为 10，所有大于 10 的值都会自动转换为 10。

如果缩进参数是一个字符串而非数值，则这个字符串将在 JSON 字符串中被用作缩进字符(不再使用空格)。

```javascript
var book = {
  title: 'Professional JavaScript',
  authors: ['Nicholas C. Zakas'],
  edition: 3,
  year: 2011
};

var jsonText = JSON.stringify(book, null, '- -');

console.log(jsonText);
//{
//- -"title": "Professional JavaScript",
//- -"authors": [
//- -- -"Nicholas C. Zakas"
//- -],
//- -"edition": 3,
//- -"year": 2011
//}
```

#### 3. `toJSON()` 方法

可以给对象定义 `toJSON()` 方法，返回其自身的 JSON 数据格式。

> 类似于在 Objective-C 语言中，你可以给对象定义 `description` 方法那样。

假设把一个对象传入 `JSON.stringify()`，序列化该对象的顺序如下：
1. 如果存在 `toJSON()` 方法而且能通过它取得有效的值，则调用该方法。否则返回对象本身。
2. 如果提供了第二个参数，应用这个函数过滤器。传入函数过滤器的值是第 1 步返回的值。
3. 对第 2 步返回的每个值进行相应的序列化。
4. 如果提供了第三个参数，执行相应的格式化。


### 解析选项

`JSON.parse()` 方法也可以接收另一个参数，该参数是一个函数，将在每个键值对上调用。

为了区别 `JSON.stringify()` 接收的替换（过滤）函数（replacer），这个函数被称为还原函数（reviver），但实际上这两个函数的签名是相同的——它们都接收两个参数，一个键和一个值，而且都需要返回一个值：
* 如果还原函数返回 `undefined`，则表示要从结果中删除相应的键;
* 如果返回其他值，则将该值插入到结果中。

```JavaScript
// JavaScript
var book = {
  title: 'Professional JavaScript',
  authors: ['Nicholas C. Zakas'],
  edition: 3,
  year: 2011,
  // releaseDate 属性，保存一个 Date 对象
  releaseDate: new Date(2011, 11, 1)
};

// JavaScript -> JSON
var jsonText = JSON.stringify(book);
console.log(jsonText);
// {"title":"Professional JavaScript","authors":["Nicholas C. Zakas"],"edition":3,"year":2011,"releaseDate":"2011-11-30T16:00:00.000Z"}

// JSON -> JavaScript
var bookCopy = JSON.parse(jsonText, function(key, value) {
  if (key == 'releaseDate') {
    return new Date(value); // 解析时，将 JSON 字符串还原为 Date 对象
  } else {
    return value;
  }
});

// 因为 bookCopy.releaseDate 属性中会保存一个 Date 对象，
// 你才能基于这个 Date 对象调用其对应的 getFullYear() 方法
console.log(bookCopy.releaseDate.getFullYear());
// 2011
```
