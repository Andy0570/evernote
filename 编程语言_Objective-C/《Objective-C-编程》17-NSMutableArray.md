* `NSMutableArray` 是可变数组，数组中存储空间可以动态修改，即可以添加、删除或对指针重新进行排序。
* `NSMutableArray` 继承于 `NSArray`。因此 `NSMutableArray` 可以使用 `NSArray` 的所有方法。

### 数组的创建

```objectivec
Person *p1 = [[Person alloc] init];
Person *p2 = [[Person alloc] init];
Person *p3 = [[Person alloc] init];
 
// 1.初始化时添加元素
NSMutableArray *mArray1 = [[NSMutableArray alloc] initWithObjects:p1,p2,p3, nil];

// 2.创建空数组，再往里面添加
NSMutableArray *mArray2 = [[NSMutableArray alloc] init];
// 类方法
NSMutableArray *mArray21 = [NSMutableArray array];

// 3.创建数组时，同时为数组分配3个存储空间，当3个空间全满之后，会自动扩展空间。
NSMutableArray *mArray1 = [[NSMutableArray alloc] initWithCapacity:3];
```

#### 关于 `arrayWithCapacity:` 方法

```objectivec
NSMutableArray * myArray = [NSMutableArray arrayWithCapacity: 10];
id myVariable = myArray[5];
```
以上代码运行报错：
```bash
***Terminating app due to uncaught exception 'NSRangeException', reason: '*** 
-[__NSArrayM objectAtIndex:]: index 5 beyond bounds for empty array'
```

向 `NSMutableArray` 对象发送 `arrayWithCapacity:10` 消息意味着，在内部，数组会将自己设置为可以容纳 10 个对象的内存空间。但它**仍然没有创建任何对象**。

通常情况下，一个空数组被创建和设置为只容纳几个对象。当你添加的对象越来越多，内部数组需要调整大小。这就意味着需要 `malloced` 更多的内存，然后将旧的对象移到新的内存中，并将旧的内存清理掉。

如果你提前知道数组未来将容纳 N 个对象，那么使用 `arrayWithCapacity` 方法可以让数组的执行更有效率。你仍然可以添加更多的对象，但是这样一来，效率优化就会失去。

除此以外，你不能访问一个数组的索引超过其当前的计数。因为一开始的计数是`0`，所以你不能访问任何对象。你需要先添加对象，然后才能访问对象。

总结：通过向 `NSMutableArray` 对象发送 `arrayWithCapacity:` 消息并传递参数的方式来初始化可变数组时，实际上并不会创建任何对象，也就是说这个数组仍然是空的。如果你知道这个可变数组未来会容纳的数量 N，那么在初始化时就传递参数 N 可以极大提升性能，但是一旦数量传递过小（比如说你初始化时设置 10，但未来可能这个 `NSMutableArray` 实际上存储了 11 个对象），虽然并不会影响这个 `NSMutableArray` 的正常使用，但之前底层的优化特性就会消失。

参考：[stackoverflow: Understanding arrayWithCapacity: method on NSMutableArray](https://stackoverflow.com/questions/22395963/understanding-arraywithcapacity-method-on-nsmutablearray)

### `addObject:` 添加元素

`addObject:` 方法会在数组的尾部添加对象。

```objectivec
[mArray1 addObject:p1];
```

### `count:` 取得数组元素个数

```objectivec
NSLog(@"%ld",[mArray1 count]);
```

### 将数组 mArray2 中所有元素全部追加到 mArray1 中

```objectivec
[mArray1 addObjectsFromArray:mArray2];
```

### 指定位置插入元素

```objectivec
[mArray1 insertObject:@"hello" atIndex:0];
```

### 替换元素

```objectivec
[mArray1 replaceObjectAtIndex:0 withObject:@"替换的元素"];
```

### 索引 0 和索引 3 上的元素调换，可用于排序。

```objectivec
[mArray1 exchangeObjectAtIndex:0 withObjectAtIndex:3];
```

反转数组的实例：
获取到的 `NSMutableArray` 类型的数据源需要逆序显示到通过 `UITableView` 显示的页面上，之前用的方法是在索引上动手脚: `NSUInteger index = self.dateSourceArray.count - indexPath.row - 1;`,缺点是在 `tableView:cellForRowAtIndexPath:` 和   `tableView:didSelectRowAtIndexPath:` 都要先获取反向索引值再获取数据。参考 YYKit 的源码中的方法，直接在数据源上做处理：`[self.dateSourceArray reverse];`

```objectivec
// 反转数组，摘自 YYCategories 
- (void)reverse {
    NSUInteger count = self.count;
    int mid = floor(count / 2.0);
    for (NSUInteger i = 0; i < mid; i++) {
        [self exchangeObjectAtIndex:i withObjectAtIndex:(count - (i + 1))];
    }
}
```

### 删除元素

```objectivec
//1>根据下标删除元素
[mArray1 removeObjectAtIndex:0];
    
//2>删除指定的对象
[mArray1 removeObject:@“hello”];
    
//3>删除最后一个元素
[mArray1 removeLastObject];
    
//4>删除所有元素
[mArray1 removeAllObjects];
```

### 排序

使用给定的排序描述符数组对接收方进行排序:
```objectivec
- (void)sortUsingDescriptors:(NSArray<NSSortDescriptor *> *)sortDescriptors;
```

例：
> 排序前：BMITime[5045:651300] employees: (
    "<Employee 0: $0 in assets>",
    "<Employee 1: $503 in assets>",
    "<Employee 2: $469 in assets>",
    "<Employee 3: $768 in assets>",
    "<Employee 4: $0 in assets>",
    "<Employee 5: $836 in assets>",
    "<Employee 6: $819 in assets>",
    "<Employee 7: $384 in assets>",
    "<Employee 8: $0 in assets>",
    "<Employee 9: $486 in assets>"
)

```objectivec
//  先根据 valueOfAssets 属性进行 升序 排序
 NSSortDescriptor *voa = [NSSortDescriptor sortDescriptorWithKey:@"valueOfAssets" ascending:YES];
// 如果某两个对象的 valueOfAssets 属性相同，再根据 employeeID 属性 升序 排序
NSSortDescriptor *eid = [NSSortDescriptor sortDescriptorWithKey:@"employeeID" ascending:YES];
        
[employees sortUsingDescriptors:@[voa,eid]];
```

排序后：
> BMITime[5010:648085] employees: (
    "<Employee 0: $0 in assets>",
    "<Employee 4: $0 in assets>",
    "<Employee 8: $0 in assets>",
    "<Employee 7: $384 in assets>",
    "<Employee 2: $469 in assets>",
    "<Employee 9: $486 in assets>",
    "<Employee 1: $503 in assets>",
    "<Employee 3: $768 in assets>",
    "<Employee 6: $819 in assets>",
    "<Employee 5: $836 in assets>"
)
