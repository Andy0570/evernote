# init

**NSObject** 类有一个名为 `init` 的方法。`init` 的示例代码如下：

```objectivec
NSMutableArray *mutableArray = [[NSMutableArray alloc] init];
```

向新创建的对象发送 `init` 消息，它就会初始化其下的实例变量。

* `alloc` 负责分配对象空间， `init` 负责初始化对象。
* 如果创建出来的新对象没有经过初始化，它仅仅是存在于内存中，但是无法接收消息。
* `init` 是实例方法，返回的是初始化后的对象地址。
* `init` 是 **NSObject** 的初始化方法。
* 如果 **NSObject** 的子类没有实现 `init` 方法，就会默认执行 **NSObject** 的 `init` 方法，将实例变量初始化为 0。


## 覆盖 `init` 方法

子类重写并覆盖父类的 `init` 方法。

```objectivec
- (instancetype)init {
    // 调用父类 NSObject 的 init 方法
  	// 将父类的 init 方法所返回的对象赋给 self
    self = [super init];
  
    // 检查父类的 init 方法的返回值是否非 nil ？
  	// 1.出于优化考虑，init 方法会释放已经分配了内存的对象，然后创建另一个新对象并返回；
  	// 2.如果 init 方法在执行过程中发生错误，会释放对象并返回 nil
    if (self) {
      
        // 为 _voltage 赋初值
        _voltage = 120;
    }
  
    // 返回指向新对象的指针
    return self;
}
```

使用并检查父类的初始化方法：覆盖 `init` 方法时，需要检查父类的初始化方法的返回值，确定不是 `nil` 并且有效。如果对象不存在，就没有必要执行自定义的初始化方法。



## instancetype 类型

`instancetype` 关键字会告诉编译器返回什么类型的对象。你编写的或是覆盖的任何初始化方法都应该返回 `instancetype` 类型的值。

### instancetype 和 id 的区别

`instancetype` 和 `id` 都可以作为初始化方法的返回值。

* `instancetype` ：关联返回类型，使【非关联返回类型的方法】返回【所在类的类型】。
* `id`：只能返回未知类型的对象。
* `instancetype` 只能作为返回值使用，而 `id` 既能作为返回值，还能作为参数使用。
* 相对于 `id` ，`instancetype` 还可以让编译器检查返回值的类型。

详情参考: [iOS instancetype和id区别详解](http://www.imlifengfeng.com/blog/?p=485)


## 带实参的 init 方法

```objectivec
// 串联(chain)使用初始化方法
// 防止因为调用了父类的 init 方法，导致子类实例的初始化不完整
- (instancetype)init {
    return [self initWithProductName:@"unKnown"];
}

// 指定初始化方法
- (instancetype)initWithProductName:(NSString *)productName {
    // 调用父类 NSObject 的 init 方法
    self = [super init];
    
    // 是否返回非 nil 的值？
    if (self) {
        
        // 为 _productName 赋值
        _productName = [productName copy];
        
        // 为 _voltage 赋初值
        _voltage = 120;
    }
    // 返回指向新对象的指针
    return self;
}

/**
 *  description 方法会返回一个描述类实例的字符串
 *
 *  默认的 NSObject 实现会以字符串的形式返回该对象在内存上的地址
 */
- (NSString *)description {
    return [NSString stringWithFormat:@"%@:%d volts",
            self.productName,self.voltage];
}
```



## 在 init 方法中使用存取方法

```objectivec
// 指定初始化方法
- (instancetype)initWithProductName:(NSString *)productName {
    // 调用父类 NSObject 的 init 方法
    self = [super init];
    
    // 是否返回非 nil 的值？
    if (self) {
      	
      // 1.直接赋值
        // 为 _productName 赋值
//        _productName = [productName copy];
        // 为 _voltage 赋初值
//        _voltage = 120;
      
        // 2.使用存取方法
        [self setProductName:productName];
        [self setVoltage:120];
    }
    // 返回指向新对象的指针
    return self;
}
```



争议1：不能在 `init` 方法中使用存取方法。使用存取方法的前提是对象已经初始化完毕，而对象只有在执行完 `init` 方法后才算完成了初始化。

> 有一个例外：永远不要在 `init` 方法（以及其他初始化方法）里面用 getter 和 setter 方法，你应当直接访问实例变量。这样做是为了防止有子类时，出现这样的情况：它的子类最终重载了其 setter 或者 getter 方法，因此导致该子类去调用其他的方法、访问那些处于不稳定状态，或者称为没有初始化完成的属性或者 ivar 。记住一个对象仅仅在 `init` 返回的时候，才会被认为是达到了初始化完成的状态。
>
> ——[禅与 Objective-C 编程艺术 （Zen and the Art of the Objective-C Craftsmanship 中文翻译）](https://github.com/oa414/objc-zen-book-cn/#instancetype)



争议2：在实际的开发中，存取方法除了能为实例变量赋值，还会完成其他的任务。

## 多个初始化方法

* BNRAppliance.h

  ```objectivec
  #import <Foundation/Foundation.h>

  @interface BNRAppliance : NSObject

  @property (nonatomic, copy) NSString *productName;
  @property (nonatomic) int voltage;
  - (instancetype)initWithProductName:(NSString *)productName;

  @end
  ```

* BNRAppliance.m

  ```objectivec
  #import "BNRAppliance.h"

  @implementation BNRAppliance

  // 串联(chain)使用初始化方法
  - (instancetype)init {
      return [self initWithProductName:@"unKnown"];
  }

  // 指定初始化方法
  - (instancetype)initWithProductName:(NSString *)productName {
      // 调用父类 NSObject 的 init 方法
      self = [super init];
      
      // 是否返回非 nil 的值？
      if (self) {
          
          // 为 _productName 赋值
          _productName = [productName copy];
          // 为 _voltage 赋初值
          _voltage = 120;
          
      }
      // 返回指向新对象的指针
      return self;
  }

  /**
   *  description 方法会返回一个描述类实例的字符串
   *
   *  默认的 NSObject 实现会以字符串的形式返回该对象在内存上的地址
   */
  - (NSString *)description {
      return [NSString stringWithFormat:@"%@:%d volts",
              self.productName,self.voltage];
  }

  @end
  ```

* BNROwnedAppliance.h
     ```objective-c
   #import "BNRAppliance.h"

   @interface BNROwnedAppliance : BNRAppliance

   // 保存拥有者的姓名
   @property (readonly) NSSet *ownerNames;

   - (instancetype)initWithProductName:(NSString *)productName
                      firstOwnerName:(NSString *)firstOwnedName;
   - (void)addOwnerName:(NSString *)name;
   - (void)removeOwnerName:(NSString *)name;

   @end
     ```

* BNROwnedAppliance.m

  ```objectivec
  #import "BNROwnedAppliance.h"

  @interface BNROwnedAppliance () {
      NSMutableSet *_ownerNames;
  }

  @end

  @implementation BNROwnedAppliance

  - (instancetype)initWithProductName:(NSString *)productName {
      return [self initWithProductName:productName firstOwnerName:nil];
  }

  // 指定初始化方法
  - (instancetype)initWithProductName:(NSString *)productName
                       firstOwnerName:(NSString *)firstOwnedName {
      // 调用父类的初始化方法
      if (self = [super initWithProductName:productName]) {
          
          // 创建 NSMutableSet 实例，用于保存拥有者的姓名
          _ownerNames = [[NSMutableSet alloc] init];
          
          // 传入的第一个拥有者姓名是否为 nil ?
          if (firstOwnedName) {
              [_ownerNames addObject:productName];
          }
      }
      // 返回指向新对象的指针
      return self;
  }

  - (void)addOwnerName:(NSString *)name {
      [_ownerNames addObject:name];
  }

  - (void)removeOwnerName:(NSString *)name {
      [_ownerNames removeObject:name];
  }

  - (NSSet *)ownerNames {
      return [_ownerNames copy];
  }
  @end
  ```


```objectivec
BNROwnedAppliance *appliance = [[BNROwnedAppliance alloc] init];
```

💡 **BNROwnedAppliance** 没有实现 **init** 方法，所以会调用其父类 **BNRAppliance** 的 **init** 方法：

```objectivec
- (instancetype)init {
    
    return [self initWithProductName:@"unKnown"];
}
```

从而调用：`[self initWithProductName:@"unKnown"]` ，因为 self 指向的是 **BNROwnedAppliance** 实例，所以调用的是 **BNROwnedAppliance** 的 `initWithProductName:` 方法：

```objective-c
- (instancetype)initWithProductName:(NSString *)productName {
    
    return [self initWithProductName:productName firstOwnerName:nil];
}
```

而该方法又会调用`[self initWithProductName:productName firstOwnerName:nil]` 。

这样以上多个初始化方法串联了起来。

编写初始化方法时，应该遵守以下规则：

* 其他的初始化方法都应该（直接或间接地）调用【指定初始化方法】。
* 【指定初始化方法】应该先调用【父类的指定初始化方法】，然后再对实例变量进行初始化。
* 如果某个类的【指定初始化方法】和父类的不同（方法名不同），就必须覆盖【父类的指定初始化方法】，并调用新的【指定初始化方法】。
* 如果某个类有多个初始化方法，就应该在相应的头文件中明确地注明哪个方法是【指定初始化方法】。



## 禁用 init 方法

```objectivec
// 抛出异常
- (instancetype)init {
    @throw [NSException exceptionWithName:@"Method Undefined"
                                   reason:@"Use +initWithProductName:"
                                 userInfo:nil];
    return nil;
}
 
- (instancetype)init {
    [NSException raise:@"BNRInitialization" format:@"Use +initWithProductName:"];
}
```
