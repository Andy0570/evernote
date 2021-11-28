我们用**运行时（runtime）**来形容应用在用户电脑中运行的一段时间。**运行时（runtime）**可以和**编译时（compile-time）**作对比，编译时是在程序运行之前，还在使用 Xcode 构建程序的一段时间。

Objective-C 运行时是 OS X 以及 iOS 系统执行 Objective-C 代码的一部分。它还负责动态地追踪记录哪些类存在，这些类定义了哪些方法，以及查看消息是否恰当地在对象之间传递。

💡💡💡 了解 Objective-C 运行时函数的存在可以帮助我们理解代码底层的工作机制。然后，运行时函数主要是苹果公司的工程师在编写 API 时使用的，并不适合用于编写日常应用。

下面介绍 Objective-C 运行时的几个特性：



# 内省

内省（introspection）是 Objective-C 运行时的一个特性：它能够**让对象在程序运行的时候回答关于自身的问题**。

例如，在 `NSObject` 类中有一个方法：

```objectivec
- (BOOL)respondsToSelector:(SEL)aSelector;
```

 该方法中需要传入的一个实参是一个选择器（一个方法的名字）。如果对象实现了该选择器的名字的方法，就会返回 `YES`；如果没有实现，则返回 `NO`。

示例代码：

```objectivec
- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 解决 UI 界面整体上移问题
    if ([self respondsToSelector:@selector(edgesForExtendedLayout)]) {
        self.edgesForExtendedLayout = UIRectEdgeNone;
    }
}
```

以上示例代码用于解决 UI 界面整体上移的问题。如果该视图控制器的实例可以响应 `edgesForExtendedLayout` 方法，则调用该方法。相比于不进行判断，直接调用该 Setter 方法的好处是：可以防止因某个视图控制器的实例无法响应该方法造成的应用崩溃问题。



# 动态查找并执行方法

一个运行中的 Objective-C 程序包含了大量的对象，对象之间彼此发送消息。对象发送消息的时候，首先它会搜索要执行的方法。通常会从接收者的 `isa` 指针指向的类开始进行搜索，然后根据继承层级搜索，直到找到需要的方法。

**动态查找并执行方法构成了 Objective-C 消息发送机制的基础，它也是 Objective-C 运行时的另一大特性。**

查找并执行方法是 C 函数 `objc_msgSend(id, SEL, ...)` 的工作。这个函数的实参是接收消息的对象，被执行的方法的选择器，以及这个方法的所有实参。

例如，以下代码用于向控制台输出给定字符串的大写版本：

```objectivec
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        NSString *string = @"hello world";
        NSString *capsString = [string uppercaseString];
        NSLog(@"%@ -> %@", string, capsString);
        // hello world -> HELLO WORLD
    }
    return 0;
}
```

当编译器看到 `uppercaseString` 消息时，它会调用 `objc_msgSend(id, SEL, ...)`：

```objectivec
#import <Foundation/Foundation.h>
#import <objc/message.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        NSString *string = @"hello world";
        NSString *capsString = objc_msgSend(string, @selector(uppercaseString));
        NSLog(@"%@ -> %@", string, capsString);
        // hello world -> HELLO WORLD
    }
    return 0;
}
```

注：在 Xcode 中编辑以上代码时，如果编辑器报错：

objc_msgSend () 报错 Too many arguments to function call ,expected 0,have2.

需要设置：Build Setting --> Apple LLVM 6.0 - Preprocessing --> Enable Strict Checking of objc_msgSend Calls  改为 NO 即可。

有一批函数是 Objective-C 程序中发送消息的中心，而 `objc_msgSend()` 函数正是其中之一。这些函数在 `objc/message.h` 中被声明。如果想要了解更多关于这类函数以及其他运行时函数的信息，可以在开发者文档中查阅 [Objective-C Runtime Reference](https://developer.apple.com/documentation/objectivec/objective-c_runtime)。



# 类以及继承层级的管理

**Objective-C 运行时不仅负责记录正在使用哪些类，还负责记录那些包含到程序中的库以及框架使用的类。**

```objectivec
#import <Foundation/Foundation.h>
#import <objc/runtime.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        // 声明一个变量，用来保存注册的类的数量
        unsigned int classCount = 0;
        
        // 创建一个指针指向应用当前加载的所有注册类的列表
        // 通过引用返回注册类的数量
        Class *classList = objc_copyClassList(&classCount);
        
        // 列表单中的每个类...
        for (int i = 0; i < classCount; i++) {
            
            // 将类的列表作为一个 C 语言数组处理，获取其中的一个类
            Class currentClass = classList[i];
            
            // 将类的名称作为字符串处理
            NSString *className = NSStringFromClass(currentClass);
            
            // 输出类的名称
            NSLog(@"%@", className);
        }
        
        // 现在已经不需要这个类列表的缓存区了，释放它
        free(classList);
    }
    return 0;
}
```

`objc_copyClassList()` 函数会返回一个由指向类对象的指针组成的 C 数组。

构建并运行程序，浏览程序打印出来的类名单。你会发现有许许多多的类在底层为我们编写的程序服务。其中类的名称以下划线开头的，是在苹果公司框架底层中嵌入的类，你可以理解为内部类。

下一步，给程序添加函数，**展示每个列出的类的继承层级，以及每个类实现的方法列表**。

```objectivec
// 该辅助函数用于创建一个 NSArray，代表传入类的继承层级
NSArray *BNRHierarchyForClass(Class cls) {
    // 声明一个数组用来保存类及其父类组成的列表，创建一个层级
    NSMutableArray *classHierarchy = [NSMutableArray array];
    
    // 继续追踪继承层级，直到再也没有父类
    for (Class c = cls; c != nil; c = class_getSuperclass(c)) {
        // 将类名转换为字符串形式，并添加到数组容器中
        NSString *className = NSStringFromClass(c);
        [classHierarchy insertObject:className atIndex:0];
    }
    return classHierarchy;
}
```

该函数会获取一个类对象后，通过 `class_getSuperclass()`  方法得到它的父类，然后通过 `for` 循环继续获取父类的父类，直到再也没有父类。通常，最后的类会是 `NSObject`。

现在，写一个程序来获取给定类的方法列表：

```objectivec
NSArray *BNRMethodsForClass(Class cls) {
    // 存储方法的数量
    unsigned int methodCount = 0;
    
    // 获取给定类的方法列表
    Method *methodList = class_copyMethodList(cls, &methodCount);
    
    // 存储方法的字符串名字
    NSMutableArray *methodArray = [NSMutableArray array];
    
    for (int m = 0; m < methodCount; m++) {
        // 获取当前的方法
        Method currentMethod = methodList[m];
        // 获取当前方法的选择器
        SEL methodSelector = method_getName(currentMethod);
        // 给数组增加字符串表示形式
        [methodArray addObject:NSStringFromSelector(methodSelector)];
    }
    return methodArray;
}
```

该辅助函数中，通过 `class_copyMethodList()` 方法获取给定类的所有方法列表。

方法类型 `Method` ：方法是一类结构的名字，这类结构的成员包括方法的选择器（即 `SEL` 类型的变量）以及一个函数指针（function pointer）——指向执行程序中内存数据段的一大块代码。这个函数指针是 `IMP` 类型的变量。

修改 `main()` 函数并使用以上的辅助函数：

```objectivec
#import <Foundation/Foundation.h>
#import <objc/runtime.h>

// 该辅助函数用于创建一个 NSArray，代表传入类的继承层级
NSArray *BNRHierarchyForClass(Class cls) {
    // 声明一个数组用来保存类及其父类组成的列表，创建一个层级
    NSMutableArray *classHierarchy = [NSMutableArray array];
    
    // 继续追踪继承层级，直到再也没有父类
    for (Class c = cls; c != nil; c = class_getSuperclass(c)) {
        // 将类名转换为字符串形式，并添加到数组容器中
        NSString *className = NSStringFromClass(c);
        [classHierarchy insertObject:className atIndex:0];
    }
    return classHierarchy;
}

NSArray *BNRMethodsForClass(Class cls) {
    // 存储方法的数量
    unsigned int methodCount = 0;
    
    // 获取给定类的方法列表
    Method *methodList = class_copyMethodList(cls, &methodCount);
    
    // 存储方法的字符串名字
    NSMutableArray *methodArray = [NSMutableArray array];
    
    for (int m = 0; m < methodCount; m++) {
        // 获取当前的方法
        Method currentMethod = methodList[m];
        // 获取当前方法的选择器
        SEL methodSelector = method_getName(currentMethod);
        // 给数组增加字符串表示形式
        [methodArray addObject:NSStringFromSelector(methodSelector)];
    }
    return methodArray;
}


int main(int argc, const char * argv[]) {
    @autoreleasepool {
        // 创建一个字典数组，每个字典都会保存类的名称、层级以及给定类的方法列表
        NSMutableArray *runtimeClassesInfo = [NSMutableArray array];
        
        // 声明一个变量，用来保存注册的类的数量
        unsigned int classCount = 0;
        
        // 创建一个指针指向应用当前加载的所有注册类的列表
        // 通过引用返回注册类的数量
        Class *classList = objc_copyClassList(&classCount);
        
        // 列表中的每个类...
        for (int i = 0; i < classCount; i++) {
            
            // 将类的列表作为一个 C 语言数组处理，获取其中的一个类
            Class currentClass = classList[i];
            
            // 将类的名称作为字符串处理
            NSString *className = NSStringFromClass(currentClass);
            
            // 获取并返回当前类的层级结构
            NSArray *hierarchy = BNRHierarchyForClass(currentClass);
            
            // 获取并返回当前类的方法列表
            NSArray *methods = BNRMethodsForClass(currentClass);
            
            NSDictionary *classInfoDict = @{
                @"classname": className,
                @"hierarchy": hierarchy,
                @"methods": methods,
            };
            
            [runtimeClassesInfo addObject:classInfoDict];
        }
        
        // 现在已经不需要这个类列表的缓存区了，释放它
        free(classList);
        
        // 按字母顺序给这些类排序，打印出来
        NSSortDescriptor *alphaAsc = [NSSortDescriptor sortDescriptorWithKey:@"name" ascending:YES];
        NSArray *sortedArray = [runtimeClassesInfo sortedArrayUsingDescriptors:@[alphaAsc]];
        NSLog(@"该应用运行时中，总共注册了 %ld 个类", sortedArray.count);
        NSLog(@"%@",sortedArray);
    }
    return 0;
}
```

构建并运行程序，控制台输出的内容中就会包含类的名称、继承层级以及程序运行时所有注册类的方法列表。

部分输出片段示例：

```objectivec
...
		{
        classname = WKNSURL;
        hierarchy =         (
            NSProxy,
            WKObject,
            WKNSURL
        );
        methods =         (
            "copyWithZone:",
            "_web_createTarget"
        );
    },
        {
        classname = WKNSString;
        hierarchy =         (
            NSProxy,
            WKObject,
            WKNSString
        );
        methods =         (
            superclass,
            class,
            "copyWithZone:",
            "_web_createTarget"
        );
    },
        {
        classname = WKNSError;
        hierarchy =         (
            NSProxy,
            WKObject,
            WKNSError
        );
        methods =         (
            "copyWithZone:",
            "_web_createTarget"
        );
    },
...
```



# KVO 的工作原理

键值观察（Key-Value Oberving）是苹果公司的 API 依赖于运行时函数的另一个例子。使用 KVO 时，如果对象使用了存取器，被观察的对象可以自动通知属性中的变化。

运行时，如果向某个对象发送 `addObserver: forKeyPath: options: context:` 消息，那么这个方法可以：

* 决定被观察对象的类，并使用 `objc_allocateClassPair()` 函数给这个类定义一个新的子类。
* 改变对象的 `isa` 指针，让它指向新的子类（高效改变对象的类型）。
* 覆盖被观察对象的存取器，发送 KVO 消息。

![KVO 动态子类](https://upload-images.jianshu.io/upload_images/2648731-5e6471b484ef03db.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

例如，一个类的 `location` 属性的存方法代码如下：

```objectivec
- (void)setLocation:(NSPoint)location {
    _location = location;
}
```

在新的子类中，存取器会被覆盖如下：

```objectivec
// 显式触发通知
// 通过 willChangeValueForKey: 和 didChangeValueForKey: 方法通知系统某个属性的值即将/已经发生变化
- (void)setLocation:(NSPoint)location {
    [self  willChangeValueForKey:@"location"];
    _location = location;
    [self didChangeValueForKey:@"location"];
}
```

子类的存取器实现会调用原始类的实现，然后将它们用简明的 KVO 通知消息封装起来。这些新的子类以及方法都会在运行时使用 Objective-C 运行时函数定义。

# 关联对象（Associated Objects）

## `objc_setAssociatedObject()` - 设置关联对象

用于给对象添加关联对象，传入 `nil` 则可以移除已有的关联对象；

让一个对象和另一个对象关联起来，即一个对象保持对另一个对象的引用，并可以获取这个对象。关键字是一个 void 类型的指针。每个关键字必须是唯一的，通常都是会采用静态变量来作为关键字。

```objectivec
/** 
 * Sets an associated value for a given object using a given key and association policy.
 * 
 * @param object 关联的源对象.
 * @param key 关联的key.
 * @param value 通过key值关联对象，通过将此个值置成nil来清除关联.
 * @param policy 关联策略
 * 
 * @see objc_setAssociatedObject
 * @see objc_removeAssociatedObjects
 */
OBJC_EXPORT void
objc_setAssociatedObject(id _Nonnull object, const void * _Nonnull key,
                         id _Nullable value, objc_AssociationPolicy policy)
    OBJC_AVAILABLE(10.6, 3.1, 9.0, 1.0, 2.0);
```



## `objc_getAssociatedObject()` - 获取关联对象

用于获取关联对象；

```objectivec
/** 
 * Returns the value associated with a given object for a given key.
 * 返回与给定键的给定对象关联的值
 * 
 * @param object The source object for the association.
 * @param key The key for the association.
 * 
 * @return The value associated with the key \e key for \e object.
 * 
 * @see objc_setAssociatedObject
 */
OBJC_EXPORT id _Nullable
objc_getAssociatedObject(id _Nonnull object, const void * _Nonnull key)
    OBJC_AVAILABLE(10.6, 3.1, 9.0, 1.0, 2.0);
```



## `objc_removeAssociatedObjects()` - 移除所有关联对象

用于移除一个对象的所有关联对象。

```objectivec
/** 
 * Removes all associations for a given object.
 * 
 * @param object An object that maintains associated objects.
 * 
 * @note The main purpose of this function is to make it easy to return an object 
 *  to a "pristine state”. You should not use this function for general removal of
 *  associations from objects, since it also removes associations that other clients
 *  may have added to the object. Typically you should use \c objc_setAssociatedObject 
 *  with a nil value to clear an association.
 * 
 * ⚠️ 此函数的主要目的是使对象返回 “原始状态” 变得更容易。
 * 你不应该使用此函数来从对象中删除关联，因为它还会删除其他客户端可能添加到对象的关联。
 * 通常，你应该使用带有 nil 值的 objc_setAssociatedObject 方法来移除你的关联。
 * 
 * @see objc_setAssociatedObject
 * @see objc_getAssociatedObject
 */
OBJC_EXPORT void
objc_removeAssociatedObjects(id _Nonnull object)
    OBJC_AVAILABLE(10.6, 3.1, 9.0, 1.0, 2.0);
```

**注**：`objc_removeAssociatedObjects` 函数我们一般是用不上的，因为这个函数会移除一个对象的所有关联对象，将该对象恢复成 “原始” 状态。这样做就很有可能把别人添加的关联对象也一并移除，这并不是我们所希望的。所以一般的做法是通过给 `objc_setAssociatedObject` 函数传入 `nil` 来移除某个已有的关联对象。



## `objc_AssociationPolicy` - 关联策略

```objectivec
/* Associative References */

/**
 * Policies related to associative references.
 * These are options to objc_setAssociatedObject()
 */
typedef OBJC_ENUM(uintptr_t, objc_AssociationPolicy) {
    OBJC_ASSOCIATION_ASSIGN = 0,           /** 弱引用关联对象 */
    OBJC_ASSOCIATION_RETAIN_NONATOMIC = 1, /** 强引用关联对象，且为非原子操作. */
    OBJC_ASSOCIATION_COPY_NONATOMIC = 3,   /** 复制关联对象，且为非原子操作 */
    OBJC_ASSOCIATION_RETAIN = 01401,       /** 强引用关联对象，且为原子操作 */
    OBJC_ASSOCIATION_COPY = 01403          /** 复制关联对象，且为原子操作 */
};
```
示例代码：

需要先导入头文件：`#import <objc/runtime.h>`
```objectivec
//1.声明一个静态变量,用作关键字.此处也可以直接用一个字符串(但不推荐)
static char associatedKey;

//2.创建一个数组变量
NSArray *array = @[@"1", @"2"];

//3.创建一个字符串变量
NSString *value = @"hello sunshine";

// 设置关联对象
objc_setAssociatedObject(array, &associatedKey, value, OBJC_ASSOCIATION_RETAIN);

// 获取关联对象
NSString *associatedObject = (NSString *)objc_getAssociatedObject(array, &associatedKey);
NSLog(@"associatedObject:%@", associatedObject);

// 移除关联对象
objc_setAssociatedObject(array, &associatedKey, nil, OBJC_ASSOCIATION_ASSIGN);
```

构建并运行程序，输出如下：
```
testProject [20557:2739471] associatedObject:hello sunshine
```

**在类的 Category 文件中，你只可以为对象增加成员方法，但不能增加成员变量。但是通过 `objc_setAssociatedObject()` 和 `objc_getAssociatedObject()` 方法可以变相地给对象增加成员变量**。

附参考示例：[JKCategories/UIButton+JKSubmitting.m](https://github.com/shaojiankui/JKCategories/blob/master/JKCategories/UIKit/UIButton/UIButton%2BJKSubmitting.m)


# 运行时 API 参考
```objectivec
//***************************************************
// 获取类的类名
const char * class_getName ( Class cls );

// 获取类的父类
Class class_getSuperclass ( Class cls );

// 判断给定的Class是否是一个元类
BOOL class_isMetaClass ( Class cls );

// 获取实例大小
size_t class_getInstanceSize ( Class cls );

// 获取类中指定名称实例成员变量的信息
Ivar class_getInstanceVariable ( Class cls, const char *name );

// 获取类成员变量的信息
Ivar class_getClassVariable ( Class cls, const char *name );

// 添加成员变量
BOOL class_addIvar ( Class cls, const char *name, size_t size, uint8_t alignment, const char *types );

// 获取整个成员变量列表
Ivar * class_copyIvarList ( Class cls, unsigned int *outCount );

// 获取指定的属性
objc_property_t class_getProperty ( Class cls, const char *name );

// 获取属性列表
objc_property_t * class_copyPropertyList ( Class cls, unsigned int *outCount );

// 为类添加属性
BOOL class_addProperty ( Class cls, const char *name, const objc_property_attribute_t *attributes, unsigned int attributeCount );

// 替换类的属性
void class_replaceProperty ( Class cls, const char *name, const objc_property_attribute_t *attributes, unsigned int attributeCount );

// 添加方法
BOOL class_addMethod ( Class cls, SEL name, IMP imp, const char *types );

// 获取实例方法
Method class_getInstanceMethod ( Class cls, SEL name );

// 获取类方法
Method class_getClassMethod ( Class cls, SEL name );

// 获取所有方法的数组
Method * class_copyMethodList ( Class cls, unsigned int *outCount );

// 替代方法的实现
IMP class_replaceMethod ( Class cls, SEL name, IMP imp, const char *types );

// 返回方法的具体实现
IMP class_getMethodImplementation ( Class cls, SEL name );
IMP class_getMethodImplementation_stret ( Class cls, SEL name );

// 类实例是否响应指定的selector
BOOL class_respondsToSelector ( Class cls, SEL sel );

// 添加协议
BOOL class_addProtocol ( Class cls, Protocol *protocol );

// 返回类是否实现指定的协议
BOOL class_conformsToProtocol ( Class cls, Protocol *protocol );

// 返回类实现的协议列表
Protocol * class_copyProtocolList ( Class cls, unsigned int *outCount );

// 获取版本号
int class_getVersion ( Class cls );

// 设置版本号
void class_setVersion ( Class cls, int version );

//***************************************************

// 调用指定方法的实现
id method_invoke ( id receiver, Method m, ... );

// 调用返回一个数据结构的方法的实现
void method_invoke_stret ( id receiver, Method m, ... );

// 获取方法名
SEL method_getName ( Method m );

// 返回方法的实现
IMP method_getImplementation ( Method m );

// 获取描述方法参数和返回值类型的字符串
const char * method_getTypeEncoding ( Method m );

// 获取方法的返回值类型的字符串
char * method_copyReturnType ( Method m );

// 获取方法的指定位置参数的类型字符串
char * method_copyArgumentType ( Method m, unsigned int index );

// 通过引用返回方法的返回值类型字符串
void method_getReturnType ( Method m, char *dst, size_t dst_len );

// 返回方法的参数的个数
unsigned int method_getNumberOfArguments ( Method m );

// 通过引用返回方法指定位置参数的类型字符串
void method_getArgumentType ( Method m, unsigned int index, char *dst, size_t dst_len );

// 返回指定方法的方法描述结构体
struct objc_method_description * method_getDescription ( Method m );

// 设置方法的实现
IMP method_setImplementation ( Method m, IMP imp );

// 交换两个方法的实现
void method_exchangeImplementations ( Method m1, Method m2 );

//***************************************************

// 方法选择器 SEL
// 返回给定选择器指定的方法的名称
const char * sel_getName ( SEL sel );

// 在Objective-C Runtime系统中注册一个方法，将方法名映射到一个选择器，并返回这个选择器
SEL sel_registerName ( const char *str );

// 在Objective-C Runtime系统中注册一个方法
SEL sel_getUid ( const char *str );

// 比较两个选择器
BOOL sel_isEqual ( SEL lhs, SEL rhs );
```


# 参考

- [objc_setAssociatedObject 使用](https://www.jianshu.com/p/29fd2359ab08)
- [高效编写代码的方法 (二十一)：Category (1)](https://www.jianshu.com/p/5c2b36fd416f)
- [[Objective C] objc_setAssociatedObject 关联详解](https://juejin.im/post/5a39dcdbf265da430b7b60d2)
- [Objective-C Associated Objects 的实现原理](http://blog.leichunfeng.com/blog/2015/06/26/objective-c-associated-objects-implementation-principle/)
- [Associated Objects @Mattt Thompson](https://nshipster.com/associated-objects/)
