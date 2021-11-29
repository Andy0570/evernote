* `NSString` 字符串的内容定义以后无法修改，是不可变的。
* `NSMutableString` 是可变字符串，可以修改字符串中的内容。
* `NSMutableString` 继承自 `NSString`，因此它能使用 `NSString` 所有的方法。

### 创建字符串

```objectivec
// initWithString
NSMutableString *ms2 = [[NSMutableString alloc] initWithString:@"abcdefg"];
    
// stringWithFormat:父类类方法创建
NSMutableString *ms3 = [NSMutableString stringWithFormat:@"abcdefg" ];
```

### 插入字符串

```objectivec
[ms3 insertString:@"插入字符" atIndex:1]; // @"a插入字符bcdefg"
```

### 追加字符串

```objectivec
[ms3 appendString:@"对象"]; // @"abcdefg对象"
```

### 删除修改字符串

```objectivec
NSRange rg = NSMakeRange(3,4);
NSRange rg = [ms3 rangeOfString:@"字符"]; // 同上，原@“a插入字符bcdefg”
[ms3 deleteCharactersInRange:rg];  // @“a插入bcdefg”
```

### 替换字符串

```objectivec
NSRange rg3 = [ms3 rangeOfString:@"字符"];
[ms3 replaceCharactersInRange:rg3 withString:@"字母"]; // 字符——>字母
```
