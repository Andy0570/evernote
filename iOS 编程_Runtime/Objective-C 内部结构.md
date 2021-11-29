> 原文：[PDF: Objective-C Internals](http%3A%2F%2Falgorithm.com.au%2Fdownloads%2Ftalks%2Fobjective-c-internals%2Fobjective-c-internals.pdf)
>
> 通过 [DeepL](https://www.deepl.com/translator) 翻译并由我人工校对，未尽之处请以原文和 Apple 官方文档为准。

![](https://upload-images.jianshu.io/upload_images/2648731-803d69a9e4b32002.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

漂亮的车牌，不是么？

在本讲座中，我们将窥探 Objective-C 引擎：对象是如何在内存中表示的，以及消息发送是如何工作的。

## 什么是对象？

![](https://upload-images.jianshu.io/upload_images/2648731-4394d58ead4c53be.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```c
int i;
i = 0xdeadbeef;
// de ad be ef
```

这是一个简单的例子：这是在 32 位机器上用 C 语言表示一个 `int` 的方法。

**1 字节（byte）= 8 位（bit）**，所以 32 位 = 4 个字节，所以 `int` 可能是这样的。

实际上，`int` 在基于英特尔芯片的 Mac 上会是这样的（或者说是 ICBM，我喜欢这样称呼它们），因为 ICBMs 是小字节序（little-endian）的。
```c
int i;
i = 0xdeadbeef;
// ef be ad de
```

> little-endian?
>
> 字节序，或字节顺序（"Endian"、"endianness" 或 "byte-order"），描述了计算机如何组织字节，组成对应的数字。
>
> 每个内存存储位置都有一个索引或地址。每一字节可以存储一个 8 位数字（即 介于 `0x00` 和 `0xff` 之间），因此，你必须保留不止一个字节来储存一个更大的数字。现在，大部分需占用多个字节的数字排序方式是 **little-endian**（译者注：可称小字节序、低字节序，即**低位字节排放在内存的低地址端，高位字节排放在内存的高地址端**。与之对应的 big-endian 排列方式相反，可称大字节序、高字节序），所有的英特尔处理器都使用 little-endian。**little-endian 的意思是使用低位储存更重要的信息**，least-to-most-significant（最不重要的（least significant）字节取第一个位置，或者说地址最低的位置），可类比欧洲通用的日期书写方式（例如，31 December 2050。
>
> ——摘自 [Endianness（字节序）](https://developer.mozilla.org/zh-CN/docs/Glossary/Endianness)

但为了简单起见，我们将假设内存布局是大字节序（big-endian）的，因为它更容易阅读。

```c
int i;
i = 0xdeadbeef;
// de ad be ef
```

---

```c
typedef struct _IntContainer {
    int i;
} IntContainer;

IntContainer ic;
ic.i = 0xdeadbeef;
// de ad be ef
```

这是包含单个 `int` 类型的 *C 结构体* 的样子。在内存层面，一个包含单个 `int` 类型的结构体看起来和一个 `int`[1]完全一样。这一点非常重要，因为这意味着你可以在 `IntContainer` 和 `int` 之间传递一个特定的值而不会损失精度。这也是 Core Foundation 和 Cocoa 的 "无缝桥接（toll-free bridging）" 的工作原理：**一个 `CFType` 的内存分配与一个 `NSObject` 完全一样**。

[1]在这一点上也许 C 语言定义中的某些东西可能在一些愚蠢的平台上与愚蠢的编译器上不真实，但幸运的是你不是在为DeathStation 9000 编写代码。

---

```c
typedef struct _NSPoint {
    CGFloat x;
    CGFloat y;
} NSPoint;

NSPoint p;
p.x = 1.0;
p.y = 2.0;
// 3f 80 00 00 40 00 00 00
//    x:1.0      y:2.0
```

这是一个稍微复杂的例子，并且你之前一定用过了上百次：Cocoa 的 `NSPoint` 结构体，它由两个 `CGFloat` 类型组成。 在 32 位平台上，一个 `CGFloat` 是 `float` 类型的 `typedef` 宏定义，在 64 位平台上是 `double` 类型。那么，一个有多个字段的结构体类型会是什么样子呢？简单来说，就是内存中一个接一个的值；也就是连续的值（还有一些额外的值对齐和填充规则，这些规则描述很无聊，与本讲座无关）。

请注意，浮点数类型（`float`）在内存中与常规的 `int` 类型完全不同：如果你有兴趣，有两篇文章非常值得一读："[What Every Computer Scientist Should Know About Floating-Point Arithmetic](https://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html)"，以及来自 Jim Blinn 的更难找到但非常值得一读的 "[Floating-Point Tricks - IEEE Computer Graphics and Applications](https://www.yumpu.com/en/document/view/6104114/floating-point-tricks-ieee-computer-graphics-and-applications)"，告诉你如何通过使用更快的位运算在浮点数上做诸如平方根和幂运算。

---

```c
int *pi;
*pi = 0xdeadbeef;
```

![](https://upload-images.jianshu.io/upload_images/2648731-94ee1fdaab0c33eb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这是指针的样子：它指向内存中特定类型的另一个位置。谁说指针很难呢？在本例中，`pi` 包含十六进制值 `0x08039170`。`*` 运算符取消引用指针的值，并将值指向一块内存地址。所以，在本例中，内存位置 `0x08039170` 保存的值包含了我们实际上需要的 `int` 值。

---

现在我们已经具备了看一个 Objective-C 类定义的能力，并谈谈对象是如何在内存中表示的。在终端输入 "`open -h NSObject.h`"，它会弹出 `NSObject.h` 头文件，其中包含 `NSObject` 根对象的定义。`NSObject` 类的声明是这样的。

```objc
@interface NSObject <NSObject> {
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Wobjc-interface-ivars"
    Class isa  OBJC_ISA_AVAILABILITY;
#pragma clang diagnostic pop
}
```

那段 `#pragm` 包裹的宏定义是用来消除编译器警告的，让我们简化它：

```objc
@interface NSObject <NSObject> {
    Class isa;
}
```

Objective-C 的 `@interface` 关键字只是一种花哨的方式，用来声明一个同名的结构体，同时告诉编译器给定的名称是一个 Objective-C 类名。也就是说，**`NSObject` 只是一个结构体，它只有一个参数，命名为 `isa`**（"is a，是一个"，如 "汽车是一辆车"），它指向某种 `Class` 类型。但是，这个 `Class` 是什么东西呢？

```objc
struct NSObject {
    Class isa;
}
```

原来，`Class` 在 `<objc/objc.h>` 中作为 `objc_class*` 结构的 `typedef` 类型定义。

```objc
// objc/objc.h
// ...

typedef struct objc_class *Class;
```

也就是说，**`NSObject` 只是一个指向 Objective-C 类定义的单一指针**，仅此而已。

```objc
struct NSObject {
    struct objc_class* isa;
}
```

所以，下一个问题是 Objective-C 类是什么样子的。在 Mac 上的 /usr/include/objc/ 目录中搜索一下，你会发现它看起来是这样的[1]。

```objc
struct objc_class {
    Class _Nonnull isa  OBJC_ISA_AVAILABILITY;

#if !__OBJC2__
    Class _Nullable super_class                              OBJC2_UNAVAILABLE;
    const char * _Nonnull name                               OBJC2_UNAVAILABLE;
    long version                                             OBJC2_UNAVAILABLE;
    long info                                                OBJC2_UNAVAILABLE;
    long instance_size                                       OBJC2_UNAVAILABLE;
    struct objc_ivar_list * _Nullable ivars                  OBJC2_UNAVAILABLE;
    struct objc_method_list * _Nullable * _Nullable methodLists                    OBJC2_UNAVAILABLE;
    struct objc_cache * _Nonnull cache                       OBJC2_UNAVAILABLE;
    struct objc_protocol_list * _Nullable protocols          OBJC2_UNAVAILABLE;
#endif

} OBJC2_UNAVAILABLE;
```

这里面包含了 Objecitve-C 运行时对对象进行任何操作所需要的所有信息：找出它遵守哪些协议，它有哪些方法，对象的 `ivars`（实例变量）在内存中的布局，它的父类是什么，等等。

一个有趣的事情是，`objc_class` 结构的第一个字段与 `NSObject` 结构中的字段类型相同。这意味着 `objc_class` 是一个对象，因为它的内存模型是一样的；因此，所有在实例对象上工作的 Objective-C 操作--比如消息发送---也可以在类对象上工作。这增加了统一性，这意味着（少了很多）区分类对象和实例对象的特例代码。但是，这个类对象的 `isa` 字段指向什么呢？

类对象的 `isa` 字段指向一个叫做元类（`metaclass`）对象的东西，正如字段的类型所示，它只是另一个 `objc_class` 结构。因此，每个类定义都有一个类对象和一个元类对象。这样做的理由是，类对象的方法列表是针对该类的实例的；也就是说，类对象的 `methodLists` 字段包含了实例方法的信息。元类对象的方法列表字段则包含了类方法的信息。同样，这也增加了统一性，减少了对特例代码的需求。当然，接下来的问题是 `metaclass` 对象的 `isa` 字段指向什么：是否指向 `metametaclass` 对象？事实证明，既然没有元类方法这个东西，那么就不需要元类对象，所以元类对象的 `isa` 字段只是指向自己，终止了这个循环。

[1]在实际的头文件中，你会看到这种结构在 Objective-C 2.0 中已经被废弃了，以使结构不透明。这使得 Objective-C 工程师可以修改布局，并为其添加/删除字段；而不是直接通过 `myClass->name` 访问结构的值，你只需使用 `class_getName()` 和 `class_setName()` 等函数代替。不过，在最底层，即使是 Objective-C 2.0 的 `objc_class` 结构的定义也会与此非常相似。

---

```bash
(gdb) p NSApp
	$2 = (struct objc_object *) 0x8039170 
(gdb) p *NSApp
	$3 = {
		isa = 0x15f8e0
	}
(gdb) p NSApp->isa
	$4 = (struct objc_class *) 0x15f8e0 
(gdb) p *NSApp->isa
	$5 = {
		isa = 0x160de0,
		super_class = 0x22d3ea0,
		name = 0x1322de "RWApplication",
		version = 0,
		info = 12206145,
		instance_size = 100, 
		ivars = 0x169720, 
		methodLists = 0x80391e0, 
		cache = 0x809d710, 
		protocols = 0x15b064
}
```

为了证明它，让我们深入到 gdb 中，偷看正在运行的应用程序中的 `NSApp` 全局变量。首先，你会看到 `NSApp` 确实只是一个指向 `objc_object` 结构的指针。请记住，在 Objective-C 中，所有的对象引用都是指针。对 `NSApp` 进行取值，显示它确实有一个 `isa` 字段，而且 `isa` 指向一个类对象的内存地址是 `0x15f8e0`。取消引用它（直接获取该内存地址的值？），你就可以看到关于这个类的细节，比如它的一个实例的大小，以及这个类的名字是什么。在这里，我们假定 `*NSApp->isa->isa` 是元类 `RWApplication` ，而 `*NSApp->isa->superclass` 是 `NSApplication` 类，它是 `RWApplication` 的子类。

---

```objc
@interface NSObject {
    Class isa;
}
@end

@interface MySubclass : NSObject {
    int i;
}
@end

@interface MySubsubclass : MySubclass {
    float f;
}
@end
```

对于子类来说，每个 `ivar` 都被简单地追加到它所继承的子类的末尾。在这个例子中，`MySubsubclass` 继承自 MySubclass，而 `MySubclass` 继承自 `NSObject`，所以，按照顺序，它首先包含 `NSObject` 中的所有 `ivars`，然后是 `MySubclass` 中的所有 `ivars`，然后是自己类定义的 `ivars`。

```objc
struct NSObject {
    Class isa;
}

struct MySubclass{
    Class isa;
    int i;
}

struct MySubsubclass {
    Class isa;
    int i;
    float f;
}
```



## 消息发送

鉴于我们现在知道了对象在内存中到底是什么样子，我们来谈谈有趣的东西：消息发送。

```objc
@implementation NSMutableString

- (void)appendString:(NSString *)aString
{
    // ...
}

@end
```

⬇️

```objc
void -[NSMutableString appendString:](id self, SEL _cmd, NSString *aString)
{
    // ...
}
```

首先，什么是方法？类比我们用 C 语言讨论 Objective-C 对象的方式，让我们用 C 语言来讨论 Objective-C 方法。

当你在代码中的 `@implementation...@end` 块之间写了一个方法定义时，编译器实际上会把它转换（transform）成一个标准的 C 函数（transform，哈哈，明白了吗？）。这个 C 函数唯一的两个不同之处是：
1. 它需要两个额外的参数 `--self` 和 `_cmd`；
2. 函数名中有一些通常在 C 函数中不允许使用的字符(`-`、`[`和`]`)。

除了第 2 点之外，它确实是一个完全标准的 C 函数，如果你能以某种方式得到一个指向该函数的函数指针，你就可以像调用其他标准 C 函数一样调用它。两个额外的参数用于你如何在方法内部访问 "隐藏的" `self` 和 `_cmd` 变量。(每个人都在使用 `self`，但是 `_cmd` 也存在，你可以用它来做一些非常有趣的事情。)

请注意，在 Objective-C 术语中，作为方法实现的实际 C 函数被称为 IMP。我们以后会用到这个。

```bash
% nm /usr/lib/libSystem.dylib | grep strcmp 00009eb0 T _strcmp

% nm Foundation.framework/Foundation | grep 'NSString compare' 
0002bbf0 t -[NSString compare:] 
0006c200 t -[NSString compare:options:] 
0000d490 t -[NSString compare:options:range:] 
0000d4e0 t -[NSString compare:options:range:locale:]
```

而且，为了证明这一点，如果你使用命令行工具 `nm` 将符号转储到二进制中，你可以看到 Objective-C 方法确实是标准的 C 函数，只是名称特殊。在 Mac OS X 上，C 函数的符号名前有一个 `_`，但相比之下，Objective-C 方法的 C 名称没有。

```objc
[string appendString:@” (that’s what she said)”];
```

⬇️

```objc
objc_msgSend(string, @selector(appendString:), @"(that’s what she said)");
```

现在，当你使用 [...] 语法向一个对象发送消息时会发生什么？编译器实际上将其转换为对一个名为 `objc_msgSend()` 的函数的调用，该函数是 Objective-C 运行时的一部分[1]。 `objc_msgSend()` 至少需要两个参数：要发送消息的对象（Objective-C 术语中的**接收器**（receiver）），以及一个叫做**选择器**（selector）的东西，它是"方法名"的术语。

从概念上讲，你可以把选择器看作是一个简单的 C 语言字符串。事实上，选择器就是一个 C 字符串：它和 C 字符串 `NUL-terminated char*` 指针具有相同的内存模型，就像我们之前提及的 `IntContainer` 结构和一个简单的 `int` 具有相同的内存模型一样。选择器和 C 字符串之间唯一的区别是，**Objective-C 运行时确保每个选择器的唯一实例--即每个方法名的唯一实例--在整个内存地址空间中只有一个**。如果你只是简单地使用 `char*s` 来表示方法名，你可能会有两个 `char*s`，它们的值都是 "`appendString:`"，但驻留在不同的内存地址上（例如 `0xdeadbeef` 和 `0xcafebabe`）。这就意味着，测试一个方法名是否等于另一个方法名，需要用 `strcmp()` 进行一个字符一个字符的比较，当你想简单地执行一个函数调用时，速度慢得令人捧腹。通过确保每个选择器只有一个唯一的内存地址，选择器的相等性可以简单地通过指针比较来完成，这样就快多了。因此，选择器的类型（SEL）与 `char*` 不同，需要使用 `sel_registerName()` 函数将 C 字符串 "转换 "为选择器。

注意，`objc_msgSend()` 是一个 `varargs` 函数，其中前两个之后的其余参数都是消息参数。

[1] Objective-C 运行时只是一个名为 objc 的 C 函数库，你可以像链接其他 C 函数库一样链接到它，它位于 `/usr/lib/libobjc.dylib`。

---

```objc
IMP class_getMethodImplementation(Class cls, SEL name);

id objc_msgSend(id receiver, SEL name, arguments…) 
{
	IMP function = class_getMethodImplementation(receiver->isa, name);
	return function(arguments); }
}
```
⬆️

```objc
objc_msgSend(string, @selector(appendString:), @” (that’s what she said)”);
```

那么，`objc_msgSend()` 的实现会是怎样的呢？从概念上看，它可能类似于这样，不过在实际操作中，它是 hand-rolled，并且高度优化的汇编，因为它的函数执行需要非常快。Objective-C 运行时有一个名为`class_getMethodImplementation()` 的方法，给定一个类对象和一个选择器，返回该方法的 IMP--C 函数实现。它的做法是：简单地查询该类的方法列表，找到与你传递给它的选择器相匹配的选择器，然后返回与选择器相匹配的 IMP 指针。现在你有了一个 IMP 指针，而该 IMP 指针实际上只是一个 C 函数指针，你可以像调用其他 C 函数一样调用它。所以，`objc_msgSend()` 所做的就是通过 `isa` 参数获取接收器的类对象，找到选择器方法所对应的 IMP 指针，然后砰，我们就可以发送消息了。这就是它的样子：没有所谓的黑魔法。

## 动态和反射（Dynamic & Reﬂective）

Objective-C 的内存模型和消息发送语义有一些非常有趣的结果。首先，由于**类对象包含了它实现了哪些方法，类的名称是什么等所有信息**，而且这些信息可以通过 Objective-C 运行时 API 来访问，所以 Objective-C 语言是**反射式**（reflective）的（也就是内省式、自省式的），这意味着**你可以查询出类层次结构有关的信息**。例如，可以获得整个类层次结构的信息，并找出总共有多少个方法。

其次，由于可以使用 API 来修改类对象，而且由于消息发送是通过一个 C 函数来隧道（链接）的，所以该语言是高度**动态**（dynamic）的。你可以做一些事情，比如**在运行时增加类，甚至用自己的方法交换预先定义的方法实现**。Objective-C 的消息传递还允许对象有 "第二次机会" 来响应消息：如果你给一个对象发送了一条它不理解的消息，运行时就会调用一个名为 `forwardInvocation` 的方法。(在Objective-C 2.0 中还有`+resolveInstanceMethod:` 方法)，该方法被传递给你想要发送的消息信息，然后对象就可以对该消息为所欲为，比如转发到另一个对象上。

这些功能使 Objective-C 与 Perl、Python、Ruby、PHP 和 JavaScript 等比较著名的动态语言和脚本语言处于同一水平：主要区别在于 Objective-C 是编译成本地代码的。请不要在这里说 JIT 引擎的迂腐。不过，它和其他脚本语言一样，几乎是动态的。相比之下，C++ 基本没有反省（只有 RTTI），也没有动态性，而 Java 除了没有等价于 `-forwardInvocation:` 的反省 API 之外，和 Objective-C 类似（反省&动态）。COM & CORBA 差不多就是 C++，上面铲了类似 Objective-C 的特性，才有了一些动态性&反射性，只是它的屁股很丑，很烂。

![](https://upload-images.jianshu.io/upload_images/2648731-e933c8820e063028.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

你可以通过查看 Apple 的 [Objective-C 2.0 Runtime Reference](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Introduction/Introduction.html) 文档来获得更多关于 Objective-C 运行时的信息。正如你在幻灯片左侧所看到的那样，有大量的 C 函数可以让你调用来窥探和捅破运行时。



## RMModelObject

```objc
@interface MyBlogEntry : NSObject { 
  NSString* _title; 
  NSCalendarDate* _postedDate; … 
}

@property (copy) NSString* title; 
@property (copy) NSCalendarDate* postedDate; 
@property (copy) NSAttributedString* bodyText; 
@property (copy) NSArray* tagNames; 
@property (copy) NSArray* categoryNames; 
@property BOOL isDraft;

@end

- (NSString*)title; (void)setTitle:(NSString*)value; …
- (BOOL)isEqual:(id)other;
- (id)copyWithZone:(NSZone*)zone;
- (id)initWithCoder:(NSCoder*)decoder;
- (void)encodeWithCoder:(NSCoder*)encoder;
- (void)dealloc;
```

除了仅仅是酷，拥有一个高度动态的运行时其实也是很有用的。Ruby 以大量使用元编程技术来做 "很酷的东西" 而闻名，比如 ActiveRecord。在这里，我们在一个名为 `RMModelObject` 的类中利用了 Objective-C 的所有反射和动态特性。它是为编写简单的模型类而设计的。

例如，如果你正在编写一个网站建设程序，可能会或不押韵 WapidReaver，想对一个博客条目进行建模，你可能会有一些属性，如 `title`、`postedDate`、bodyText、条目的 `tagNames` 等。然而，你意识到你必须：
1. 为属性声明实例变量作为后备存储；
2. 为所有属性写 Getter 方法；
3. 实现 `-isEqual:` 和 `-hash` 方法；
4. 实现 `-copyWithZone:` 方法；
5. 实现 `-initWithCoder:` 和 `encodeWithCoder:` 以支持 NSCoding 编码；
6. 实现 `dealloc` 方法来释放所有的变量属性。

现在没那么好玩了吧？

```objc
@interface MyBlogEntry : RMModelObject {

}

@property (copy) NSString* title; 
@property (copy) NSCalendarDate* postedDate; 
@property (copy) NSAttributedString* bodyText; 
@property (copy) NSArray* tagNames; 
@property (copy) NSArray* categoryNames; 
@property BOOL isDraft;

@end

@implementation MyBlogEntry

@dynamic title, postedDate, bodyText, tagNames, categoryNames, isDraft;

@end
```

在 `RMModelObject` 中，你所要做的就是在你的 `@implementation` 上下文中把你的属性声明为 `@dynamic` 类型，只有这样才能使编译器警告沉默。

就是这样，RMModelObject 自动完成了剩下的工作。 它为你动态地创建一个新的类，为该类添加实例变量和访问器方法，并实现其他所需的方法，如 `-initWithCoder:`/`-encodeWithCoder:`。如果你想要一个很好的用例和一些如何使用 Objective-C 2.0 运行时 API 的示例代码，`RMModelObject` 可能是一个很好的案例研究。

其他利用 Objective-C 2.0 运行时的很酷的项目是 [Objective-C port of ActiveRecord](https://github.com/yfactorial/objectiveresource) 和 [SQLitePersistentObjects](https://code.google.com/archive/p/sqlitepersistentobjects/)，看看他们吧。

## 更高阶的信息传递

现在，我终于可以说我最喜欢的两个词了， “higher order”…

```objc
@implementation NSArray (Mapping) 

// Usage:
// NSArray* result = [myArray map:@selector(uppercaseString:)]; 
- (NSArray*)map:(SEL)selector {
	NSMutableArray* mappedArray = [NSMutableArray array];

  // 如果数组中的项能响应 selector 消息，则返回该项
	for(id object in self) {
    id mappedObject = [object performSelector:selector];
    [mappedArray addObject:mappedObject]; 
  } 
  
  return mappedArray;
} 

@end
```

这是`NSArray` 中一个名为 `map`的新方法。它很简单：它接收一个选择器，对数组中的每一项调用该选择器，然后返回一个新的 NSArray，其中包含调用的结果。代码在这里并不重要，重要的是它的一般概念。如果你是一个 Smalltalk，在 Smalltalk 术语中，这通常被称为 collect。("map" 来自函数式编程世界。)

```objc
NSArray* result = [myArray map:@selector(uppercaseString:)]; 

                  vs

NSArray *result = [[myArray map] uppercaseString];

id tramponline = [myArrap map];
NSArray *result = [trampoline uppercaseString];

// map creates a proxy object (trampoline)
// trampoline receives uppercaseString message
// trampoline sends uppercaseString to each object in the original array and collects the results
// trampoline returns new array
```

然而，`-map:` 方法的语法看起来相当啰嗦和笨重。与其写 "`[myArray map:@selector(uppercaseString:)]`"，如果我们可以写更短的"`[[myArray map] uppercaseString]`"，会怎么样呢？

Marcel Weiher 和 cocoadev.com 网站上的一群有进取心的 Objective-C 开发者将这种技术称为 "高阶消息"。关于它的工作原理的细节在幻灯片上，希望不用再解释就足够清楚了。

```objc
// Threading:
[[earth inBackground] computeAnswerToUniverse]; 
[[window inMainThread] display];

// Threading:
[myArray inPlaceMergeSort]; // synchronous 
[[myArray future] inPlaceMergeSort]; // asynchronous

// Parallel Map:
[[myArray parallelMap] uppercaseString];

// Control Flow:
[[myArray logAndIgnoreExceptions] stupidMethod];
```

在通常的函数式编程风格的 map/fold（neé reduce）/filter 集合函数之外，高阶消息传递有很多用途。这里展示了一些例子。

关于 [Higher Order Messaging](http%3A%2F%2Fwww.metaobject.com%2Fpapers%2FHigher_Order_Messaging_OOPSLA_2005.pdf) 的更多信息，只需在 Google 上搜索，你就可以找到 Marcel Weiher 和 Stéphane Ducasse 提交给 OOPSLA [employee salary] 的论文，是 2005 年关于它的现状分析。它比我更好地解释了本文中 HOM 与更传统的高阶函数之间的细微差别的贡献。[employee setSalary:10000]。





