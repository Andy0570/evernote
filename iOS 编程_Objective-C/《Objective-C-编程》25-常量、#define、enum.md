# 常量

* 程序中的某些信息的值是永远不会变的，这类不变的值称为**常量（constant）**。
* 在 Objective-C 中，可以通过两种途径来定义 **常量**：
    1. 全局变量；
    2. `#define` 预处理程序；
* 常量应该以驼峰法命名，并以相关类名作为前缀。

💡💡💡最佳实践：若常量仅局限于**实现文件(.m)**之内，则在前面加字母 K。若常量在类之外可见，则通常以类名为前缀。

  ```objectivec
  static const NSTimeInterval ZOCSignInViewControllerFadeOutAnimationDuration = 0.4;
  static NSString * const ZOCCacheControllerDidClearCacheNotification = @"ZOCCacheControllerDidClearCacheNotification";
  static const CGFloat ZOCImageThumbnailHeight = 50.0f;
  ```

* 常量应该在头文件中以这样的形式暴露给外部：
```objectivec
extern NSString *const ZOCCacheControllerDidClearCacheNotification;

// 示例
// .h
UIKIT_EXTERN NSString *const HQFormRowDescriptorTypeProvinceAndCity;
UIKIT_EXTERN const CGFloat HQLTableViewCellHeight;
// .m
NSString *const HQFormRowDescriptorTypeProvinceAndCity = @"HQProvinceAndCityCell";
const CGFloat HQLTableViewCellHeight = 115.0f;
```
并在实现文件中为它赋值。
只有公有的常量才需要添加命名空间作为前缀。尽管实现文件中私有常量的命名可以遵循另外一种模式，你仍旧可以遵循这个规则。


# 预处理指令

C、C++ 或 Objective-C 代码文件要经过两步才能完成编译。首先，**预处理器**（preprocessor）会读入并处理整个文件。接着，预处理器的输出结果会作为输入交给真正的**编译器**。**预处理器**指令都以 **#** 开头。其中最常用的三个为 **#include**、**#import** 和 **#define**。

## #include 与 #import

* `#include` 与 `#import` 的作用类似，都是先要求预处理器读取某个文件，然后将读入的内容添加至输出结果。
* 区别：`#import` 会确保预处理器只导入特定的文件一次，而 `#include` 则允许多次导入同一个文件。

```objectivec
#import <<#header#>>
#import "<#header#>"
```

*  < >尖括号的文件，表示编译器会先在预先设定好的**标准目录**下查找相应的头文件。
*  " "双引号的文件，表示编译器会先在**项目目录**下查找相应的头文件。
*  凡是出现在预编译文件（**.pch**）中的头文件，Xcode 都会事前**编译好并自动导入每个文件**。

# #define

`#define` 告诉预处理器，在编译器看到A之前，使用B替换之。

```objectivec
int main(int argc, const char * argv[]) {
    @autoreleasepool {
        // M_PI 是在 <math.h> 中定义的常量
        NSLog(@"\u03c0 is %f",M_PI);  
    }
    return 0;
}
```

M_PI 是在 `math.h` 中定义的常量:

```objectivec
#define M_PI        3.14159265358979323846264338327950288   /* pi             */
```

💡💡💡在定义常量时，你应该使用全局变量和 `enum`，而不是 `#define`。

除了定义常量，还可以使用 `#define` 构建类似函数的代码段，称之为 **宏（macro）**。

```objectivec
NSLog(@"%d is larger",MAX(10, 12));
```

这里的 **MAX** 不是函数，而是一个 `#define` 指令：

```objectivec
#if !defined(MAX)
    #define __NSMAX_IMPL__(A,B,L) ({ __typeof__(A) __NSX_PASTE__(__a,L) = (A); __typeof__(B) __NSX_PASTE__(__b,L) = (B); (__NSX_PASTE__(__a,L) < __NSX_PASTE__(__b,L)) ? __NSX_PASTE__(__b,L) : __NSX_PASTE__(__a,L); })
    #define MAX(A,B) __NSMAX_IMPL__(A,B,__COUNTER__)
#endif
```

## 宏定义的日常使用：

* 宏定义颜色
```objectivec
// 获取RGB颜色
#define Color_A(r, g, b, a) [UIColor colorWithRed:(r)/255.0 green:(g)/255.0 blue:(b)/255.0 alpha:(a)]
#define Color(r, g, b) [UIColor colorWithRed:(r)/255.0 green:(g)/255.0 blue:(b)/255.0 alpha:1.0]

#define RGBA(r,g,b,a) [UIColor colorWithRed:r/255.0f green:g/255.0f blue:b/255.0f alpha:a]
#define RGB(r,g,b) RGBA(r,g,b,1.0f)

// rgb颜色转换（16进制->10进制），输入十六进值颜色值@“#000000”
#define UIColorFromRGB(rgbValue) [UIColor colorWithRed:((float)((rgbValue & 0xFF0000) >> 16))/255.0 green:((float)((rgbValue & 0xFF00) >> 8))/255.0 blue:((float)(rgbValue & 0xFF))/255.0 alpha:1.0]

// 随机色
#define RANDOM_COLOR [UIColor colorWithRed:arc4random_uniform(255)/255.0 green:arc4random_uniform(255)/255.0 blue:arc4random_uniform(255)/255.0 alpha:arc4random_uniform(255)/255.0]

// 背景色
#define COLOR_BACKGROUND [UIColor colorWithRed:239/255.0 green:239/255.0 blue:239/255.0 alpha:1.0]

// Chameleon 框架中的宏定义语法: https://github.com/ViccAlexander/Chameleon
#define rgba(r,g,b,a) [UIColor colorWithRed:r/255.0f green:g/255.0f blue:b/255.0f alpha:a]
#define rgb(r,g,b) [UIColor colorWithRed:r/255.0f green:g/255.0f blue:b/255.0f alpha:1.0]
#define hsba(h,s,b,a) [UIColor colorWithHue:h/360.0f saturation:s/100.0f brightness:b/100.0f alpha:a]
#define hsb(h,s,b) [UIColor colorWithHue:h/360.0f saturation:s/100.0f brightness:b/100.0f alpha:1.0]
```

* 宏定义字体

```objectivec
#define CYLabelFont [UIFont boldSystemFontOfSize:13]
#define CYFont(f) [UIFont systemFontOfSize:(f)]
#define CYFontB(f) [UIFont boldSystemFontOfSize:(f)]
```

* ~~屏幕的宽高~~

```objectivec
#define CYScreen [UIScreen mainScreen]
#define CYScreenWidth CYScreen.bounds.size.width
#define CYScreenHeight CYScreen.bounds.size.height
```
补充，如果你使用 YYKit 框架，那么 YYCGUtilities.h 中已经写好了：
```objectivec
// main screen's scale
#ifndef kScreenScale
#define kScreenScale YYScreenScale()
#endif

// main screen's size (portrait)
#ifndef kScreenSize
#define kScreenSize YYScreenSize()
#endif

// main screen's width (portrait)
#ifndef kScreenWidth
#define kScreenWidth YYScreenSize().width
#endif

// main screen's height (portrait)
#ifndef kScreenHeight
#define kScreenHeight YYScreenSize().height
#endif
```

* ~~弧度与角度的相互转换~~
```objectivec
/** 角度转换为弧度 */
#define DEGREES_TO_RADIANS(degrees) ((degrees) * M_PI / 180)
/** 弧度转换为角度 */
#define RADIANS_TO_DEGREES(radians) ((radians) * 180 / M_PI)
```
补充，如果你使用 YYKit 框架，那么 YYCGUtilities.h 中已经写好了：
```objectivec
/// Convert degrees to radians.
static inline CGFloat DegreesToRadians(CGFloat degrees) {
    return degrees * M_PI / 180;
}

/// Convert radians to degrees.
static inline CGFloat RadiansToDegrees(CGFloat radians) {
    return radians * 180 / M_PI;
}
```

* ~~强引用与弱引用~~

```objectivec
#define WS(weakself)  __weak __typeof(&*self)weakself = self;
#define SS(strongself)  __strong __typeof(&*self)strongself = self;

#define WeakSelf(weakSelf)      __weak __typeof(self)weakSelf = self;
#define StrongSelf(strongSelf)  __strong __typeof(weakSelf)strongSelf = weakSelf;
```
补充，如果你使用 YYKit 框架，那么 YYCategoriesMacro.h 中已经写好了：

```objectivec
#ifndef weakify
    #if DEBUG
        #if __has_feature(objc_arc)
        #define weakify(object) autoreleasepool{} __weak __typeof__(object) weak##_##object = object;
        #else
        #define weakify(object) autoreleasepool{} __block __typeof__(object) block##_##object = object;
        #endif
    #else
        #if __has_feature(objc_arc)
        #define weakify(object) try{} @finally{} {} __weak __typeof__(object) weak##_##object = object;
        #else
        #define weakify(object) try{} @finally{} {} __block __typeof__(object) block##_##object = object;
        #endif
    #endif
#endif

#ifndef strongify
    #if DEBUG
        #if __has_feature(objc_arc)
        #define strongify(object) autoreleasepool{} __typeof__(object) object = weak##_##object;
        #else
        #define strongify(object) autoreleasepool{} __typeof__(object) object = block##_##object;
        #endif
    #else
        #if __has_feature(objc_arc)
        #define strongify(object) try{} @finally{} __typeof__(object) object = weak##_##object;
        #else
        #define strongify(object) try{} @finally{} __typeof__(object) object = block##_##object;
        #endif
    #endif
#endif
```


* ~~获取沙盒目录~~

```objectivec
#define PATH_TEMP  NSTemporaryDirectory()
#define PATH_DOCUMENT  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject];
#define PATH_CACHE  [NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) firstObject];
```
补充，如果你使用 YYKit 框架，那么UIApplication+YYAdd 中已经写成了属性的形式：
```objectivec
/// "Documents" folder in this app's sandbox.
@property (nonatomic, readonly) NSURL *documentsURL;
@property (nonatomic, readonly) NSString *documentsPath;

/// "Caches" folder in this app's sandbox.
@property (nonatomic, readonly) NSURL *cachesURL;
@property (nonatomic, readonly) NSString *cachesPath;

/// "Library" folder in this app's sandbox.
@property (nonatomic, readonly) NSURL *libraryURL;
@property (nonatomic, readonly) NSString *libraryPath;
```
实现的方式是一模一样的：
```objectivec
- (NSURL *)documentsURL {
    return [[[NSFileManager defaultManager]
             URLsForDirectory:NSDocumentDirectory
             inDomains:NSUserDomainMask] lastObject];
}

- (NSString *)documentsPath {
    return [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject];
}

- (NSURL *)cachesURL {
    return [[[NSFileManager defaultManager]
             URLsForDirectory:NSCachesDirectory
             inDomains:NSUserDomainMask] lastObject];
}

- (NSString *)cachesPath {
    return [NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) firstObject];
}

- (NSURL *)libraryURL {
    return [[[NSFileManager defaultManager]
             URLsForDirectory:NSLibraryDirectory
             inDomains:NSUserDomainMask] lastObject];
}

- (NSString *)libraryPath {
    return [NSSearchPathForDirectoriesInDomains(NSLibraryDirectory, NSUserDomainMask, YES) firstObject];
}
```

* View 设置圆角边框

```objectivec
#define ViewBorderRadius(View, Radius, Width, Color)  \
[View.layer setCornerRadius:(Radius)];                \
[View.layer setMasksToBounds:YES];                    \
[View.layer setBorderWidth:(Width)];                  \
[View.layer setBorderColor:[Color CGColor]]
```

* 宏定义指定字符串

```objectivec
#define URL 	@"https://www.google.com"
```

* 图片路径

```objectivec
#define CYPasswordViewSrcName(file) [@"CYPasswordView.bundle" stringByAppendingPathComponent:file]
```

* 判断系统版本（来自 **XLForm.h**）
```objectivec
#define SYSTEM_VERSION_EQUAL_TO(v)                  ([[[UIDevice currentDevice] systemVersion] compare:v options:NSNumericSearch] == NSOrderedSame)
#define SYSTEM_VERSION_GREATER_THAN(v)              ([[[UIDevice currentDevice] systemVersion] compare:v options:NSNumericSearch] == NSOrderedDescending)
#define SYSTEM_VERSION_GREATER_THAN_OR_EQUAL_TO(v)  ([[[UIDevice currentDevice] systemVersion] compare:v options:NSNumericSearch] != NSOrderedAscending)
#define SYSTEM_VERSION_LESS_THAN(v)                 ([[[UIDevice currentDevice] systemVersion] compare:v options:NSNumericSearch] == NSOrderedAscending)
#define SYSTEM_VERSION_LESS_THAN_OR_EQUAL_TO(v)     ([[[UIDevice currentDevice] systemVersion] compare:v options:NSNumericSearch] != NSOrderedDescending)

// 使用示例，传入版本号字符串即可
if (SYSTEM_VERSION_GREATER_THAN_OR_EQUAL_TO(@"8.0")){
    self.tableView.rowHeight = UITableViewAutomaticDimension;
    self.tableView.estimatedRowHeight = 44.0;
}
```

# 全局变量

* **局部变量**：程序只在函数运行时才会存在的变量；
* **全局变量**：任何函数都可以在任意时刻访问的变量；
* **静态变量**: 只有声明某个静态变量的文件才能访问该变量,静态变量既保留了非局部的，存在于任何函数之外的优点，又避免了会被其他文件修改的问题。
* 如果没有为**静态变量**和**全局变量**赋初值，则程序会将他们的值自动赋为0.

编写 Objective-C 程序时，通常不是使用 `#define` 来保存 **常量**，而是使用 **全局变量** 来完成这项任务。**因为在某些情况下，使用全局变量的效率更高**。

```objectivec
// 获取用户当前区域的货币
NSLocale *here = [NSLocale currentLocale];
//	NSString *currency = [here objectForKey:@"currency"];
// 使用全局变量替换
NSString *currency = [here objectForKey:NSLocaleCountryCode];
NSLog(@"Money is %@",currency);
```

**NSLocaleCountryCode** 是一个全局变量，它的声明如下：

```objectivec
FOUNDATION_EXPORT NSLocaleKey const NSLocaleCountryCode;		// NSString
```

## 关键字

* **const** ：在程序的整个运行过程中，**NSLocaleCountryCode** 指针的值不会发生变化。

  ```objectivec
  ‼️ 难点 ‼️
  // ********************************************************
  const CGFloat count = 6;
  const NSString *string = @"string";
  // const放在最前面可以往后移一位,含义不变,因此下面两个写法和上面的等价
  CGFloat const count = 6;
  NSString const *string = @"string";

  // ********************************************************
  // age 是【指针变量】, const 修饰指针变量,意味着【这个指针变量】不可修改,而指针变量存的是地址,意思就是不能把 【这个指针变量存的地址】 改成 【其他地址】.或者说不能改变指针的指向。
  int * const age = 25;

  // age 是【指针变量】, *age 是取得【指针所指向的值】, const 修饰 *age, 意味着 【*age 取得指针所指向的值】 不可修改。
  int const *age = 23;


  // ********************************************************
  // p  : 指针变量
  // *p : 取指针指向的内存
  // &p : 取指针本身的地址
  // const修饰【指针变量访问的内存空间】，修饰的是右边*p1，
  // 两种方式一样
  const int *p1; // *p1：常量 p1:变量
  int const *p1; // *p1：常量 p1:变量

  // const修饰指针变量p1
  int * const p1; // *p1:变量 p1:常量


  // 第一个const修饰*p1，第二个const修饰 p1 (技巧：从后向前看)
  // 两种方式一样
  const int * const p1;  // *p1：常量 p1：常量
  int const * const p1;  // *p1：常量 p1：常量

  // 开发中经常拿到key修改值，因此用const修饰key,表示key只读，不允许修改。
  static  NSString * const key = @"name";

  // 如果 const修饰 *key1,表示*key1只读，key1还是能改变。
  static  NSString const *key1 = @"name";
  ```

* **extern**：**NSLocaleCountryCode** 是存在的，但是会在另一个文件中定义。(即在**.h**文件中声明，在**.m**文件中赋值)
* **UIKIT_EXTERN**: 是苹果使用的 **extern** ,只是添加了一些编译器代码，是经过处理的 **extern**。
* **static** : 该变量仅在定义此变量的编译单元中可见。


# enum 枚举类型

枚举类型用于定义**一组常量**。

* 使用 enum 枚举类型

```objectivec
typedef enum : NSUInteger {
    MyEnumValueA,
    MyEnumValueB,
    MyEnumValueC,
} MyEnum;
```

* 使用 `NS_ENUM(…)` 语法

```objectivec
typedef NS_ENUM(NSUInteger, BlenderSpeed) {
    BlenderSpeedStir = 1,
    BlenderSpeedChop = 2,
    BlenderSpeedLiquify = 3,
    BlenderSpeedPulse = 4,
    BlenderSpeedIceCrush = 5
};

@property (nonatomic, assign) BlenderSpeed mySpeed;
```

`NS_ENUM(…)` 实际上是一个预处理宏，它带有两个实参：数据类型和名字。使用 `NS_ENUM(…)` 的优点是它可以声明整数数据类型（`short`、`unsigned`、`long` 等）。

如果使用旧的语法，编辑器会为 `enum` 选择合适的数据类型，通常是 `int` 类型。如果你的 `enum` 只需要枚举四个常量，那么它的值是什么都无所谓，就不需要四个字节来存储它。一个字节就可以存储到 255 的整数。比如 `char` 类型就是一个一个字节的整数，因此，我们可以**声明一个节省内存的 `enum`**：
```objectivec
typedef NS_ENUM(char, BlenderSpeed) {
    BlenderSpeedStir,
    BlenderSpeedChop,
    BlenderSpeedLiquify,
    BlenderSpeedPulse,
    BlenderSpeedIceCrush
};
```

# 参考

* [宏定义的黑魔法 - 宏菜鸟起飞手册 @onevcat](https://onevcat.com/2014/01/black-magic-in-macro/)
* [iOS NSLog在项目release版本中的使用注意](http://www.jianshu.com/p/2e79436e5fe3)
* [关于全局常量的定义](http://www.jianshu.com/p/ed2dfbca6e73)
* [iOS const的使用](http://www.cnblogs.com/oumygade/p/4316024.html)
* [【如何正确使用const,static,extern】|那些人追的干货](http://www.jianshu.com/p/2fd58ed2cf55)
* [iOS pch 文件的新建和其缺点](https://www.jianshu.com/p/2b449a4ae20f)
* [iOS宏定义的使用与规范](https://cnbin.github.io/blog/2016/03/21/ioshong-ding-yi-de-shi-yong-yu-gui-fan/)
