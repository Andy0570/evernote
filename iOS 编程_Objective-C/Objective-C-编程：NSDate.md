> 💡
> 生产环境下，推荐使用开源时间类框架：
> [DateTools](https://github.com/MatthewYork/DateTools) | [DateTools 使用「日期工具库」](https://www.jianshu.com/p/10c5a9e60880)

# NSDate

* **NSDate**: 用于处理日期的类，它提供日期的创建、比较、计算时间间隔等功能。
* **NSDate** 是一个类簇，我们所使用的 **NSDate** 对象，都是 **NSDate** 的私有子类的实体。
* **NSDate** 存储的是 **GMT** 时间，使用的时候会根据当前应用指定的 **时区** 进行时间上的增减，以供计算或显示。

### 创建时间对象

* 当前时间

```objectivec
// 现在时间
NSDate *now1 = [NSDate date];
// 现在时间（同上）
NSDate *now2 = [[NSDate alloc] init];
NSDate *now3 = [NSDate new];
```

* 未来时间和过去时间

```objectivec
// 可以表示的最远的未来时间 : 4001-01-01 00:00:00 +0000
NSDate *distantFuture = [NSDate distantFuture];
// 可以表示的最早的过去时间 : 0000-12-30 00:00:00 +0000
NSDate *distantPast = [NSDate distantPast];
```

* 创建相对时间 = 某个时间节点 + 时间戳（**NSTimeInterval**）

```objectivec
// ----------------------------- 类方法 --------------------------------
// 相对于当前时间
NSDate *dateFromNow = [NSDate dateWithTimeIntervalSinceNow:0];
// 相对于给定的某个已知时间
NSDate *dateFromAnotherDate = [NSDate dateWithTimeInterval:0 sinceDate:now];
// 相对于2001年1月1日0时0分0秒
NSDate *dateFromReferenceDate = [NSDate dateWithTimeIntervalSinceReferenceDate:0];
// 相对于1970年1月1日0时0分0秒
NSDate *dateFrom1970 = [NSDate dateWithTimeIntervalSince1970:0];

// ------------------------------ 实例化方法 (同上)-------------------------------
// 相对于当前时间
NSDate *dateFromNow2 = [[NSDate alloc] initWithTimeIntervalSinceNow:0];
// 相对于给定的某个已知时间
NSDate *dateFromAnotherDate2 = [[NSDate alloc] initWithTimeInterval:0 sinceDate:now];
// 相对于2001年1月1日0时0分0秒
NSDate *dateFromReferenceDate2 = [[NSDate alloc] initWithTimeIntervalSinceReferenceDate:0];
// 相对于1970年1月1日0时0分0秒
NSDate *dateFrom19702 = [[NSDate alloc] initWithTimeIntervalSince1970:0];
```

* 昨天、今天、明天

```objectivec
// 24H 的时间戳值
NSTimeInterval time = 24 * 60 * 60;  

//昨天此刻的时间
NSDate *yesterday = [[NSDate alloc] initWithTimeIntervalSinceNow:- time];
//现在的时间
NSDate *today = [[NSDate alloc] initWithTimeIntervalSinceNow:0]; 
//明天此刻的时间
NSDate 	*tomorrow = [[NSDate alloc] initWithTimeIntervalSinceNow:time];
```


* 日期的比较

```objectivec
// -------------------------------------------------------------
// 1. 通过 compare: 比较
NSComparisonResult result = [tomorrow compare:yesterday];
switch (result) {
    case NSOrderedAscending:
        // 升序
        NSLog(@"tomorrow < yesterday");
        break;
    case NSOrderedSame:
        // 相同
        NSLog(@"tomorrow = yesterday");
        break;
    case NSOrderedDescending:
        // 降序
        NSLog(@"tomorrow > yesterday");
        break;
}

// -------------------------------------------------------------
// 2. 通过时间戳值比较
if ([date1 timeIntervalSince1970] >
    [date3 timeIntervalSince1970]){
    NSLog(@"date1 > date3");
}else{
    NSLog(@"date1 < date3");
}

// -------------------------------------------------------------
// 3. isEqualToDate:
if (![yesterday isEqualToDate:tomorrow]) {
NSLog(@"不同的一天");
}

// -------------------------------------------------------------
// 4. earlierDate:
// 比较两个给定日期并返回更早的日期
NSDate *earlierDate = [tomorrow earlierDate:yesterday];
NSLog(@"earlierDate: %@",earlierDate);	// earlierDate: Wed Apr 12 12:30:36 2017

// -------------------------------------------------------------
// 5. laterDate:
// 比较两个给定日期并返回更晚的日期
NSDate *laterDate = [tomorrow laterDate:yesterday];
NSLog(@"laterDate: %@",laterDate);	// laterDate: Fri Apr 14 12:30:36 2017
```

* 创建某个指定日期

```objectivec
//mikey.hireDate = [NSDate dateWithNaturalLanguageString:@"Aug 2nd,2010"];

//Specifying date
NSDateComponents *components = [NSDateComponents new];
[components setYear:2010];
[components setDay:2];
[components setMonth:8];
//We need this calendar so that we can use -dateFromComponents function:)
NSCalendar *gregorian = [[NSCalendar alloc] initWithCalendarIdentifier: NSCalendarIdentifierGregorian];
//Assigning final date to our Object/Employee
mikey.hireDate = [gregorian dateFromComponents: components];
//Giving date nice look (Aug %day, %year)
NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];
dateFormatter.timeStyle = NSDateFormatterNoStyle;
dateFormatter.dateStyle = NSDateFormatterMediumStyle;
//Displays time in ENG (or localization), i get russian output by default without these two lines: Август 2, 2010
NSLocale *usLocale = [[NSLocale alloc] initWithLocaleIdentifier:@"en_US"];
[dateFormatter setLocale:usLocale];
//Applying dateFormatter to our date(mikey.hireDate), because it needs to look fancy... the result will be string, i just don't know how to make mikey.hireDate look right (%month %day, %year) without converting it to String    :}
NSString *finalDateItsStringNow = [dateFormatter stringFromDate:mikey.hireDate];

//----------
NSCalendar *calendar = [NSCalendar currentCalendar];
NSDateComponents *components = [[NSDateComponents alloc] init];
[components setYear:1998];
[components setMonth:5];
[components setDay:1];
NSDate *birthday = [calendar dateFromComponents:components];
```


#  NSTimeInterval

* **NSTimeInterval（时间戳）**：用于指定两个时间节点之间的时间间隔（以秒为单位）。
* **NSTimeInterval** 始终以 **秒** 为单位指定; 它在10,000年的范围内产生了亚毫秒的精度。


### 声明

```objectivec
typedef double NSTimeInterval;
```

* 获取时间戳

```objectivec
// -------------------------------------------------------------
// 给定时间 相对于 现在时间 的时间戳
NSDate *now = [NSDate date];
NSTimeInterval timeInterval = [now timeIntervalSinceNow];
NSLog(@"timeInterval is %f",timeInterval); // timeInterval is -0.000023

// -------------------------------------------------------------
// 两个给定时间节点之间的时间戳
NSDate *distantFuture = [NSDate distantFuture];
NSDate *distantPast = [NSDate distantPast];
NSTimeInterval timeInterval = [distantFuture timeIntervalSinceDate:distantPast];
NSLog(@"timeInterval is %.f",timeInterval);	// timeInterval is 126227980800

// -------------------------------------------------------------
// 2001年1月1日0时0分0秒 与 现在时间 之间的时间戳
NSDate *now = [NSDate date];
NSTimeInterval timeInterval = [now timeIntervalSinceReferenceDate];
NSLog(@"timeInterval is %f",timeInterval); // timeInterval is 513745346.558782

// -------------------------------------------------------------
// 1970年1月1日0时0分0秒 与 现在时间 之间的时间戳
NSDate *now = [NSDate date];
NSTimeInterval timeInterval = [now timeIntervalSince1970];
NSLog(@"timeInterval is %f",timeInterval); // timeInterval is 1492052119.655184
```

* **NSDate**对象与时间戳（**NSTimeInterval**） 相互转换

```objectivec
// NSDate对象 ------> 相对于1970年的时间戳
NSDate *now =[[NSDate alloc] init]; //创建此刻的时间
NSTimeInterval timeInterval = [now timeIntervalSince1970]; 

// 相对于1970年的时间戳 ------> NSDate 对象
NSDate *date = [NSDate dateWithTimeIntervalSince1970:timeInterval]; //获取时间
```

* 数字字符串 ——> 时间戳 ——> **NSDate** 对象

```objectivec
NSString *timeString = @"1234567890";
NSDate *convertDate = [NSDate dateWithTimeIntervalSince1970:[timeString doubleValue]];
NSLog(@"convertDate %@", convertDate);	// convertDate Sat Feb 14 07:31:30 2009
```

# NSDateFormatter

**NSDateFormatter** 是 **NSFormatter** 的子类。另，**NSFormatter** 的用途是“将数据在字符串与特定类型的对象之间转换”，目前 **NSFormatter** 只有两个子类 **NSNumberFormatter** 和 **NSDateFormatter**。

尽管 **NSDateFormatter** 提供了许多已定义好的时间格式，但是开发中开发人员更加喜欢自定义时间格式。

过多使用 **NSDateFormatter** 将影响程序的性能，且程序中 **NSDateFormatter** 对象的时间格式基本一致，所以使用 **NSDateFormatter** 的时候尽量使用单例模式。

* [性能优化之 NSDateFormatter](http://www.jianshu.com/p/82c1104aea6c)

1. 将时间字符串转换到 `NSDate` 对象，一般都是使用"年月日 时分秒"，数据库中的date类型基本上也是这样的时间类型。 格式一般为：`yyyy-MM-dd HH:mm:ss`。
   注：yyyy是小写的！大写的YYYY的意思有些不同——“将这一年中第一周的周日当作今年的第一天”，因此有时结果和yyyy相同，有时就会不同。
2. 将 `NSDate` 对象转换成特定格式的字符串。

   转换后的字符串会根据设备的“区域格式”，显示特定语言的结果。假如程序需要保证不同语言环境下显示一致，请注意这方面的问题，使用其他代替方法！

日期格式RFC文档：[Date_Format_Patterns](http://www.unicode.org/reports/tr35/tr35-25.html#Date_Format_Patterns)

```objectivec
NSDate *date = [NSDate date];
NSLog(@"date = %@\n",date);
// date = 2017-10-27 05:03:09 +0000

NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];
[dateFormatter setTimeZone:[NSTimeZone defaultTimeZone]];
[dateFormatter setLocale:[NSLocale currentLocale]];
[dateFormatter setDateFormat:@"yyyy-MM-dd aHH:mm:ss"];
NSLog(@"%@",[dateFormatter stringFromDate:date]);
// 2017-10-27 下午13:30:52

NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];
[dateFormatter setDateFormat:@"'公元（G=GG=GGG=GGGG=GGGGG）':G'='GG'='GGG'='GGGG'='GGGGG"];
NSLog(@"%@",[dateFormatter stringFromDate:date]);
// 公元（G=GG=GGG=GGGG=GGGGG）:公元=公元=公元=公元=公元

[dateFormatter setDateFormat:@"'年度（y=yy=yyy=yyyy=yyyyy）':y'='yy'='yyy'='yyyy'='yyyyy"];
NSLog(@"%@",[dateFormatter stringFromDate:date]);
// 年度（y=yy=yyy=yyyy=yyyyy）:2017=17=2017=2017=02017

[dateFormatter setDateFormat:@"'季度（Q=QQ=QQQ=QQQQ）':Q'='QQ'='QQQ'='QQQQ"];
NSLog(@"%@",[dateFormatter stringFromDate:date]);
// 季度（Q=QQ=QQQ=QQQQ）:4=04=4季度=第四季度

[dateFormatter setDateFormat:@"'季度（q=qq=qqq=qqq）':q'='qq'='qqq'='qqqq"];
NSLog(@"%@",[dateFormatter stringFromDate:date]);
// 季度（q=qq=qqq=qqq）:4=04=4季度=第四季度

[dateFormatter setDateFormat:@"'月份（M=MM=MMM=MMMM=MMMMM）':M'='MM'='MMM'='MMMM'='MMMMM"];
NSLog(@"%@",[dateFormatter stringFromDate:date]);
// 月份（M=MM=MMM=MMMM=MMMMM）:10=10=10月=十月=10
// 月份（M=MM=MMM=MMMM=MMMMM）:3=03=3月=三月=3

[dateFormatter setDateFormat:@"'本年第几周（w）':w"];
NSLog(@"%@",[dateFormatter stringFromDate:date]);
// 本年第几周（w）:43

[dateFormatter setDateFormat:@"'本月第几周（W）':W"];
NSLog(@"%@",[dateFormatter stringFromDate:date]);
// 本月第几周（W）:4

[dateFormatter setDateFormat:@"'本年第几天（D）':D"];
NSLog(@"%@",[dateFormatter stringFromDate:date]);
// 本年第几天（D）:300

[dateFormatter setDateFormat:@"'本月第几天（d=dd）':d'='dd"];
NSLog(@"%@",[dateFormatter stringFromDate:date]);
// 本月第几天（d=dd）:27=27
// 本月第几天（d=dd）:3=03

[dateFormatter setDateFormat:@"'本周第几天（F）':F"];
NSLog(@"%@",[dateFormatter stringFromDate:date]);
// 本周第几天（F）:4

[dateFormatter setDateFormat:@"'星期（e=ee=eee=eeee=eeeee）':e'='ee'='eee'='eeee'='eeeee"];
NSLog(@"%@",[dateFormatter stringFromDate:date]);
// 星期（e=ee=eee=eeee=eeeee）:6=06=周五=星期五=五

[dateFormatter setDateFormat:@"'星期（c=ccc=cccc=ccccc）':c'='ccc'='cccc'='ccccc"];
NSLog(@"%@",[dateFormatter stringFromDate:date]);
// 星期（c=ccc=cccc=ccccc）:6=周五=星期五=五

[dateFormatter setDateFormat:@"'上午/下午（a）':a"];
NSLog(@"%@",[dateFormatter stringFromDate:date]);
// 上午/下午（a）:下午

[dateFormatter setDateFormat:@"'小时（h=hh=H=HH）':h'='hh'='H'='HH"];
NSLog(@"%@",[dateFormatter stringFromDate:date]);
// 小时（h=hh=H=HH）:1=01=13=13

[dateFormatter setDateFormat:@"'分钟（m=mm）':m'='mm"];
NSLog(@"%@",[dateFormatter stringFromDate:date]);
// 分钟（m=mm）:3=03

[dateFormatter setDateFormat:@"'秒（s=ss）':s'='ss"];
NSLog(@"%@",[dateFormatter stringFromDate:date]);
// 秒（s=ss）:9=09

[dateFormatter setDateFormat:@"'毫秒（S）':S"];
NSLog(@"%@",[dateFormatter stringFromDate:date]);
// 毫秒（S）:9

[dateFormatter setDateFormat:@"'一天中的毫秒（A）':A"];
NSLog(@"%@",[dateFormatter stringFromDate:date]);
// 一天中的毫秒（A）:46989943

[dateFormatter setDateFormat:@"'时区（z/zz/zzz=zzzz=v=vvvv）':z'='zzzz'='v'='vvvv"];
NSLog(@"%@",[dateFormatter stringFromDate:date]);
// 时区（z/zz/zzz=zzzz=v=vvvv）:GMT+8=中国标准时间=中国时间=中国标准时间

[dateFormatter setDateFormat:@"'时区编号（Z/ZZ/ZZZ=ZZZZ）':Z'='ZZZZ"];
NSLog(@"%@",[dateFormatter stringFromDate:date]);
// 时区编号（Z/ZZ/ZZZ=ZZZZ）:+0800=GMT+08:00
```

* **NSDate** 对象 —> 格式化日期字符串

```objectivec
NSDate *date = [NSDate date];
//设置日期的格式 ：2015年01月03日 03：32：12
NSDateFormatter *dateFormat = [[NSDateFormatter alloc] init];
[dateFormat setDateFormat:@"yyyy年MM月dd日 HH:mm:ss"];
NSString *dateString = [dateFormat stringFromDate:date];
NSLog(@"当前系统时间：%@ \n 格式化后的时间字符串：%@",date,dateString);
```

>**当前系统时间：** **2017-04-13 04:38:23 +0000**
>**格式化后的时间字符串：** **2017** **年** **04** **月** **13** **日** **12:38:23**

* 日期字符串 —> **NSDate** 对象

```objectivec
//----------------------------------------------------------
NSString *str = @"2014-03-21 15:55:55";
// 设置字符串的日期格式
NSDateFormatter *dateFormater = [[NSDateFormatter alloc] init];
[dateFormater setDateFormat:@"yyyy-M-d HH:mm:ss"];

NSDate *date = [dateFormater dateFromString:str];
NSLog(@"date:%@",date); // date:Fri Mar 21 15:55:55 2014


//----------------------------------------------------------
NSString *dateString = @"2019-08-27T00:00:00.000+0000"; 
// 设置字符串的日期格式
NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];
// dateFormatter.locale = [[NSLocale alloc] initWithLocaleIdentifier:@"en_US_POSIX"];
// ISO 8601: 2019-08-27T00:00:00.000+0000
[dateFormatter setDateFormat:@"yyyy-MM-dd'T'HH:mm:ss.SSSZ"];
    
NSDate *date = [dateFormatter dateFromString:dateString];
DDLogDebug(@"date = %@",date); // date = 2019-08-27 00:00:00 +0000
```
> ⚠️ 日期字符串转换为 **NSDate** 对象时，设置的 **NSDateFormatter** 对象是字符串的日期格式，而不是转换后的 **NSDate** 对象格式。

* NSString —> NSDate —> NSString

```objectivec
// YYModel 框架方法、DateTool 方法
- (BOOL)modelCustomTransformFromDictionary:(NSDictionary *)dic {

    // 自定义转换日期
    NSString *treatmentDateString = dic[@"就诊时间"];
    if (![treatmentDateString isKindOfClass:[NSString class]]) return NO;

    // string -> date -> string
    // (yyyy-MM-dd HH:mm:ss) -> (yyyy年MM月dd日 HH:mm:ss)
    NSDate *date = [NSDate dateWithString:treatmentDateString formatString:@"yyyy-MM-dd"];
    _treatmentDate = [date formattedDateWithFormat:@"yyyy年MM月dd日"];
    return YES;
}

``` 


* **NSDateFormatterStyle**

```objectivec
typedef NS_ENUM(NSUInteger, NSDateFormatterStyle) {    // date and time format styles
    NSDateFormatterNoStyle = kCFDateFormatterNoStyle,
    NSDateFormatterShortStyle = kCFDateFormatterShortStyle,
    NSDateFormatterMediumStyle = kCFDateFormatterMediumStyle,
    NSDateFormatterLongStyle = kCFDateFormatterLongStyle,
    NSDateFormatterFullStyle = kCFDateFormatterFullStyle
};
```

代码示例：

```objectivec
- (NSDateFormatter *)dateFormatter  
{  
    static NSDateFormatter *dateFormatter;  
    if (!dateFormatter) {  
        dateFormatter = [[NSDateFormatter alloc] init];  
          
        dateFormatter = [[NSDateFormatter alloc] init];  
        // NSDateFormatterStyle几种取值样式  
        // NSDateFormatterNoStyle  
        // 例如： （其实就是空白的，不显示）  
  
        // NSDateFormatterShortStyle  
        // 例如：下午7:00 | 15/5/19  
          
        // NSDateFormatterMediumStyle  
        // 例如：下午7:00:00 | 2013年5月19日  
          
        // NSDateFormatterLongStyle  
        // 例如：GMT +8下午7:00:00 | 2013年5月19日  
          
        // NSDateFormatterFullStyle  
        // 例如：中国标准时间下午7:00:00 | 2013年5月19日 星期日  
        dateFormatter.timeStyle = NSDateFormatterFullStyle;  
        dateFormatter.dateStyle = NSDateFormatterLongStyle;  
    }  
    return dateFormatter;  
} 
```




# NSTimeZone

* 时区是一个地理名字，是为了克服各个地区或国家之间在使用时间上的混乱。

### 基本概念：

1. * GMT 0:00 格林威治标准时间; 
   * UTC +00:00 校准的全球时间; 
   * CCD +08:00 中国标准时间 ;
2. 夏时制，英文"DaylightSavingTime"。夏季时将时区内的时间提前（一般为1小时），以节省资源，提高效率。使用夏时制期间，当前时区相对于GMT的时间偏移量会发生变化。在某些应用中可能需要考虑。
3. 任何时区都以 **GMT** 为基准，即，任何 **NSTimeZone** 对象所代表的时区都是相对于 **GMT** 的，这里的相对性是 **NSTimeZone** 中最重要的属性，我们称之为当前时区相对于GMT的偏移量。一旦知道了一个偏移量，便可以确定一个时区。在iOS中，偏移量是以"秒"为单位的。
4. **NSTimeZone** 是一个类簇，我们所使用的任何 **NSTimeZone** 对象都是 **NSTimeZone** 的私有子类。
5. iOS中的时间类 **NSDate** 中存储的时间，都是相对于 **GMT** 的，我们使用 **NSDate** 时，会根据App的时区设置返回与时区对应的数据。
6. iOS系统中的 ```/usr/share/zoneinfo/```目录中保存了所有的可根据 地理位置名称 或 时区别名 得到的时区信息。时区别名都是与具体的地理位置一一对应的。(已越狱的童鞋请看)
7. iOS中的时区表示方法：```GMT+0800``` 、```GMT-0800```。(【+：东区】【 -：西区】【 08：小时数】【 00：分钟数】)。 ```GMT+0830 ```就是指比GMT早8小时外加30分钟的时区。




### 方法


* ```+ (NSArray *)knownTimeZoneNames;```

  返回列出系统已知的所有时区ID的字符串数组。

* ```+ (NSDictionary *)abbreviationDictionary;```

  获取所有的时区名称缩写


+ ```+ (id)timeZoneWithName:(NSString *)tzName;```
  由时区的名称获得对应的NSTimeZone对象
  通过时区名称可以获得时区文件，通过时区文件就可以获得“偏移量”，“名称缩写”，“是否使用夏时制”等信息。


+ ```(id)timeZoneWithAbbreviation:(NSString *)abbreviation;```
  由时区名称缩写获得对应的NSTimeZone对象
  > 这里的时区名称缩写有两种情况：
  >
  > * 第一种是上面说的HKT这样的缩写，与时区名称一一对应，通过这样的缩写获得的NSTimeZone对象，与使用时区名称获得得NSTimeZone对象一样。（大概读取得是同一个时区文件）
  > * 第二种是"GMT+0800"这样格式得缩写，其实这就是偏移量。通过偏移量在iOS中是不能读到与之对应得时区文件的，因此就无法知道“时区名称”，“名称缩写”，“是否使用夏时制”这样的信息了。默认情况下，"时区名称"和"名称缩写"都会赋值为"GMT+0800"，"是否使用夏时制"则不会设置（默认不使用）。


+ ```+ (id)timeZoneForSecondsFromGMT:(NSInteger)seconds;``` 
  由偏移量获得对应的NSTimeZone对象

  > 只说一点：通过偏移量获得的 **NSTimeZone** 对象的“市区名称”，“名称缩写”都会赋值为"GMT+0800"，"是否使用夏时制"则不会设置（默认不使用）。
  >  注意！！！！该方法不做参数的范围检查！！！




### 根据指定时区格式化日期


```objectivec
NSDate *date = [NSDate date];
//设置日期的格式
NSDateFormatter *dateFormat = [[NSDateFormatter alloc] init];
[dateFormat setDateFormat:@"yyyy年MM月dd日 HH:mm:ss"];

//获取到所有时区的名字
NSArray *zoneName = [NSTimeZone knownTimeZoneNames];
NSLog(@"%@",zoneName);

//设置时区为纽约时区
NSTimeZone *timeZone = [[NSTimeZone alloc] initWithName:@"America/New_York"];
[dateFormat setTimeZone:timeZone];
  
NSString *dateString = [dateFormat stringFromDate:date];
NSLog(@"当前时间：%@ \n纽约时间:%@",date,dateString);
```
> **当前时间：** **2017-04-13 04:46:23 +0000 **
>
> **纽约时间：** **2017** **年** **04** **月** **13** **日** **00:46:23**



# NSLocale

若你只开发中国区的应用，需要保证用户修改当前语言环境时应用的显示不发生变化。而像NSDateFormatter这样的类，会根据设备的设置，自动返回不同语言的数据。为了保证返回数据的语言一致，我们需要设置NSLocale。 下面的代码就可以保证在任何语言环境下，只返回中文的数据：

```objectivec
NSLocale *locale = [[NSLocale alloc] initWithLocaleIdentifier:@"zh"];
NSDateFormatter *secondDateFormatter = [[NSDateFormatter alloc] init];
[secondDateFormatter setDateFormat:@"cccc"];
secondDateFormatter.locale = locale;
NSDate *date = [NSDate date];
NSLog(@"%@", [secondDateFormatter stringFromDate:date]);
```

当然，像上面的需求很罕见。

作为大家都不常用的一个类，NSLocale类是将与国家和语言相关的信息进行简单的组合，包括货币，文学方面的信息。 货币：货币的国际名称（人民币的国际货币名称是CNY）；货币符号（人民币的国际货币符号是¥） 文学：标点符号，文字的书写顺序（左右顺序），引用的起止符号等等 若做金融一类的应用可能会用到NSLocale这个类。


# 附录

##### 日期说明符号对照一览表

* [UNICODE LOCALE DATA MARKUP LANGUAGE (LDML)](http://unicode.org/reports/tr35/tr35-10.html#Date_Format_Patterns)

```objectivec
// -------------------------------------------------------------
d 月中的某一天。一位数的日期没有前导零。  

dd 月中的某一天。一位数的日期有一个前导零。  

ddd 周中某天的缩写名称,在 AbbreviatedDayNames 中定义。  

dddd 周中某天的完整名称,在 DayNames 中定义。  

// -------------------------------------------------------------
M 月份数字。一位数的月份没有前导零。  

MM 月份数字。一位数的月份有一个前导零。  

MMM 月份的缩写名称,在 AbbreviatedMonthNames 中定义。  

MMMM 月份的完整名称,在 MonthNames 中定义。  

// -------------------------------------------------------------
y 不包含纪元的年份。如果不包含纪元的年份小于 10,则显示不具有前导零的年份。  

yy 不包含纪元的年份。如果不包含纪元的年份小于 10,则显示具有前导零的年份。  

yyyy 包括纪元的四位数的年份。  

gg 时期或纪元。如果要设置格式的日期不具有关联的时期或纪元字符串,则忽略该模式。   

// -------------------------------------------------------------
h 12小时制的小时。一位数的小时数没有前导零。  

hh 12小时制的小时。一位数的小时数有前导零。  

H 24小时制的小时。一位数的小时数没有前导零。  

HH 24小时制的小时。一位数的小时数有前导零。  

// -------------------------------------------------------------
m 分钟。一位数的分钟数没有前导零。  

mm 分钟。一位数的分钟数有一个前导零。   

// -------------------------------------------------------------
s 秒。一位数的秒数没有前导零。   

ss 秒。一位数的秒数有一个前导零。   

// -------------------------------------------------------------
f 秒的小数精度为一位。其余数字被截断。  

ff 秒的小数精度为两位。其余数字被截断。  

fff 秒的小数精度为三位。其余数字被截断。  

ffff 秒的小数精度为四位。其余数字被截断。  

fffff 秒的小数精度为五位。其余数字被截断。  

ffffff 秒的小数精度为六位。其余数字被截断。  

fffffff 秒的小数精度为七位。其余数字被截断。  

// -------------------------------------------------------------
t 在 AMDesignator 或 PMDesignator 中定义的 AM/PM 指示项的第一个字符(如果存在)。   

tt 在 AMDesignator 或 PMDesignator 中定义的 AM/PM 指示项(如果存在)。  

// -------------------------------------------------------------
z 时区偏移量(“+”或“-”后 仅跟小时)。一位数的小时数没有前导零。例如,太平洋标准时间是“-8”。   

zz 时区偏移量(“+”或“-”后 仅跟小时)。一位数的小时数有前导零。例如,太平洋标准时间是“-08”。   

zzz 完整时区偏移量(“+”或“-”后 跟有小时和分钟)。一位数的小时数和分钟数有前导零。例如,太平 洋标准时间是“-08:00”。  

// -------------------------------------------------------------
: 在 TimeSeparator 中定义的默认时间分隔符。  

/ 在 DateSeparator 中定义的默认日期分隔符。  

% c 其中 c 是格式模式(如果单独使 )。如果格式模式与原义字符或其他格式模式合并,则可以省略 “%”字符。  

\ c 其中 c 是任意字符。照原义显示字符。若要显示反斜杠字符,请使 “\”。
```



### 参考

* [Unicode Technical Standard #35 - Unicode Locale Data Markup Language (LDML)](http://www.unicode.org/reports/tr35/tr35-25.html)
* [iOS时间那点事--NSDate](https://my.oschina.net/yongbin45/blog/150114)
* [iOS时间那点事--NSDateFormatter](https://my.oschina.net/yongbin45/blog/150667)
* [iOS时间那点事--NSTimeZone](https://my.oschina.net/yongbin45/blog/151376)
* [ios时间那点事--NSLocale](https://my.oschina.net/yongbin45/blog/156130)
* [[NSDateFormatterStyle 几种取值的区别](http://blog.csdn.net/ahut_qyb_6737/article/details/42494211)](http://blog.csdn.net/ahut_qyb_6737/article/details/42494211)
