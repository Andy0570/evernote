> 以下内容来源于官方源码、 README 文档、测试 Demo或个人使用总结 ！

## YYModel

[**查看在线文档**](http://cocoadocs.org/docsets/YYModel/1.0.4/)

高性能 iOS/OSX 模型转换框架。
(该项目是 [YYKit](https://github.com/ibireme/YYKit) 组件之一)


## 性能

处理 GithubUser 数据 10000 次耗时统计 (iPhone 6):

![](http://upload-images.jianshu.io/upload_images/2648731-86bf06b3b6e4064b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

更多测试代码和用例见 `Benchmark/ModelBenchmark.xcodeproj`。


## 特性

- **高性能**: 模型转换性能接近手写解析代码。
- **自动类型转换**: 对象类型可以自动转换，详情见下方表格。
- **类型安全**: 转换过程中，所有的数据类型都会被检测一遍，以保证类型安全，避免崩溃问题。
- **无侵入性**: 模型无需继承自其他基类。
- **轻量**: 该框架只有 5 个文件 (包括.h文件)。
- **文档和单元测试**: 文档覆盖率100%, 代码覆盖率99.6%。

## 使用方法


### 简单的 Model 与 JSON 相互转换

```objectivec
// JSON:
{
    "uid":123456,
    "name":"Harry",
    "created":"1965-07-31T00:00:00+0000"
}

// Model:
@interface User : NSObject
@property UInt64 uid;
@property NSString *name;
@property NSDate *created;
@end
@implementation User
@end

// ❇️ 将 JSON (NSData,NSString,NSDictionary) 转换为 Model:
User *user = [User yy_modelWithJSON:json];

// ❇️ 将 Model 转换为 JSON 对象:
NSDictionary *json = [user yy_modelToJSONObject];
```

```objectivec
// 1️⃣ JSON → Model
+ (nullable instancetype)yy_modelWithJSON:(id)json;
//    NSDictionary → Model
+ (nullable instancetype)yy_modelWithDictionary:(NSDictionary *)dictionary;

// 2️⃣ 是否使用 JSON 对象设置接收者的属性。
- (BOOL)yy_modelSetWithJSON:(id)json;
//  是否使用 NSDictionary 对象设置接收者的属性。
- (BOOL)yy_modelSetWithDictionary:(NSDictionary *)dic;

// 3️⃣  Model → JSON 对象:
- (nullable id)yy_modelToJSONObject;
//     Model → JSONData:
- (nullable NSData *)yy_modelToJSONData;
//     Model → JSONString:
- (nullable NSString *)yy_modelToJSONString;
```

> ⚠️
>
> ~~实测 Bug：Model 中的  `@property`  属性不可以声明为 `readonly`。~~ （截止2017-06-05 ）
>
> 更新 （2017-10-25）
> 经过与源码作者沟通后了解到：
> 在 .h 文件中`@property` 声明为 `readonly`后，需要在 .m 文件中的 extension 中重新将属性声明为 `readwrite`。
> 这也是《Effective Objective-C 2.0》书中第18条（尽量使用不可变对象）所推崇的做法。



当 JSON/Dictionary 中的对象类型与 Model 属性不一致时，**YYModel** 将会进行如下自动转换。自动转换不支持的值将会被忽略，以避免各种潜在的崩溃问题。

|  JSON/Dictionary  |                  Model                   |
| :---------------: | :--------------------------------------: |
|     NSString      |         NSNumber,NSURL,SEL,Class         |
|     NSNumber      |                 NSString                 |
| NSString/NSNumber | 基础类型 (BOOL,int,float,NSUInteger,UInt64,…)  <br> NaN 和 Inf 会被忽略 |
|     NSString      | NSDate 以下列格式解析: <br> yyyy-MM-dd <br> yyyy-MM-dd HH:mm:ss<br>yyyy-MM-dd'T'HH:mm:ss<br>yyyy-MM-dd'T'HH:mm:ssZ<br>EEE MMM dd HH:mm:ss Z yyyy |
|      NSDate       | NSString 格式化为 ISO8601:<br>  "YYYY-MM-dd'T'HH:mm:ssZ" |
|      NSValue      |        struct (CGRect,CGSize,...)        |
|      NSNull       |                  nil,0                   |
| "no","false",...  |                 @(NO),0                  |
| "yes","true",...  |                 @(YES),1                 |




### Model 属性名和 JSON 中的 Key 不相同

```objectivec
// JSON:
{
    "n":"Harry Pottery",
    "p": 256,
    "ext" : {
        "desc" : "A book written by J.K.Rowing."
    },
    "ID" : 100010
}

// Model:
@interface Book : NSObject
@property NSString *name;
@property NSInteger page;
@property NSString *desc;
@property NSString *bookID;
@end
@implementation Book

// ❇️ 返回一个 Dict，将 Model 属性名对映射到 JSON 的 Key。
+ (NSDictionary *)modelCustomPropertyMapper {
    return @{@"name" : @"n",
             @"page" : @"p",
             @"desc" : @"ext.desc",
             @"bookID" : @[@"id",@"ID",@"book_id"]};
}
@end
```

你可以把一个或一组 json key (key path) 映射到一个或多个属性。如果一个属性没有映射关系，那默认会使用相同属性名作为映射。

* 在 json → model 的过程中：如果一个属性对应了多个 json key，那么转换过程会按顺序查找，并使用第一个不为空的值。


* 在 model → json 的过程中：如果一个属性对应了多个 json key (key path)，那么转换过程仅会处理第一个 json key (key path)；如果多个属性对应了同一个 json key，则转换过过程会使用其中任意一个不为空的值。

### Model 包含其他 Model

```objectivec
// JSON
{
    "author":{
        "name":"J.K.Rowling",
        "birthday":"1965-07-31T00:00:00+0000"
    },
    "name":"Harry Potter",
    "pages":256
}

// Model: 什么都不用做，转换会自动完成
@interface Author : NSObject
@property NSString *name;
@property NSDate *birthday;
@end
@implementation Author
@end

@interface Book : NSObject
@property NSString *name;
@property NSUInteger pages;
@property Author *author; //Book 包含 Author 属性
@end
@implementation Book
@end
```

	

### 容器类属性
> 含义：
模型类的属性是 Collection 类（NSArray、NSDictionary、NSSet等）,Collection 类中的项是其他模型


```objectivec
#import <Foundation/Foundation.h>
@class Shadow, Border, Attachment;

@interface Attributes
@property NSString *name;
@property NSArray *shadows; //Array<Shadow>
@property NSSet *borders;   //Set<Border>
@property NSMutableDictionary *attachments; //Dict<NSString,Attachment>
@end

@implementation Attributes
// ❇️ 返回容器类中的所需要存放的数据类型 (以 Class 或 Class Name 的形式)。
+ (NSDictionary *)modelContainerPropertyGenericClass {
    return @{@"shadows" : [Shadow class],
             @"borders" : Border.class,
             @"attachments" : @"Attachment" };
}
@end
```




### 黑名单与白名单

```objectivec
@interface User
@property NSString *name;
@property NSUInteger age;
@end

@implementation Attributes
// ❇️ 如果实现了该方法，则处理过程中会忽略该列表内的所有属性
+ (NSArray *)modelPropertyBlacklist {
    return @[@"test1", @"test2"];
}
// ❇️ 如果实现了该方法，则处理过程中不会处理该列表外的属性。
+ (NSArray *)modelPropertyWhitelist {
    return @[@"name"];
}
@end
```

### 数据校验与自定义转换

```objectivec
// JSON:
{
	"name":"Harry",
	"timestamp" : 1445534567
}

// Model:
@interface User
@property NSString *name;
@property NSDate *createdAt;
@end

@implementation User
// ❇️ 当 JSON 转为 Model 完成后，该方法会被调用。
// 你可以在这里对数据进行校验，如果校验不通过，可以返回 NO，则该 Model 会被忽略。
// 你也可以在这里做一些自动转换不能完成的工作。
- (BOOL)modelCustomTransformFromDictionary:(NSDictionary *)dic {
    NSNumber *timestamp = dic[@"timestamp"];
    if (![timestamp isKindOfClass:[NSNumber class]]) return NO;
    _createdAt = [NSDate dateWithTimeIntervalSince1970:timestamp.floatValue];
    return YES;
}

// ❇️ 当 Model 转为 JSON 完成后，该方法会被调用。
// 你可以在这里对数据进行校验，如果校验不通过，可以返回 NO，则该 Model 会被忽略。
// 你也可以在这里做一些自动转换不能完成的工作。
- (BOOL)modelCustomTransformToDictionary:(NSMutableDictionary *)dic {
    if (!_createdAt) return NO;
    dic[@"timestamp"] = @(n.timeIntervalSince1970);
    return YES;
}
@end
```

### Coding/Copying/hash/equal/description

```objectivec
@interface YYShadow :NSObject <NSCoding, NSCopying>
@property (nonatomic, copy) NSString *name;
@property (nonatomic, assign) CGSize size;
@end

@implementation YYShadow
// 直接添加以下代码即可自动完成
- (void)encodeWithCoder:(NSCoder *)aCoder { [self yy_modelEncodeWithCoder:aCoder]; }
- (id)initWithCoder:(NSCoder *)aDecoder { self = [super init]; return [self yy_modelInitWithCoder:aDecoder]; }
- (id)copyWithZone:(NSZone *)zone { return [self yy_modelCopy]; }
- (NSUInteger)hash { return [self yy_modelHash]; }
- (BOOL)isEqual:(id)object { return [self yy_modelIsEqual:object]; }
- (NSString *)description { return [self yy_modelDescription]; }
@end
```

### JSON Array → Model Array【将字典数组转成模型数组】
[MJExtension](https://github.com/CoderMJLee/MJExtension) 框架中有一个方法 `mj_objectArrayWithKeyValuesArray:` 可以将一个字典数组转换成模型数组，即 NSArray 中的项是模型指针，查看 [YYModel](https://github.com/ibireme/YYModel) 源码，发现也有相同的方法：`+ (NSArray *)modelArrayWithClass:(Class)cls json:(id)json;`

示例：
```objectivec
    // 读取property list数据
    NSString *path = [[NSBundle mainBundle] pathForResource:@"navigationButton"
                                                     ofType:@"plist"];
    NSArray *rootArray = [NSArray arrayWithContentsOfFile:path];

    // MJExtension:
    NSArray *squareModelArray =
        [Model mj_objectArrayWithKeyValuesArray:rootArray];
    // YYModel:
    NSArray *squareModelArray = [NSArray modelArrayWithClass:[Model class]
                                                        json:rootArray];
```

###  JSON Dictioanry → Model Dictionary【 JSON 字典转成 字典模型】 

```objectivec
/**
 从 JSON 对象中创建并返回 NSDictionary 模型.
 This method is thread-safe.
 
 @param cls  The value instance's class in dictionary.
 @param json  A json dictionary of `NSDictionary`, `NSString` or `NSData`.
              Example: {"user1":{"name","Mary"}, "user2": {name:"Joe"}}
 
 @return A dictionary, or nil if an error occurs.
 */
+ (nullable NSDictionary *)yy_modelDictionaryWithClass:(Class)cls json:(id)json;
```
