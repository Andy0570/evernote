![](http://upload-images.jianshu.io/upload_images/2648731-7dcb0ad568b4fb8b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 第7章 系统框架

### 第47条：熟悉系统框架

**框架**：将一系列代码封装为动态库（dynamic library)，并在其中放入描述其接口的头文件。

**静态库**：有时为 iOS 平台构建的第三方框架所使用的是静态库（static library)，这是因为 iOS 应用程序不允许在其中包含动态库。这些东西严格来讲并不是真正的框架，然而也经常视为框架。不过，所有 iOS 平台的系统框架仍然使用动态库。

* **Cocoa**：在为 Mac OS X 或 iOS 系统开发“带图形界面的应用程序"（graphical application)时，会用到名为**Cocoa** 的框架，在iOS上称为 **Cocoa Touch**。
* **Foundation**：它是所有 Objective-C 应用程序的基础。**Foundation** 框架中的类使用 **NS** 前缀。
* **CoreFoundation**：从技术上讲，**CoreFoundation** 框架不是 Objective-C 框架，但它却是编写 Objective-C 应用程序时所应熟悉的重要框架， **Foundation** 框架中的许多功能，都可以在此框架中找到对应的 C 语言 API。
  > **无缝桥接**（toll- free bridging )功能:
  >
  > 把 **CoreFoundation** 中的 C 语言数据结构平滑转换为 **Foundation** 中的 Objective-C 对象，也可以反向转换。
  >
  > 无缝桥接技术所实现的代码可以使运行期系统把 **CoreFoundation** 框架中的对象视为普通的 Objective-C 对象。
  >
  > 例如：NSString  <—> CFString
* **CFNetwork**：此框架提供了 C 语言级别的网络通信能力，它将 **BSD套接字**（BSD socket）抽象成易于使用的网络接口。而 **Foundation** 则将该框架里的部分内容封装为 Objective-C 语言的接口，以便进行网络通信，例如可以用 **NSURLConnection** 从 URL 中下载数据。
* **CoreAudio**：该框架所提供的 C 语言 API 可用来操作设备上的音频硬件。这个框架属于比较难用的那种，因为音频处理本身就很复杂。所幸由这套 API 可以抽象出另外一 套 Objective-C 式 API，用后者来处理音频问题会更简单些。
* **AVFoundation**：此框架所提供的 Objective-C 对象可用来回放并录制音频及视频，比如能够在 UI视图类里播放视频。
* **CoreData**：此框架所提供的 Objective-C 接口可将对象放入数据库，便于持久保存。 **CoreData** 会处理数据的获取及存储事宜，而且可以跨越 Mac OS X 及 iOS 平台。
* **CoreText**：此框架提供的 C 语言接口可以高效执行文字排版及渲染操作。

Objective-C 编程的重要特性：经常需要使用底层的 C 语言级 API。用 C 语言来实现 API 的好处是，可以绕过 Objective-C 的运行期系统，从而提升执行速度。

读者可能会编写使用 UI 框架的 Mac OS X 或 iOS 应用程序。这两个平台的核心 UI 框架分别叫做 **AppKit** 及 **UIKit** ,它们都提供了构建在 **Foundation** 与 **CoreFoundation** 之上的 Objective-C 类。框架里含有 UI 元素，也含有粘合机制，令开发者可将所有相关内容组装为应用程序。在这些主要的 UI 框架之下，是 **CoreAnimation** 与 **CoreGraphics** 框架。

* **CoreAnimation** 是用 Objective-C 语言写成的，它提供了一些工具，而 UI 框架则用这些工具来渲染图形并播放动画。开发者编程时可能从来不会深入到这种级别，不过知道该框架总是好的。**CoreAnimation** 本身并不是框架，它是 **QuartzCore** 框架的一部分。然而在框架的国度里，**CoreAnimation** 仍应算作“一等公民”（first-class citizen)。
* **CoreGraphics** 框架以 C 语言写成，其中提供了 2D 渲染所必备的数据结构与函数。例如, 其中定义了 CGPoint、CGSize、CGRect 等数据结构，而 **UllKit** 框架中的 **UlView** 类在确定视图控件之间的相对位置时，这些数据结构都要用到。

还有很多框架构建在 UI 框架之上，比方说 **MapKit** 框架，它可以为 iOS 程序提供地图功能。又比如 **Social** 框架，它为 Mac OS X 及 iOS 程序提供了社交网络（social networking) 功能。开发者通常会将这些框架与操作系统平台所对应的核心 UI 框架结合起来使用。

总的来说，许多框架都是安装 Mac OS X 与 iOS 系统时的标准配置。所以，在打算编写新的工具类之前，最好在系统框架里搜一下，通常都有写好的类可供直接使用。

#### 要点

* 许多系统框架都可以直接使用。其中最重要的是 **Foundation** 与 **CoreFoundation**，这两个框架提供了构建应用程序所需的许多核心功能。
* 很多常见任务都能用框架来做，例如音频与视频处理、网络通信、数据管理等。


* 请记住，**用纯 C 写成的框架与用 Objective-C 写成的一样重要，若想成为优秀的 Objective-C 开发者，应该掌握 C 语言的核心概念**。




### 第48条：多用块枚举，少用 for 循环

实现枚举的方法：

1. 标准的 C 语言循环；
2. Objective-C 1.0 的 **NSEnumerator** 方法；
3. Objective-C 2.0 的快速遍历法（fast enumeration)。
4. 基于块的遍历方式。

#### for 循环

```objective-c
/** NSArray */
NSArray *array;
for (int i = 0; i < array.count; i++) {
    id object = array[i];
    // Do something with 'object'
}

/** NSDictionary */
NSDictionary *dictionary;
NSArray *keys = [dictionary allKeys];
for (int i = 0; i < keys.count; i++) {
    id key = keys[i];
    id value = dictionary[key];
    // Do something with 'key' and 'value'
}

/** NSSet */
NSSet *set;
NSArray *objects = [set allObjects];
for (int i = 0; i < objects.count; i++) {
    id object = object[i];
    // Do something with 'object'
}
```

> 根据定义，字典与set都是“无序的"（imoniered)，所以无法根据特定的整数下标来直接访问其中的值。于是，就需要先获取字典里的所有键或是set里的所有对象，这两种情况下，都可以在获取到的有序数组上遍历，以便借此访问原字典及原set中的值。创建这个附加数组会有额外开销，而且还会多创建一个数组对象，它会保留collection中的所有元素对象。

#### 使用 Objective-C 1.0 的 NSEnumerator 遍历 

**NSEnumerator.h**

```objective-c
typedef struct {
    unsigned long state;
    id __unsafe_unretained _Nullable * _Nullable itemsPtr;
    unsigned long * _Nullable mutationsPtr;
    unsigned long extra[5];
} NSFastEnumerationState;

@protocol NSFastEnumeration

- (NSUInteger)countByEnumeratingWithState:(NSFastEnumerationState *)state objects:(id __unsafe_unretained _Nullable [_Nonnull])buffer count:(NSUInteger)len;

@end

@interface NSEnumerator<ObjectType> : NSObject <NSFastEnumeration>

//❇️ 返回枚举里的下个对象。每次调用该方法时，其内部数据结构都会更新，使得下次调用方法时能返回下个对象。等到枚举中的全部对象都已返回之后，再调用就将返回nil，这表示达到枚举末端了。
- (nullable ObjectType)nextObject;

@end

@interface NSEnumerator<ObjectType> (NSExtendedEnumerator)

@property (readonly, copy) NSArray<ObjectType> *allObjects;

@end
```

使用示例：

```objective-c
/** NSArray */
NSArray *array;
NSEnumerator *enumerator = [array objectEnumerator];
id object;
while ((object = [enumerator nextObject]) != nil) {
    // Do something with 'object'
}

/** NSDictionary */
NSDictionary *dictionary;
NSEnumerator *enumerator = [dictionary keyEnumerator];
id key;
while ((key = [enumerator nextObject]) != nil) {
    id value = dictionary[key];
    // Do something with 'key' and 'value'
}

/** NSSet */
NSSet *set;
NSEnumerator *enumerator = [set objectEnumerator];
id object;
while ((object = [enumerator nextObject]) != nil) {
    // Do something with 'object'
}
```

* 优点：不论遍历哪种 collection，都可以采用这套相似的语法。

* **NSEnumerator** 还有多种“枚举器"（enumerator) 可供使用，如反向遍历数组：

  ```objective-c
  /** NSArray */
  NSArray *array;
  // reverseObjectEnumerator：反向遍历数组
  NSEnumerator *enumerator = [array reverseObjectEnumerator];
  id object;
  while ((object = [enumerator nextObject]) != nil) {
      // Do something with 'object'
  }
  ```

#### 快速遍历

```objective-c
/** NSArray */
NSArray *array;
for (id object in array) {
    // Do something with 'object'
}

/** NSDictionary */
NSDictionary *dictionary;
for (id key in dictionary) {
    id value = dictionary[key];
    // Do something with 'key' and 'value'
}

/** NSSet */
NSSet *set;
for (id object in set) {
    // Do something with 'object'
}
```

如果某个类的对象支持快速遍历，那么就可以宣称自己遵从名为 **NSFastEmimeraticm** 的协议，从而令开发者可以采用此语法来迭代该对象。

**该方法允许类实例同时返回多个对象，使得循环遍历操作更为髙效。**

```objective-c
@protocol NSFastEnumeration

- (NSUInteger)countByEnumeratingWithState:(NSFastEnumerationState *)state
                                  objects:(id __unsafe_unretained _Nullable [_Nonnull])buffer
                                    count:(NSUInteger)len;

@end
```

#### 基于块的遍历方式

**方法列表**:

```objective-c
/** NSArray */
- (void)enumerateObjectsUsingBlock:(void (NS_NOESCAPE ^)(ObjectType obj, NSUInteger idx, BOOL *stop))block;
- (void)enumerateObjectsWithOptions:(NSEnumerationOptions)opts usingBlock:(void (NS_NOESCAPE ^)(ObjectType obj, NSUInteger idx, BOOL *stop))block;
- (void)enumerateObjectsAtIndexes:(NSIndexSet *)s options:(NSEnumerationOptions)opts usingBlock:(void (NS_NOESCAPE ^)(ObjectType obj, NSUInteger idx, BOOL *stop))block;

/** NSDictionary */
- (void)enumerateKeysAndObjectsUsingBlock:(void (NS_NOESCAPE ^)(KeyType key, ObjectType obj, BOOL *stop))block;
- (void)enumerateKeysAndObjectsWithOptions:(NSEnumerationOptions)opts usingBlock:(void (NS_NOESCAPE ^)(KeyType key, ObjectType obj, BOOL *stop))block;

/** NSSet */
- (void)enumerateObjectsUsingBlock:(void (NS_NOESCAPE ^)(ObjectType obj, BOOL *stop))block;
- (void)enumerateObjectsWithOptions:(NSEnumerationOptions)opts usingBlock:(void (NS_NOESCAPE ^)(ObjectType obj, BOOL *stop))block;
```

示例代码：

```objective-c
/** NSArray */
NSArray *array;
[array enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
    // Do something with 'object'
    if (shouldStop) {
        *stop = YES;
    }
}];

/** NSDictionary */
NSDictionary *dictionary;
[dictionary enumerateKeysAndObjectsUsingBlock:^(id  _Nonnull key, id  _Nonnull obj, BOOL * _Nonnull stop) {
    // Do something with 'key' and 'value'
    if (shouldStop) {
        *stop = YES;
    }
}];

/** NSSet */
NSSet *set;
[set enumerateObjectsUsingBlock:^(id  _Nonnull obj, BOOL * _Nonnull stop) {
    // Do something with 'object'
    if (shouldStop) {
        *stop = YES;
    }
}];
```

此方式的优点：

1. 遍历时可以直接从块里获取更多信息。

2. 能够修改块的方法签名，以免进行类型转换操作，从效果上讲，相当于把本来需要执行的类型转换操作交给块方法签名来做。

   ```objective-c
   // for-in 遍历：
   NSDictionary *dictionary;
   for (NSString *key in dictionary) {
       NSString *object = (NSString *)dictionary[key];
       // Do something with 'key' and 'value'
   }

   // 基于块的遍历，可以在块方法签名中直接转换数据类型：
   [dictionary enumerateKeysAndObjectsUsingBlock:^(NSString *key, NSString *obj, BOOL *stop) {
       // Do something with 'key' and 'value'
   }];
   ```

3. 反向遍历：设置 **NSEnumerationOptions** 选项为 `NSEnumerationReverse`。

4. 并行执行：设置 **NSEnumerationOptions** 选项为 `NSEnumerationConcurrent`。

#### 要点

* 遍历 collection 有4种方式。最基本的办法是 **for 循环**，其次是 **NSEnumerator** 遍历方法及**快速遍历法**，最新、最先进的方式则是 **块枚举法**。
* **块枚举法** 本身就能通过 GCD 来并发执行遍历操作，无需另行编写代码。而采用其他遍历方式则无法轻易实现这一点。
* 若提前知道待遍历的 collection 含有何种对象，则应修改块签名，指出对象的具体类型。




### 第49条：对自定义其内存管理语义的 collection 使用无缝桥接

**无缝桥接**：可以在定义于 **Foundation** 框架中的 Objective-C 类和定义于 **CoreFoundation** 框架中的 C 数据结构之间互相转换。

```objective-c
// NSArray → CFArray
NSArray *anNSArray = @[@1, @2, @3, @4, @5];
// CFArray 需要通过 CFArrayRef 来引用，
// CFArrayRef 是指向 struct__CFArray 的指针
CFArrayRef aCFArray = (__bridge CFArrayRef)anNSArray;
NSLog(@"Size of array = %li", CFArrayGetCount(aCFArray));
```

#### 桥式转换

* `__bridge` 告诉ARC (参见第30条）如何处理转换所涉及的 Objective-C 对象。
* `__bridge` 本身的意思是：ARC 仍然具备这个 Objective-C 对象的所有权。而 `__bridge_ retained` 则与之相反，意味着 ARC 将交出对象的所有权。
* 反向转换可通过 `__bridge_transfer` 来实现。

#### 需要使用无缝桥接的原因：

* **Foundation** 框架中的 Objective-C 类所具备的某些功能，是 **CoreFoundation** 框架中的 C 语言数据结构所不具备的。
* 在使用 **Foundation** 框架中的字典对象时会遇到一个大问题，那就是其键的内存管理语义为“拷贝”，而值的语义却是“保留”。除非使用强大的无缝桥接技术，否则无法改变其语义。

 创建 **CFMutableDictionary** 时，可以通过下列方法来指定键和值的内存管理语义:

```objective-c
CFMutableDictionaryRef CFDictionaryCreateMutable(
    CFAllocatorRef allocator,
    CFIndex capacity,
    const CFDictionaryKeyCallBacks *keyCallBacks,
    const CFDictionaryValueCallBacks *valueCallBacks
);
```

* `CFAllocatorRef`：将要使用的内存分配器（allocator) 。**CoreFoundation** 对象里的数据结构需要占用内存，而分配器负责分配及回收这些内存。开发者通常为这个参数传人 **NULL**，表示采用默认的分配器。

* `CFIndex`：定义字典的初始大小。它并不会限制字典的最大容景，只是向分配器提示一开始应该分配多少内存。假如要创建的字典含有10个对象，那就向该参数传入10。

* 最后两个参数值得注意。它们定义了许多回调函数，用于指示字典中的键和值在遇到各种事件时应该执行何种操作。这两个参数都是指向结构体的指针，二者所对应的结构体如下：

  ```objective-c
  typedef struct {
      CFIndex				version;
      CFDictionaryRetainCallBack		retain;
      CFDictionaryReleaseCallBack		release;
      CFDictionaryCopyDescriptionCallBack	copyDescription;
      CFDictionaryEqualCallBack		equal;
      CFDictionaryHashCallBack		hash;
  } CFDictionaryKeyCallBacks;
  
  typedef struct {
      CFIndex				version;
      CFDictionaryRetainCallBack		retain;
      CFDictionaryReleaseCallBack		release;
      CFDictionaryCopyDescriptionCallBack	copyDescription;
      CFDictionaryEqualCallBack		equal;
  } CFDictionaryValueCallBacks;
  
  /*
   * version参数目前应设为0。当前编程时总是取这个值，不过将来苹果公司也许会修改此结构体，所以
   * 要预留该值以表示版本号。这个参数可以用于检测新版与旧版数据结构之间是否兼容。
   * 结构体中的其余成员都是函数指针，它们定义了当各种事件发生时应该采用哪个函数来执行相关任务。
   */
  ```

  > 底层实现有点复杂，暂缓研究😂



#### 要点

* 通过无缝桥接技术，可以在 **Foundation** 框架中的 Objective-C 对象与 **CoreFoundation** 框架中的 C 语言数据结构之前来回转换。
* 在 **CoreFoundation** 层面创建 collection 时，可以指定许多回调函数，这些函数表示此 collection 应如何处理其元素。然后，可运用无缝桥接技术，将其转换成具备特殊内存管理语义的 Objective-C collection。




### 第50条：构建缓存时选用 NSCache 而非 NSDictionary
* **NSCache** 胜过 **NSDictionary** 之处在于，当系统资源将要耗尽时，它可以自动删减缓存。
* **NSCache** 还会先行删减 **最久未使用的（lease recently used) 对象**。
* **NSCache** 并不会“拷贝”键，而是会“保留”它。原因在于，很多时候，键都是由不支持拷贝操作的对象来充当的。
* **NSCache** 是线程安全的。在开发者自己不编写加锁代码的前提下， 多个线程便可以同时访问**NSCache**。对缓存来说，线程安全通常很重要，因为开发者可能要在某个线程中读取数据，此时如果发现缓存里找不到指定的键，那么就要下载该键所对应的数据了。而下载完数据之后所要执行的回调函数，有可能会放在背景线程中运行，这样的话，就等于是用另外一个线程来写入缓存了。
* 开发者可以操控缓存删减其内容的时机。有两个与系统资源相关的尺度可供调整，其一是缓存中的对象总数，其二是所有对象的“总开销"（overall cost)。

示例代码：

```objective-c
// EOCNetworkFetcher.h
#import <Foundation/Foundation.h>

// Network fetcher class
typedef void(^EOCNetworkFetcherCompletionHandler)(NSData *data);

@interface EOCNetworkFetcher : NSObject
@property (nonatomic, strong, readonly) NSURL *url;
- (instancetype)initWithURL:(NSURL *)url;
- (void)startWithCompletionHandler:(EOCNetworkFetcherCompletionHandler)completion;
@end

// EOCNetworkFetcher.m
#import "EOCNetworkFetcher.h"

@implementation EOCNetworkFetcher {
    NSCache *_cache;
}

- (instancetype)init {
    if (self = [super init]) {
        _cache = [NSCache new];
        
        // Cache a maximu of 100 URLs
        // 可缓存的总对象数目上限
        _cache.countLimit = 100;
        
        /**
         * The size in bytes of data is used as the cost,
         * 开销值 以 字节 为单位
         * so this sets a cost limit of 5MB
         * 因此需要将 MB 换算为 字节
         */
        _cache.totalCostLimit = 5 * 1024 * 1024;
    }
    return self;
}

- (instancetype)initWithURL:(NSURL *)url {
    if (self = [super init]) {
        _url = url;
    }
    return self;
}

- (void)downloadDataForURL:(NSURL *)url {
    NSData *cacheData = [_cache objectForKey:url];
    if (cacheData) {
        // Cache hit
        [self useData:cacheData];
    } else {
        // Cache miss,缓存中没有访问者所需的数据
        EOCNetworkFetcher *fetcher = [[EOCNetworkFetcher alloc] initWithURL:url];
        [fetcher startWithCompletionHandler:^(NSData *data) {
            [self useData:data];
        }];
    }
}
```

* **`NSPurgeableData`** 类，**`NSMutableData`** 的子类。如果某个对象所占的内存能够根据需要随时丢弃，那么就可以实现 **`NSDiscardableContent`** 协议所定义的接口。

  ```objective-c
  - (void)downloadDataForURL:(NSURL *)url {
      NSPurgeableData *cacheData = [_cache objectForKey:url];
      if (cacheData) {
          // Stop the data being purged
          [cacheData beginContentAccess];
          
          // Use the cached data
          [self useData:cacheData];
          
          // Mark that the data may be purged again
          [cacheData endContentAccess];
      }else {
          // Cache miss
          EOCNetworkFetcher *fetcher = [[EOCNetworkFetcher alloc] initWithURL:url];
          [fetcher startWithCompletionHandler:^(NSData *data) {
              NSPurgeableData *purgeableData = [NSPurgeableData dataWithData:data];
              [_cache setObject:purgeableData
                         forKey:url
                           cost:purgeableData.length];
              
              // Don't need to beginContentAccess as it begins
              // with access already marked
              
              // Use the retrieved data
              [self useData:data];
              
              // Mark that the data may be purged now
              [cacheData endContentAccess];
          }];
      }
  }
  ```

#### 要点

* 实现缓存时应选用 **`NSCache`** 而非 **`NSDictionary`** 对象。因为 **`NSCache`** 可以提供优雅的自动删减功能，而且是线程安全的，此外，它与字典不同，并不会拷贝键。
* 可以给 **`NSCache`** 对象设置上限，用以限制缓存中的对象总个数及总成本，而这些尺度则定义了缓存删减其中对象的时机。但是绝对不要把这些尺度当成可靠的硬限制，他们仅对 **`NSCache`** 起指导作用。
* 将 **`NSPurgeableData`** 与 **`NSCache`** 搭配使用，可实现自动清除数据的功能，也就是说，当 **`NSPurgeableData`** 对象所占内存为系统丢弃时，该对象自身也会从缓存中移除。
* 如果缓存使用得当。那么应用程序的响应速度就能提高。只有那种重新计算起来很费事的数据，才值得放入缓存，比如那些需要从网络获取或从磁盘读取的数据。




### 第51条：精简 initialize 与 load 的实现代码

#### load 方法

```objective-c
+ (void)load;
```

* 对于加入运行期系统中的每个类（class) 及分类（category) 来说，必定会调用此方法，而且仅调用一次。当包含类或分类的程序库载入系统时，就会执行此方法，而这通常就是指应用程序启动的时候，若程序是为iOS平台设计的，则肯定会在此时执行。
* 如果分类和其所属的类都定义了 `load` 方法，则先调用类里的，再调用分类里的。
* 在 `load` 方法中使用其他类是不安全的。
* 不遵循继承规则。如果某个类本身没实现 `load` 方法，那么不管其各级超类是否实现此方法，系统都不会调用。此外，分类和其所属的类里，都可能出现 `load` 方法。此时两种实现代码都会调用，类的实现要比分类的实现先执行。
* 而且 `load` 方法务必实现得精简一些，也就是要尽量减少其所执行的操作，因为整个应用程序在执行 `load` 方法时都会阻塞。应用程序必须阻塞并等着所有类的 `load` 都执行完，才能继续。
* `load` 方法真正用途仅在于调试程序，比如可以在分类里编写此方法，用来判断该分类是否已经正确载入系统中。也许此方法一度很有用处，但现在完全可以说：时下编写 Objective-C 代码时, 不需要用它。

#### initialize 方法

```objective-c
+ (void)initialize;
```

* 对于每个类来说，该方法会在程序首次用该类之前调用，且只调用一次。它是由运行期系统来调用的，绝不应该通过代码直接调用。
* 它是“惰性调用的”，也就是说，只有当程序用到了相关的类时，才会调用。
* 运行期系统在执行该方法时，是处于正常状态的。而且，运行期系统也能确保 `initialize` 方法一定会在**线程安全的环境**中执行。
* 遵循继承规则。`initialize` 方法与其他消息一样，如果某个类未实现它，而其超类实现了，那么就会运行超类的实现代码。

#### 总结

* 这两个方法的实现代码要尽量精简。在里面设置一些状态，使本类能够正常运作就可以了，不要执行耗时太久或需要加锁的任务。
* 开发者无法控制类的初始化时机。
* 如果某个类的实现代码很复杂，那么其中可能会直接或间接用到其他类。
* `initialize` 方法只应该用来设置内部数据。若某个全局状态无法在编译期初始化，则可以放在 `initialize` 里来做。



#### 要点

* 在加载阶段，如果实现了 `load` 方法，那么系统就会调用它。分类里也可以定义此方法，类的 `load`方法要比分类中的先调用。与其他方法不同，`load` 方法不参与覆写机制。
* 首次使用某个类之前，系统会向其发送 `initialize` 消息。由于此方法遵从普通的覆写规则，所以通常应该在里面判断当前要初始化的是哪个类。
* `load`与`initialize`方法都应该实现的精简一些，这有助于保持应用程序的响应能力，也能减少引入依赖环的几率。
* 无法在编译期设定的全局常量，可以放在`initialize`方法里初始化。




### 第52条：别忘了 NSTimer 会保留其目标对象

* **Foundation** 框架中有个类叫做 `NSTimer` ，开发者可以指定绝对的日期与时间，以便到时执行任务，也可以指定执行任务的相对延迟时间。计时器还可以重复运行任务，有个与之相关联的“间隔值”（interval) 可用来指定任务的触发频率。
* 只有把计时器放在运行循环里，它才能正常触发任务。

```objective-c
+ (NSTimer *)scheduledTimerWithTimeInterval:(NSTimeInterval)ti
                                     target:(id)aTarget
                                   selector:(SEL)aSelector
                                   userInfo:(nullable id)userInfo
                                    repeats:(BOOL)yesOrNo;
```

* 用此方法创建出来的计时器，会在指定的间隔时间之后执行任务。也可以令其反复执行任务，直到开发者稍后将其手动关闭为止。target与selector参数表示计时器将在哪个对象上调用哪个方法。计时器会保留其目标对象，等到自身“失效”时再释放此对象。调用 `invalidate` 方法可令计时器失效；执行完相关任务之后，一次性的计时器也会失效。开发者若将计时器设置成重复执行模式，那么必须自己调用 `invalidate` 方法，才能令其停止。

* 由于计时器会保留其目标对象，所以反复执行任务通常会导致应用程序出问题。也就是说，设置成重复执行模式的那种计时器，很容易引人“保留环”。

  ```objective-c
  //  EOCClass.h
  #import <Foundation/Foundation.h>
  
  @interface EOCClass : NSObject
  - (void)startPolling;
  - (void)stopPolling;
  @end
  
  //  EOCClass.m
  #import "EOCClass.h"
  
  @implementation EOCClass {
      // ❇️ EOCClass → _pollTimer
      NSTimer *_pollTimer;
  }
  
  - (instancetype)init {
      return [super init];
  }
  
  - (void)dealloc {
      [_pollTimer invalidate];
  }
  
  - (void)startPolling {
      // ❇️ _pollTimer → EOCClass
      _pollTimer =
      [NSTimer scheduledTimerWithTimeInterval:5.0
                                       target:self
                                     selector:@selector(p_doPoll)
                                     userInfo:nil
                                      repeats:YES];
  }
  
  - (void)stopPolling {
      [_pollTimer invalidate];
      _pollTimer = nil;
  }
  
  - (void)p_doPoll {
      // Poll the resource
  }
  
  @end
  ```

  解决引用循环方法：

  ```objective-c
  //  NSTimer+EOCBlocksSupport.h
  #import <Foundation/Foundation.h>
  
  @interface NSTimer (EOCBlocksSupport)
  
  + (NSTimer *)eoc_scheduledTimerWithTimeInterval:(NSTimeInterval)interval
                                            block:(void(^)())block
                                          repeats:(BOOL)repeats;
  @end
  
  //  NSTimer+EOCBlocksSupport.m
  #import "NSTimer+EOCBlocksSupport.h"
  
  @implementation NSTimer (EOCBlocksSupport)
  
  + (NSTimer *)eoc_scheduledTimerWithTimeInterval:(NSTimeInterval)interval
                                            block:(void(^)())block
                                          repeats:(BOOL)repeats {
      return [self scheduledTimerWithTimeInterval:interval
                                           target:self
                                         selector:@selector(eoc_blockInvoke:) userInfo:[block copy] repeats:repeats];
  }
  
  + (void)eoc_blockInvoke:(NSTimer *)timer {
      void (^block)() = timer.userInfo;
      if (block) {
          block();
      }
  }
  
  @end
  ```

  将计时器所应执行的任务封装成 block 。在调用计时器函数时，把它作为 userlnfo 参数传进去。该参数可用来存放不透明值(opaque value)，只要计时器还有效，就会一直保留着它。传人参数时要通过 copy 方法将 block 拷贝到“堆”上（参见第37条)，否则等到稍后要执行它的时候，该块可能已经无效了。计时器现在的 target 是 NSTimer 类对象，这是个单例，因此计时器是否会保留它，其实都无所谓。此处依然有保留环，然而因为类对象（classobject)无须回收，所以不用担心。

  优化：

  ```objective-c
  - (void)startPolling {
      __weak EOCClass *weakSelf = self;
      _pollTimer =
      [NSTimer eoc_scheduledTimerWithTimeInterval:5.0
                                            block:^{
                                                EOCClass *strongSelf = weakSelf;
                                                [strongSelf p_doPoll];
                                            }
                                          repeats:YES];
      
  }
  ```

#### iOS 10.0 中已经引入了该方法

```objective-c
+ (NSTimer *)timerWithTimeInterval:(NSTimeInterval)interval repeats:(BOOL)repeats block:(void (^)(NSTimer *timer))block;

+ (NSTimer *)scheduledTimerWithTimeInterval:(NSTimeInterval)interval repeats:(BOOL)repeats block:(void (^)(NSTimer *timer))block;
```


#### 要点

* `NSTimer` 对象会保留其目标，直到计时器本身失效为止，调用 `invalidate` 方法可令计时器失效，另外，一次性的计时器在触发完任务之后也会失效。
* 反复执行任务的计时器，很容易引入保留环，如果这种计时器的目标对象又保留了计时器本身，那肯定会导致循环引用。这种循环引用，可能是直接发生的，也可能是通过对象图里的其他对象间接发生的。
* 可以扩充 `NSTimer` 的功能，用块来打破循环引用。不过，除非 `NSTimer` 将来在公共接口里提供此功能，否则必须创建分类，将相关实现代码加入其中。









