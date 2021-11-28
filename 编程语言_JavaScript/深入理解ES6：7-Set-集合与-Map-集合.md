## ECMAScript 6 中的 Set 集合

* Set 集合是一种无重复元素的列表。
* Set 集合中的 +0 和 -0 被认为是相等的。

### 创建 Set 集合并添加元素

```js
// 创建 Set 集合
let set = new Set();

// 用数组来初始化 Set 集合
let set = new Set([1, 2, 3, 4, 5, 5, 5, 5]);
console.log(set.size); // 5

// 添加元素
set.add(5);
set.add('5');

// 获取集合元素数量
console.log(set.size); // 2

// 检测 Set 集合中是否存在某个值
console.log(set.has(5)); // true
```

### 移除元素

* `delete()` 方法：移除 Set 集合中的某一个元素。
* `clear()` 方法：移除集合中的所有元素。


### Set 集合的 forEach 方法

`Set` 集合的 `forEach` 方法的三个参数：
* 参数一：Set 集合下一次索引的位置。
* **参数二：同参数一！！！！**
* 被遍历的 Set 集合本身。

```javascript
set.forEach(function(value, key, ownerSet) {
  console.log(key + ' ' + value);
  console.log(ownerSet === set);
});

/*
1 1
true
2 2
true
*/
```

### 将 Set 集合转换为数组

```js
let set = new Set([1, 2, 3, 4, 5, 5, 5, 5]);

// 通过展开运算符将 Set 集合转换为数组
let array = [...set];

console.log(array); // [ 1, 2, 3, 4, 5 ]
```


### Weak Set 集合

**Weak Set 集合只存储对象的弱引用，并且不可以存储原始值**；集合中的弱引用如果是对象唯一的引用，则会被回收并释放相应内存。

```js
let set = new WeakSet(),
  key = {};

// Set.add()：向集合中添加对象
set.add(key);

// Set.has()：检查集合中是否存在指定对象的引用
console.log(set.has(key)); // true

// Set.delete()：向集合中删除一个元素
set.delete(key);

console.log(set.has(key)); // false
```

如果你只需要跟踪对象引用，你更应该使用 Weak Set 集合而不是普通的 Set 集合。


## ECMAScript 6 中的 Map 集合

Map 集合内含有多组键值对，集合中每个元素分别存放着可访问的键名和它对应的值。

在 Map 集合中，可以将对象名作为键名。

```js
let map = new Map();

// 向 Map 集合中添加新的元素
map.set('key', 'value');

// 从 Map 集合中获取信息
console.log(map.get('key')); // value
```

### Map 集合支持的方法

* `has(key)`：检测指定的键名在 Map 集合中是否已经存在。
* `delete(key)`：从 Map 集合中移除指定键名及其对应的值。
* `clear()`：移除 Map 集合中的所有键值对。


### Map 集合的初始化方法

可以向 Map 构造函数传入数组来初始化一个 Map 集合。

```js
let map = new Map([
  ['name', 'Nicholas'],
  ['age', 25]
]);

console.log(map.has('name')); // true
console.log(map.get('name')); // Nicholas
console.log(map.has('age')); // true
console.log(map.get('age')); // 25
console.log(map.size); // 2
```

### Map 集合的 forEach() 方法

```js
let map = new Map([
  ['name', 'Nicholas'],
  ['age', 25]
]);

map.forEach(function(value, key, ownerMap) {
  console.log(key + ' ' + value);
  console.log(ownerMap === map);
});

/* 
name Nicholas
true
age 25
true
*/
```

### Weak Map 集合

* Weak Map 是弱引用 Map 集合，用于存储对象的弱引用。
* Weak Map 集合中的键名必须是一个对象。
* Weak Map 集合中的键值对，只有键名会遵从垃圾回收机制，如果值是一个对象，则保存的是对象的强引用，不会触发垃圾回收机制。
* Weak Map 集合最大的用途是保存 Web 页面中的 DOM 元素。




