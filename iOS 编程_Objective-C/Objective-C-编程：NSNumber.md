* **NSArray**、**NSDictionary**、**NSSet** 等集合对象只能保存对象，如果要保存 `char`、`short`、`int`、`float`、`double`、`BOOL`  或指向结构的指针等基础数据类型，则可以先将这些基本数据类型封装成 **NSNumber** 对象，再存入集合对象。 
* **NSNumber** 类用来包装**基本数据类型**。
* **NSValue** 是 **NSNumber** 的父类。
* 不能直接用 **NSNumber** 对象做计算，只能提取值。

### 一般用法：

```objectivec
// 包装基本数据类型
NSNumber *intNumber = [NSNumber numberWithInteger:100];
NSNumber *floatNumber =[NSNumber numberWithFloat:24.58];
NSNumber *boolNumber = [NSNumber numberWithBool:YES];

// 字面量语法
NSNumber *intNum    = @1;
NSNumber *floatNum  = @2.5f;
NSNumber *doubleNum = @3.14159;
NSNumber *boolNum   = @YES;
NSNumber *charNum   = @'a';

int x = 5;
float y = 6.32f;
NSNumber *expressionNum = @(x * y);

//  封装完后存入数组
NSArray *array = @[intNumber, floatNumber, boolNumber];

// 解包
NSInteger intValue = [intNumber integerValue];
float floatValue = [floatNumber floatValue];
```

### 详细示例

可封装的数据类型如下：

```objectivec
- (NSNumber *)initWithChar:(char)value;
- (NSNumber *)initWithUnsignedChar:(unsigned char)value;

- (NSNumber *)initWithShort:(short)value;
- (NSNumber *)initWithUnsignedShort:(unsigned short)value;

- (NSNumber *)initWithInt:(int)value;
- (NSNumber *)initWithUnsignedInt:(unsigned int)value;

- (NSNumber *)initWithLong:(long)value;
- (NSNumber *)initWithUnsignedLong:(unsigned long)value;

- (NSNumber *)initWithLongLong:(long long)value;
- (NSNumber *)initWithUnsignedLongLong:(unsigned long long)value;

- (NSNumber *)initWithFloat:(float)value;
- (NSNumber *)initWithDouble:(double)value;

- (NSNumber *)initWithBool:(BOOL)value;
- (NSNumber *)initWithInteger:(NSInteger)value;
- (NSNumber *)initWithUnsignedInteger:(NSUInteger)value;
```

使用示例：

```objectivec
// char 类型
NSNumber *charNumber = [NSNumber numberWithChar:'X'];
NSLog(@"%c",[charNumber charValue]); // X
NSLog(@"%@ %@",NSStringFromClass(charNumber.class),@(charNumber.objCType)); 
// __NSCFNumber c

// unsigned char 类型
NSNumber *unsignedCharNumber = [NSNumber numberWithUnsignedChar:'a'];
NSLog(@"%c",[unsignedCharNumber unsignedCharValue]); // a
NSLog(@"%@ %@",NSStringFromClass(unsignedCharNumber.class),@(unsignedCharNumber.objCType)); 
// __NSCFNumber s

// short
NSNumber *shortNumber = [NSNumber numberWithShort:50];
NSLog(@"%hi",[shortNumber shortValue]); // 50
NSLog(@"%@ %@",NSStringFromClass(shortNumber.class),@(shortNumber.objCType)); 
// __NSCFNumber s

// unsigned short
NSNumber *unsignedshortNumber = [NSNumber numberWithUnsignedShort:50];
NSLog(@"%hu",[unsignedshortNumber unsignedShortValue]); // 50
NSLog(@"%@ %@",NSStringFromClass(unsignedshortNumber.class),@(unsignedshortNumber.objCType)); 
// __NSCFNumber i

// int 类型
NSNumber *intNumber = [NSNumber numberWithInt:100];
NSLog(@"%i",[intNumber intValue]); // 100
NSLog(@"%@ %@",NSStringFromClass(intNumber.class),@(intNumber.objCType)); 
// __NSCFNumber i

// unsigned int 类型
NSNumber *unsignedIntNumber = [NSNumber numberWithUnsignedInt:100];
NSLog(@"%u",[unsignedIntNumber unsignedIntValue]); // 100
NSLog(@"%@ %@",NSStringFromClass(unsignedIntNumber.class),@(unsignedIntNumber.objCType)); // __NSCFNumber q

// long 类型
NSNumber *longNumber = [NSNumber numberWithLong:0xabcdef];
NSLog(@"%lx",[longNumber longValue]); // abcdef
NSLog(@"%@ %@",NSStringFromClass(longNumber.class),@(longNumber.objCType)); 
// __NSCFNumber q

// unsigned long 类型
NSNumber *unsignedLongNumber = [NSNumber numberWithUnsignedLong:0xabcdef];
NSLog(@"%lu",[unsignedLongNumber unsignedLongValue]); // 11259375
NSLog(@"%@ %@",NSStringFromClass(unsignedLongNumber.class),@(unsignedLongNumber.objCType)); 
// __NSCFNumber q

//  long long 类型
NSNumber *longlongNumber = [NSNumber numberWithLong:0xabcdef];
NSLog(@"%lld",[longlongNumber longLongValue]); // 11259375
NSLog(@"%@ %@",NSStringFromClass(longlongNumber.class),@(longlongNumber.objCType)); 
// __NSCFNumber q

// unsigned long long 类型
NSNumber *unsignedLongLongNumber = [NSNumber numberWithUnsignedLongLong:0xabcdef];
NSLog(@"%llu",[unsignedLongLongNumber unsignedLongLongValue]); // 11259375
NSLog(@"%@ %@",NSStringFromClass(unsignedLongLongNumber.class),@(unsignedLongLongNumber.objCType)); // __NSCFNumber q

// float 类型
NSNumber *floatNumber = [NSNumber numberWithFloat:101.00];
/*
 float 类型的格式转换符
 %f —— 十进制计数法
 %e —— 科学计数法
 %g —— 以%f、%e中较短的输出宽度输出单、双精度实数，在指数小于-4或者大于等于精度时使用%e格式
 */
NSLog(@"%g",[floatNumber floatValue]); // 101
NSLog(@"%@ %@",NSStringFromClass(floatNumber.class),@(floatNumber.objCType)); 
// __NSCFNumber f

// double 类型
NSNumber *doubleNumber = [NSNumber numberWithDouble:12345e+15];
NSLog(@"%lg",[doubleNumber doubleValue]); // 1.2345e+19
NSLog(@"%@ %@",NSStringFromClass(doubleNumber.class),@(doubleNumber.objCType)); 
// __NSCFNumber d

// BOOL 类型
NSNumber *boolNumber = [NSNumber numberWithBool:YES];
NSLog(@"%@",[boolNumber boolValue] ? @"YES" : @"NO"); // YES
NSLog(@"%@ %@",NSStringFromClass(boolNumber.class),@(boolNumber.objCType)); 
// __NSCFBoolean c

// Integer 类型
NSNumber *integerNumber = [NSNumber numberWithInt:100];
NSInteger integer = [intNumber integerValue];
NSLog(@"%li",(long)integer); // 100
NSLog(@"%@ %@",NSStringFromClass(integerNumber.class),@(integerNumber.objCType)); 
// __NSCFNumber i

// ‼️错误使用‼️
// 这段代码并不会报错，而是输出其他数字，这个错误很难被发现！
NSLog(@"%li",(NSInteger)integerNumber); // 25655

// UnSigned Integer 类型
NSNumber *unsignedIntegerNumber = [NSNumber numberWithInt:100];
NSUInteger UInteger = [intNumber unsignedIntegerValue];
NSLog(@"%li",(long)UInteger); // 100
NSLog(@"%@ %@",NSStringFromClass(unsignedIntegerNumber.class),@(unsignedIntegerNumber.objCType)); 
// _NSCFNumber i

// 验证两个 Number 是否相等
if ([intNumber isEqualToNumber:floatNumber]) {
    NSLog(@"Numbers are equal");
}else {
    NSLog(@"Numbers are not equal");
}
// 输出：Numbers are not equal

// 验证一个 Number 是否小于、等于或大于另一个 Number
NSComparisonResult result = [intNumber compare:floatNumber];
switch (result) {
    case NSOrderedAscending:
        NSLog(@"intNumber < floatNumber");
        break;
    case NSOrderedSame:
        NSLog(@"intNumber = floatNumber");
        break;
    case NSOrderedDescending:
        NSLog(@"intNumber > floatNumber");
        break;
}
// 输出：intNumber < floatNumber

// stringValue 方法可以把任意封装的类型当作字符串输出
NSString *string = [integerNumber stringValue];
```



### 判断 NSNumber 封装的基础类型

```objectivec
// num.objCType 代表类型编码@encode(type-name)
// 这样就看出来了，判断整型int类，包括short, unsigned short, int, unsigned int, long, unsigned long, long long, unsigned long long，就这样判断
NSArray *intTypeArray = @[@"s", @"i", @"q"];
NSString *intType = [NSString stringWithFormat:@"%s", num.objCType];
if ([NSStringFromClass(num.class) isEqualToString:@"__NSCFNumber"] && [intTypeArray containsObject:intType]) {
    NSLog(@"%@是一个整数", num);
}
// 浮点型判断
NSArray *floatTypeArray = @[@"f", @"d"];
NSString *floatType = [NSString stringWithFormat:@"%s", num.objCType];
if ([NSStringFromClass(num.class) isEqualToString:@"__NSCFNumber"] && [floatTypeArray containsObject:floatType]) {
    NSLog(@"%@是一个浮点型", num);
}
// char判断
NSArray *charTypeArray = @[@"c"];
NSString *charType = [NSString stringWithFormat:@"%s", num.objCType];
if ([NSStringFromClass(num.class) isEqualToString:@"__NSCFNumber"] && [charTypeArray containsObject:charType]) {
    NSLog(@"%@是一个字符型", num);
}
// BOOL判断
if ([NSStringFromClass(num.class) isEqualToString:@"__NSCFBoolean"]) {
    NSLog(@"%@是一个字符型", num);
}
if ([num isKindOfClass:NSClassFromString(@"__NSCFBoolean")]) {
    NSLog(@"num is BOOL type");
} else {
    NSLog(@"num is not BOOL type");
}

```

### NSNumberFormatter——格式化数据类型

##### CFNumberFormatterStyle

```objectivec
// @"12345678.89"
typedef CF_ENUM(CFIndex, CFNumberFormatterStyle) {    // number format styles
    kCFNumberFormatterNoStyle = 0,       // 默认，无格式
    kCFNumberFormatterDecimalStyle = 1,  // 十进制格式
    kCFNumberFormatterCurrencyStyle = 2,  // 货币格式【英文】：$12,345,678.89 【中文】：¥12,345,678.89
    kCFNumberFormatterPercentStyle = 3,  // 百分比格式 %
    kCFNumberFormatterScientificStyle = 4,  // 科学计数法
    kCFNumberFormatterSpellOutStyle = 5,  // 口语格式
    kCFNumberFormatterOrdinalStyle,  // 顺序格式【英文】：12,345,679th 【中文】：第1234,5679
    kCFNumberFormatterCurrencyISOCodeStyle,  // ISO标准货币格式：
    kCFNumberFormatterCurrencyPluralStyle,  // 复数货币
    kCFNumberFormatterCurrencyAccountingStyle, // 会计货币
};
```

示例：

```objectivec
// 格式化输出字符串
NSInteger myInteger = 5;
NSString *codeString = [NSString stringWithFormat:@"100150%2.2ld",myInteger];
NSLog(@"code = %@",codeString); // code = 10015005

// NSNumberFormatter
NSNumberFormatter *numberFormatter = [[NSNumberFormatter alloc] init];
numberFormatter.numberStyle = kCFNumberFormatterNoStyle;
numberFormatter.formatWidth = 2;
numberFormatter.paddingCharacter = @"0";
codeString = [NSString stringWithFormat:@"100150%@",[numberFormatter stringFromNumber:@(myInteger)]];
NSLog(@"code = %@",codeString); // code = 10015005

// 字符串分隔
NSString *formatterString;
numberFormatter.numberStyle = kCFNumberFormatterDecimalStyle;
formatterString = [numberFormatter stringFromNumber:@123456789];
NSLog(@"%@",formatterString); // 123,456,789

// 货币符号
numberFormatter.numberStyle = kCFNumberFormatterCurrencyStyle;
formatterString = [numberFormatter stringFromNumber:@3.25];
NSLog(@"%@",formatterString); // ¥3.25

// 百分数格式
numberFormatter.numberStyle = kCFNumberFormatterPercentStyle;
formatterString = [numberFormatter stringFromNumber:@1.2];
NSLog(@"%@", formatterString); // 120%

// 科学计数格式
numberFormatter.numberStyle = kCFNumberFormatterScientificStyle;
formatterString = [numberFormatter stringFromNumber:@1.2];
NSLog(@"%@", formatterString); // 1.2E0

// 口语格式
numberFormatter.numberStyle = kCFNumberFormatterSpellOutStyle;
formatterString = [numberFormatter stringFromNumber:@1.2];
NSLog(@"%@", formatterString); // 一点二
```
