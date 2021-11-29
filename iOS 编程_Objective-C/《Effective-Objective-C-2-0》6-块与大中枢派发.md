![](http://upload-images.jianshu.io/upload_images/2648731-87bda0c1063653a9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 第6章 块与大中枢派发

> block、块、Block 块、Block 对象在大多数 Objective-C 文档中语义相同。

 **block** 是一种可在 C、C++ 及 Objective-C 代码中使用的“词法闭包"（lexical closure)，借由此机制，开发者可将代码像对象一样传递，令其在不同环境（context）下运行。还有个关键的地方是，在定义 block 的范围内，它可以访问到其中的全部变量。

**GCD** 是一种与 block 有关的技术，它提供了对线程的抽象，而这种抽象则基于“派发队列” (dispatch queue) 。开发者可将块排入队列中，由GCD负责处理所有调度事宜。GCD会根据系统资源情况，适时地创建、复用、摧毁后台线程（background thread)，以便处理每个队列。 此外，使用GCD还可以方便地完成常见编程任务，比如编写 “只执行一次的线程安全代码” (thread-safe single-code execution)，或者根据可用的系统资源来并发执行多个操作。



### 第37条：理解“块”这一概念

#### 块的基础知识

![](http://upload-images.jianshu.io/upload_images/2648731-36c35369377855f8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



简单的 block：

```objective-c
^{
    //Block implementation here
};
```

block 语法结构：

```objective-c
typedef returnType(^name)(arguments);
```

定义一个名为 someBlock 的变量：

```objective-c
void (^someBlock)() = ^{
    //Block implementation here
};
```

block 的强大之处在于，在 block 内部可以访问 block 外部变量：

```objective-c
// 将变量声明为 __block 之后才可以在 Block 内部对此变量进行修改
__block int additional = 5;
// 声明Block块
int (^addBlock)(int a, int b) = ^(int a, int b) {
    additional = 10;
    return a + b + additional;
};
// 使用Block块
int add = addBlock(2, 5);
```

* 如果block所捕获的变量是对象类型，那么就会自动保留它。系统在释放这个块的时候，也会将其一并释放。
* **block本身可视为对象**，它也有引用计数。
* 如果将block定义在 Objective-C 类的实例方法中，那么除了可以访问类的所有实例变童之外，还可以使用 `self` 变量。block总能修改实例变量，所以在声明时无须加 `_block`。不过，如果通过读写操作捕获了实例变量，那么也会自动把 `self` 变量一并捕获，因为实例变量是与`self`所指代的实例关联在一起的。


  ```objective-c
  - (void)anInstanceMethod {
      //...
      void (^someBlock)() = ^{
          _anInstanceVariable = @"Something";
          NSLog(@"_anInstanceVariable = %@",_anInstanceVariable);
      };
      //...
  }
  ```

* 在block中，直接访问实例变量和通过 `self` 来访问该实例变量是等效的。
*  `self` 也是个对象，因而 block 在捕获它时也会将其保留。如果 `self` 所指代的那个对象同时也保留了块，那么这种情况通常就会导致**引用循环**。

#### 块的内部结构

block对象在栈中的结构：

![](http://upload-images.jianshu.io/upload_images/2648731-b34a08b1ea34a3de.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对应的结构体定义：

```objective-c
struct Block_descriptor {
    unsigned long int reserved;
    unsigned long int size;
    void (*copy)(void *dst, void *src);
    void (*dispose)(void *);
};

struct Block_layout {
    void *isa;
    int flags;
    int reserved;
    void (*invoke)(void *, ...);
    struct Block_descriptor *descriptor;
    /* Imported variables. */
};
```

- `isa`指针：指向表明该block类型的类。
- `flags`：按bit位表示一些block的附加信息，比如判断block类型、判断block引用计数、判断block是否需要执行辅助函数等。
- `reserved`：保留变量，我的理解是表示block内部的变量数。
- `invoke`：函数指针，指向具体的block实现的函数调用地址。
- `descriptor`：block的附加描述信息，比如保留变量数、block对象的大小、进行`copy`或`dispose`的辅助函数指针。
- `variables`：即捕获到的变量，因为block有闭包性，所以可以访问block外部的局部变量。这些`variables`就是复制到结构体中的外部局部变量或变量的地址。



#### 全局块、栈块及堆块

* 虽然 block 是对象，但是其所占的内存区域是分配在**栈**中的。这就是说，块只在定义它的那个范围内有效。
* 给 block 对象发送 `copy` 消息可以把 Block 从**栈**复制到**堆**。拷贝后的块，可以在定义它的那个范围之外使用。

```objective-c
void (^block)();
if (/** condition */) {
    block = [^{
        NSLog(@"Block A");
    } copy];
}else {
    block = [^{
        NSLog(@"Block B");
    } copy];
}
block();
```

* **全局块**（global block）不会捕捉任何状态（比如外围的变量等)，运行时也无须有状态来参与。块所使用的整个内存区域，在编译期已经完全确定了，因此，全局块可以声明在全局内存里，而不需要在每次用到的时候于栈中创建。另外，全局块的拷贝操作是个空操作，因为全局块决不可能为系统所回收。这种块实际上相当于单例。下面就是个全局块：

```
void (^block) () = ^{
	NSLog(@"This is a block");
)；
```

- 全局的静态 block： `_NSConcreteGlobalBlock`类型的block要么是空block，要么是不访问任何外部变量的block。它既不在栈中，也不在堆中，我理解为它可能在内存的全局区。
- 保存在栈中的block：`_NSConcreteStackBlock`类型的block有闭包行为，也就是有访问外部变量，并且该block只且只有有一次执行，因为栈中的空间是可重复使用的，所以当栈中的block执行一次之后就被清除出栈了，所以无法多次使用。
- 保存在堆中的block：`_NSConcreteMallocBlock`类型的block有闭包行为，并且该block需要被多次执行。当需要多次执行时，就会把该block从栈中复制到堆中，供以多次执行。

#### 要点

- Clang 是开发 Mac OS X 及 iOS 程序所用的编译器。
- block 块是 C、C++、Objective-C 中的词法闭包。
- block 块可以接收参数，也可以有返回值。
- block 块可以分配在栈或堆上，也可以是全局的。分配在栈上的 block 块可拷贝到堆里，这样的话，就和标准的 Objective-C 对象一样，具备引用计数了。




### 第38条：为常用的块类型创建 typedef

代码块便捷写法：`typedefBlock`

```objective-c
typedef <#returnType#>(^<#name#>)(<#arguments#>);
```

示例一：

```objective-c
// 定义 Block 块
typedef int(^EOCSomeBlock)(BOOL flag, int value);

// 使用 Block 块
EOCSomeBlock blcok = ^(BOOL flag, int value) {
    // Implementation
};
```

示例二：

```objective-c
typedef void(^EOCCompletionHandler)(NSData *data, NSError *error);

// 方法使用 Block 块作为参数
- (void)startingWithCompletionHandler:(EOCCompletionHandler)completion;
```

使用 `typedef` 类型定义还便于重构 block 的类型签名。

```objective-c
// 新增一个参数，用以表示完成任务所花的时间
typedef void(^EOCCompletionHandler)
        (NSData *data, NSTimeInterval duration, NSError *error);
```

#### 要点

* 以 `typedef` 重新定义 block 类型，可以令 block 变量用起来更加简单。
* 定义新类型时应遵循现有的命名习惯，勿使其名称与别的的类型相冲突。
* 不妨为同一个 block 签名定义多个类型别名。如果要重构的代码使用了 block 类型的某个别名，那么只需修改相应的 `typedef` 中的 block 签名即可，无需改动其他 `typedef`。




### 第39条：用 handler 块降低代码分散程度

为用户界面编码时，一种常用的范式就是“异步执行任务”（perform task asynchronously)。这种范式的好处在于：处理用户界面的显示及触摸操作所用的线程，不会因为要执行I/O或网络通信这类耗时的任务而阻塞。这个线程通常称为主线程（main thread)。

异步方法在执行完任务之后，需要以某种手段通知相关代码。实现此功能有很多办法。常用的技巧是设计一个委托协议（参见第23条)，令关注此事件的对象遵从该协议。对象成为delegate之后，就可以在相关事件发生时（例如某个异步任务执行完毕时）得到通知了。

**Delegate** 模式：

```objective-c
#import <Foundation/Foundation.h>
@class EOCNetworkFetcher;

@protocol EOCNetworkFetcherDelegate <NSObject>
- (void)networkFetcher:(EOCNetworkFetcher *)fetcher
        didFinishWithData:(NSData *)data;
@end

@interface EOCNetworkFetcher : NSObject
@property (nonatomic, weak) id<EOCNetworkFetcherDelegate> delegate;
- (instancetype)initWithURL:(NSURL *)url;
- (void)start;
@end
```

其他类则可以像下面这样使用此类所提供的 API 并遵守实现相应的 delegate 协议：
```objective-c
- (void)fetchFooData {
    NSURL *url = [[NSURL alloc] initWithString:
                  @"https://www.pinterest.com"];
    EOCNetworkFetcher *fetcher =
        [[EOCNetworkFetcher alloc] initWithURL:url];
    fetcher.delegate = self;
    [fetcher start];
}

- (void)networkFetcher:(EOCNetworkFetcher *)fetcher didFinishWithData:(NSData *)data {
    // deal with data
}
```

**block** 模式：

```objective-c
#import <Foundation/Foundation.h>

typedef void(^EOCNetworkFetcherCompletionHandler)(NSData *data);

@interface EOCNetworkFetcher : NSObject
- (instancetype)initWithURL:(NSURL *)url;
- (void)startWithCompletionHandler:(EOCNetworkFetcherCompletionHandler)handler;
@end
```

其他类获取数据：
```objective-c
- (void)fetchFooData {
    NSURL *url = [[NSURL alloc] initWithString:
                  @"https://www.pinterest.com"];
    EOCNetworkFetcher *fetcher =
        [[EOCNetworkFetcher alloc] initWithURL:url];
    // 调用 start 方法时直接以内联形式定义 Completion Handler。
    [fetcher startWithCompletionHandler:^(NSData *data) {
        // deal with data
    }];
}
```

相比于委托模式，block 模式可以使代码更清晰整洁、API 更紧致、逻辑关联性更强。

委托模式有个缺点：如果类要分别使用多个获取器下载不同数据，那么就得在 delegate 回调方法里根据传入的获取器参数来切换。

而使用 block 来写的好处是：无须保存获取器，也无须在回调方法里切换。每个 completion handler 的业务逻辑都是和相关的获取器对象一起来定义的。

#### 1. 分别用两个处理程序来处理操作失败和操作成功

这种 API 设计风格很好，由于成功和失败的情况要分别处理，所以调用此 API 的代码也就会按照逻辑，把应对成功和失败情况的代码分开来写，这将令代码更易读懂。而且，若有需要，还可以把处理失败情况或成功情况所用的代码省略。

```objective-c
#import <Foundation/Foundation.h>

typedef void(^EOCNetworkFetcherCompletionHandler)(NSData *data);
typedef void(^EOCNetworkFetcherErrorHandler)(NSError *error);

@interface EOCNetworkFetcher : NSObject
- (instancetype)initWithURL:(NSURL *)url;
- (void)startWithCompletionHandler:
            (EOCNetworkFetcherCompletionHandler)handler
                    failureHandler:
            (EOCNetworkFetcherErrorHandler)failure;
@end

// 其他类使用：
- (void)fetchFooData {
    NSURL *url = [[NSURL alloc] initWithString:
                  @"https://www.pinterest.com"];
    EOCNetworkFetcher *fetcher =
        [[EOCNetworkFetcher alloc] initWithURL:url];
    [fetcher startWithCompletionHandler:^(NSData *data) {
        // deal with data
        
    } failureHandler:^(NSError *error) {
        // deal with error
        
    }];
}
```

##### 2. 把处理失败所需代码与处理成功所用代码，都封装到同一个 completion handle 块里

缺点：由于全部逻辑都写在一起，导致块代码冗长复杂。

优点：能把所有业务逻辑都放在一起使其更加灵活。例如，在传入错误信息时，可以把数据也传进来。有时数据正下载到一半，突然网络故障了。在这种情况下，可以把数据及相关的错误都回传给块。这样的话，completion handler 就能据此判断问题并适当处理了，而且还可利用已下载好的这部分数据做些事情。

总体来说，笔者建议使用同一个块来处理成功与失败情况。

```objective-c
#import <Foundation/Foundation.h>

typedef void(^EOCNetworkFetcherCompletionHandler)
                                (NSData *data, NSError *error);

@interface EOCNetworkFetcher : NSObject
- (instancetype)initWithURL:(NSURL *)url;
- (void)startWithCompletionHandler:
            (EOCNetworkFetcherCompletionHandler)handler;
@end

// 其他类使用：
- (void)fetchFooData {
    NSURL *url = [[NSURL alloc] initWithString:
                  @"https://www.pinterest.com"];
    EOCNetworkFetcher *fetcher =
        [[EOCNetworkFetcher alloc] initWithURL:url];
    // 需要在块代码中检测传人的error变量，并且要把所有逻辑代码都放在一处
    [fetcher startWithCompletionHandler:^(NSData *data, NSError *error) {
        if (error) {
            // handle failure
        }else {
            // handle success
        }
    }];
}
```

基于 handler 来设计API还有个原因，就是某些代码必须运行在特定的线程上，比如，Cocoa 与 Cocoa Touch 中的 UI 操作必须在主线程上执行：

```objective-c
// NSNotificationCenter
- (id <NSObject>)addObserverForName:(nullable NSNotificationName)name
                             object:(nullable id)obj
                              queue:(nullable NSOperationQueue *)queue
                         usingBlock:(void (^)(NSNotification *note))block;
```

#### 要点

* 在创建对象时，使用内联的 handler 块将相关业务逻辑一并声明。
* 在有多个实例需要监控时，如果采用委托模式，那么经常需要根据传入的对象来切换，而若改用 handler 块来实现，则可直接将 block 与相关对象放在一起。
* 设计 API 时如果用到了 handler 块，那么可以增加一个参数，使调用者可通过此参数来决定应该把 block 安排在哪个队列上执行。




### 第40条：用块引用其所属对象时不要出现保留环

使用 block 很容易导致循环引用：

```objective-c
//  EOCNetworkFetcher.h
#import <Foundation/Foundation.h>

typedef void(^EOCNetworkFetcherCompletionHandler)
                                (NSData *data);

@interface EOCNetworkFetcher : NSObject
@property (nonatomic, strong, readonly) NSURL *url;
- (instancetype)initWithURL:(NSURL *)url;
- (void)startWithCompletionHandler:
            (EOCNetworkFetcherCompletionHandler)completion;
@end

//  EOCNetworkFetcher.m
#import "EOCNetworkFetcher.h"

@interface EOCNetworkFetcher ()
@property (nonatomic, strong, readwrite) NSURL *url;
@property (nonatomic, copy)
            EOCNetworkFetcherCompletionHandler completionHandler;
@property (nonatomic, strong) NSData *downloadData;
@end

@implementation EOCNetworkFetcher

- (instancetype)initWithURL:(NSURL *)url {
    if (self = [super init]) {
        _url = url;
    }
    return self;
}

- (void)startWithCompletionHandler:
        (EOCNetworkFetcherCompletionHandler)completion {
    self.completionHandler = completion;
    // 开启网络请求
    // 设置 downloadData 属性
    // 下载完成后，以回调方式执行 Block
    [self p_requestCompleted];
}

// 为了能在下载完成后通过 p_requestCompleted 方法执行调用者所指定的块，
// 需要把 completion handler 保存到实例变量
// ❇️ _networkFetcher → _completionHandler
- (void)p_requestCompleted {
    if (_completionHandler) {
        _completionHandler(_downloadData);
    }
}

@end

// 某个类可能会创建以上网络数据获取器对象，并用其从URL中下载数据：
@implementation EOCDataModel {
    // ❇️ EOCDataModel → _networkFetcher
    EOCNetworkFetcher *_networkFetcher;
    NSData *_fetchedData;
}

- (void)downloadData {
    NSURL *url = [[NSURL alloc] initWithString:@"http://www.example.com"];
    _networkFetcher = [[EOCNetworkFetcher alloc] initWithURL:url];
    [_networkFetcher startWithCompletionHandler:^(NSData *data) {
        
        NSLog(@"Request URL %@ finished",_networkFetcher.url);
        // 因为 completion handler 块要设置 _fetchedData 实例变量,所以它必须捕获 self 变量，而 self 指向 EOCDataModel 类
        // ❇️ _completionHandler → EOCDataModel
        _fetchedData = data;
      
        // 💡等 completion handler 块执行完毕后，再打破保留环，以便使获取器对象在handler 块执行期间保持存活状态。
        _networkFetcher = nil;
    }];
}
```

问题：

* 在上例中，唯有 completion handler 运行过后，方能解除保留环。若是completion handler—直不运行，那么保留环就无法打破，于是内存就会泄漏。
* 如果 completion handler 块所引用的对象最终又引用了这个块本身，那么就会出现保留环。

```objective-c
- (void)downloadData {
    NSURL *url = [[NSURL alloc] initWithString:
                  @"http://www.example.com"];
    EOCNetworkFetcher *networkFetcher = [[EOCNetworkFetcher alloc] initWithURL:url];
    [networkFetcher startWithCompletionHandler:^(NSData *data) {
        // completionHandler → networkFetcher.url
        // networkFetcher → completionHandler
        NSLog(@"Request URL %@ finished",networkFetcher.url);
        _fetchedData = data;
    }];
}
```

解决方法：获取器对象之所以要把 completion handler 块保存在属性里面，其唯一目的就是想稍后使用这个块。可是，获取器一旦运行过 completion handler 之后，就没有必要再保留它了。所以，只需将 `p_requestCompleted` 方法按如下方式修改即可：

```objective-c
- (void)p_requestCompleted {
    if (_completionHandler) {
        _completionHandler(_downloadData);
    }
    self.completionHandler = nil; // ❇️
}
```

要想清楚块可能会捕获并保留哪些对 象。如果这些对象又直接或间接保留了块，那么就要考虑怎样在适当的时机解除保留环。

#### 要点

- 如果 block 所捕获的对象直接或间接的保留了 block 本身，那么就得当心循环引用的问题。
- 一定要找个适当的时机解除循环引用，而不能把责任推给 API 的调用者。




### 第41条：多用派发队列，少用同步锁

使用同步锁实现同步机制：

1. **@synchronized** 同步块：

   ```objective-c
   - (void)synchronizedMethod {
       @synchronized (self) {
           // ...
           // 根据给定对象，自动创建一个锁，并等待块中的代码执行完毕。
           // 滥用 @synchronized (self) 会降低代码效率
           
       }   // 执行到这段代码结尾处，释放锁。
   }
   ```

2. **NSRecursiveLock** 递归锁

   * 线程能够多次持有该锁， 而不会出现死锁（deadlock)现象。
   * 在极端情况下，同步块会导致死锁。


#### GCD：串行并发队列

```objective-c
@implementation HQLBlockObject {
    dispatch_queue_t _syncQueue;
}

    // 自定义并发队列
    // ⚠️注意到，文章中此处作者使用的是全局并发队列，而在 Ray Wenderlich 的GCD系列教程中使用的是自定义并发队列：
    // 原因在于：全局队列中还可能有其他任务正在执行，一旦加锁就会阻塞其他任务的正常执行，因此我们开辟一个新的自定义并发队列专门处理这个问题。
    _syncQueue = dispatch_queue_create("com.effectiveobjectivec.syncQueue", DISPATCH_QUEUE_CONCURRENT);

- (NSString *)someString {
    __block NSString *localSomeString;
    dispatch_sync(_syncQueue, ^{
        localSomeString = _someString;
    });
    return localSomeString;
}

- (void)setSomeString:(NSString *)someString {
    dispatch_barrier_async(_syncQueue, ^{
        _someString = someString;
    })
}
```

把设置操作与获取操作都安排在序列化的队列里执行，这样的话，所有针对属性的访问操作就都同步了。

#### 栅栏块

在队列中，栅栏块必须单独执行，不能与其他块并行。这只对**并发队列**有意义，因为串行队列中的块总是按顺序逐个来执行的。并发队列如果发现接下来要处理的块是个栅栏块 (barrier block) ，那么就一直要等当前所有并发块都执行完毕，才会单独执行这个栅栏块。待栅栏块执行过后，再按正常方式继续向下处理。

```objective-c
dispatch_barrier_sync(dispatch_queue_t  _Nonnull queue, ^{

})
dispatch_barrier_async(dispatch_queue_t  _Nonnull queue, ^{

})
```



```objective-c
- (NSString *)someString {
    // 后台执行
    _syncQueue = dispatch_get_global_queue(0, 0);
    
    __block NSString *localSomeString;
    // 同步后台队列
    dispatch_sync(_syncQueue, ^{
        localSomeString = _someString;
    });
    return localSomeString;
}

- (void)setSomeString:(NSString *)someString {
    // 异步栅栏队列
    dispatch_barrier_async(_syncQueue, ^{
       _someString = someString;
    });
}
```

![](http://upload-images.jianshu.io/upload_images/2648731-7c49a2d5fc91fe88.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




#### 要点

- 派发队列可用来表述同步语义，这种做法要比使用 @synchronized 块或 NSLock 对象更简单。
- 将同步与异步派发结合起来，可以实现与普通加锁机制一样的同步行为，而这么做却不会阻塞执行异步派发的线程。
- 使用同步队列及栅栏块，可以令同步行为更加高效。




### 第42条：多用GCD，少用 performSelector 系列方法

```objective-c
// 接受的参数就是要执行的选择子
- (id)performSelector:(SEL)aSelector;
```
该方法与直接调用选择子等效：

```objective-c
[self performSelector:@selector(selectorMethod)];
[self selectorMethod];
```

特点：编译器要等到运行期才能知道执行的选择子。可以在动态绑定之上再次使用动态绑定，因而可以实现出下面这种功能:

```objective-c
SEL selector;
if ( /** condition A */ ) {
    selector = @selector(foo);
}else if ( /** condition B */ ) {
    selector = @selector(bar);
}else {
    selector = @selector(baz);
}
[self performSelector:selector];
```

不推荐使用 `performSelector` 方法的原因：

⚠️ ARC 下会引起内存泄漏：编译器并不知道将要执行的选择子是什么，因此，也就不了解其方法签名及返回值，甚至连是否有返回值都不清楚，而由于编译器不知道方法名，所以就没办法运用ARC的内存管理规则来判定返回值是不是应该释放。鉴于此，ARC采用了比较谨慎的做法，就是不添加释放操作。然而这么做可能导致内存泄漏，因为方法在返回对象时可能已经将其保留了。

即使使用静态分析器，也很难侦测到随后的内存泄漏。

⚠️返回值只能是 void 或对象类型，`performSelector` 方法的返回值类型是 id。如果想返回整数或浮点数等类型的值，那么就需要执行一些复杂的转换操作了，而这种转换很容易出错。

如果返冋值的类型为C语言结构体，则不可以使用 `performSelector` 方法。

⚠️`performSelector` 方法还有诸多局限性，传入的参数类型必须是对象类型且最多只能接受2个参数、具备延后执行的方法无法处理带有2个参数的选择子。

下面是几个使用 Block 的替代方案：

#### 延后执行

✅推荐：

```objective-c
double delayInSeconds = 5.0;
dispatch_time_t popTime = dispatch_time(DISPATCH_TIME_NOW,
                                        delayInSeconds *NSEC_PER_SEC);
dispatch_after(popTime, dispatch_get_main_queue(), ^{
    [self doSomething];
});
```

❎反对：

```
[self performSelector:@selector(doSomething)
             withObject:nil
             afterDelay:5.0];
```

#### 主线程执行

✅推荐：

```objective-c
// 同步主线程（waitUntilDone:YES）
dispatch_sync(dispatch_get_main_queue(), ^{
    [self doSomething];
});

// 异步主线程（waitUntilDone:NO）
dispatch_async(dispatch_get_main_queue(), ^{
    [self doSomething];
});
```

❎反对：

```objective-c
[self performSelectorOnMainThread:@selector(doSomething)
                       withObject:nil
                    waitUntilDone:NO];
```

#### 要点

* `performSelector` 系列方法在内存管理方面容易有疏失。它无法确定将要执行的 selector 具体是什么，因而ARC编译器就无法插入适当的内存管理方法。
* `performSelector`  系列方法所能处理的 selector 太过局限了，selector 的返回值类型及发送给方法的参数个数都受到限制。
* 如果想把任务放在另一个线程上执行，那么最好不要用 `performSelector` 系列方法而是应该把任务封装到 block 里然后调用 GCD 机制的相关方法来实现。




### 第43条：掌握 GCD 及操作队列的使用时机

#### GCD & NSOperation

* GCD 是纯 C 的 API，而 NSOperation（操作队列）则是 Objective-C 的对象。

* 在GCD中，任务用 Block 来表示，而 Block 是个轻量级数据结构（参见第37条）。与之相反，“操作"（operation) 则是个更为重量级的 Objective-C 对象

* 用 NSOperationQueue 类的 `addOperationWithBlock:` 方法搭配 NSBlockOperation 类来使用操作队列，其语法与纯 GCD 方式非常类似。使用 NSOperation 及 NSOperationQueue 的好处如下：

  * 取消某个操作。运行任务之前, 可以在 NSOperation 对象上调用 `cancel` 方法取消任务执行。
  * 指定操作间的依赖关系。
  * 通过键值观测机制（简称 KVO）监控 NSOperation 对象的属性。
  * 指定操作的优先级。操作的优先级表示此操作与队列中其他操作之间的优先关 系。优先级高的操作先执行，优先级低的后执行。
  * 重用 NSOperation 对象。

* 操作队列有很多地方胜过派发队列。操作队列提供了多种预设的执行任务的方式，开发者可以直接使用。

* 有一个API选用了操作队列而非派发队列，这就是 NSNotificationCemer，开发者可通过其中的方法来注册监听器，以便在发生相关事件时得到通知，而这个方法接受的参数是块，不是选择子。

  ```objective-c
  - (id <NSObject>)addObserverForName:(nullable NSNotificationName)name
                               object:(nullable id)obj
                                queue:(nullable NSOperationQueue *)queue
                           usingBlock:(void (^)(NSNotification *note))bloc;
  ```

* 应该尽可能选用高层API，只在确有必要时才求助于底层。笔者也同意这个说法，但我并不盲从。某些功能确实可以用高层的Objective-C方法来做，但这并不等于说它就一定比底层实现方案好。要想确定哪种方案更佳，最好还是测试一下性能。

#### 要点

- 在解决多线程与任务管理问题时，派发队列并非唯一方案。
- 操作队列提供了一套高层的 Objective-C API，能实现纯 GCD 所具备的绝大部分功能，而且还能完成一些更为复杂的操作，那些操作若改用GCD来实现，则需另外编写代码。




### 第44条：通过 Dispatch Group 机制，根据系统资源状况来执行任务

**dispatch group** 是 GCD 的一项特性，能够把任务分组。调用者可以等待这组任务执行完毕，也可以在提供回调函数之后继续往下执行，这组任务完成时，调用者会得到通知。这个功能有许多用途，其中最重要、最值得注意的用法，就是**把将要并发执行的多个任务合为一组**，于是调用者就可以知道这些任务何时才能全部执行完毕。比方说，可以把压缩一系列文件的任务表示成 **dispatch group**。

创建及使用 **dispatch group**：

```objective-c
dispatch_group_t group = dispatch_group_create();
dispatch_group_async(group, dispatch_get_global_queue(0, 0), ^{
    // 并行执行的线程一
});
dispatch_group_async(group, dispatch_get_global_queue(0, 0), ^{
    // 并行执行的线程二
});
dispatch_group_notify(group, dispatch_get_global_queue(0, 0), ^{
   // 汇总结果
});
```

给任务编组的两种方法：

```objective-c
// 1.比 dispatch_async 多一个参数，用于表示待执行的块所归属的组
dispatch_group_async(dispatch_group_t group,
                     dispatch_queue_t queue,
                     dispatch_block_t block);

// 2.dispatch_group_enter、dispatch_group_leave 需要成对使用
dispatch_group_enter(dispatch_group_t group); // 使分组里正要执行的任务数递增，
dispatch_group_leave(dispatch_group_t group); // 使分组里正要执行的任务数递减.
```

**dispatch_group_wait** 函数用于等待 **dispatch group** 执行完毕：

```objective-c
dispatch_group_wait(dispatch_group_t group, 
                    dispatch_time_t timeout);
```



示例：

令数组中的每个对象都执行某项任务，并且等待所有任务执行完毕:

```objective-c
dispatch_queue_t queue =
    dispatch_get_global_queue(QOS_CLASS_DEFAULT, 0);
dispatch_group_t group = dispatch_group_create();
for (id object in collection) {
    dispatch_group_async(group, queue, ^{
        [object performTask];
    });
}

dispatch_group_wait(group, DISPATCH_TIME_FOREVER);
// 任务执行完毕后继续操作

// 若当前线程不应阻塞，则可以使用 dispatch_group_notify 代替 dispatch_group_wait
dispatch_queue_t notifyQueue = dispatch_get_main_queue();
dispatch_group_notify(group, notifyQueue, ^{
    // 任务执行完毕后继续操作
});
```

开发者未必总需要使用 **dispatch group**。有时候采用单个队列搭配标准的异步派发，也可实现同样效果：

```
// 自定义串行队列
dispatch_queue_t queue =
    dispatch_queue_create("com.effecitveobjectivec.queue", NULL);
for (id object in collection) {
    dispatch_async(queue, ^{
        [object performTask];
    });
}
dispatch_async(queue, ^{
    // 任务执行完毕后继续操作
});
```

> 根据系统资源状况来执行任务：
>
> 为了执行队列中的块，**GCD会在适当的时机自动创建新线程或复用旧线程。**如果使用并发队列，那么其中有可能会有多个线程，这也就意味着多个块可以并发执行。在并发队列中，执行任务所用的并发线程数量，取决于各种因素，而GCD主要是根据系统资源状况来判定这些因素的。假如CPU有多个核心，并且队列中有大量任务等待执行，那么GCD就可能会给该队列配备多个线程。通过dispatch group所提供的这种简便方式，既可以并发执行一系列给定的任务，又能在全部任务结束时得到通知。由于GCD 有并发队列机制，所以能够根据可用的系统资源状况来并发执行任务。而开发者则可以专注于业务逻辑代码，无须再为了处理并发任务而编写复杂的调度器。



遍历某个 collection ，并在其每个元素上执行任务，而这也可以用另外一个 GCD 函数来实现：

```objective-c
dispatch_apply(size_t iterations,
               dispatch_queue_t queue,
               void (^block)(size_t));
```

此函数会将块反复执行一定的次数，每次传给块的参数值都会递增，从0开始，直至 “ iterations-1 ”。

```objective-c
// 自定义串行队列
dispatch_queue_t queue =
    dispatch_queue_create("com.effecitveobjectivec.queue", NULL);    
dispatch_apply(10, queue, ^(size_t) {
    // Perform Task：0~9
});
```

与 for 循环不同的是， **dispatch_apply** 所用的队列可以是并发队列。但是 **dispatch_apply**  会持续阻塞，直到所有任务都执行完毕为止。

假如把块派给了当前队列（或者体系中高于当前队列的某个串行队列)，就将导致死锁。若想在后台执行任务，则应使用 **dispatch group**。

#### 要点

- 一系列任务可归入一个 **dispatch group** 之中。开发者可以在这组任务执行完毕时获得通知。
- 通过 **dispatch group**，可以在并发式派发队列里同时执行多项任务。此时GCD会根据系统资源状况来调度这些并发执行的任务。开发者若自己来实现此功能，则需编写大量代码。




### 第45条：使用 dispatch_once 来执行只需运行一次的线程安全代码

单例模式：

```objective-c
+ (instancetype)sharedInstance {
    static EOCClass *sharedInstance = nil;
    // 为保证线程安全，将创建单例实例的代码包裹在同步块里。
    @synchronized (self) {
        if (!sharedInstance) {
            sharedInstance = [[self alloc] init];
        }
    }
    return sharedInstance;
}
```

**dispatch_once** 函数：

```objective-c
_dispatch_once(dispatch_once_t *predicate,
		       dispatch_block_t block)
```

该函数保证相关的块必定会执行，且仅执行一次。首次调用该函数时，必然会执行块中的代码，最重要的一点在于，**此操作完全是线程安全的**。

```objective-c
+ (instancetype)sharedInstance {
    static EOCClass *sharedInstance = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        sharedInstance = [[self alloc] init];
    });
    return sharedInstance;
}
```

* 使用 **dispatch_once** 可以简化代码并且彻底保证线程安全。
* 由于每次调用时都必须使用完全相同的标记, 所以标记要声明成 `static`。把该变量定义在`static` 作用域中，可以保证编译器在每次执行 `sharedlnstance` 方法时都会复用这个变量，而不会创建新变量。
* 此外，**dispatch_once** 更高效。 **@synchronized** 使用了重量级的同步机制，每次运行代码前都要获取锁。而 **dispatch_once**  采用原子访问（atomic access）来查询标记，以判断其所对应的代码原来是否已经执行过。

#### 要点

- 经常需要编写只需执行一次的线程安全代码。通过 GCD 所提供的 **dispatch_once** 函数，很容易就能实现此功能。
- 标记应该声明在 `static` 或 `global` 作用域中，这样的话，在把只需执行一次的 block 传给**dispatch_once** 函数时，传进去的标记也是相同的。



### 第46条：不要使用 dispatch_get_current_queue

```objective-c
// 此函数返回当前正在执行代码的队列。
// This function is deprecated and will be removed in a future release.
dispatch_get_current_queue(void);
```

Tips：iOS 系统从 6.0 版本起，已经正式弃用此函数了。

#### 要点

* dispatch_get_current_queue 函数的行为常常与开发者所预期的不同。此函数已废弃，只应做调试之用。
* 由于派发队列是按层级来组织的，所以无法单用某个队列对象来描述当前队列这一概念。
* dispatch_get_current_queue 函数用于解决由不可重入的代码所引发的死锁，然而能用此函数解决的问题，通常也能改用“队列特定数据”来解决。




### 参考

* [Objective-C中的Block @程序员说](http://www.devtalking.com/articles/you-should-know-block/)
* [Objective-C中的Block @onevcat](https://onevcat.com/2011/11/objc-block/)
* [使用GCD @唐巧](http://blog.devtang.com/2012/02/22/use-gcd/)
* [起底多线程同步锁(iOS)](http://www.cocoachina.com/ios/20160129/15170.html)
