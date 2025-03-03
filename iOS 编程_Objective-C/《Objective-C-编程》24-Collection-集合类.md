Collection 类的实例用于保存指向其他对象的指针。Collection 类包括：
![](http://upload-images.jianshu.io/upload_images/2648731-31f74128e559fe76.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##  NSArray 、NSMutableArray

* [第17章——NSArray](http://www.jianshu.com/p/aaa89e070f84)
* [第17章——NSMutableArray](http://www.jianshu.com/p/64443aecc4dd)

## NSSet 、NSMutableSet

* `NSSet` 对象所包含的“内容”是**无序**的。而且在一个 `NSSet` 对象中，某个特定的对象**只能出现一次**（唯一性）。
* `NSSet` 对象的最大用处是**检查某个对象是否存在**，`NSSet` 完成此类任务的速度比数组快得多。

### NSSet 与 NSArray 的区别：

* `NSSet` 和数组一样，是个数据容器。
* 数组是有序的，`NSSet `是无序的。
* 数组可以容纳重复对象，`NSSet` 不能。
* 数组是有下标的，`NSSet` 没有下标。

### NSSet 的创建

```objectivec
// alloc 创建
NSSet *set1 = [[NSSet alloc] initWithObjects:@“张三”,@“李四”, nil];

// 类方法创建
NSSet *set2 = [NSSet setWithObjects:s@“张三”,@“李四”，nil];
```

###  其他集合常用创建方法

```objectivec
// 创建一个新的集合1，集合2有两个对象(a和b)
NSSet *set1 = [NSSet setWithObjects:@"a", nil];
NSSet *set2 = [set1 setByAddingObject:@"b"];
	
// 通过已有集合7和集合8，创建一个新的集合9
NSSet *set7 = [NSSet setWithObjects:@"a", nil];
NSSet *set8 = [NSSet setWithObjects:@"z", nil];
NSSet *set9 = [set7 setByAddingObjectsFromSet:set8];
	
// 通过已有的集合10和数组对象，创建一个新的集合11
NSArray *array = [NSArray arrayWithObjects:@"a", @"b", @"c", nil]; 
NSSet *set10 = [NSSet setWithObjects:@"z", nil];
NSSet *set11 = [set10 setByAddingObjectsFromArray:array]; 
```

### NSSet 与 NSArray 相互转换	

```objectivec
// NSSet -> NSArray
NSArray *array = [set1 allObjects];

// NSArray --> NSSet
// 如果同一个对象在数组中出现多次，那么它只会添加一次到给定的集合。 每个对象在被添加到集合中时只会收到一次保留消息。
NSSet *set3 = [NSSet setWithArray:array];
```

### 返回集合元素的个数

```objectivec
NSUInteger count = [set1 count];
```

### 从容器中随机取出一个元素

```objectivec
NSString *s = [set1 anyObject];
```

### 判断对象是否在 NSSet 中已经存在 

`- (BOOL)containsObject:(ObjectType)anObject;`

示例：
```objectivec
BOOL isContain = [set1 containsObject:@"李四"]; 
```

* `NSSet` 对象中的对象是无序的，所以不能通过索引来访问，只能向 `NSSet` 对象查询某一个对象是否存在。
* 当 `NSSet` 收到 `- (BOOL)containsObject:(ObjectType)anObject;` 消息时，会在其包含的对象中查找和 anObject **相等**的对象 (内容相同，地址可能不同，也就是说可能 【指针指向的内容是相等的】，但是他们两个【未必是同一块地址】，返回 **YES**)。
* 因此，相同的变量（指针指向同一块内存地址）一定是相等的，而相等的变量不一定是相同的。


## NSMutableSet

`NSMutableSet` 是可变集合，它继承自 `NSSet`。

### 常用方法

```objectivec
// 创建一个空的集合
NSMutableSet *set1 = [NSMutableSet set];
NSMutableSet *set2 = [NSMutableSet setWithObjects:@"1", @"2", nil]; 
	
// 集合2“减去”集合3中的元素
[set2 minusSet:set3];
	
// 集合2与集合3中元素的交集
[set2 intersectSet:set3]；
	
// 集合2与集合3中的元素的并集；
[set2 unionSet:set3];
	
// 将空集合1设置为集合3中的内容
[set1 setSet:set3];
	
// 根据数组的内容删除集合中的对象
[set2 addObjectsFromArray:array];
[set2 removeObject:@"1"];
[set2 removeAllObjects];  	
```

## NSDictionary

* 字典是由 **键—值**对组成的数据容器, 通过  **key** (键，通常是字符串)，查找对应 **value** (值，可以是任意类型的对象)。
* 字典中的 **键—值**对是**无序**的。
* 字典对象中的**键**都是独一无二的，如果想要在现存的键后再添加第二个对象，第一个键—值对就会被替换掉。
* 字典中的**key**可以是任意类型，一般使用字符串作为一个元素的 key。
* `NSDictionary` 是不可变字典。

### 字典的创建

```objectivec
NSArray *array1 = @[@"zhang3",@"zhangfei"];
NSArray *array2 = @[@"li4",@"liming"];
 
/*
 字典的结构
 第一个元素：key:@"zhang"     value:array1
 第二个元素：key:@"li"        value:array2
 ...
 */

// 1.alloc方法创建
NSDictionary *dic1 = [[NSDictionary alloc] initWithObjectsAndKeys:
                      array1,@"zhang",
                      array2,@"li", nil];
                      
// xcode4.4以后对字典的创建和访问,语法上做了优化。
NSDictionary *dic11 = @{
        @"zhang": array1,
        @"li": array2
};
                       
// 2.类方法创建字典 
NSDictionary *dic2 = [NSDictionary dictionaryWithObjectsAndKeys: 
                                      array1,@"zhang",
                                      array2,@"li", nil];
```

### 返回字典元素个数

```objectivec
NSUInteger count = [dic1 count];
```

### 通过 key 获取对应的 value

```objectivec
NSArray *array3 = [dic1 objectForKey:@"zhang"];

// 简便写法
NSArray *array3 = dic1[@"zhang"];
```

### 取得所有的 keys

```objectivec
NSArray *allkeys = [dic1 allKeys];
```

### 取得所有的 Values

```objectivec
NSArray *allvalue = [dic1 allValues];  //同下get方法
NSArray *allvalue2 = dic1.allValues;
```


### 遍历字典    

```objectivec
// 1.普通遍历
NSArray *keys = [dic1 allKeys];   //NSDictionary -> NSArray
for (int i =0;i <keys.count;i++) {
  NSString *key = keys[i]; //通过下标获取对象key

  NSArray *names =[dic1 objectForKey:key]; //通过key获取对应的value
  NSLog(@"key=%@,value=%@",key,names);
}

// 2.快速遍历
for (NSString *key in dic1) {
  NSArray *names = [dic1 objectForKey:key];
  NSLog(@"key=%@,value=%@",key,names);
}
```



### 删除服务器返回的 value 为 NULL 的情况

```objectivec
#import <Foundation/Foundation.h>

/**
 范畴
 */
@interface NSDictionary (MyCustomize)

/**
 填充value为空造成的crash

 @return 修改后的NSDictionary
 */
- (NSDictionary *)deleteAllNULLValue;

@end

#import "NSDictionary+MyCustomize.h"

/**
 自定义范畴类
 */
@implementation NSDictionary (MyCustomize)

- (NSDictionary *)deleteAllNULLValue {
    NSMutableDictionary *mutableDic = [[NSMutableDictionary alloc] init];
    for (NSString *keyStr in self.allKeys) {
        if ([[self objectForKey:keyStr] isEqual:[NSNull null]]) {
            [mutableDic setObject:@"null" forKey:keyStr];
            HQLog(@"changed!");
        }
        else{
            [mutableDic setObject:[self objectForKey:keyStr] forKey:keyStr];
            HQLog(@"no null");
        }
    }
    return mutableDic;
}

@end
```

## NSMutableDictionary

`NSMutableDictionary` 是可变字典，字典中的存储是可以修改的。`NSMutableDictionary` 继承于 `NSDictionary`。

### 创建可变字典

```objectivec
// 1.alloc 方法创建
NSArray *array = [NSArray arrayWithObjects:@"张三",@"李四",@"王五", nil];
NSMutableDictionary *mdic = [[NSMutableDictionary alloc] 
	                           initWithObjectsAndKeys:array,@"names", nil];
	
NSMutableDictionary *mdic1 = [[NSMutableDictionary alloc] init];
[mdic1 setObject:array forKey:@"names"]; // 添加元素
	
NSMutableDictionary *mdic2 = [[NSMutableDictionary alloc] initWithCapacity:3];
```


### 添加元素

```objectivec
// 如果key是重复的，会将之前的value覆盖掉
[mdic setObject:@"value" forKey:@"zhang"];
	
// 将另外一个字典所有的元素添加到mdic中
[mdic addEntriesFromDictionary:<#(nonnull NSDictionary *)#>];
```


### 删除元素

```objectivec
// 1>根据Key删除元素
[mdic removeObjectForKey:@"names"];
    
// 2>根据多个key删除多个元素
[mdic removeObjectForKey:@[@"zhang",@"li"]];
    
// 3>删除所有元素
[mdic removeAllObjects];
```

## 数组排序

[第17章——NSMutableArray](http://www.jianshu.com/p/64443aecc4dd)

## 过滤

1. 匹配字符串：正则表达式

```objectivec
// 正则表达式:验证码
- (BOOL) validateYzm:(NSString *)yzm {
    NSString *regex = @"^\\d{4}$";
    NSPredicate *predicate = [NSPredicate predicateWithFormat:@"SELF MATCHES %@",regex];
    return [predicate evaluateWithObject:yzm];
}
```

2. 过滤 collection

* `NSMutableArray` 有一个名为 ` - filterUsingPredicate:` 的方法，通过该方法可以很方便地剔除所有不能“满足”传入的 `NSPredicate` 对象的对象。
* `NSArray` 有一个名为 `filteredArrayUsingPredicate:` 的方法，可以创建一个新的数组，包含所有能够满足传入的 `NSPredicate` 对象的对象。

```objectivec
// 要收回拥有物品总价值高于75美元的员工的物品
NSPredicate *predicate = [NSPredicate predicateWithFormat:@"holder.valueOfAssets > 70"];

NSArray *toBeReclaimed = [allAssets filteredArrayUsingPredicate:predicate];

NSLog(@"toBeReclaimed:%@",toBeReclaimed);
```

`NSSet` 也有一个方法可以来做过滤，代码如下：
```objectivec
- (NSSet<ObjectType> *)filteredSetUsingPredicate:(NSPredicate *)predicate;
```

`NSMutableSet` 也有一个这样的方法，如下：
```objectivec
- (void)filterUsingPredicate:(NSPredicate *)predicate;
```

## NSNull

collection 对象不能保存 `nil`。如果要将“空洞”加入集合对象中，可以使用 `NSNull` 类。`NSNull` 类只有一个实例，代表“空”。

示例代码如下：
```objectivec
NSMutableArray *hotel = [[NSMutableArray alloc] init];

// 底楼大厅
[hotel addObject:@"lobby"];

// 二楼泳池
[hotel addObject:@"pool"];

// 三楼未建，为空！！！
[hotel addObject:[NSNull null]];

// 四楼卧室
[hotel addObject:@"bedrooms"];
```

## NSIndexSet

这个类用于存储有序的索引到某种数据结构，比如数组。例如，使用这个类可以生成一份数组对象的索引号清单，这些对象满足指定的查询条件。

### `- (NSUInteger)indexOfObjectPassingTest:`

示例代码：

```objectivec
NSArray *array = @[@1, @2, @3, @4, @5];

// 遍历数组，取出满足条件的数组索引
NSUInteger result = [array indexOfObjectPassingTest:^BOOL(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
    if ([(NSNumber *)obj intValue] % 2 == 0) {
        return YES;
    }else {
        return NO;
    }
}];

// 如果找到一个匹配，则查看并打印此项
if (result != NSNotFound) {
    NSLog(@"%@",array[result]); 
}

// >输出：2
```

结论：只可以返回一个索引。


### `- (NSIndexSet *)indexesOfObjectsPassingTest:`

示例代码：

```objectivec
NSArray *array = @[@1, @2, @3, @4, @5];

// 遍历数组，取出满足条件数组的所有索引
NSIndexSet *result = [array indexesOfObjectsPassingTest:^BOOL(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
    if ([(NSNumber *)obj intValue] % 2 == 0) {
        return YES;
    }else {
        return NO;
    }
}];

// 遍历 NSIndexSet
[result enumerateIndexesUsingBlock:^(NSUInteger idx, BOOL * _Nonnull stop) {
    NSLog(@"%@",array[idx]);
}];

// Output：
// 2017-11-17 10:20:13.694049+0800 Test[39023:9364337] 2
// 2017-11-17 10:20:13.694204+0800 Test[39023:9364337] 4
```

### NSIndexSet 常用方法

```objectivec
// 创建一个空的索引集合
+ (instancetype)indexSet;
// 给定索引创建索引集合
+ (instancetype)indexSetWithIndex:(NSUInteger)value;
// 给定索引范围创建索引集合
+ (instancetype)indexSetWithIndexesInRange:(NSRange)range;

- (instancetype)initWithIndexesInRange:(NSRange)range NS_DESIGNATED_INITIALIZER;
// 根据另一个索引集合创建索引集合
- (instancetype)initWithIndexSet:(NSIndexSet *)indexSet NS_DESIGNATED_INITIALIZER;

// 给定索引值初始化索引集合
- (instancetype)initWithIndex:(NSUInteger)value;

// 判断两个索引集合是否相等
- (BOOL)isEqualToIndexSet:(NSIndexSet *)indexSet;

// 是否包含指定的索引
- (BOOL)containsIndex:(NSUInteger)value;
// 是否包含索引范围
- (BOOL)containsIndexesInRange:(NSRange)range;

// 遍历索引集合方法
- (void)enumerateIndexesUsingBlock:(void (NS_NOESCAPE ^)(NSUInteger idx, BOOL *stop))block API_AVAILABLE(macos(10.6), ios(4.0), watchos(2.0), tvos(9.0));
- (void)enumerateIndexesWithOptions:(NSEnumerationOptions)opts usingBlock:(void (NS_NOESCAPE ^)(NSUInteger idx, BOOL *stop))block API_AVAILABLE(macos(10.6), ios(4.0), watchos(2.0), tvos(9.0));
- (void)enumerateIndexesInRange:(NSRange)range options:(NSEnumerationOptions)opts usingBlock:(void (NS_NOESCAPE ^)(NSUInteger idx, BOOL *stop))block API_AVAILABLE(macos(10.6), ios(4.0), watchos(2.0), tvos(9.0));

// ...
```
