![](http://upload-images.jianshu.io/upload_images/2648731-6096cbfd905b1eb0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 第4章 协议与分类

### 第23条：通过委托与数据源协议进行对象间通信

#### 委托模式（Delegate pattern）

主旨：定义一套接口，某个对象若想接受另一个对象的委托，则需要遵从此接口，以便成为其“委托对象”（delegate）。而这“另一个对象”则可以给其委托对象回传一些信息，也可以在发生相关事件时通知委托对象。

##### EOCNetworkFetcherDelegate.h

```objective-c
#import <Foundation/Foundation.h>
@class EOCNetworkFetcher;

// 委托协议名通常是在相关类名后面加上 Delegate。
// 委托模式:对象把应对某个行为的责任委托给另一个类。
@protocol EOCNetworkFetcherDelegate <NSObject>
@optional
- (void)networkFetcher:(EOCNetworkFetcher *)fetcher
        didReceiveData:(NSData *)data;
- (void)networkFetcher:(EOCNetworkFetcher *)fetcher
      didFailWithError:(NSError *)error;
- (void)networkFetcher:(EOCNetworkFetcher *)fetcher
        didUpdateProgressTo:(float)progress;
- (BOOL)networkFetcher:(EOCNetworkFetcher *)fetcher
        shouldFollowRedirectToURL:(NSURL *)url;
@end
```

##### EOCNetworkFetcher 类

```objective-c
//  EOCNetworkFetcher.h
#import <Foundation/Foundation.h>
#import "EOCNetworkFetcherDelegate.h"

@interface EOCNetworkFetcher : NSObject
// 使用属性定义其委托对象
// 使用 weak 关键字，避免引用循环
@property (nonatomic, weak) id<EOCNetworkFetcherDelegate> delegate;
@end

#import "EOCNetworkFetcher.h"

//  EOCNetworkFetcher.m
@interface EOCNetworkFetcher () {
    // ❇️ 使用 bitfield 数据类型
    // 缓存委托对象是否能响应协议中的相关方法
    struct {
        unsigned int didReceiveData            : 1;
        unsigned int didFailWithError          : 1;
        unsigned int didUpdateProgressTo       : 1;
        unsigned int shouldFollowRedirectToURL : 1;
    } _delegateFlags;
}
@end
@implementation EOCNetworkFetcher

- (void)setDelegate:(id<EOCNetworkFetcherDelegate>)delegate {
    _delegate = delegate;
    // ① ❇️实现缓存功能
    _delegateFlags.didReceiveData = [delegate respondsToSelector:@selector(networkFetcher:didReceiveData:)];
    _delegateFlags.didFailWithError = [delegate respondsToSelector:@selector(networkFetcher:didFailWithError:)];
    _delegateFlags.didUpdateProgressTo = [delegate respondsToSelector:@selector(networkFetcher:didUpdateProgressTo:)];
    _delegateFlags.shouldFollowRedirectToURL = [delegate respondsToSelector:@selector(networkFetcher:shouldFollowRedirectToURL:)];
}

- (void)testMethod {
    // data obtained from network
    NSData *data;
    // ❇️在委托对象上调动可选方法，必须提前使用类型信息查询方法判断这个委托对象能否响应相关选择子
    if ([_delegate respondsToSelector:
            @selector(networkFetcher:didReceiveData:)]) {
        [_delegate networkFetcher:self didReceiveData:data];
    }
    
    float currentProgress = 0.0;
    // ② ❇️查询结构体标志
    // 不必每次使用类型信息查询方法，对需要调用很多次的方法时，值得进行这种优化
    if (_delegateFlags.didUpdateProgressTo) {
        [_delegate networkFetcher:self didUpdateProgressTo:currentProgress];
    }
}

@end
```

##### EOCDataModel

```objective-c
//  EOCDataModel.h
#import <Foundation/Foundation.h>

/**
 EOCDataModel 对象是 EOCNetworkFetcher 的委托对象
 */
@interface EOCDataModel : NSObject

@end

//  EOCDataModel.m
#import "EOCDataModel.h"
#import "EOCNetworkFetcherDelegate.h"
#import "EOCNetworkFetcher.h"

// 1.声明此类遵从委托协议
@interface EOCDataModel () <EOCNetworkFetcherDelegate>
@property (nonatomic, strong) EOCNetworkFetcher *myFetcherA;
@property (nonatomic, strong) EOCNetworkFetcher *myFetcherB;
@end

@implementation EOCDataModel

// 2.实现委托协议中的方法
- (void)networkFetcher:(EOCNetworkFetcher *)fetcher
        didReceiveData:(NSData *)data {
    // ❇️调用 delegate 中的方法时，总是应该把发起委托的实例也一并传入方法中,
    // 这样，delegate 对象在实现相关方法时，就能根据传入的实例分别执行不同的代码了。
    if (fetcher == _myFetcherA) {
        // handle data
    }else if (fetcher == _myFetcherB) {
        // handle data
    }
}

- (void)networkFetcher:(EOCNetworkFetcher *)fetcher
      didFailWithError:(NSError *)error {
    // handle error
}

@end
```

#### 要点

- 委托模式为对象提供了一套接口，使其可由此将相关事件告知其他对象。
- 将委托对象应该支持的接口定义成协议，在协议中把可能需要处理的事件定义成方法。
- 当某对象需要从另外一个对象中获取数据时，可使用委托模式。在这种情况下，该模式亦称**数据源协议**（data source protocal）。
- 若有必要，可实现含有位段的结构体，将委托对象是否能响应相关协议方法这一信息缓存至其中。




### 第24条：将类的实现代码分散到便于管理的数个分类之中

通过 Objective-C 的"分类"（Category）机制，把类代码按逻辑划入几个分区中，这对开发与调试都有好处。

##### 不使用 Category 分类：

```objective-c
#import <Foundation/Foundation.h>

@interface EOCPerson : NSObject
@property (nonatomic, copy, readonly) NSString *firstName;
@property (nonatomic, copy, readonly) NSString *lastName;
@property (nonatomic, strong, readonly) NSSet *friends;

- (instancetype)initWithFirstName:(NSString *)firstName
                      andLastName:(NSString *)lastName;

/** Friendship methods */
- (void)addFirend:(EOCPerson *)person;
- (void)removeFriend:(EOCPerson *)person;
- (BOOL)isFriendsWith:(EOCPerson *)person;

/* Word methods */
- (void)performDaysWork;
- (void)takeVacationFromWork;

/** Play methods */
- (void)goToTheCinema;
- (void)goToSportsGame;

@end
```

##### 使用 Category 分类：

```objective-c
//  EOCPerson.h
#import <Foundation/Foundation.h>

@interface EOCPerson : NSObject
@property (nonatomic, copy, readonly) NSString *firstName;
@property (nonatomic, copy, readonly) NSString *lastName;
@property (nonatomic, strong, readonly) NSSet *friends;

- (instancetype)initWithFirstName:(NSString *)firstName
                      andLastName:(NSString *)lastName;
@end

@interface EOCPerson (Friendship)
- (void)addFirend:(EOCPerson *)person;
- (void)removeFriend:(EOCPerson *)person;
- (BOOL)isFriendsWith:(EOCPerson *)person;
@end

@interface EOCPerson (Work)
- (void)performDaysWork;
- (void)takeVacationFromWork;
@end

@interface EOCPerson (Play)
- (void)goToTheCinema;
- (void)goToSportsGame;
@end
```

使用 **Category** 分类机制：

* 你可以把整个类都定义在一个接口文件中，并将其代码写在一个实现文件里。
* 随着分类数量的增加，可以把每个分类提取到各自的文件中去。
    * EOCPerson+Friendship(.h/.m)
    * EOCPerson+Work(.h/.m)
    * EOCPerson+Play(.h/.m)
* 优点：可以把类代码分成很多个**易于管理**的小块，以便单独检视。
* 优点：**便于调试**。对于某个分类中的所有方法，分类名称都会出现在其符号中。可以根据调试器回溯信息中的分类名称，精确定位到类中的方法所属的功能区。

#### 要点

- 使用分类机制把类的实现代码划分成易于管理的小块。
- 将应该视为私有的方法归入名叫 **Private** 的分类中，以隐藏实现细节。以编写“自我描述式代码”（self-documenting code）。




### 第25条：总是为第三方类的分类名称加前缀

#### 使用分类的问题：

1. 【将分类方法加入类中】这一操作是在【运行期系统加载分类时】完成的。运行期系统会把分类中所实现的每个方法都加入类的方法列表中。
2. 如果类中本来就有此方法，而分类又实现了一次，那么分类中的方法会覆盖原来那一份实现代码。

#### 解决方法：

1. 以命名空间来区别各个**分类的名称**与其中所定义的**方法**。
2. 给相关名称都加上某个共用的前缀。

```objective-c
//  NSString+ABC_HTTP.h
#import <Foundation/Foundation.h>

@interface NSString (ABC_HTTP)

// Encode a string with URL encoding
- (NSString *)abc_urlEncodedString;

// Decode a URL encoded string
- (NSString *)abc_urlDecodedString;

@end
```

#### 要点

* 向第三方类中添加分类时，总应给其**名称**加上你专用的前缀。
* 向第三方类中添加分类时，总应给其中的**方法名**加上你专用的前缀。




### 第26条：勿在分类中声明属性

* **Category** 分类无法把实现属性所需的实例变量合成出来，即无法自动实现存取方法。
* 关联对象（参考第10条）能够解决在分类中不能合成实例变量的问题，但是不推荐。
* 所有属性都应该定义在主接口中。
* **Category** 分类的作用是扩展类的功能，而非封装数据。
* 可以在 **Category** 分类中使用只读（readonly）属性：


```objective-c
//  NSCalendar+EOC_Additions.h
#import <Foundation/Foundation.h>

@interface NSCalendar (EOC_Additions)
@property (nonatomic, strong, readonly) NSArray *eoc_allMonths;

- ()
@end

//  NSCalendar+EOC_Additions.m
#import "NSCalendar+EOC_Additions.h"

@implementation NSCalendar (EOC_Additions)
// ❇️ readonly，不需要设置 set 方法。
// ❇️ get方法不会访问类数据，属性也不需要由实例变量来实现
- (NSArray *)eoc_allMonths {
    if ([self.calendarIdentifier
            isEqualToString:NSCalendarIdentifierGregorian]) {
        return @[@"January",@"Feburary",
                 @"March",@"April",
                 @"May",@"June",
                 @"July",@"August",
                 @"September",@"October",
                 @"November",@"December",];
    }else if (/** other calendar identifier */) {
        /** return months for other calendars */
    }
}
@end
```

上例中，直接声明一个方法或许更好：

```objective-c
#import <Foundation/Foundation.h>

@interface NSCalendar (EOC_Additions)
- (NSArray *)eoc_allMonths;
@end
```

#### 要点

* 把封装数据所用的全部属性都定义在主接口里。
* 在 "class-continuation" 分类之外的其他分类中，可以定义存取方法，但尽量不要定义属性。




### 第27条：使用  "class-continuation分类"  隐藏实现细节

#### Objective-C Class Extension

```objective-c
@interface <#class name#> ()

@end
```

* 可以将私有实例变量和私有方法声明在 "class-continuation分类" 中，实现对外隐藏。
* 将实例变量添加到  "class-continuation分类" 中与添加到 **@implementation** 实现块中是等效的。
* 编写 Objective-C++ 代码时使用 "class-continuation分类" 也尤为有用。
* "class-continuation分类" 还可以将 public 接口中声明为 `readonly` 的属性扩展为 `readwrite`。【参见：第18条：尽量使用不可变对象】
* 若对象所遵从的协议（delegate）只应视为私有，则可以在 "class-continuation分类" 中声明。


#### 要点

* 通过 "class-continuation分类" 向类中新增实例变量。
* 如果某属性在主接口中声明为"只读"，而类的内部又要用设置方法修改此属性，那么就在 "class-continuation分类" 中将其扩展为"可读写"。
* 把私有方法的原型声明在 "class-continuation分类" 里面。
* 若想使类所遵循的协议不为人所知，则可于 "class-continuation分类" 中声明。




### 第28条：通过协议提供匿名对象

* 将返回的对象设计为遵从协议的纯 `id` 类型。

* "匿名对象"（anonymous object）：

  ```objective-c
  @property (nonatomic, weak) id<EOCNetworkFetcherDelegate> delegate;
  ```

* 有时候对象类型并不重要，重要的是对象有没有实现某些方法。


#### 示例代码

以下示例是参考自 [objc中国期刊：整洁的 Table View 代码](https://www.objccn.io/issue-1-2/)

> ### 让 Cells 可复用
>
> 有时多种 model 对象需要用同一类型的 cell 来表示，这种情况下，我们可以进一步让 cell 可以复用。首先，我们给 cell 定义一个 protocol，需要用这个 cell 显示的对象必须遵循这个 protocol。然后简单修改 category 中的设置方法，让它可以接受遵循这个 protocol 的任何对象。这些简单的步骤让 cell 和任何特殊的 model 对象之间得以解耦，让它可适应不同的数据类型。

```objective-c
// ****************************************************
//  UITableViewCell+ConfigureModel.h
#import <UIKit/UIKit.h>

@protocol HQLTableViewCellConfigureDelegate <NSObject>
@required
- (NSString *)imageName;
- (NSString *)titleLabelText;
@end

@protocol HQLTableViewCellKeyValueConfigureDelegate <NSObject>
@required
- (NSString *)titleLabelText;
- (NSString *)detailLabelText;
@end

@interface UITableViewCell (ConfigureModel)

/**
 配置查询功能 Cell

 @param model 模型：图片 + 标题 + 指示箭头>
 */
- (void)hql_configureForModel:(id<HQLTableViewCellConfigureDelegate>)model;


/**
 配置数据显示 Cell

 @param model 模型：titleLabel + detailLabel
 */
- (void)hql_configureForKeyValueModel:(id<HQLTableViewCellKeyValueConfigureDelegate>)model;

@end
  
// ****************************************************
//  UITableViewCell+ConfigureModel.m
#import "UITableViewCell+ConfigureModel.h"

@implementation UITableViewCell (ConfigureModel)

- (void)hql_configureForModel:(id<HQLTableViewCellConfigureDelegate>)model {
    self.imageView.image = [UIImage imageNamed:model.imageName];
    self.textLabel.text  = model.titleLabelText;
    self.accessoryType   = UITableViewCellAccessoryDisclosureIndicator;
}

- (void)hql_configureForKeyValueModel:(id<HQLTableViewCellKeyValueConfigureDelegate>)model {
    self.textLabel.text       = model.titleLabelText;
    self.detailTextLabel.text = model.detailLabelText;
}

@end
```

#### 要点

- 协议可在某种程度上提供匿名类型。具体的对象类型可以淡化成遵从某协议的 id 类型，协议里规定了对象所应实现的方法。
- 使用匿名对象来隐藏类型名称(或类名)。
- 如果具体类型不重要，重要的是对象能够响应（定义在协议里的）特定方法，那么可使用匿名对象来表示。
