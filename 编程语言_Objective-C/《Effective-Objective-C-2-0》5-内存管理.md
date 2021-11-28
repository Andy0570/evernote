![](http://upload-images.jianshu.io/upload_images/2648731-bc415805a22122ac.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 第5章 内存管理

### 第29条：理解引用计数

* Objective-C 语言使用引用计数来管理内存，每个对象都有一个可以递增或递减的计数器。

* 使用 ARC 时，所有与引用计数有关的方法都无法编译。

  ```objective-c
  - (instancetype)retain OBJC_ARC_UNAVAILABLE;
  - (oneway void)release OBJC_ARC_UNAVAILABLE;
  - (instancetype)autorelease OBJC_ARC_UNAVAILABLE;
  - (NSUInteger)retainCount OBJC_ARC_UNAVAILABLE;

  - (struct _NSZone *)zone OBJC_ARC_UNAVAILABLE;
  ```

#### 引用计数工作原理

* 对象有个**计数器**，表示当前有多少个事物想令此对象继续存活。这在 Objective-C 中叫做 **保留计数**（retain count）或者 **引用计数**（reference count）。
* 对象创建出来时，其引用计数**至少**为1。
* 不应该说引用计数一定是某个值，而应该说执行的操作递增/递减了该计数。
* 在 iOS 应用程序中，根对象为 **UIApplication** 单例对象。

**NSObject** 协议声明了三个方法用于操作计数器：

1. `retain` :递增引用计数。
2. `release` : 递减引用计数。
3. `autorelease`: 待稍后清理“自动释放池”（autorelease pool）时，再递减引用计数。



![](http://upload-images.jianshu.io/upload_images/2648731-bc5fcaab7de857c0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



```objective-c
NSMutableArray *array = [[NSMutableArray alloc] init];

NSNumber *number = [[NSNumber alloc] initWithInt:1337]; // retainCount >= 1
[array addObject:number]; // retainCount >= 2
[number release]; // retainCount >= 1
number = nil;  // 避免悬挂指针（指向无效对象的指针）

// do somethind with "array"
[array release];
```

#### 属性存取方法中的内存管理

```objective-c
// strong 属性：
- (void)setFoo:(id)foo {
    // 顺序很重要，如果先 release，则此对象将被永久回收
    [foo retain];   // 先保留新值。
    [_foo release]; // 再释放旧值。
    _foo = foo;     // 更新实例变量，令其指向新值。
}
```

#### 自动释放池

* `autorelease`  方法会在稍后递减计数，通常是在下一次 **事件循环**（event loop）时递减。
* `autorelease`  能延长对象的生命周期，保证对象在 **跨越方法调用边界** 后依然可以存活一段时间。

示例：

```objective-c
- (NSString *)stringValue {
    NSString *str = [[NSString alloc]
                        initWithFormat:@"I am this:%@",self]; // retainCount >= 1
    return str; // retainCount >= 2
}
```

此处需要使用 `autorelease` 释放对象：

```objective-c
- (NSString *)stringValue {
    NSString *str = [[NSString alloc]
                        initWithFormat:@"I am this:%@",self];
    return [str autorelease];
}
```

#### 引用循环/保留环

引用循环：呈环状相互引用的多个对象，内存无法正常释放，导致内存泄漏。

解决方法：弱引用（weak reference）、从外界命令循环中的某个对象不再保留另外一个对象。

![](http://upload-images.jianshu.io/upload_images/2648731-14d7c85c188b8660.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### 要点

- 引用计数机制通过可以递增递减的计数器来管理内存。对象创建好后，其保留计数至少为1。若保留计数为正，则对象继续存活。当保留计数降为0时，对象就被销毁了。
- 在对象生命期中，其余对象通过引用来保留或释放此对象。保留与释放操作分别会递增及递减保留计数。




### 第30条：以 ARC 简化引用计数

* ARC 原理：静态分析器（static analyzer）可以查明内存管理问题，也可以预先加入适当的保留或释放操作以避免内存管理问题。
* 在 ARC 中，不能直接调用 `retain`、`release`、`autorelease`、`dealloc` 方法。
* ARC 会直接调用底层 C 语言函数自动管理内存。
* ARC 会自动调用“保留”与“释放”方法。
* ARC 包含运行期组件。

#### 使用 ARC 时必须遵循的方法命名规则

若方法名以下列词语开头，则其返回的对象归调用者所有：

* alloc
* new
* copy
* mutableCopy

**归调用者所有**：【调用上述四种方法的那段代码】要负责【释放方法所返回的对象】。

#### 变量的内存管理语义

默认情况下，每个变量都是指向对象的强引用。

修饰符：

* `__strong`：默认语义，保留此值。
* `__unsafe_unretained`：不保留此值，这么做可能不安全，因为等到再次使用变量时，其对象可能已经回收了。
* `__weak`：不保留此值，但是变量可以安全使用，因为如果系统把这个对象回收了，那么变量也会自动清空。
* `__autoreleasing`：把对象“按引用传递”给方法时，使用这个特殊的修饰符。此值在方法返回时自动释放。

#### ARC 如何清理实例变量

* ARC 会借用 Objective-C++ 的一项特性来生成清理例程。

* ARC 会自动生成回收对象时所执行的代码。

* **CoreFoundation** 对象不归 ARC 管理，开发者必须适时调用 `CFRetain/CFRelease`。

  ```objective-c
  - (void)dealloc {
      CFRelease(coreFoundationObject);
      free(_heapAllocatedMemoryBlob);
  }
  ```

#### 覆写内存管理方法

在 ARC 环境下不要覆写 `release` 方法。

#### 要点

* 有 ARC 之后，程序员就无须担心内存管理问题了。使用 ARC 来编程，可省去类中的许多 "样板代码"。
* ARC 管理对象生命期的办法基本上就是：在合适的地方插入"保留"及"释放"操作。在 ARC 环境下，变量的内存管理语义可以通过**修饰符**指明，而原来则需要手工执行"保留"及"释放"操作。
* 由方法所返回的对象，其内存管理语义总是通过方法名来体现。ARC 将此确定为开发者必须遵守的规则。
* ARC 只负责管理 Objective-C 对象的内存。尤其要注意：**CoreFoundation** 对象不归 ARC 管理，开发者必须适时调用 `CFRetain/CFRelease`。




### 第31条：在 dealloc 方法中只释放引用并解除监听

* `dealloc`  方法主要用于释放对象所拥有的引用（即释放所有 Objective-C 对象），ARC 会通过自动生成的 .cxx_destruct 方法在 `dealloc` 中自动添加释放代码。
* 非 Objective-C 对象（如 **CoreFoundation** 对象）则必须手工释放。
* 还需要清理观测行为（observation behavior），注销通知。
* 开销较大或系统内稀缺资源不应在 `dealloc` 中释放，当应用程序用完资源后应及时释放，还有一个原因是：系统并不保证每个创建出来的对象的 `dealloc` 都会执行。

```objective-c
- (void)dealloc {
    CFRelease(coreFoundationObject);
    [[NSNotificationCenter defaultCenter] removeObserver:self];
}
```

* 如果对象管理着某些资源，那么在 `dealloc` 方法中也要调用“清理方法”，以防开发者忘记清理这些资源。

  ```objective-c
  - (void)close {
      /** clean up resoureces */
      _close = YES;
  }

  - (void)dealloc {
      if (!_close) {
          NSLog(@"ERROR:close was not called before dealloc");
          [self close];
      }
  }
  ```

#### 要点

* 在 `dealloc` 方法里，应该做的事情就是释放指向其它对象的引用，并取消原来订阅的"键值观测"（KVO）或 **NSNotificationCenter** 等通知，不要做其他事情。
* 如果对象持有文件描述符等系统资源，那么应该专门编写一个方法来释放此种资源。这样的类要和其使用者约定：用完资源后必须调用 `close` 方法。
* 执行异步任务的方法不应在 `dealloc` 里调用；只能在正常状态下执行的那些方法也不应在 `dealloc` 里调用，因为此时对象已处于正在回收的状态了。




### 第32条：编写“异常安全代码”时留意内存管理问题
* 在 Objective-C 中，异常只应在发生严重错误后抛出（参见第21条）。
* 在 `try` 块中，如果先保留了某个对象，释放它之前又抛出了异常，就会导致内存泄漏。而且 ARC 不会自动处理这个问题。
* 可以在编译器中开启 `-fobjc-arc-exceptions`（默认关闭）， 让 ARC 生成安全处理异常所用的附加代码。
* Objective-C++模式下，编译器会自动打开 `-fobjc-arc-exceptions`标志。

#### 要点

- 捕获异常时，一定要注意将 try 块内所创立的对象清理干净。
- 在默认情况下，ARC 不生成安全处理异常所需的清理代码。开启编译器标志后，可生成这种代码，不过会导致应用程序变大，而且会降低运行效率。




### 第33条：以弱引用避免保留环

![](http://upload-images.jianshu.io/upload_images/2648731-6fe8b8ec7b16342f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 几个对象相互引用会导致引用循环造成内存泄露。
* 避免引用循环的最佳方式就是弱引用（weak）。
* unsafe_unretained（不保留也不释放）、assign（通常只用于整体类型，如 int、float、结构体等）、weak（属性被回收后会自动设置为nil） 之间的区别？


![](http://upload-images.jianshu.io/upload_images/2648731-394995e450c46083.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 要点

- 将某些引用设为 `weak`，可避免出现引用循环。
- `weak` 引用可以自动清空，也可以不自动清空。自动清空是随着ARC而引入的新特性，由 runtime 来实现，在具备自动清空功能的弱引用上，可以随意读取其数据，因为这种引用不会指向已经回收过的对象。




### 第34条：以“自动释放池”降低内存峰值

**自动释放池（autorelease pool）**：存放需要在稍后某个时刻释放的对象。

释放对象的两种方式：

1. 调用 `release` 方法，使其引用计数立即递减；
2. 调用 `autorelease` 方法，将对象加入自动释放池中。清空自动释放池时，系统会向其中的对象发送 `release` 消息；

创建自动释放池语法：

```objective-c
@autoreleasepool {
   // ...
}
```

程序员无需自己创建自动释放池，系统自动创建的线程中默认有自动释放池。除了 `main` 函数中：

```objective-c
int main(int argc, char * argv[]) {
    @autoreleasepool {
        return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
    }
}
```

自动释放池的嵌套：

```objective-c
@autoreleasepool {
    NSString *string = [NSString stringWithFormat:@"hello"];
    @autoreleasepool {
        NSNumber *number = [NSNumber numberWithInteger:1];
    }
}
```

❇️嵌套自动释放池可以控制应用程序的内存峰值，使其不至过高。

**内存峰值（high-memory waterline）**：应用程序在某个特定时段内的最大内存用量。

```objective-c
NSArray *databaseRecords = /** ... */ ;
NSMutableArray *people = [NSMutableArray new];
// 将循环内的代码包裹在自动释放池中
// 系统就会在块末尾释放对象，而不是在主线程集中释放。
for (NSDictionary *record in databaseRecords) {
    @autoreleasepool {
        EOCPerson *person = [[EOCPerson alloc] initWithRecord:record];
        [people addObject:person];
    }
}
```

❇️是否应该使用自动释放池优化效率还应视情况而定，因为创建自动释放池本身也有一定的开销。

**@autoreleasepool** 语法的另一个好处：每个自动释放池均有其范围，可以避免无意间误用在自动释放池中已经被系统回收的对象。

#### 要点

- 自动释放池排布在栈中，对象收到 `autorelease` 消息后，系统将其放入最顶端的池里。
- 合理运用自动释放池，可降低应用程序的内存峰值。
- **@autoreleasepool** 这种新式写法能创建出更为轻便的自动释放池。




### 第35条：用“僵尸对象”调试内存管理问题

向业已回收的对象发送消息是不安全的，可行与否完全取决于**对象所占内存有没有为其他内容所覆写**。

#### 调试内存管理的最佳方式：僵尸对象（Zombie Object）

❇️原理：启用僵尸对象调试功能之后，运行期系统会把所有已经回收的实例转化成特殊的"僵尸对象"，而不会真正回收它们。这种对象所在的核心内存无法重用，因此不可能遭到覆写。僵尸对象收到消息后，会抛出异常，其中准确说明了发送过来的消息，并描述了回收之前的那个对象。

将 **NszombieEnabled** 环境变量设为 **YES**, 即可开启此功能。

❇️位置：Xcode菜单栏 → Product → Scheme → Edit Scheme → Run → Diagnostics诊断选项 → Memory Management → 勾选 Zombie Objects。

![](http://upload-images.jianshu.io/upload_images/2648731-c7669b832c47ed88.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

⚠️ APP 打包发布之前一定要取消此勾选！



#### 要点

* 系统在回收对象时，可以不将其真的回收，而是把它转化为僵尸对象。通过环境变量 **NSZombieEnabled** 可开启此功能。
* 系统会修改对象的 isa 指针，令其指向特殊的僵尸类，从而使该对象变为僵尸对象。僵尸类能够响应所有的selector，响应方式为：打印一条包含消息内容及其接受者的消息，然后终止应用程序。




### 第36条：不要使用 retainCount

```objective-c
- (NSUInteger)retainCount OBJC_ARC_UNAVAILABLE; // 查询对象当前的保留计数
```

❇️ `retainCount` 所返回的保留计数只是某个给定时间点上的值，该方法并未考虑到自动释放池的情况。

```objective-c
// 错误示例：
while ([object retainConut]) {
    [object release]
}
// 错误一：没有考虑到后续的自动释放操作；
// 错误二：retainConut 可能永远不返回0；
```

#### 要点

- 对象的保留计数看似有用，实则不然，因为任何给定时间点上的"绝对保留计数"（absoulte retain count）都无法反映对象生命期的全貌。
- 引入 ARC 后，`retainCount` 方法就正式废止了，在 ARC 下调用该方法会导致编译器报错。
