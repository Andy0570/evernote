
生成器是一种返回迭代器的函数。

```js
function* createIterator() {
  yield 1;
  yield 2;
  yield 3;
}

// 生成器的调用方式与普通函数相同，只不过返回的是一个迭代器
let iterator = createIterator();

console.log(iterator.next().value); // 1
console.log(iterator.next().value); // 2
console.log(iterator.next().value); // 3
```
