![](https://upload-images.jianshu.io/upload_images/2648731-c60a8134a1528448.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## [DateTools](https://github.com/MatthewYork/DateTools)

DateTools 可以简化 iOS 中日期和时间的处理。 DateTools 的灵感来自其他语言中的类和概念，尤其是 .NET 语言中的 [DateTime](http://msdn.microsoft.com/en-us/library/system.datetime(v=vs.110).aspx) 结构和 [Time Period Library](http://www.codeproject.com/Articles/168662/Time-Period-Library-for-NET) 库。 通过 DateTools 中定义的类和方法，DateTools 无需编写访问日期组件所需的样板代码，而且处理了更细微的日期比较方式，并作为时间段及其集合等全新概念的基础。

特性：时间处理的便捷语法、时间段、国际化语言。

编程语言支持：支持 **Objective-C** 和 **Swift**。


## 安装

**CocoaPods**

Swift：
```ruby
pod 'DateToolsSwift'
```

Objective-C (Legacy)：
```ruby
pod 'DateTools'
```

**手动安装**

DateTools 所需的所有类都位于此存储库根目录的 DateTools 文件夹中。它们有如下这些：

Swift (可以在 `DateToolsSwift/DateTools` 目录下找到)
* `Constants.swift`
* `Date+Bundle.swift`
* `Date+Comparators.swift`
* `Date+Components.swift`
* `Date+Format.swift`
* `Date+Inits.swift`
* `Date+Manipulations.swift`
* `Date+TimeAgo.swift`
* `DateTools.bundle`
* `Enums.swift`
* `Integer.DateTools.swift`
* `TimeChunk.swift`
* `TimePeriod.swift`
* `TimePeriodChain.swift`
* `TimePeriodCollection.swift`
* `TimePeriodGroup.swift`

Objective-C (可以在 `DateTools/DateTools` 目录下找到)
* `DateTools.h`
* `NSDate+DateTools.{h,m}`
* `DTConstants.h`
* `DTError.{h,m}`
* `DTTimePeriod.{h,m}`
* `DTTimePeriodGroup.{h,m}`
* `DTTimePeriodCollection.{h,m}`
* `DTTimePeriodChain.{h,m}`

另外，如果你想支持国际化语言或想要使用 “Time Ago” 功能，则必须使用以下bundle 包。
你可以在 `Project` 菜单中 `Info` 下的 `Localizations` 子标题中添加本地化支持。

* `DateTools.bundle`

![添加本地化支持](https://upload-images.jianshu.io/upload_images/2648731-c1ce80bff0c64bc0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


`DateTools.h` 文件是包含所有其他文件的头文件。如果你想导入整个 DateTools 框架，Import 这个文件就可以了。


## DateTools

**[这里](http://cocoadocs.org/docsets/DateToolsSwift/2.0.0/) 可以找到完整的代码文档**

DateTools 的任务之一就是让 Date 感觉更完整。还有许多其他语言允许直接访问日期类别中的日期信息，但是 Date（很遗憾）没有这个方法。 它通过 ` timeIntervalSince` 方法安全地仅在 Unix 时间偏移中构建日期并且仍然让日历不可知。但这并不总是我们想要做的。有时，我们希望在更抽象的层面上根据日期组件处理日期 (比如年、月、日等等) 。这就是 DateTools 的用武之地。

#### Time Ago 距离现在过去多久

> Time Ago 就是将日期转变为相对日期的表达形式，即我们常用的 “昨天、今天、明天、几天前、几分钟前、一周以后……” 这样的表达方式。

如果一个时间库没有「获取某个时间比现在早多久的 `NSString` 类型的字符串」的能力，那它就不是一个完整的库。
 DateTools 可以为你提供便利。这些 “time ago” 的字符串有长和短两种形式，后者与 Twitter 非常相似。
你可以像这样得到这些字符串：

Swift：
```swift
let timeAgoDate = 2.days.earlier
print("Time Ago: ", timeAgoDate.timeAgoSinceNow)
print("Time Ago: ", timeAgoDate.shortTimeAgoSinceNow)

//Output:
//Time Ago: 2 days ago
//Time Ago: 2d
```

Objective-C：
```objectivec
NSDate *date = [NSDate dateWithTimeIntervalSinceNow:60 * 60 * 60];
NSString *timeAgo = [date timeAgoSinceNow];  // 2天前
NSString *shortTimeAgo = [date shortTimeAgoSinceNow]; // 2天
NSString *weekTimeAgo = [date weekTimeAgoSinceNow]; // 星期五
```

Time Ago 支持的 Objective-C 方法列表：
```objectivec
#pragma mark - Time Ago
// 类方法，距离指定时间过去多久
+ (NSString*)timeAgoSinceDate:(NSDate*)date;
+ (NSString*)shortTimeAgoSinceDate:(NSDate*)date;
+ (NSString *)weekTimeAgoSinceDate:(NSDate *)date;

// 距离现在过去多久
- (NSString*)timeAgoSinceNow;
- (NSString *)shortTimeAgoSinceNow;
- (NSString *)weekTimeAgoSinceNow;

// 实例方法，距离指定时间过去多久
- (NSString *)timeAgoSinceDate:(NSDate *)date;
- (NSString *)timeAgoSinceDate:(NSDate *)date numericDates:(BOOL)useNumericDates;
- (NSString *)timeAgoSinceDate:(NSDate *)date numericDates:(BOOL)useNumericDates numericTimes:(BOOL)useNumericTimes;

- (NSString *)shortTimeAgoSinceDate:(NSDate *)date;
- (NSString *)weekTimeAgoSinceDate:(NSDate *)date;
```

假设你已将 localization 本地化添加到项目中，DateTools 还支持下面这些语言： 

- ar (Arabic)
- bg (Bulgarian)
- ca (Catalan)
- zh_Hans (Chinese Simplified) 简体中文
- zh_Hant (Chinese Traditional) 传统中文？？？支持农历？
- cs (Czech)
- da (Danish)
- nl (Dutch)
- en (English)
- fi (Finnish)
- fr (French)
- de (German)
- gre (Greek)
- gu (Gujarati)
- he (Hebrew)
- hi (Hindi)
- hu (Hungarian)
- is (Icelandic)
- id (Indonesian)
- it (Italian)
- ja (Japanese)
- ko (Korean)
- lv (Latvian)
- ms (Malay)
- nb (Norwegian)
- pl (Polish)
- pt (Portuguese)
- ro (Romanian)
- ru (Russian)
- sl (Slovenian)
- es (Spanish)
- sv (Swedish)
- th (Thai)
- tr (Turkish)
- uk (Ukrainian)
- vi (Vietnamese)
- cy (Welsh)
- hr (Croatian)

#### Date Components 日期组件

There is a lot of boilerplate associated with getting date components from an Date. You have to set up a calendar, use the desired flags for the components you want, and finally extract them out of the calendar. 

从 Date 获取日期组件有很多样板代码。你必须设置日历，使用所需组件的所需标志，最后从日历中提取它们。

有了 DateTools, 在 Swift 中下面的代码:

```swift
//Create calendar
let calendar = Calendar(identifier: .gregorian)
let dateComponents = calendar.dateComponents(Set<Calendar.Component>([.month,.year]), from: Date())
        
//Get components
let year = dateComponents.year!
let month = dateComponents.month!
```

...会变成这样:
```swift
let year = Date().year
let month = Date().month
```

日期组件支持的 Objective-C 方法列表：
```objectivec
#pragma mark - Date Components Without Calendar
- (NSInteger)era;
- (NSInteger)year;
- (NSInteger)month;
- (NSInteger)day;
- (NSInteger)hour;
- (NSInteger)minute;
- (NSInteger)second;
- (NSInteger)weekday;
- (NSInteger)weekdayOrdinal;
- (NSInteger)quarter;
- (NSInteger)weekOfMonth;
- (NSInteger)weekOfYear;
- (NSInteger)yearForWeekOfYear;
- (NSInteger)daysInMonth;
- (NSInteger)dayOfYear;
-(NSInteger)daysInYear;
-(BOOL)isInLeapYear;
- (BOOL)isToday;
- (BOOL)isTomorrow;
-(BOOL)isYesterday;
- (BOOL)isWeekend;
-(BOOL)isSameDay:(NSDate *)date;
+ (BOOL)isSameDay:(NSDate *)date asDate:(NSDate *)compareDate;
```

#### Date Editing 日期编辑

DateTools 中的日期编辑方法可以通过添加和减去日期组件轻松地前移或者推迟日期。例如，如果你希望获得给定日期起 1 年后的日期，只需调用 `dateByAddingYears` 方法即可 。

有了 DateTools, 在 Objective-C 中下面的代码:
```objectivec
// Create calendar
NSCalendar *calendar = [[NSCalendar alloc] initWithCalendarIdentifier:[NSDate defaultCalendar]];
NSDateComponents *components = [[NSDateComponents alloc] init];

// Make changes
[components setYear:1];

// Get new date with updated year
NSDate *newDate = [calendar dateByAddingComponents:components toDate:date options:0];
```

...会变成这样:
```objectivec
NSDate *newDate = [date dateByAddingYears:1];
```

也完全支持减去日期组件的方法：`dateBySubtractingYears`

日期编辑支持的 Objective-C 方法列表：

```objectivec
#pragma mark - 日期编辑
#pragma mark 增加日期方法
- (NSDate *)dateByAddingYears:(NSInteger)years;
- (NSDate *)dateByAddingMonths:(NSInteger)months;
- (NSDate *)dateByAddingWeeks:(NSInteger)weeks;
- (NSDate *)dateByAddingDays:(NSInteger)days;
- (NSDate *)dateByAddingHours:(NSInteger)hours;
- (NSDate *)dateByAddingMinutes:(NSInteger)minutes;
- (NSDate *)dateByAddingSeconds:(NSInteger)seconds;
#pragma mark 减少日期方法
- (NSDate *)dateBySubtractingYears:(NSInteger)years;
- (NSDate *)dateBySubtractingMonths:(NSInteger)months;
- (NSDate *)dateBySubtractingWeeks:(NSInteger)weeks;
- (NSDate *)dateBySubtractingDays:(NSInteger)days;
- (NSDate *)dateBySubtractingHours:(NSInteger)hours;
- (NSDate *)dateBySubtractingMinutes:(NSInteger)minutes;
- (NSDate *)dateBySubtractingSeconds:(NSInteger)seconds;
```

#### Date Comparison 时间比较

DateTools 的另一个任务是大大增加日期比较的灵活性。日期为你提供了四种基本方法:

```objectivec
-(BOOL)isEarlierThan:(NSDate *)date;
-(BOOL)isLaterThan:(NSDate *)date;
-(BOOL)isEarlierThanOrEqualTo:(NSDate *)date;
-(BOOL)isLaterThanOrEqualTo:(NSDate *)date;
```

这些方法非常适合以布尔值方式比较日期，但是如果我们想要比较日期并返回一些有意义的信息来分析它们分开多远呢？
Date 附带两个方法 `timeIntervalSinceDate:` 和 `timeIntervalSinceNow`，它为您提供 `double` 偏移量，表示两个日期之间的秒数。
这很好，但有时候人们想知道两个日期之间有多少年或几天。为此，DateTools 回到了可靠的 `NSCalendar` 并为您抽象出所有必要的代码。

有了 DateTools, 在 Objective-C 中下面的代码:
```objectivec
NSCalendar *calendar = [[NSCalendar alloc] initWithCalendarIdentifier:[NSDate defaultCalendar]];
NSDate *earliest = [firstDate earlierDate:secondDate];
NSDate *latest = (secondDate == firstDate) ? secondDate : firstDate;
NSInteger multiplier = (secondDate == firstDate) ? -1 : 1;
NSDateComponents *components = [calendar components:allCalendarUnitFlags fromDate:earliest toDate:latest options:0];
NSInteger yearsApart = multiplier*(components.month + 12*components.year);
```
..会变成这样:
```objectivec
NSInteger yearsApart = [firstDate yearsFrom:secondDate];
```

此类比较方法包括:
```objectivec
#pragma mark - Date Comparison
#pragma mark Time From
-(NSInteger)yearsFrom:(NSDate *)date;
-(NSInteger)monthsFrom:(NSDate *)date;
-(NSInteger)weeksFrom:(NSDate *)date;
-(NSInteger)daysFrom:(NSDate *)date;
-(double)hoursFrom:(NSDate *)date;
-(double)minutesFrom:(NSDate *)date;
-(double)secondsFrom:(NSDate *)date;
#pragma mark Time From With Calendar
-(NSInteger)yearsFrom:(NSDate *)date calendar:(NSCalendar *)calendar;
-(NSInteger)monthsFrom:(NSDate *)date calendar:(NSCalendar *)calendar;
-(NSInteger)weeksFrom:(NSDate *)date calendar:(NSCalendar *)calendar;
-(NSInteger)daysFrom:(NSDate *)date calendar:(NSCalendar *)calendar;

#pragma mark Time Until
-(NSInteger)yearsUntil;
-(NSInteger)monthsUntil;
-(NSInteger)weeksUntil;
-(NSInteger)daysUntil;
-(double)hoursUntil;
-(double)minutesUntil;
-(double)secondsUntil;
#pragma mark Time Ago
-(NSInteger)yearsAgo;
-(NSInteger)monthsAgo;
-(NSInteger)weeksAgo;
-(NSInteger)daysAgo;
-(double)hoursAgo;
-(double)minutesAgo;
-(double)secondsAgo;
#pragma mark Earlier Than
-(NSInteger)yearsEarlierThan:(NSDate *)date;
-(NSInteger)monthsEarlierThan:(NSDate *)date;
-(NSInteger)weeksEarlierThan:(NSDate *)date;
-(NSInteger)daysEarlierThan:(NSDate *)date;
-(double)hoursEarlierThan:(NSDate *)date;
-(double)minutesEarlierThan:(NSDate *)date;
-(double)secondsEarlierThan:(NSDate *)date;
#pragma mark Later Than
-(NSInteger)yearsLaterThan:(NSDate *)date;
-(NSInteger)monthsLaterThan:(NSDate *)date;
-(NSInteger)weeksLaterThan:(NSDate *)date;
-(NSInteger)daysLaterThan:(NSDate *)date;
-(double)hoursLaterThan:(NSDate *)date;
-(double)minutesLaterThan:(NSDate *)date;
-(double)secondsLaterThan:(NSDate *)date;
```

#### Formatted Date Strings 时间格式化为字符串

DateTools 有一些方便的方法，可以将 NSDate 日期快速转换为 NSString 字符串。
```objectivec
#pragma mark - Formatted Dates

#pragma mark Formatted With Style
-(NSString *)formattedDateWithStyle:(NSDateFormatterStyle)style;
-(NSString *)formattedDateWithStyle:(NSDateFormatterStyle)style timeZone:(NSTimeZone *)timeZone;
-(NSString *)formattedDateWithStyle:(NSDateFormatterStyle)style locale:(NSLocale *)locale;
-(NSString *)formattedDateWithStyle:(NSDateFormatterStyle)style timeZone:(NSTimeZone *)timeZone locale:(NSLocale *)locale;

#pragma mark Formatted With Format
-(NSString *)formattedDateWithFormat:(NSString *)format;
-(NSString *)formattedDateWithFormat:(NSString *)format timeZone:(NSTimeZone *)timeZone;
-(NSString *)formattedDateWithFormat:(NSString *)format locale:(NSLocale *)locale;
-(NSString *)formattedDateWithFormat:(NSString *)format timeZone:(NSTimeZone *)timeZone locale:(NSLocale *)locale;
```

## Time Periods 时间段

> 时间段支持目前用不到，以后再研究。。。

Dates are important, but the real world is a little less discrete than that. Life is made up of spans of time, like an afternoon appointment or a weeklong vacation. In DateTools, time periods are represented by the TimePeriod class and come with a suite of initializaiton, manipulation, and comparison methods to make working with them a breeze.

#### Initialization 初始化

Time peroids consist of an Date start date and end date. To initialize a time period, call the init function.

```objectivec
DTTimePeriod *timePeriod = [[DTTimePeriod alloc] initWithStartDate:startDate endDate:endDate];
```
or, if you would like to create a time period of a known length that starts or ends at a certain time, try out a few other init methods. The method below, for example, creates a time period starting at the current time that is exactly 5 hours long.
```objc
DTTimePeriod *timePeriod = [DTTimePeriod timePeriodWithSize:DTTimePeriodSizeHour amount:5 startingAt:[NSDate date]];
```

#### Time Period Info 时间段信息

A host of methods have been extended to give information about an instance of TimePeriod. A few are listed below
* `hasStartDate` - 如果该时间段有开始日期，返回 `true`
* `hasEndDate` - 如果该时间段有结束日期，返回 `true`
* `isMoment` - 如果该时间段的开始日期和结束日期相同，返回 `true`
* `durationIn....` - 返回请求单元的时间段长度

#### Manipulation 操纵

时间段是可以操纵的，你可以前移后者后移，也可以扩展和压缩。

**Shifting** 移动

When a time period is shifted, the start dates and end dates are both moved earlier or later by the amounts requested.
To shift a time period earlier, call <code>shiftEarlierWithSize:amount:</code> and to shift it later, call <code>shiftLaterWithSize:amount:</code>. The amount field serves as a multipler, just like in the above initializaion method.

**Lengthening/Shortening** 扩展和压缩

When a time periods is lengthened or shortened, it does so anchoring one date of the time period and then changing the other one. There is also an option to anchor the centerpoint of the time period, changing both the start and end dates.

An example of lengthening a time period is shown below:
```objectivec
DTTimePeriod *timePeriod  = [DTTimePeriod timePeriodWithSize:DTTimePeriodSizeMinute endingAt:[NSDate date]];
[timePeriod lengthenWithAnchorDate:DTTimePeriodAnchorEnd size:DTTimePeriodSizeMinute amount:1];
```
This doubles a time period of duration 1 minute to duration 2 minutes. The end date of "now" is retained and only the start date is shifted 1 minute earlier.

#### Relationships 两个时间段之间的关系

There may come a need, say when you are making a scheduling app, when it might be good to know how two time periods relate to one another. Are they the same? Is one inside of another? All these questions may be asked using the relationship methods of TimePeriod.

Below is a chart of all the possible relationships between two time periods:
![TimePeriods](https://upload-images.jianshu.io/upload_images/2648731-39c7d75452f7653d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

A suite of methods have been extended to check for the basic relationships. They are listed below:
* isEqualToPeriod:
* isInside:
* contains:
* overlapsWith:
* intersects:

You can also check for the official relationship (like those shown in the chart) with the following method:
```objectivec
-(DTTimePeriodRelation)relationToPeriod:(DTTimePeriod *)period;
```
All of the possible relationships have been enumerated in the TimePeriodRelation enum. 

**For a better grasp on how time periods relate to one another, check out the "Time Periods" tab in the example application. Here you can slide a few time periods around and watch their relationships change.**

![TimePeriods](https://upload-images.jianshu.io/upload_images/2648731-b99e9b0d40d0a636.gif?imageMogr2/auto-orient/strip)

## Time Period Groups 时间段组

Time period groups are the final abstraction of date and time in DateTools. Here, time periods are gathered and organized into something useful. There are two main types of time period groups,  <code>TimePeriodCollection</code> and <code>TimePeriodChain</code>. At a high level, think about a collection as a loose group where overlaps may occur and a chain a more linear, tight group where overlaps are not allowed.

Both collections and chains operate like an NSArray. You may add,insert and remove TimePeriod objects from them just as you would objects in an array. The difference is how these periods are handled under the hood.

### Time Period Collections 时间段集合
Time period collections serve as loose sets of time periods. They are unorganized unless you decide to sort them, and have their own characteristics like a StartDate and EndDate that are extrapolated from the time periods within. Time period collections allow overlaps within their set of time periods. 

![TimePeriodCollections](https://upload-images.jianshu.io/upload_images/2648731-2ca6489f362ec363.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

To make a new collection, call the class method like so:

```objectivec
//Create collection
DTTimePeriodCollection *collection = [DTTimePeriodCollection collection];

//Create a few time periods
 DTTimePeriod *firstPeriod = [DTTimePeriod timePeriodWithStartDate:[dateFormatter dateFromString:@"2014 11 05 18:15:12.000"] endDate:[dateFormatter dateFromString:@"2015 11 05 18:15:12.000"]];
    DTTimePeriod *secondPeriod = [DTTimePeriod timePeriodWithStartDate:[dateFormatter dateFromString:@"2015 11 05 18:15:12.000"] endDate:[dateFormatter dateFromString:@"2016 11 05 18:15:12.000"]];

//Add time periods to the colleciton
[collection addTimePeriod:firstPeriod];
[collection addTimePeriod:secondPeriod];

//Retreive collection items
DTTimePeriod *firstPeriod = collection[0];
```
**Sorting** 排序
Sorting time periods in a collection is easy, just call one of the sort methods. There are a total of three sort options, listed below:

* **Start Date** - `sortByStartAscending`, `sortByStartDescending`
* **End Date** - `sortByEndAscending`, `sortByEndDescending`
* **Time Period Duration** - `sortByDurationAscending`, `sortByDurationDescending`

**Operations**
It is also possible to check an Date's or TimePeriod's relationship to the collection. For instance, if you would like to see all the time periods that intersect with a certain date, you can call the <cdoe>periodsIntersectedByDate:</code> method. The result is a new TimePeriodCollection with all those periods that intersect the provided date. There are a host of other methods to try out as well, including a full equality check between two collections.

![TimePeriodCollectionOperations](https://upload-images.jianshu.io/upload_images/2648731-c2df828f04f7a545.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### Time Period Chains 时间段链
Time period chains serve as a tightly coupled set of time periods. They are always organized by start and end date, and have their own characteristics like a StartDate and EndDate that are extrapolated from the time periods within. Time period chains do not allow overlaps within their set of time periods. This type of group is ideal for modeling schedules like sequential meetings or appointments.

![TimePeriodChains](https://upload-images.jianshu.io/upload_images/2648731-eb80735140a14004.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

To make a new chain, call the class method like so:
```objectivec
// Create chain
DTTimePeriodChain *chain = [DTTimePeriodChain chain];

// Create a few time periods
 DTTimePeriod *firstPeriod = [DTTimePeriod timePeriodWithStartDate:[dateFormatter dateFromString:@"2014 11 05 18:15:12.000"] endDate:[dateFormatter dateFromString:@"2015 11 05 18:15:12.000"]];
DTTimePeriod *secondPeriod = [DTTimePeriod timePeriodWithStartDate:[dateFormatter dateFromString:@"2015 11 05 18:15:12.000"] endDate:[dateFormatter dateFromString:@"2016 11 05 18:15:12.000"]];

// Add test periods
[chain addTimePeriod:firstPeriod];
[chain addTimePeriod:secondPeriod];

//Retreive chain items
DTTimePeriod *firstPeriod = chain[0];
```

Any time a date is added to the time chain, it retains its duration, but is modified to have its StartDate be the same as the latest period in the chain's EndDate. This helps keep the tightly coupled structure of the chain's time periods. Inserts (besides those at index 0) shift dates after insertion index by the duration of the new time period while leaving those at indexes before untouched. Insertions at index 0 shift the start date of the collection by the duration of the new time period. A full list of operations can be seen below.

**Operations**
Like collections, chains have an equality check and the ability to be shifted earlier and later. Here is a short list of other operations.

![TimePeriodChainOperations](https://upload-images.jianshu.io/upload_images/2648731-24a32f99ee76af44.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 参考

* [简书：DateTools 使用「日期工具库」](https://www.jianshu.com/p/10c5a9e60880)
