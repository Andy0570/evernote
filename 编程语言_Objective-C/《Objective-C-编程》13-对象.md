# 框架
* **框架（famework）**是由许多相关的类、函数、常量以及数据类型组成的库。
* 编写 **Objective-C** 程序时，要使用 **Foundation 框架**，该框架包含了很多基础类。

# 对象
![](http://upload-images.jianshu.io/upload_images/2648731-a988a713b3fbda6a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 类
* **类（class）** 负责描述某个特定类型的 **对象（object）**；
* **类**可以描述抽象概念的对象，也可以描述实际存在的事物；
* 一个类定义了一种对象，同时它也可以用来创建这种对象。类既是对象的原型，又是生产对象的工厂。

# #import 与 #include

```objectivec
#import <Foundation/Foundation.h>
```

* `#import` 指令导入更快更有效率;
* `#include`指令告诉编译器做呆板的复制粘贴，将包含的内容粘贴到指定的目标文件夹中；
*  `#import`指令会让编译器先检查之前是否已经导入过这个文件，或是已经被包含到目标文件夹中了。

## 关于 @import

Objective-C 类的数量非常庞大，一般来说需要用到系统框架的时候，都会使用 `#import ` 命令来引入，`#import ` 命令会自动引入框架的全部头文件，这样，不再需要单独引入某个类的头文件。

```objectivec
#import <Foundation/Foundation.h>
```
当项目中使用的框架越来越多时，编译器也会花费越来越多的时间处理大量重复的头文件。
为了解决这一问题以提高效率，Xcode 为所有项目都添加了一个预编译头文件（precompiled header file，PCH），第一次编译项目时，预编译头文件中列出的文件会被编译并缓存，编译器会重复使用缓存结果快速编译项目中的其他文件。

```objectivec
#ifdef __OBJC__

#import <Foundation/Foundation.h>
#import <UIKit/UIKit.h>
#import <CoreGraphics/CoreGraphics.h>

#endif  /* __OBJC__ */
```
但是**维护 pch 文件低效耗时**，因此继续优化编译器并引入了 `@import` 指令：
```objectivec
@import Foundation;
```
这行代码告诉编译器需要使用 Foundation框架，之后编译器会优化预编译头文件和缓存编译结果的过程。同时，文件中不用再明确引用框架编译器会根据`＠import`自动导入相应的框架。

Tips: 在项目的 Build Settings 配置中，将 **Enable Modules (C and Objective-C)** 设置为 **YES**，编译器会自动将  `#import` 指令转换为 Modules 的形式。

# 创建并使用对象

```objectivec
NSDate *now = [NSDate date];
```

* `NSDate *now` 声明了一个数据类型为 `NSDate *` 的指针变量 now ，这个指针保存了 `NSDate` 实例在内存中的地址；
* `[NSDate date]` 是一条**消息发送语句**，向 `NSDate` 类发送了 `date` 消息，让它执行 `date` 方法。`date` 方法执行后，`NSDate` 类会在堆（heap）上给 `NSDate` 实例声明一部分内存（去堆上申请一块连续的内存，称之为缓冲区（buffer）），将 `NSDate` 实例初始化为当前的日期／时间，然后返回新对象的地址。
* **消息发送语句的格式**：`[接收方 选择器]`；

```objectivec
NSDate *now = [NSDate date];
double seconds = [now timeIntervalSince1970];
```

* 发送 `timeIntervalSince1970`  消息给 now 变量指向的 `NSDate` 实例，返回一个双精度数（double）保存在了变量名为 seconds 的变量中。
* `date` 是一个 **类方法**，通常来说，类方法会创建类的实例，并初始化实例变量；
* `timeIntervalSince1970` 是一个 **实例方法**，通常来说，实例方法会提供实例中实例变量的信息，或是对实例的实例变量进行操作。

# Objective-C语言命名习惯

* **Objective-C** 语言是区分大小写的；
* 实例变量和方法都采用“**骆驼拼写法**”命名；
* 类的名称要加前缀，以大写字母开头；
