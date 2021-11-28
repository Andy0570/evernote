![图片来自Pixabay](http://upload-images.jianshu.io/upload_images/2648731-7d55c3228ac95ab8.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 问题分析
* 需求：在UI页面上显示起止年月。
* 后台服务器返回的原始字符串：`201709—————201709`，在后台老大哥的眼里，这种格式当然是完美的🤷🏻♂️，可在前端眼里，如果直接把酱紫的字符串直接显示在页面上，显示粗糙，又难读，用户体验非常糟糕，连我这样毫无审美感的程序猿都看不下去了好吧。
* 优化后显示字符串：`2017年9月  ⇀ 2017年9月`。

### 最初的方法：用正则表达式匹配，再截取，可能存在边界溢出导致崩溃。

```objective-c
// 正则表达式
- (BOOL)validateExpensePeriod {
    NSString *regex = @"^\\d{6}\\D+\\d{6}$";
    NSPredicate *predicate = [NSPredicate predicateWithFormat:@"SELF MATCHES %@",regex];
    return [predicate evaluateWithObject:_expensePeriod];
}

// 输入日期格式
- (NSDateFormatter *)inputDateFormatter {
    if (!_inputDateFormatter) {
        _inputDateFormatter = [[NSDateFormatter alloc] init];
        _inputDateFormatter.dateFormat = @"yyyyMM"; // 201710
    }
    return _inputDateFormatter;
}

// 输出日期格式
- (NSDateFormatter *)outputDateFormatter {
    if (!_outputDateFormatter) {
        _outputDateFormatter = [[NSDateFormatter alloc] init];
        _outputDateFormatter.dateFormat = @"yyyy年MMM"; // 2017年10月
    }
    return _outputDateFormatter;
}

// ...

// 因为后台有时候会返回预料之外的字符串，如果仍按照正常逻辑截取会导致边界溢出崩溃——要背锅的哦
NSUInteger strLength = _expensePeriod.length;
if (strLength < 16 || ![self validateExpensePeriod]) {
    return _expensePeriod;
}

// 截取字符串中的开始日期和结束日期
NSString *beginDateStr = [_expensePeriod substringToIndex:6];
NSString *endDateStr = [_expensePeriod substringFromIndex:strLength-6];    

// 将字符串转换为
NSDate *beginDate = [self.inputDateFormatter dateFromString:beginDateStr];
NSDate *endDate   = [self.inputDateFormatter dateFromString:endDateStr];
if ((beginDate == nil) || (endDate == nil)) {
    return _expensePeriod;
}else {
    NSString *formartDateStr = [NSString stringWithFormat:@"%@ ⇀ %@",
                                [self.outputDateFormatter stringFromDate:beginDate],
                                [self.outputDateFormatter stringFromDate:endDate]];
    return formartDateStr;
}
```



### 优化方法，正则表达式返回所有匹配结果的范围，自动截取，实现成功，方法太过复杂

```objective-c
// 标签文本字符串
- (NSString *)expensePeriodLabelText {
    // 优化显示起止日期 201709----201709
    NSUInteger strLength = _expensePeriod.length;
    __block NSString *beginDateStr;
    __block NSString *endDateStr;
    
    // 正则表达式匹配结果
    NSString *regex = @"\\d{6}";
    NSRegularExpression *regularExpression = [NSRegularExpression regularExpressionWithPattern:regex options:NSRegularExpressionCaseInsensitive error:nil];
  	// 正则表达式匹配，将结果返回到数组
    NSArray *matches = [regularExpression matchesInString:_expensePeriod options:NSMatchingReportProgress range:NSMakeRange(0, strLength)];
    if (matches.count > 0 && matches.count == 2) {
        [matches enumerateObjectsUsingBlock:^(NSTextCheckingResult *obj, NSUInteger idx, BOOL * _Nonnull stop) {
            // 用匹配结果的范围去截取字符串
            if (idx == 0) {
                beginDateStr = [_expensePeriod substringWithRange:obj.range];
            }else {
                endDateStr = [_expensePeriod substringWithRange:obj.range];
            }
        }];
    }else {
        return _expensePeriod;
    }
    
    NSDate *beginDate = [self.inputDateFormatter dateFromString:beginDateStr];
    NSDate *endDate   = [self.inputDateFormatter dateFromString:endDateStr];
    if ((beginDate == nil) || (endDate == nil)) {
        return _expensePeriod;
    }else {
        NSString *formartDateStr = [NSString stringWithFormat:@"%@ ⇀ %@",
                                    [self.outputDateFormatter stringFromDate:beginDate],
                                    [self.outputDateFormatter stringFromDate:endDate]];
        return formartDateStr;
    }
}
```



### 再优化方法，使用 YYKit 方法，方法相对简洁

```objective-c
- (NSString *)expensePeriodLabelText {
    __block NSMutableArray *mutableArray = [NSMutableArray arrayWithCapacity:2];
    [_expensePeriod enumerateRegexMatches:@"\\d{6}" options:NSRegularExpressionCaseInsensitive usingBlock:^(NSString * _Nonnull match, NSRange matchRange, BOOL * _Nonnull stop) {
        [mutableArray addObject:match];
    }];
    if (mutableArray.count != 2) {
        return _expensePeriod;
    }
    
    NSDate *beginDate = [self.inputDateFormatter dateFromString:mutableArray.firstObject];
    NSDate *endDate   = [self.inputDateFormatter dateFromString:mutableArray.lastObject];
    if (!beginDate || !endDate) {
        return _expensePeriod;
    }
    
    NSString *formartDateStr = [NSString stringWithFormat:@"%@ ⇀ %@",
                                [self.outputDateFormatter stringFromDate:beginDate],
                                [self.outputDateFormatter stringFromDate:endDate]];
    return formartDateStr;
}
```
最终的效果：

![](http://upload-images.jianshu.io/upload_images/2648731-dd9478bfaa14c70d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
