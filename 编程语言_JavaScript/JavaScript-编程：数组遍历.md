## 数组遍历

### 传统语法

```javascript
const colors = [ 'red', 'green', 'blue' ];

for (let index = 0; index < colors.length; index++) {
  console.log(colors[index]);
}
```



###  `forEach` 语法

```javascript
const colors = [ 'red', 'green', 'blue' ];

colors.forEach(color => {
  console.log(color);
});
```



### `for-of`语法

```javascript
const colors = [ 'red', 'green', 'blue' ];

for (const color of colors) {
  console.log(color);
}
```



## 迭代方法

*  `every()`  对数组中的每一项运行给定函数，如果该函数对每一项返回 true，则返回 true。
*  `filter()` 对数组中的每一项运行给定函数，返回该函数会返回 true 的项组成的数组。
* `forEach()` 对数组中的每一项运行给定函数，没有返回值。
* `map()`   对数组中的每一项运行给定函数，返回每次函数调用结果组成的数组。
* `some()`  对数组中的每一项运行给定函数，如果该函数对任一项返回 true，则返回 true。



### `every()` 方法

场景：判断数组中每个本书的 `year` 是否都大于 2018（即：一假即假）。

```javascript
const books = [
  { id: 1, name: 'book1', year: 2019 },
  { id: 2, name: 'book2', year: 2018 },
  { id: 3, name: 'book3', year: 2017 },
];

const every = books.every(book => {
  return book.year > 2018;
});

console.log(every);
// false
```



### `some()` 方法

场景：判断数组中是否存在 year 小于 2018 的元素（即：一真即真）。

```javascript
const books = [
  { id: 1, name: 'book1', year: 2019 },
  { id: 2, name: 'book2', year: 2018 },
  { id: 3, name: 'book3', year: 2017 },
];

const some = books.some(book => {
  return book.year < 2018;
});

console.log(some);
// true
```



### `filter()` 方法

场景：过滤掉数组中元素 `weight` 小于 10 的元素（即：大于等于 10 才有效）。

```javascript
const numbers = [ 1, 2, 4, 8, 16 ];

const filtered = numbers.filter(value => {
  return value >= 10;
});

console.log(filtered);
// [ 16 ]
```



### `map()` 方法

场景：数组每个元素翻倍，存储到另一个数组中。

```javascript
const numbers = [ 1, 2, 4, 8, 16 ];

const double = numbers.map(value => {
  return value * 2;
});

console.log(double);
// [ 2, 4, 8, 16, 32 ]
```



### `find()` 方法

场景：找到并返回数组中值为 8 的元素。

```javascript
const numbers = [ 1, 2, 4, 8, 16 ];

const found = numbers.find(number => {
  return number === 8;
});

console.log(found);
// 8
```



## 归并方法

* `reduce()`   从数组的第一项开始，逐个遍历到最后。
* `reduceRight()` 从数组的最后一项开始，向前遍历到第一项。



### `reduce()` 方法

```javascript
const numbers = [ 2, 4, 8, 16 ];

const sum = numbers.reduce((previoutNumber, currentNumber) => {
  return previoutNumber + currentNumber;
});

console.log(sum);
// 30
```


