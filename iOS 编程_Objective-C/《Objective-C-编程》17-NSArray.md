* `NSArray` 实例可以保存一组 (指向其他对象的)  **指针**；
* `NSArray` 的实例是无法改变的。一旦 `NSArray` 实例被创建后，就无法添加或删除数组里的指针,  也无法改变数组的指针顺序；
* `NSArray`数组是个数据容器，可以往该容器里任意添加多个对象；
* `NSArray`数组中**只能存放对象类型，不能存放基本数据类型**；
* `NSArray`数组中存入对象的类型需要一致；
* `NSArray` 中的指针是**有序排列**的，可以通过索引（index）存取数组中的对象；
* 注意数组越界问题。

## NSArray 的常用方法

### 数组的创建

```objectivec
// 创建 3 个 NSDate 对象
NSDate *now = [NSDate date];
NSDate *tomorrow = [now dateByAddingTimeInterval:24.0 * 60.0 * 60.0];
NSDate *yesterday = [now dateByAddingTimeInterval:- 24.0 * 60.0 * 60.0];

// 创建一个数组包含这三个 NSDate 对象
// 1. 使用字面量语法
NSArray *dateList1 = @[now,tomorrow,yesterday];

// 2. 旧式数组方法,类方法
NSArray *dateList2 = [NSArray arrayWithObjects:now,tomorrow,yesterday, nil];

// 3. 初始化方法
NSArray *dateList3 = [[NSArray alloc] initWithObjects:now,tomorrow,yesterday, nil];

// 4. 创建数组并且往数组中存入一个元素
NSArray *dateList4 = [NSArray arrayWithObject:now];

// 5. 创建一个数组，数组中的元素来自另一个数组
NSArray *dateList5 = [NSArray arrayWithArray:dateList1];
```

### 通过下标获取对象

用来访问 `NSArray` 实例中指针的语句称为**下标**。

* 1⃣️ 字面量语法:

```objectivec
NSLog(@"%@",dateList1[0]);
```

* 2⃣️ `objectAtIndex:` 方法

```objectivec
NSLog(@"%@",[dateList1 objectAtIndex:1]);
```

### 获取数组中的元素个数：`count` 方法

```objectivec
NSUInteger count = [array count];
```

### 获取数组中某个元素，防止数组越界

```objectivec
/// 来自YYCategories
- (id)objectOrNilAtIndex:(NSUInteger)index {
    return index < self.count ? self[index] : nil;
}
```

### 判断数组中是否包含某一个对象

```objectivec
BOOL isContains = [array containsObject:@"list"];
```

> 注：如果数组中的元素都是非重复对象的话，建议使用 `NSSet` 来检查某个对象是否存在，因为后者的速度更快。

### 查找出数组中某个对象的索引

```objectivec
NSUInteger index = [array indexOfObject:@"lisi--"];
if (index == NSNotFound) {
    NSLog(@"数组中没有此元素");
}
```

注：  
* `- (NSUInteger)indexOfObject:(ObjectType)anObject;`
* `- (NSUInteger)indexOfObjectIdenticalTo:(ObjectType)anObject;`

两者都会返回相应数组值等于给定对象的最低索引。区别在于第一个方法会枚举数组中的每一个对象并发送 ```isEqual:``` 消息（anObject 会作为实参传入），检查是否 “相等”（内容相等）。第二个方法也会枚举数组中的每一个对象，但是会用 `==` 运算符来和 anObject 做比较，检查是否“相同”(是否还指向同一块内存地址)。

### 使用连接符将数组中的字符串拼接起来

```objectivec
NSString *joinString = [array componentsJoinedByString:@"-"];
```

#### 将字符串分割后存进数组中

```objectivec
NSString *s = @"ios-Android-Windows";
NSArray *separete = [s componentsSeparatedByString:@"-"];
```

### `NSString` 和 `NSArray` 的相互转换：

```objectivec
NSArray *dataSourceArray = @[@"[https://test1.jpg](https://test1.jpg)",
                             @"[https://test2.jpg](https://test2.jpg)",
                             @"[https://test3.jpg](https://test3.jpg)"];

// NSArray -> NSString
NSString *imageString = [dataSourceArray componentsJoinedByString:@","];
// @"[https://test1.jpg,https://test2.jpg,https://test3.jpg](https://test1.jpg,https://test2.jpg,https://test3.jpg)"

// NSString -> NSArray
NSArray *imagesArray = [imageString componentsSeparatedByString:@","];
// @[@"[https://test1.jpg](https://test1.jpg)",@"[https://test2.jpg](https://test2.jpg)",@"[https://test3.jpg](https://test3.jpg)"]
```


### 访问数组中最后一个元素

```objectivec
NSString *lastObj = [array lastObject];
```

### 追加对象，array1追加1个对象，存放到array2中

```objectivec
NSArray *array2 = [array1 arrayByAddingObject:@"赵六"];
```

## 数组的遍历

### 1.使用传统的 `for` 循环来遍历数组

```objectivec
NSUInteger dateCount = [dateList1 count];
for (int i = 0; i < dateCount; i++) {
   NSDate *d = dateList1[i];
   NSLog(@"Here is a date:%@",d);
}
```

### 2.快速枚举法（for-in）

快速枚举（fast enumeration）是一种简化的 `for` 循环语法，它能够过高效地遍历数组中的所有元素。使用快速枚举省去了对数组计数的麻烦。

```objectivec
for (NSDate *d in dateList1) {
    NSLog(@"Here is a date:%@",d);
}
```

💡 当使用快速枚举法遍历  `NSMytableArray` 类型的数组时，不能在枚举过程中增加或者删除数组中的指针。如果遍历时需要添加或删除指针，则需要使用标准的 `for` 循环。

### 3.基于 Block 对象的枚举法

推荐使用基于 `block` 的枚举方法：
```objectivec
- (void)enumerateObjectsUsingBlock:(void (NS_NOESCAPE ^)(ObjectType obj, NSUInteger idx, BOOL *stop))block;
- (void)enumerateObjectsWithOptions:(NSEnumerationOptions)opts usingBlock:(void (NS_NOESCAPE ^)(ObjectType obj, NSUInteger idx, BOOL *stop))block;
- (void)enumerateObjectsAtIndexes:(NSIndexSet *)s options:(NSEnumerationOptions)opts usingBlock:(void (NS_NOESCAPE ^)(ObjectType obj, NSUInteger idx, BOOL *stop))block;
```

示例代码：
```objectivec
NSArray *array;
[array enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
    // Do something with 'object'
    if (shouldStop) {
        *stop = YES;
    }
}];
```
