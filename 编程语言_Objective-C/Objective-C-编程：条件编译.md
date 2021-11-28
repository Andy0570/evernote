预处理程序是 Objective-C 编译过程的一部分，它可以识别散布在程序中的特定语句。顾名思义，预处理程序实际上是在分析 Objective-C 程序之前处理这些语句。

![](http://upload-images.jianshu.io/upload_images/2648731-eb39d7195f75f6b6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)

条件编译的作用：

* 创建可以在不同计算机系统上编译运行的程序；
* 开关程序中的各种语句。

## ifdef、#endif、#else 和 #ifndef 语句

如果程序编译时，`#ifdef` 行中所指定的符号已经通过 `#define` 语句或命令行定义了，那么编译器将处理从此处开始到 `#else`、`#elif` 或 `#endif` 的程序段，否则就忽略这个程序段。

`#ifndef` 语句的含义与 `#ifdef` 相反，如果指定的符号没有定义，它将导致程序处理后续行。

### 判断 iOS 系统版本

```objectivec
// 如果是 iOS 11 系统，使用 barnHD.png 图片。否则，使用 barn.png 图片。
#ifdef __iPhone_11_0 
  #define KImageFile @"barnHD.png"
#else
  #define KImageFile @"barn.png"
#endif
```

### 判断 iOS 系统版本

```objectivec
// 判断当前系统版本是否大于 iOS 3.0
#ifndef __iPhone_3_0
#warning "This project uses features only available in iOS SDK 3.0 and later."
#endif
```

### 判断编程语言

```objectivec
// 保证在#ifdef中的宏定义只会在OC的代码中被引用，
// 否则，一旦引入C/C++的代码或者框架，就会出错！
#ifdef __Objective-C__
    #import <UIKit/UIKit.h>
    #import <Foundation/Foundation.h>
    #import <QuartzCore/QuartzCore.h>

    //define this constant if you want to use Masonry without the 'mas_' prefix
    #define MAS_SHORTHAND

    //define this constant if you want to enable auto-boxing for default syntax
    #define MAS_SHORTHAND_GLOBALS

    #import "Masonry.h"
#endif
```

### 判断开发模式： debug 模式/release 模式

```objective-c
#ifdef __Objective-C__

// 开发模式下打印日志，否则不打印
#ifdef DEBUG
  #define NSLog(fmt, ...) NSLog((@"%s [Line %d] " fmt), __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__)
#else
  #define NSLog(...)
#endif

#endif
```

参数含义：

1. `__VA_ARGS__` 是一个可变参数的宏，很少人知道这个宏，这个可变参数的宏是新的C99规范中新增的，目前似乎只有gcc支持（VC6.0的编译器不支持）。宏前面加上##的作用在于，当可变参数的个数为0时，这里的##起到把前面多余的","去掉的作用,否则会编译出错, 你可以试试。
2. `__FILE__` 宏在预编译时会替换成当前的源文件名。
3. `__LINE__` 宏在预编译时会替换成当前的行号。
4. `__FUNCTION__` 宏在预编译时会替换成当前的函数名称。


## #if 、 #elif 和 #endif 预处理程序语句

`#if` 预处理程序语句为控制条件编译提供了更通用的方法。如果满足`#if`后面的条件，就编译`#if`和`#endif`之间的程序段，否则不编译。

```objectivec
#if defined (DEBUG)
  //...
#endif
```

效果同下：

```objectivec
#ifdef DEBUG
  //...
#endif
```

### 判断应用运行环境
```objectivec
#if !TARGET_IPHONE_SIMULATOR
    // 如果当前环境不是模拟器，则执行这里的代码或者方法。
    // 一般用于需要打开手机硬件的场景，如相机、GPS、陀螺仪等。
#endif
```


## #undef 语句

使已经定义的名称成为未定义的，来消除特点名称的定义

```objectivec
#undef name // 取消定义
```

## NS_DEPRECATED_IOS 与 NS_AVAILABLE_IOS

```objectivec
// NS_DEPRECATED_IOS(2_0, 10_0）表示该函数只能在 iOS 2.0 和 iOS 10.0 之间使用，是已被废弃的函数，
// 但并不是说在 iOS 10.0 之后不能使用该函数了，是可以使用，但也需要考虑找其他替代方法了。
- (BOOL)openURL:(NSURL*)url NS_DEPRECATED_IOS(2_0, 10_0, "Please use openURL:options:completionHandler: instead") NS_EXTENSION_UNAVAILABLE_IOS("");

// NS_AVAILABLE_IOS(10_0)：表示自 iOS 10.0 开始支持该方法，若在 iOS 10.0 之前的版本使用该函数，则会导致 Crash。
- (void)openURL:(NSURL*)url options:(NSDictionary<NSString *, id> *)options completionHandler:(void (^ __nullable)(BOOL success))completion NS_AVAILABLE_IOS(10_0) NS_EXTENSION_UNAVAILABLE_IOS("");
```

解决某些方法失效导致的版本兼容性问题：

示例代码一：
```objectivec
// @available() 语法
if (@available(iOS 10.0, *)) {
    [[UIApplication sharedApplication] openURL:url options:@{UIApplicationOpenURLOptionUniversalLinksOnly: @YES} completionHandler:nil];
} else {
    [[UIApplication sharedApplication] openURL:url];
}
```

示例代码二：
```objectivec
// 根据 iOS 系统版本适配 UI 显示
if (@available(iOS 11.0, *)) {
    make.top.equalTo(self.view.mas_safeAreaLayoutGuideTop);
}else {
    make.top.equalTo(self.view.mas_top).with.offset(64);
}
```
