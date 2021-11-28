* [八大排序算法](http://www.jianshu.com/p/e75a2eb8b312)

## 冒泡排序算法
### 算法原理
1. 比较相邻两个数。如果前一个数大于后一个数，就交换它们。
2. 对每一对相邻的元素作同样的工作，从开始第一对到结尾的最后一对。在这一点，最后的元素应该会是最大的数。
3. 针对所有的元素重复以上的步骤，除了最后一个。
4. 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。

### 示例代码（Objective-C）
```
// 冒泡排序算法
- (void)BubbleSortMethod {
    NSMutableArray *mutableArray = @[@40,@8,@15,@18,@12].mutableCopy;
    NSLog(@"初始化序列：%@",mutableArray);
    
    // 性能优化：提前计算数组元素个数，避免每一次循环都 mutable.count 去数个数。
    NSUInteger count = mutableArray.count;
    for (int i=0; i<count-1; i++) {
        for (int j=0; j<count-1-i; j++) {
            NSLog(@"i =%d,j=%d:\n", i, j);
            
            if (mutableArray[j] > mutableArray[j+1]) {
                [mutableArray exchangeObjectAtIndex:j withObjectAtIndex:j+1];
            }
        }
        NSLog(@"%@",mutableArray);
    }
}
```
示例说明：
1. i = 0 时，两两比较前5个数，最后第5个数会是所有数中最大的。共比较4次；
2. i = 1 时，两两比较前4个数，最后第4个数会是（前4个数中）最大的。共比较3次；
3. i = 2 时，两两比较前3个数，最后第3个数会是（前3个数中）最大的。共比较2次；
4. i = 3 时，比较前两个数，使第2个数比第1个数大。共比较1次；
5. 5个数冒泡排序，共比较10（4+3+2+1）次。



## 插入排序算法
### 算法原理

### 示例代码（Objective-C）
```
// @"98",@"76",@"109",@"34",@"67",@"190",@"80"

// 按从小到大插入排序
// 每一轮排序，当前轮次最小的数被插入到最前的位置
- (void)insertSort:(NSMutableArray *)array {
    NSUInteger count = array.count;
    for (int i=0; i<count-1; i++) {
        // 如果前面一位大于后面一位
        if ([array[i] intValue] > [array[i+1] intValue]) {
            // 保存后面一位
            NSString *temp = array[i+1];
            // 从后一位开始
            for (int y=i+1; y>0; y--) {
                if ([array[y-1] intValue] > [temp intValue]) {
                    // 如果前一位大于temp值，前一位向后移留一个空位，temp值插入
                    [array exchangeObjectAtIndex:y-1 withObjectAtIndex:y];
                    NSLog(@"i=%d,y=%d;\n%@",i, y, array);
                }
            }
        }
    }
}
```
示例说明：
* 首先整个序列初始化时是无序的。
* 从第二个数开始，从前向后排序，把小的数插入到前面的有序序列中。

把一个新数插入到有序序列中。
```
// 直接插入排序算法

- (void)insertSortMethod {
    NSMutableArray *mutableArray = @[@5,@8,@15,@40].mutableCopy;
    NSLog(@"初始化序列：\n%@",mutableArray);
    
    NSNumber *newNumber = @10;
    [mutableArray enumerateObjectsUsingBlock:^(NSNumber *obj, NSUInteger idx, BOOL * _Nonnull stop) {
        if (newNumber <= obj) {
            [mutableArray insertObject:newNumber atIndex:idx];
            *stop = YES;
        }
    }];
    NSLog(@"插入新数字后序列：\n%@",mutableArray);
}
```


## 逻辑相关性排序
> 无论是冒泡排序还是插入排序，它们都是按照数字的大小排序的，从大到小或者从小到大，总是有可比性。如果被排序的是中文呢？而且不是按照拼音首字母来排序，需要按照逻辑相关性排序。

比如说，返回的数据顺序是：年龄、姓名、住址、身份证号码、性别。
而在UI上显示时，需要按照：姓名、性别、身份证号码、年龄、住址。

一种解决方法是提前写好标准顺序，返回的数据按照标准顺序排序并显示：
示例代码：
```
- (void)sortMethod {
    // 原始数据 - 字典类型9个
    NSDictionary *oldDict = @{
                              @"姓名":@"张三",
                              @"员工号":@"123456",
                              @"性别":@"男",
                              @"单位代码":@"88888",
                              @"单位名称":@"Apple研发中心",
                              @"基本工资":@"$2017",
                              @"身份证号码":@"130403196211061258",
                              @"入职时间":@"20170709",
                              @"工作地址":@"北京",
                            };
    NSLog(@"原始字典：\n%@",oldDict);
    
    // 标准顺序 -11个
    NSArray *sortArray = @[@"姓名",
                           @"性别",
                           @"身份证号码",
                           @"员工号",
                           @"单位名称",
                           @"单位代码",
                           @"单位地址",
                           @"部门",
                           @"入职时间",
                           @"基本工资",
                           @"其它数据"];
    
    // 用 NSMutableSet 实例变量保存字典的 keys，
    // 为什么不用 NSArray 保存key ？因为向 NSSet 对象查询某一个对象是否存在更快。
    NSMutableSet *allKeys = [NSMutableSet setWithArray:oldDict.allKeys];
    
    __block NSMutableArray *sortedMutableArray = [NSMutableArray arrayWithCapacity:allKeys.count];
    [sortArray enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
        if ([allKeys containsObject:obj]) {
            [allKeys removeObject:obj];
            [sortedMutableArray addObject:(NSString *)obj];
        }
    }];
    
    // 如果原始数据中有标准顺序没有收录的key,(如：工作地址)
    [allKeys enumerateObjectsUsingBlock:^(id  _Nonnull obj, BOOL * _Nonnull stop) {
        [sortedMutableArray addObject:obj];
    }];
    
    NSLog(@"排好的key的顺序%@",sortedMutableArray);
    NSLog(@"按照指定顺序获取数据");
    for (NSString *key in sortedMutableArray) {
        NSLog(@"%@ -> %@",key,oldDict[key]);
    }
}
```

