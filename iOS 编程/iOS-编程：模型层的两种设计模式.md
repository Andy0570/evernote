![图片来自Pixabay](http://upload-images.jianshu.io/upload_images/2648731-a91ab457809031d4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

模型层（Model）的两种设计方案：

* 使用自定义的初始化器；
* 使用生成器模式；



### 一、自定义初始化器

这是设计模型层的常用方式。

```objective-c
// ---------------------------------------------------
//  HQLUser.h
// ---------------------------------------------------
#import <Foundation/Foundation.h>

@interface HQLUser : NSObject

// 属性的设置：向外层暴露的属性尽量设置为不可变(immutable)、只读（readonly） 
@property (nonatomic, copy, readonly) NSString *userID;
@property (nonatomic, copy, readonly) NSString *firstName;
@property (nonatomic, copy, readonly) NSString *lastName;
@property (nonatomic, copy, readonly) NSString *gender;
@property (nonatomic, copy, readonly) NSDate *dateOfBirth;
@property (nonatomic, copy, readonly) NSArray *albums;

// 指定初始化方法
- (instancetype)initWithUserID:(NSString *)userID
                     firstName:(NSString *)firstName
                      lastName:(NSString *)lastName
                        gender:(NSString *)gender
                   dateOfBirth:(NSDate *)dateOfBirth
                        albums:(NSArray *)albums;

@end

// ---------------------------------------------------
//  HQLUser.m
// ---------------------------------------------------
#import "HQLUser.h"

@interface HQLUser ()

// 在内部将属性重新封装为 readwrite，这样属性在内部就是可读可写的。
@property (nonatomic, copy, readwrite) NSString *userID;
@property (nonatomic, copy, readwrite) NSString *firstName;
@property (nonatomic, copy, readwrite) NSString *lastName;
@property (nonatomic, copy, readwrite) NSString *gender;
@property (nonatomic, copy, readwrite) NSDate *dateOfBirth;
@property (nonatomic, copy, readwrite) NSArray *albums;

@end

@implementation HQLUser

- (instancetype)initWithUserID:(NSString *)userID
                     firstName:(NSString *)firstName
                      lastName:(NSString *)lastName
                        gender:(NSString *)gender
                   dateOfBirth:(NSDate *)dateOfBirth
                        albums:(NSArray *)albums {
    if (self = [super init]) {
        _userID      = [userID copy];
        _firstName   = [firstName copy];
        _lastName    = [lastName copy];
        _gender      = [gender copy];
        _dateOfBirth = dateOfBirth;
        _albums      = [albums copy];
    }
    return self;
}

- (instancetype)init {
    @throw [NSException exceptionWithName:@"Method Undefined"
                                   reason:@"Use Designated Initializer Method"
                                 userInfo:nil];
    return nil;
}

@end
```

调用如下：

```objective-c
// 使用自定义的初始化器    
HQLUser *user = [[HQLUser alloc] initWithUserID:@"1214138" firstName:@"Hello" lastName:@"Kitty" gender:@"man" dateOfBirth:[NSDate date] albums:[NSArray array]];
```

> 💡
> 指定初始化方法和非指定初始化方法可以用宏来进行标记：
> * **NS_DESIGNATED_INITIALIZER**
> * **NS_UNAVAILABLE**


这种设计模式的缺点：

1. 如果类包含的属性很多，指定初始化方法的方法名会很长、非指定初始化方法的个数将会很多；
2. 向下兼容问题：如果新增一个属性，就需要重构指定初始化方法，因此，新版模型无法实现向下兼容；



### 二、生成器模式

 需要引入外部类进行管理。生成器有setter方法，也需要提供与模型数据完全一致的存储。生成器最终也会使用初始化器。

```objective-c
// ---------------------------------------------------
//  HPUser.h
// ---------------------------------------------------

#import <Foundation/Foundation.h>

@class HPUserBuilder;
@class HPUser;

typedef void(^HPUserBuilderBlock)(HPUserBuilder *builder);

@interface HPUserBuilder : NSObject

@property (nonatomic, copy) NSString *username;
@property (nonatomic, copy) NSString *gender;
@property (nonatomic, copy) NSDate *dateOfBirth;

- (HPUser *)build;
- (HPUserBuilder *)username:(NSString *)username;
- (HPUserBuilder *)gender:(NSString *)gender;
- (HPUserBuilder *)dateOfBirth:(NSDate *)dateOfBirth;

@end

@interface HPUser : NSObject

@property (nonatomic, copy) NSString *username;
@property (nonatomic, copy) NSString *gender;
@property (nonatomic, copy) NSDate *dateOfBirth;

- (instancetype)initWithBuilder:(HPUserBuilder *)builder;
- (instancetype)initWithBlock:(HPUserBuilderBlock)block;

@end

// ---------------------------------------------------
//  HPUser.m
// ---------------------------------------------------

#import "HPUser.h"

@implementation HPUserBuilder

- (HPUser *)build {
    // 构造器的初始化方法，调用的还是模型层的指定初始化方法
    return [[HPUser alloc] initWithBuilder:self];
}

- (HPUserBuilder *)username:(NSString *)username {
    _username = [username copy];
    return self;
}

- (HPUserBuilder *)gender:(NSString *)gender {
    _gender = [gender copy];
    return self;
}

- (HPUserBuilder *)dateOfBirth:(NSDate *)dateOfBirth {
    _dateOfBirth = dateOfBirth;
    return self;
}

@end

@implementation HPUser

// 指定初始化方法
- (instancetype)initWithBuilder:(HPUserBuilder *)builder {
    if (self = [super init]) {
        _username = builder.username;
        _gender = builder.gender;
        _dateOfBirth = builder.dateOfBirth;
    }
    return self;
}

- (instancetype)initWithBlock:(HPUserBuilderBlock)block {
    HPUserBuilder *builder = [[HPUserBuilder alloc] init];
    block(builder);
    // 返回的是构造器的初始化方法
    return [builder build];
}

@end
```

生产器模式解决了自定义初始化方法的两个缺点，它的方法名不会很长、也解决了向下的兼容性问题，调用如下：

```objective-c
// 1
HPUserBuilder *builder = [[[[[HPUserBuilder alloc] init] username:@"username"] gender:@"man"] dateOfBirth:[NSDate date]];
HPUser *usr = [[HPUser alloc] initWithBuilder:builder];

// 2
HPUser *user = [[HPUser alloc] initWithBlock:^(HPUserBuilder *builder) {
    builder.username    = @"username";
    builder.gender      = @"man";
    builder.dateOfBirth = [NSDate date];
}];
```

**生成器模式** 其实就是在普通的 Model 层之上又封装了一个中间层，这个中间类的属性和 Model 层是一样的。**本质上到最后还是要去调用 Model 层的指定初始化方法**。

使用指南：

1. 当你的 Model 层中的属性很多、或者后期很有可能新增其他属性，那么使用生成器模式可以解决向后的兼容性问题（Model 层新增属性后，使用了该 Model 类的其他地方不需要因为 Model 类指定初始化方法的改变而跟着修改）。
2. 当你的 Model 层中的属性不是很多，后期也不大可能新增属性时，不太适合使用生成器模式，这种模式几乎要写原本 2 倍的代码量，有种为了封装而封装的感觉，会延长我们的开发周期，降低开发效率，提高开发成本。




### 总结

生成器模式可以引用一句经典的话来总结：

>  All problems in computer science can be solved by another level of indirection.——Butler Lampson
>
>  “计算机科学领域的任何问题都可以通过增加一个间接的中间层来解决”



### 参考

*  Gaurav Vaish. 高性能iOS应用开发 [M]. 北京：人民邮电出版社，2017. 102-105
*  [Improving Immutable Object Initialization in Objective-C](http://holko.pl/2015/05/12/immutable-object-initialization/)
*  [iOS 创建对象的姿势 @MrPeak](http://mrpeak.cn/blog/ios-init/)
