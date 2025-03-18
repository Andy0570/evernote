## 概述

`NSString` 及其可变子类 `NSMutableString` 提供了一组用于处理字符串的 API，包括用于比较，搜索和修改字符串的方法。`NSString` 对象可以在整个 **Foundation** 框架和其他 **Cocoa** 框架中使用，作为平台上所有文本和语言功能的基础。

### Unicode 与 NSString

* Unicode 标准为世界上几乎所有的书写系统里所使用的每一个字符或符号定义了一个唯一的数字。
* NeXT 在 1994 年推出的 **Foundation Kit** 是所有编程语言中最先基于 Unicode 的标准库之一。
* 最初，Unicode 编码是被设计为 16 位的，提供了 65,536 个字符的空间。后来，Unicode 编码扩展到了 21 位。
* `NSString` 实例可以保存任意 Unicode 字符。
* `NSString` 代表的是用 UTF-16 编码的文本，长度、索引和范围都基于 UTF-16 的码元。


## 常见使用

### 创建 `NSString` 实例对象

* 使用 **字面量语法**（literal syntax）：`@"string"` ，根据给定的字符串创建一个 `NSString` 对象：

```objectivec
// 声明一个指向 NSString 实例的指针变量，它保存了 NSString 实例在内存中的地址
NSString *str1 = @"Hello";
// 指针变量指向了新的内存地址
str1 = @"World";
```

* `- init` 方法

```objectivec
NSString *string = [[NSString alloc] init];
```

* `- initWithFormat:` 方法

```objectivec
// 创建动态字符串，字符串的内容需要等到程序运行时才知道
NSString *str2 = [[NSString alloc] initWithFormat:@"The string is %@",str1];
```

* `+ stringWithFormat:` 方法

```objectivec
NSString *str3 = [NSString stringWithFormat:@"The date is %@",str1];
```

* `+ stringWithString:` 方法

```objectivec
// 通过复制另一个给定字符串来创建字符串。
NSString *str2 = [NSString stringWithString:str1];
```

### 格式化字符串

同 C 语言中的 `sprintf` 函数（ANSI C89 中的一个函数）类似，Objective-C 中的 `NSString` 类也有如下的 3 个方法：

* `-initWithFormat:`
* `-initWithFormat:arguments:`
* `+stringWithFormat:`

需要注意这些格式化方法都是***非本地化***的。所以这些方法得到的字符串是**不能直接拿来显示在用户界面上**的。如果需要本地化，那我们需要使用下面这些方法:

* `-initWithFormat:locale:`
* `-initWithFormat:locale:arguments:`
* `+localizedStringWithFormat:`

传入特定参数，来设置最小字段宽度和最小数字位数（默认两者都是 0）

```objectivec
int m = 42;
NSString *s = [NSString stringWithFormat:@"'%4d' '%-4d' '%+4d' '%4.3d' '%04d'", m, m, m, m, m];
// "[  42] [42  ] [ +42] [ 042] [0042]"
m = -42;
NSString *s = [NSString stringWithFormat:@"'%4d' '%-4d' '%+4d' '%4.3d' '%04d'", m, m, m, m, m];
// "[ -42] [-42 ] [ -42] [-042] [-042]"
```


### C 字符串与 `NSString` 对象的相互转换

示例一：
```objectivec
// C 字符串 -> NSString 对象
char *greeting = "Hello";
NSString *string = [NSString stringWithCString:greeting
                                      encoding:NSUTF8StringEncoding];

// NSString 对象 -> C 字符串
const char *x = NULL;
// 转换之前，检查能不能将 NSString 对象转换成指定编码的 C 字符串
if ([string canBeConvertedToEncoding:NSUTF8StringEncoding]) {
    x = [string cStringUsingEncoding:NSUTF8StringEncoding];
}
```

示例二：
```objectivec
// 1. stringWithCString:
char *S = "\xc3\x8a";
NSString *str1 = [NSString stringWithCString:S encoding:NSUTF8StringEncoding];

// 2. stringWithUTF8String:
//返回一个UTF—8编码的C字符串,参数为 char * 类型
NSString *str2 = [NSString stringWithUTF8String:"长破折号：\xe2\x80\x94"];
```

另外，关于 `stringWithUTF8String:`

> 官方文档描述：Returns a string created by copying the data from a given C array of UTF8-encoded bytes.
>
> 通过复制一个给定的 UTF-8 编码的 C 数组来返回字符串。
>
> [stackoverflow:What does stringWithUTF8String: do and how it works](http://stackoverflow.com/questions/20700247/what-does-stringwithutf8string-do-and-how-it-works)



### 字符串转换为基本数据类型

```objectivec
NSString *str = @"3.14";

// double
double d = [str doubleValue];
NSLog(@"double:%lf",d);

// float
float f = [str floatValue];
NSLog(@"float:%f",f);

// int
int i = [str intValue];
NSLog(@"int:%d",i);

// NSInteger
NSInteger integer = [str integerValue];
NSLog(@"NSInteger:%ld",integer);

// long long
long long l = [str longLongValue];
NSLog(@"long long:%lld",l);

// BOOL
NSString *string = @"YES";
BOOL b = [string boolValue]; 
```



### 大小写转换

```objectivec
// uppercaseString: 全部大写
NSString *string1 = @"hEllo";
NSLog(@"upper:%@",[string1 uppercaseString]);

// lowercaseString: 全部小写
NSLog(@"lower:%@",[string1 lowercaseString]);

// capitalizedString: 首字母大写
NSLog(@"capital:%@",[string1 capitalizedString]);
```
Tips: 一定不要使用 `NSString` 的 `-uppercaseString` 或者 `-lowercaseString` 的方法来处理 UI 显示的字符串，而应该使用 `-uppercaseStringWithLocale` 来代替， 比如：
```objectivec
NSString *name = @"Tómas";
cell.text = [name uppercaseStringWithLocale:[NSLocale currentLocale]];
```

###  追加拼接字符串

```objectivec
NSString *string1 = @"Android";
NSString *string2 = @"ios";

// stringWithFormat: 类方法创建，=》Android & ios
NSString *string11 = [NSString stringWithFormat:@"%@ & %@",string1,string2]

// stringByAppendingString: 追加字符串 =>Androidios
NSString *string22 = [string1 stringByAppendingString:string2];  

// stringByAppendingFormat: 格式化追加字符串 => Android追加字符串ios
NSString *string33 =[string1 stringByAppendingFormat:@“追加字符串%@”，string2]；
```

#### 示例：生成 50 个 0～10 之间的随机数，并保留一位小数。

```objectivec
NSString *formattedString = [[NSString alloc] init];
for (int i = 0; i < 50; i++) {
    // (0,10) 之间的浮点数
    double x = arc4random() % 10 + (arc4random() % 10) * 0.1;
    // 格式化为字符串，并保留一位小数
    NSString *randomString = [NSString stringWithFormat:@" %.1f",x];
    // 将 50 个随机数拼接起来存放
    formattedString = [formattedString stringByAppendingString:randomString];
}
NSLog(@"%@",s);
```
输出：
> 3.8 2.9 6.9 7.5 2.9 6.8 8.6 7.9 1.1 1.0 7.6 4.5 3.7 6.3 5.8 9.1 7.3 0.4 9.5 3.8 0.1 3.2 7.1 6.0 1.1 4.2 6.3 9.5 4.2 1.7 9.2 4.1 3.8 4.3 2.3 2.1 7.6 6.9 5.2 6.7 4.5 3.1 7.6 3.7 6.7 8.9 9.7 0.7 9.6 1.6

### 字符串的分割

```objectivec
- (NSArray<NSString *> *)componentsSeparatedByString:(NSString *)separator;
```

此方法返回一个数组，该数组中包含接收方的子字符串，这些子字符串被指定的分隔符分割，示例代码：
```objectivec
NSString *list = @"Karin, Carrie, David";
NSArray *listItems = [list componentsSeparatedByString:@", "];
// @[@"Karin", @"Carrie", @"David"]
```



### 字符串的截取

```objectivec
NSString *string = @"andywhm@163.com";

// substringToIndex: 从起始位置截取到指定位置（不包含指定位置）
NSString *substring1 = [string substringToIndex:7];
NSLog(@"substring1:%@",substring1); // substring1:andywhm

// substringFromIndex: 从指定位置截取到字符串的末尾（包含指定位置）
NSString *substring2 = [string substringFromIndex:8];
NSLog(@"substring2:%@",substring2); // substring2:163.com

// substringWithRange: 截取中间部分的字符串 @163
// NSRange range = {location,length};
// NSRange range = {7,4};
NSRange range = NSMakeRange(7, 4);
NSString *substring3 = [string substringWithRange:range];
NSLog(@"substring3:%@",substring3); // substring3:@163
```

官方文档建议，为了避免拆散字符序列，可以与 `rangeOfComposedCharacterSequencesForRange` 一起使用。

比如 emoji 表情在字符串中的长度是 2，当字符串截取时，如果截断位置刚好在 emoji 表情的中间，此时 emoji 表情就会出现无法解码。

```objectivec
NSString *str = @"hello😜world";
NSUInteger index = 5;
if (str.length < index) return;

NSRange range = [str rangeOfComposedCharacterSequenceAtIndex:index];
NSLog(@"location=%lu, length=%lu",range.location,range.length);
// location=5, length=2
        
NSString *subStr1 = [str substringToIndex:NSMaxRange([str rangeOfComposedCharacterSequenceAtIndex:index])];
NSLog(@"subStr2:%@",subStr1);
// subStr1:hello😜

NSString *subStr2 = [str substringFromIndex:[str rangeOfComposedCharacterSequenceAtIndex:index].location];
NSLog(@"subStr1:%@",subStr2);
// subStr2:😜world

NSString *subStr3 = [str substringWithRange:[str rangeOfComposedCharacterSequencesForRange:range]];
NSLog(@"subStr3:%@",subStr3);
// subStr3:😜
```

#### 示例：截取字符串

```objectivec
 // 日期区间 201709----201709
NSString *string = @"201709----201710";
NSUInteger strLength = string.length;
NSLog(@"%lu",(unsigned long)strLength); // 16
        
NSString *beginDateStr = [string substringToIndex:6];
NSLog(@"beginData = %@",beginDateStr); // beginData = 201709
        
NSString *endDateStr = [string substringFromIndex:strLength-6];
NSLog(@"endData = %@",endDateStr); // endData = 201710
```

Tips: 字符串截取之前最好判断字符串长度是否满足要求或者正则表达式校验字符串内容，否则很容易引起越界异常。
```objectivec
if (str.length < index) return;
```

### 字符串的查找并替换

```objectivec
NSString *email = @"andywhm@163.com";
NSString *searchString = @"@163";

// rangeOfString: 查找并返回接收器中给定字符串第一次出现的范围。
NSRange range = [email rangeOfString:searchString];
NSLog(@"被查找字符串在接受字符串中的起始点:%lu",range.location);
NSLog(@"被查找字符串在接受字符串中的长度：%lu",range.length);

if (range.location == NSNotFound) {
    NSLog(@"字符串没有找到！");
} else {
    // 将指定范围内的字符串替换为新的字符串
    NSString *newString = [email stringByReplacingCharactersInRange:range withString:@"@qq"];
    NSLog(@"替换后的字符串为：%@",newString);
}
```

* 查找指定字符串

```objectivec
// 查找特定的子字符串是否在字符串中
NSString *listOfNames = @"..."; // 名单
NSString *name = @"Word";
NSRange match = [listOfNames rangeOfString:name];
if (match.location == NSNotFound) {
    NSLog(@"NO Match found!");
    // 未匹配到指定字符，采取下一步行动！
} else {
    NSLog(@"Match found!");
    // 匹配到指定字符，采取下一步行动！
}
```

* 正则表达式查找字符串

```objectivec
// 待匹配字符串
NSString *string = @"201709----201710";
// 返回匹配到的第一个字符串范围
NSRange range = [string rangeOfString:@"\\d+" options:NSRegularExpressionSearch];
if (range.location != NSNotFound) {
    NSLog(@"%@",[string substringWithRange:range]); // 201709
}
```

* 在一个字符串中搜索子字符串，最灵活的方式是：

```objectivec
- (NSRange)rangeOfString:(NSString *)aString
                 options:(NSStringCompareOptions)mask
                   range:(NSRange)searchRange
                  locale:(NSLocale *)locale;
```


### 字符串替换

* `stringByReplacingCharactersInRange:withString:`
*  `stringByReplacingOccurrencesOfString:withString: `接收器中目标字符串的所有出现的都将被另一个给定字符串替换。

```objectivec
NSString *email = @"andywhm@163.com";
NSString *newString2 = [email stringByReplacingOccurrencesOfString:@"163.com" withString:@"qq.com"];
NSLog(@"替换后的字符串为：%@",newString2);
```

*  使用以下方法整体替换字符串时，还可以设置替换的区域：

```objectivec
stringByReplacingOccurrencesOfString:(NSString *)withString:(NSString *) options:(NSStringCompareOptions) range:(NSRange)
```

#### 示例：字符串的隐藏显示

```objectivec
// 截取字符串，再用*填充
NSString *string = @"123456";
NSUInteger length = string.length;
string = [string substringToIndex:3];
string = [string stringByPaddingToLength:length withString:@"*" startingAtIndex:0];
NSLog(@"string = %@",string);
// string = 123***

// 隐藏身份号码，显示前5位和后2位
string = [string stringByReplacingCharactersInRange:NSMakeRange(6, 8) withString:@"********"];
// string = 33030********80
```


### 字符串的删除

删除指定范围内的字符串。

```objectivec
NSMutableString *email = [NSMutableString stringWithString:@"andywhm@163.com"];

// 1. 获得子字符串的范围
NSRange range = [email rangeOfString:@"@163"];
// NSRange range2 = NSMakeRange(7, 4);

// 2. 删除指定范围内的字符串
[email deleteCharactersInRange:range];
```

### 字符串的插入

在指定索引处插入字符串。

```objectivec
NSMutableString *email = [NSMutableString stringWithString:@"andywhm@163.com"];
[email insertString:@"12345" atIndex:2];

NSLog(@"new email:%@",email);
// new email:an12345dywhm@163.com
```


### 字符串拷贝

```objectivec
NSMutableString *mutableStr1 = [NSMutableString stringWithString:@"Hello"];
NSMutableString *mutableStr2;

//  两个指针同时指向同一块内存
mutableStr2 = mutableStr1;
[mutableStr2 appendString:@" World"];

NSLog(@"mutableStr1:%@",mutableStr1); // mutableStr1:Hello World
NSLog(@"mutableStr2:%@",mutableStr2); // mutableStr2:Hello World
```

### 字符串的获取及遍历

* 获取字符串长度：`length`

```objectivec
//length : 获取字符串的字符个数
NSString *string1 = @"abcdefg";
NSUInteger length = [string1 length]; // length = 7
```

> `[NSString length] `返回字符串里 **unichar** 的个数。我们已经了解了三个可能导致这个返回值与实际（可见）字符数不符的 Unicode 特性。
> * 基本多文种平面外的字符：记住，BMP 里所有的字符在 UTF-16 里都可以用一个码元表示。所有其余的字符都需要两个码元（一个代理对）。基本上所有现代使用的字符都在 BMP 里，因此在实际中很难遇到代理对。然而，几年前随着 emoji 被引入 Unicode（在 1 号平面），这种情况已经有所变化。
> * 组合字符序列：如果字母 é 是以分解形式（e + ´）编码的，算作两个码元。
> * 变体序列：它们和分解形式的组合字符序列的工作方式一样，因此变体选择符也算作单独的字符。
> ——摘自  [ObjC中国-NSString 与 Unicode](https://www.objccn.io/issue-9-1/)


* 获取特定字符：`characterAtIndex:`

```objectivec
//characterAtIndex : 通过下标获取字符串中的特定字符
NSString *string1 = @"abcdefg";
unichar result = [string1 characterAtIndex:2];
NSLog(@"result = %c",result); //result = c
```

* 遍历字符串：`characterAtIndex: `

```objectivec
// 结合以上两个方法，通过循环遍历字符串
NSString *str8 = @"Communication";

//遍历字符串中的每一个字符
for (int i = 0; i<str8.length; i++) {
    unichar c = [str8 characterAtIndex:i];
    NSLog(@"index:%d,value:%c",i,c);
}
```

> 用 `characterAtIndex: ` 方法以索引方式直接访问 unichar 可能会有问题。字符串可能会包含组合字符序列、代理对或变体序列。幸运的是，`NSString` 有更好地方式：`enumerateSubstringsInRange:options:usingBlock: `方法。

```objectivec
NSString *s = @"The weather on \U0001F30D is \U0001F31E today.";
// The weather on 🌍 is 🌞 today.
NSRange fullRange = NSMakeRange(0, [s length]);
[s enumerateSubstringsInRange:fullRange
                      options:NSStringEnumerationByComposedCharacterSequences
                   usingBlock:^(NSString *substring, NSRange substringRange, NSRange enclosingRange, BOOL *stop)
{
    NSLog(@"%@ %@", substring, NSStringFromRange(substringRange));
}];
```


###  字符串的比较

* 判断字符串是否相等：`isEqualToString：`
`isEqual:` 和 `isEqualToString:` 这两个方法都是一个字节一个字节地比较的。

```objectivec
NSString *string1 = @"abcd";
NSString *string2 = @"ABCD";

// == 是比较两个字符串的指针地址，而不是比较字符串的内容
if (string1 == string2) {
    NSLog(@"两个指针指向同一块内存");
}

// isEqualToString：比较指针变量所指的地址内容，区分大小写
BOOL isEqual = [string1 isEqualToString:string2];
if (isEqual) {
    NSLog(@"相等");
}else {
    NSLog(@"不等");
}
```

复杂的情况：如果希望字符串的合成和分解的形式相吻合，需要先执行正规化（normalize）操作：
```objectivec
NSString *s = @"\u00E9"; // é  
NSString *t = @"e\u0301"; // e + ´
BOOL isEqual = [s isEqualToString:t];
NSLog(@"%@ is %@ to %@", s, isEqual ? @"equal" : @"not equal", t);  
// => é is not equal to é

// Normalizing to form C  
NSString *sNorm = [s precomposedStringWithCanonicalMapping];  
NSString *tNorm = [t precomposedStringWithCanonicalMapping];  
BOOL isEqualNorm = [sNorm isEqualToString:tNorm];  
NSLog(@"%@ is %@ to %@", sNorm, isEqualNorm ? @"equal" : @"not equal", tNorm);  
// => é is equal to é
```

> 另一个选择是使用 `compare:` 方法（或者它的其它变形方法，比如：`localizedCompare:`），这个方法返回一个和它相容等价的字符串。对此，苹果没有很好地写入文档。请注意，你常常还需要作标准等价的比较。`compare:` 没法作这个比较。

```objectivec
NSString *s = @"ff"; // ff  
NSString *t = @"\uFB00"; // ﬀ ligature  
NSComparisonResult result = [s localizedCompare:t];  
NSLog(@"%@ is %@ to %@", s, result == NSOrderedSame ? @"equal" : @"not equal", t);  
// => ff is equal to ﬀ
```

* 字符串是否包含指定开头：`hasPrefix:`

```objectivec
if ([string1 hasPrefix:@"A"]) {
    NSLog(@"字符串string1以A开头");
}

// 示例：简单判断传入 URL 有效性
NSString *urlString = (NSURL *)url.absoluteString;
BOOL isUrlValid = [urlString hasPrefix:@"http://"] || [urlString hasPrefix:@"https://"];
if (isUrlValid) {
    // MARK: URL 有效
}
```

* 字符串是否包含指定结尾：`hasSuffix:`

```objectivec
if ([string1 hasSuffix:@"D"]) {
    NSLog(@"字符串string1以D结尾");
}
```

* 区分大小写比较，3 种返回值：`compare: `

```objectivec
// NSOrderedSame: 判断两者内容是否相等
BOOL isSame = [string1 compare:string2] == NSOrderedSame;

// NSOrderedAscending: 判断两对象值的大小(按字母顺序进行比较，string2大于string1为真)
BOOL isAscending = [string1 compare:string2] == NSOrderedAscending;

// NSOrderedAscending: 判断两对象值的大小(按字母顺序进行比较，string2小于string1为真)
BOOL isDescending = [string1 compare:string2] == NSOrderedDescending;
```

* 不区分大小写比较，3 种返回值：`caseInsensitiveCompare:`

```objectivec
NSComparisonResult result = [string1 caseInsensitiveCompare:(string2)];
switch (result) {
    case NSOrderedSame:
        NSLog(@"不区分大小写, 两者相同");
        break;

    case NSOrderedDescending:
        NSLog(@"降幂");
        break;

    case NSOrderedAscending:
        NSLog(@"升幂");
        break;

    default:
        NSLog(@"无法判断");
        break;
```

* 带参数的比较：`compare: options:`

```objectivec
// NSNumericSearch:比较字符串的字符个数，而不是字符值
BOOL results = [string1 compare:string2 options:NSNumericSearch] == NSOrderedSame;
```

* 复杂情况下的比较：

```objectivec
- (NSComparisonResult)compare:(NSString *)string
                      options:(NSStringCompareOptions)mask
                        range:(NSRange)rangeOfReceiverToCompare
                       locale:(id)locale;
```


### 字符串的文件操作(读写)

> 纯文本文件，和文件格式或者网络协议应该选择 **UTF-8** 编码，除非有特别的需要只能用其它的编码。要向文件中写入文本，使用 `writeToURL:atomically:encoding:error: `这个方法。

> 这个方法会在 **UTF-16** 或 **UTF-32** 编码的文件上自动加上字节顺序标记。它还会把文件的编码存储在名为 com.apple.TextEncoding 的扩展文件属性里。鉴于 `initWithContentsOf…`: `usedEncoding:error:` 方法知道有这个属性，当你从文件里载入文本时，使用标准的 NSString 方法就能让确保使用正确的编码更加容易。

* 从文件读取字符串：`initWithContentsOfFile:`

```objectivec
NSString *path = @"string.text";  
NSString *string = [[NSString alloc] initWithContentsOfFile:path];  
NSLog(@"string:%@",string);  
[string release]; //string=nil; 
```

```objectivec
// 用于保存 NSError 对象地址的局部指针变量
NSError *error;
NSString *myEssay = [[NSString alloc] initWithContentsOfFile:@"/some/path/file"
                                                    encoding:NSUTF8StringEncoding 
                                                       error:&error];
if (!myEssay) {
     NSLog(@"Error reading file:%@",[error localizedDescription]);
 }
```

* 写字符串到文件：`writeToFile:`

```objectivec
NSString *string = [[NSString alloc] initWithString:@"This is a String!"];   
NSString *path = @"string.text";      
[string writeToFile: path atomically: YES];  
[string release];  //string=nil; 

// 用于保存 NSError 对象地址的局部指针变量
NSError *error;
NSString *someString = @"Text Data";
BOOL success = [someString writeToFile:@"some/path/file"
                            atomically:YES
                              encoding:NSUTF8StringEncoding
                                 error:&error];
if (!success) {
    NSLog(@"Error writing file:%@",[error localizedDescription]);
}
```

* 扩展路径

```objectivec
NSString *Path = @"~/NSData.txt";  
NSString *absolutePath = [Path stringByExpandingTildeInPath];  
NSLog(@"absolutePath:%@",absolutePath);  
NSLog(@"Path:%@",[absolutePath stringByAbbreviatingWithTildeInPath]);
```

* 文件扩展名 

```objectivec
NSString *Path = @"~/NSData.txt";  
NSLog(@"Extension:%@",[Path pathExtension]); 
```

> **文件路径**
简单来说就是我们不应该使用 `NSString` 来描述文件路径。对于 OS X 10.7 和 iOS 5，`NSURL` 更便于使用，而且更有效率，它还能缓存文件系统的属性。

>再者，```NSURL``` 有八个方法来访问被称为 resource values 的东西。这些方法提供了一个稳定的接口，使我们可以用来获取和设置文件与目录的各种属性，例如本地化文件名（`NSURLLocalizedNameKey`）、文件大小（`NSURLFileSizeKey`），以及创建日期（```NSURLCreationDateKey```），等等。

>尤其是在遍历目录内容时，使用 `-[NSFileManager enumeratorAtURL:includingPropertiesForKeys:options:errorHandler:]`，并传入一个**关键词（keys）**列表，然后用 `-getResourceValue:forKey:error:` 检索它们，能带来显著的性能提升。

下面是一个简短的例子展示了如何将它们组合在一起：

```objectivec
NSError *error = nil;
NSFileManager *fm = [[NSFileManager alloc] init];
NSURL *documents = [fm URLForDirectory:NSDocumentationDirectory inDomain:NSUserDomainMask appropriateForURL:nil create:NO error:&error];
NSArray *properties = @[NSURLLocalizedNameKey, NSURLCreationDateKey];
NSDirectoryEnumerator *dirEnumerator = [fm enumeratorAtURL:documents
                                includingPropertiesForKeys:properties
                                                   options:0
                                              errorHandler:nil];
for (NSURL *fileURL in dirEnumerator) {
    NSString *name = nil;
    NSDate *creationDate = nil;
    if ([fileURL getResourceValue:&name forKey:NSURLLocalizedNameKey error:NULL] &&
        [fileURL getResourceValue:&creationDate forKey:NSURLCreationDateKey error:NULL])
    {
        NSLog(@"'%@' was created at %@", name, creationDate);
    }
}
```

我们把属性的键传给 `-enumeratorAtURL:... `方法中，在遍历目录内容时，这个方法能确保用非常高效的方式获取它们。在循环中，调用 `-getResourceValue:...` 能简单地从 `NSURL` 得到已缓存的值，而不用去访问文件系统。



### 参考

* [NSString / NSMutableString 字符串处理，常用代码 @**天梯梦**](http://justcoding.iteye.com/blog/1405951)
* [NSString API Reference](https://developer.apple.com/reference/foundation/nsstring) 
* [iOS字符串安全 @程序媛念茜](https://nianxi.net/ios/ios-string-security.html)
* [objc中国：第9期 字符串](https://www.objccn.io/issues/)
  * [卷首语](https://www.objccn.io/issue-9-0/)
  * [NSString 与 Unicode](https://www.objccn.io/issue-9-1/)
  * [玩转字符串](https://www.objccn.io/issue-9-2/)
  * [字符串本地化](https://www.objccn.io/issue-9-3/)
  * [字符串解析](https://www.objccn.io/issue-9-4/)
  * [字符串渲染](https://www.objccn.io/issue-9-5/)
