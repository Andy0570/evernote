如果在 console 中遇到了 `NSArray`、`NSDictionary` 打印中文乱码问题，解决方法之一是创建 `NSArray+Extension` 和 `NSDictionary+Extension` 两个Category 分类。

⚠️ Xcode 8.0 之后使用 `NSLog` 打印时可能会遇到该问题。

### NSArray+Extension

```objectivec
-(NSString *)descriptionWithLocale:(id)locale
{
    NSMutableString *msr = [NSMutableString string];
    [msr appendString:@"["];
    for (id obj in self) {
        [msr appendFormat:@"\n\t%@,",obj];
    }
    //去掉最后一个逗号（,）
    if ([msr hasSuffix:@","]) {
        NSString *str = [msr substringToIndex:msr.length - 1];
        msr = [NSMutableString stringWithString:str];
    }
    [msr appendString:@"\n]"];
    return msr;
}
```

### NSDictionary+Extension

```objectivec
-(NSString *)descriptionWithLocale:(id)locale
{
    NSMutableString *msr = [NSMutableString string];
    [msr appendString:@"{"];
    [self enumerateKeysAndObjectsUsingBlock:^(id key, id obj, BOOL *stop) {
        [msr appendFormat:@"\n\t%@ = %@,",key,obj];
    }];
    //去掉最后一个逗号（,）
    if ([msr hasSuffix:@","]) {
        NSString *str = [msr substringToIndex:msr.length - 1];
        msr = [NSMutableString stringWithString:str];
    }
    [msr appendString:@"\n}"];
    return msr;
}
```

## NSDictionary 的 value 为空造成的 crash

```objectivec
/**
 填充 value 为空造成的 crash

 @return 修改后的NSDictionary
 */
//.h
- (NSDictionary *)deleteAllNULLValue;

//.m
- (NSDictionary *)deleteAllNULLValue {
    NSMutableDictionary *mutableDic = [[NSMutableDictionary alloc] init];
    for (NSString *keyStr in self.allKeys) {
        if ([[self objectForKey:keyStr] isEqual:[NSNull null]]) {
            [mutableDic setObject:@"null" forKey:keyStr];
        }
        else{
            [mutableDic setObject:[self objectForKey:keyStr] forKey:keyStr];
        }
    }
    return mutableDic;
}
```


### 第三方解决方案

[GitHub：HYBUnicodeReadable](https://github.com/524429264/HYBUnicodeReadable)
