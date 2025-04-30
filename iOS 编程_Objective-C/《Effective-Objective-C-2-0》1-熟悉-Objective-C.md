![](http://upload-images.jianshu.io/upload_images/2648731-0e0fd7bd5b6205d9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 第一章 熟悉 Objective-C

### 第1条：了解 Objective-C 语言的起源

* Objective-C 语言在 C 语言基础上添加了面向对象特性。
* Objective-C 语言由 [Smalltalk](https://zh.wikipedia.org/wiki/Smalltalk) 演化而来，后者是消息型语言的鼻祖。
* Objective-C 与 C++、Java 等面向对象语言类似，但是在语法上使用**消息结构** （messaging structure），而非**函数调用** （function calling）。


  ```objective-c
// Messaging structure (Objective-C)
Object *obj = [Object new];
[obj performWith:parameter1 and:parameter2];

// Function calling (C++)
Object *obj = new Object;
obj ->perform(parameter1, parameter2)
  ```

* 区别：使用**消息结构**的语言，其运行时所应执行的代码由**运行环境**来决定；而使用**函数调用**的语言，则由**编译器**决定。
* Objective-C 的重要工作都由**运行期组件**（runtime component）而非编译器来完成。**运行期组件**本质上就是一种与开发者所编代码相链接的**动态库**（dynamic library）。
* Objective-C 语言中的指针是用来指示对象的。而对象所占内存总是分配在**堆空间**（heap space）。
* 分配在堆中的内存必须直接管理，而分配在栈上用于保存变量的内存则会在其栈帧弹出时自动清理。
* Objective-C 使用**引用计数**来管理堆内存。
* 定义中不含 `*` 的变量使用的是栈空间（stack space）。


#### 要点

* Objective-C 为 C 语言添加了面向对象特性，是其超集。Objective-C 使用动态绑定的消息结构，也就是说，在运行时才会检查对象类型。接收一条消息之后，究竟应执行何种代码，由运行期环境而非编译器来决定。
* 理解 C 语言的核心概念有助于写好 Objective-C 程序。尤其要掌握内存模型与指针。




### 第2条：在类的头文件中尽量少引入其他头文件

**前向声明**：在编译一个使用了 EOCPerson 类的文件时，不需要知道 EOCEmployer 类的全部细节，只需要知道有一个类名叫 EOCEmployer 就好。

**后向引用**：EOCPerson 类的实现文件则需要引入 EOCEmployer 类的头文件，因为若要使用后者，则必须知道其所有接口细节。

将引入头文件的时机尽量延后，只在确有需要时才引入，这样就可以**减少类的使用者所需引入的头文件数量**。

![](http://upload-images.jianshu.io/upload_images/2648731-9874da319d3b0fd9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```objective-c
//  EOCPerson.h
#import <Foundation/Foundation.h>

@class EOCEmployer;

@interface EOCPerson : NSObject
@property (nonatomic, copy) NSString *firstName;
@property (nonatomic, copy) NSString *lastName;
@property (nonatomic, strong) EOCEmployer *employer;
@end

//  EOCPerson.m
#import "EOCPerson.h"
#import "EOCEmployer.h"

@implementation EOCPerson

@end
```

必须在头文件中引入其他头文件：

* 子类继承父类，则必须引入定义父类的头文件。
* 声明你写的类遵从某个协议（protocol）,那么该协议必须有完整定义。

#### 要点

* 除非确有必要，否则不要引入头文件。一般来说，应在某个类的头文件中使用向前声明来提及别的类，并在实现文件中引入那些类的头文件。这样做可以尽量降低类之间的耦合（coupling）。
* 有时无法使用向前声明，比如要声明某个类遵循一项协议。这种情况下，尽量把 “该类遵循某协议” 的这条声明移至 “class-continuation 分类”中。如果不行的话，就把协议单独放在一个头文件中，然后将其引入。




### 第3条：多用字面量语法，少用与之等价的方法

使用字面量语法（literal syntax）可以缩减源代码长度，使其更为易读。

#### NSString
```objective-c
// 字符串字面量
NSString *string = @"Effective Objective-C 2.0";
```

#### NSNumber

```objective-c
// 使用字面量语法
NSNumber *intNumber    = @1;
NSNumber *floatNumber  = @2.5f;
NSNumber *doubleNumber = @3.14159;
NSNumber *boolNumber   = @YES;
NSNumber *charNumber   = @'a';

int x = 5;
float y = 6.32f;
NSNumber *expressionNumber = @(x * y);
```

#### NSArray

```objective-c
// 标准语法
NSArray *animals = [NSArray arrayWithObjects:@"cat",
                                             @"dog",
                                             @"mouse",
                                             @"badger",nil];
NSString *dog = [animals objectAtIndex:1];

// 使用字面量语法
NSArray *animals = @[@"cat",@"dog",@"mouse",@"badger"];
NSString *dog = animals[1];
```

#### NSDictionary

```objective-c
// 标准语法
// 顺序：<对象>,<键>,<对象>,<键>,nil
NSDictionary *personData = [NSDictionary dictionaryWithObjectsAndKeys:
                                    @"Matt",@"firstName",
                                    @"Galloway",@"lastName",
                                    [NSNumber numberWithInteger:28],@"age",
                                    nil];
NSString *lastName = [personData objectForKey:@"lastName"];

// 字面量语法
// 顺序：,<键>,<对象>,<键>,<对象>,
NSDictionary *personData = @{@"firstName":@"Matt",
                             @"lastName" :@"Galloway",
                             @"age"      :@"@28"};
NSString *lastName = personData[@"lastName"];
```

#### NSMutableArray & NSMutableDictionary

```objective-c
// 修改可变数组与字典内容
[mutableArray replaceObjectAtIndex:1 withObject:@"dog"];
[mutableDictionary setObject:@"Galloway" forKey:@"lastName"];

//下标操作
mutableArray[1] = @"dog";
mutableDictionary[@"lastName"] = @"Galloway";
```

#### 💡NSURL
你知道 **NUSRL** 也有字面量语法吗？`@@` 是创建 NSURL 的字面量的绝佳方法:
```
// 标准语法
NSURL *url = [NSURL URLWithString:@"http://example.com"];

// 字面量语法
NSURL *url = @@"http://example.com";
```

#### 要点

* 应该使用字面量语法来创建字符串、数值、数组、字典。与创建此类对象的常规方法相比，这么做更加简明扼要。
* 应该通过取下标操作来访问数组下标或字典中的键所对应的元素。
* 用字面量语法创建数组或字典时，若值中有 `nil`，则会抛出异常。因此，务必确保值里不含 `nil`。




### 第4条：多用类型常量，少用 `#define` 预处理指令

* `#define` 预处理指令只是简单的替换，定义出来的常量**没有类型信息**。



推荐方法：

```objective-c
// In the implementation file
static const NSTimeInterval KAnimationDuration = 0.3;
```

常用的命名法：若常量局限于**实现文件**之内，则在前面加字母 `K`；若常量在类之外可见，则通常以类名为前缀。

**const**：如果修改由 `const` 修饰符所声明的变量，编译器就会报错。

**static**：该变量仅在定义此变量的编译单元中可见。



对外公开某个常量：

示例一：

```objective-c
// In the header file
extern NSString *const EOCStringConst;

// In the implementation file
NSString *const EOCStringConst = @"VALUE";
```

示例二：

```objective-c
// EOCAnimatedView.h
extern const NSTimeInterval EOCAnimatedViewAnimationDruation;

// EOCAnimatedView.m
const NSTimeInterval EOCAnimatedViewAnimationDruation = 0.3;
```
> [!TIP]
>
> 推荐使用 `UIKIT_EXTERN` 关键字代替 `extern`。




#### 要点

* 不要用预处理指令定义常量。这样定义出来的常量不含类型信息，编译器只是会在编译前据此执行查找与替换操作。即使有人重新定义了常量值，编译器也不会产生警告信息，这将导致应用程序中的常量值不一致。
* 在实现文件中使用 `static const` 来定义只在编译单元内可见的常量。由于此类常量不在全局符号表中，所以无需为其名称加前缀。
* 在头文件中使用 `extern` 来声明全局常量，并在相关实现文件中定义其值。这种常量要出现在全局符号表中，所以其名称要加以区隔，通常用与之相关的类名做前缀。



### 第5条：用枚举表示状态、选项、状态码

在用一系列常量来表示错误状态码或可组合的选项时，极宜使用枚举为其命名。

用 **Foundation** 框架中的辅助宏定义枚举类型：

方法一，普通枚举类型：

`nsenum - Enumerated Type Declaration(NS_ENUM)`

```objective-c
typedef NS_ENUM(NSUInteger, EOCConnectionState) {
    EOCConnectionStateDisconnected,
    EOCConnectionStateConnecting,
    EOCConnectionStateConnected,
};
```



方法二，需要用 **按位或操作** 来组合的枚举定义：

`nsoptions - Enumerated Type Declaration(NS_OPTIONS)`

```objective-c
typedef NS_OPTIONS(NSUInteger, EOCPermittedDirection) {
    EOCPermittedDirectionUP    = 1 << 0,
    EOCPermittedDirectionDown  = 1 << 1,
    EOCPermittedDirectionLeft  = 1 << 2,
    EOCPermittedDirectionRight = 1 << 3,
};
```



方法二用在定义可以组合的选项：

示例：

```objective-c
typedef enum UIViewAutoresizing : NSUInteger {
    UIViewAutoresizingNone                 = 0,
    UIViewAutoresizingFlexibleLeftMargin   = 1 << 0,
    UIViewAutoresizingFlexibleWidth        = 1 << 1,
    UIViewAutoresizingFlexibleRightMargin  = 1 << 2,
    UIViewAutoresizingFlexibleTopMargin    = 1 << 3,
    UIViewAutoresizingFlexibleHeight       = 1 << 4,
    UIViewAutoresizingFlexibleBottomMargin = 1 << 5
} UIViewAutoresizing;

// 通过「按位或操作符」组合多个选项
UIViewAutoresizing resizing = UIViewAutoresizingFlexibleWidth | UIViewAutoresizingFlexibleHeight;
// 通过「按位与操作符」判断是否已启用某个选项
if (resizing & UIViewAutoresizingFlexibleWidth) {
    // UIViewAutoresizingFlexibleWidth is set
}
```

![](http://upload-images.jianshu.io/upload_images/2648731-134f62cad5c0ad8e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 要点

* 应该用枚举来表示状态机的状态、传递给方法的选项以及状态码等值，给这些值起个易懂的名字。
* 如果把传递给某个方法的选项表示为枚举型，而多个选项又可同时使用，那么就将各选项值定义为2的幂，以便通过按位或者操作将其组合起来。
* 用 `NS_ENUM` 与 `NS_OPTIONS` 宏来定义枚举类型，并指明其底层数据类型。这样做可以确保枚举是用开发者所选的底层数据类型实现出来的，而不会采用编译器所选的类型。
* 在处理枚举类型的 `switch` 语句中不要实现 default 分支。这样的话，加入新枚举之后，编译器就会提示开发者：`switch` 语句并未处理所有的枚举。
