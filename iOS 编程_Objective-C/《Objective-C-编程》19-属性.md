* 尽管可以使用 `obj->arr` 的形式去强制读取对象的成员变量，但是良好的编程形式是对外界提供成员变量的读写接口。`@property` 关键字提供了外界对成员变量的访问接口，**其本质是自动为某一个属性生成 `set` 和 `get` 方法**。
* 通过 `@property` 声明的属性特性只对 `setter` 方法有效，对 `getter` 方法无效。

属性声明格式：
```objectivec
@property(原子性, 读写性, 内存管理特性) Object *name;
```

### 1. 原子性

* **atomic**（默认类型）：原子性，多线程环境下，存在线程保护。
  `atomic` 意为操作是原子的，意味着只有一个线程访问实例变量。`atomic` 是线程安全的，至少在当前的存取器上是安全的。它是一个默认的特性，但是很少使用，因为比较影响效率，这跟 ARM 平台和内部锁机制有关。
* **nonatomic**： 非原子性，多线程环境下，不存在线程保护。
  `nonatomic` 跟 `atomic` 刚好相反。表示非原子的，可以被多个线程访问。它的效率比 `atomic` 快。但不能保证在多线程环境下的安全性，在单线程和明确只有一个线程访问的情况下广泛使用。

### 2. 读写性

* **readwrite**（默认类型）：读写性，编译器会自动生成 `setter` 、`getter` 方法。
  `readwrite` 是默认值，表示该属性同时拥有 `setter` 和 `getter` 方法。
* **readonly**：只读属性，编译器只生成 `getter` 方法。
  `readonly` 表示该属性只有 `getter` 方法，没有 `setter` 方法。

### 3. 内存管理特性

* **strong**（对象属性的默认类型）：强引用，同 `retain`。
  `strong` 是在 iOS 引入 ARC 的时候引入的关键字，是 `retain` 的一个可选的替代。表示实例变量对传入的对象要有所有权关系，即强引用。`strong` 跟 `retain` 的意思相同并产生相同的代码，但是语意上更好更能体现对象的关系。
* **weak**：弱引用，一般用来修饰对象。
  针对对象类型，在 `setter` 方法中，不会对传入的对象执行引用计数器+1的操作。也就是对传入的对象没有所有权，是弱引用。另外当传入的对象引用计数器为0，也就是被释放后，用 `weak` 声明的实例变量会指向 `nil`，即空对象。
* **copy**：拷贝对象；`NSString`／`NSArray` 的对象都使用 `copy`；
* **unsafe_unretained**（非对象属性的默认值）：存方法会将传入的值直接赋给实例变量，对象之外的类型使用，通常用于修饰基本数据类型。
  1. 从字面上来看，我们可以拆分成两部分来理解，`unretained` 和 `retain` 相反，也就是和 `strong` 相反，因此等同于 `weak`，其实也是用在 iOS5 之前替代 `weak` 使用的。
  2. `unsafe`，不安全的，如上所述等同于 `weak` 的话那应该是安全的，这里不安全的是指当传入的对象被释放后，使用`unsafe_unretained` 修饰的变量是不知道的，也不会像 `weak` 一样指向 `nil`，所以此时访问可能会引起应用 crash。因此，总结来说`unsafe_unretained` 作用是等同于 `weak`，但是是不安全的。   
  3.  `unsafe_unretained` 特性不会保留传入的对象，然后，如果该对象被释放，那么相应的实例变量不会被自动赋为 `nil`。
* **assign**（非对象属性的默认值）：同上，引入 ARC 之前使用的特性，对象之外的类型使用，一般用来修饰基础数据类型。
    1. 用于修饰值类型（基本数据类型），如 `int`，`float`，`double`，`CGFloat`，`NSInteger` 等。还包括不存在所有权关系的对象，如 `delegate`。
    2. 💡 相对于 `assign` ，我们一般使用 `weak` 来修饰 `delegate`，会更加安全。
    3. `assign` 修饰的属性不牵涉内存管理，不会被引用计数器管理。
* **retain**：保留对象，释放旧值，增加新的 `retaincount`；（所有的对象，都使用 `retain`）。
     在 `setter` 方法中，需要对传入的对象进行引用计数加1的操作。简单来说，就是对传入的对象拥有所有权，只要对该对象拥有所有权，该对象就不会被释放。

### 4. 是否可以为空

* **nullable**：该属性可以设置为空（`nil`）。
* **nonnull**：该属性不可以设置为空。

### 属性声明 Tips

```objectivec
// 基础数据类型：int、float、double、CGFloat、NSInteger 等
// Tips: 非对象属性的默认内存管理类型就是 unsafe_unretained，表示直接赋值，因此不用明确写出来！
@property (nonatomic, readonly) int num;
@property (nonatomic, readonly) float latitude;
@property (nonatomic, readonly) float longtitude;

// 指定 Bool 类型的 getter 方法名
@property (nonatomic, getter=isOn) BOOL on;

// NSString
@property (nonatomic, readonly, copy) NSString *identifier;
@property (nonatomic, readonly, copy) NSString *title;
@property (nonatomic, readonly, copy) NSString *string;
@property (nonatomic, readwrite, strong) NSMutableString *mutableString;

// NSArray
@property (nonatomic, copy) NSArray *array;

// NSURL
@property (nonatomic, readonly, strong) NSURL *url;

// Delegate
@property (nonatomic, weak) id<delegate> name;
@property (nonatomic, weak) id<EOCNetworkFetcherDelegate> delegate;

// enum 枚举类型
typedef NS_ENUM(NSUInteger, BlenderSpeed) {
    BlenderSpeedStir = 1,
    BlenderSpeedChop = 2,
    BlenderSpeedLiquify = 3,
    BlenderSpeedPulse = 4,
    BlenderSpeedIceCrush = 5
};

@property (nonatomic, assign) BlenderSpeed mySpeed;


// collection 类
// ✏️ 对外使用 NSSet
@property (nonatomic, strong, readonly) NSSet *friends;

// ...

@implementation EOCPerson {
    // ✏️ 类的内部使用 NSMutableSet
    NSMutableSet *_internalFriends;
}

// ✏️ 访问 NSSet 时，提供 NSMutableSet 的深拷贝不可变对象
- (NSSet *)friends {
    return [_internalFriends copy];
}
```

### 参考

* [我所理解的内存管理：4、property相关 @杨淳引](http://www.jianshu.com/p/74af03847e74)
* [理解@property使用的关键字 @MrLeoZou](http://www.jianshu.com/p/14552b4946c5)
* [Objective-C中的@property @程序员说](http://www.devtalking.com/articles/you-should-to-know-property/)
* [Objective-C 内存管理——你需要知道的一切](https://segmentfault.com/a/1190000004943276)
* [理解 iOS 的内存管理 @唐巧](http://blog.devtang.com/2016/07/30/ios-memory-management/)
