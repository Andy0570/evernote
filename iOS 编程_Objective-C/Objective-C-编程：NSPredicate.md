**Cocoa** 框架中提供了 `NSPredicate` 类，指定过滤器的条件。将符合条件的对象保留下来。


### NSPredicate 使用示例
```objectivec
// 待匹配字符串
NSString *string = @"201708----201710";
// 正则表达式
NSString *regix = @"^\\d{6}\\D+\\d{6}$";
// 创建 NSPredicate 实例对象
NSPredicate *predicate = [NSPredicate predicateWithFormat:@"SELF MATCHES %@",regix];
// 返回正则表达式匹配结果，YES or NO
BOOL isMatch = [predicate evaluateWithObject:string];
```

### NSString 使用示例
```objectivec
// 待匹配字符串
NSString *string = @"Hello World123";
// 返回匹配到的第一个字符串范围
NSRange range = [string rangeOfString:@"\\d+" options:NSRegularExpressionSearch];
// 如果匹配到就打印匹配到的子字符串，在这里为123。
if (range.location != NSNotFound){
    NSLog(@"%@",[string substringWithRange:range]);
}
```

### NSRegularExpression 使用示例

查找第一个匹配结果：
```objectivec
// 待匹配字符串
NSString *string = @"201709----201710";

// NSRegularExpression
// 创建 NSRegularExpression 实例对象
NSRegularExpression *regularExpression =
    [NSRegularExpression regularExpressionWithPattern:regex
                                              options:NSRegularExpressionCaseInsensitive
                                                error:nil];
// 查找第一个匹配结果，如果查找不到则返回 nil
NSTextCheckingResult *firstMatch =
    [regularExpression firstMatchInString:string
                                  options:NSMatchingReportCompletion
                                    range:NSMakeRange(0, string.length)];
if (firstMatch) {
    // 如果匹配到结果，就打印
    NSLog(@"%@",[string substringWithRange:firstMatch.range]);
}
```

查找所有匹配结果：

```objectivec
// 待匹配字符串
NSString *string = @"201709----201710";

// NSRegularExpression
// 创建 NSRegularExpression 实例对象
NSRegularExpression *regularExpression =
    [NSRegularExpression regularExpressionWithPattern:regex
                                              options:NSRegularExpressionCaseInsensitive
                                                error:nil];
// 查找字符串中所有的匹配结果
NSArray *matches = [regularExpression matchesInString:string
                                              options:NSMatchingReportProgress
                                                range:NSMakeRange(0, string.length)];
if (matches.count > 0) {
    [matches enumerateObjectsUsingBlock:^(NSTextCheckingResult *obj, NSUInteger idx, BOOL * _Nonnull stop) {
        NSLog(@"第 %lu 个结果 = %@",idx, [string substringWithRange:obj.range]);
    }];
}
```



### 创建谓词

```objectivec
// 设置谓词条件
NSPredicate *predicate = [NSPredicate predicateWithFormat:@"age <= 28"];

for (Person *person in array) {
    // 表示指定的对象是否满足谓词条件
    if ([predicate evaluateWithObject:person]) {
        NSLog(@"person name : %@", person.name);
    }
}

// 返回一个符合谓词条件的数组
NSArray *newArray = [array filteredArrayUsingPredicate:predicate];
for (Person *person in newArray) {
    NSLog(@"person name : %@", person.name);
}
```



### 运算符
运算符号的加⼊，谓词不区分⼤小：&& AND || OR


```objectivec
// 格式占位符号
NSPredicate *pre3 = [NSPredicate predicateWithFormat:@"name > 'bruse' && age < %d", 30];
NSArray *array4 = [array filteredArrayUsingPredicate:pre3];

// 关键字，注意字符串一定要添加''
NSPredicate *pre4 = [NSPredicate predicateWithFormat:@"self.name IN {'rose', 'bruse'}"];
NSArray *array5 = [array filteredArrayUsingPredicate:pre4];
NSLog(@"person name : %@", [array5 valueForKey:@"_name"]);
```



### 关键字

* 以 ** 开始——`BEGINSWITH`

```objectivec
// BEGINSWITH 检查某个字是否以**开头
NSPredicate *pre5 = [NSPredicate predicateWithFormat:@"self.name BEGINSWITH 'J'"];
NSArray *array6 = [array filteredArrayUsingPredicate:pre5];
NSLog(@"person name : %@", [array6 valueForKey:@"name"]);
```




* 以 ** 结束——`ENDSWITH`

```objectivec
// ENDSWITH 检查某个字符是以**结尾
NSPredicate *pre6 = [NSPredicate predicateWithFormat:@"self.name ENDSWITH 'e'"];	
NSArray *array7 = [array filteredArrayUsingPredicate:pre6];
NSLog(@"array7 : %@", [array7 valueForKey:@"name"]);
```



* 包含——`CONTAINS`

```objectivec
// CONTAINS 检查包含某个字符
NSPredicate *pre8 = [NSPredicate predicateWithFormat:@"self.name CONTAINS 'string'"];
NSArray *array8 = [array filteredArrayUsingPredicate:pre8];
NSLog(@"array8 : %@", [array8 valueForKey:@"name"]);
```


