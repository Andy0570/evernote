![生命是什么呢？生命是时时刻刻不知如何是好。](http://upload-images.jianshu.io/upload_images/2648731-90ad8a4d1caad964.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/720)


### nullable 与 nonnull 的区别

 `__nullable`：表示对象可以是 NULL 或者 nil。

 `__nonnull`：表示对象不可以为空。

`__null_unspecified`：表示对象未指定、不明确。

 如果没有显式声明，则表示 `__nonnull` 或者` _Null_unspecified` （不明确）

 在 Xcode 7 中，为了避免与第三方库的冲突，Apple 把 `__nullable` / `__nonnull` 改成了 `_Nullable`/`_Nonnull`，也支持没有下划线的写法：`nullable` / `nonnull`。

 

### 使用规范：

1. 对于属性、方法返回值、方法参数的修饰，使用 `nonnull`/`nullable`。
2. 对于 C 函数的参数、Block 的参数、Block 返回值的修饰，使用 `_Nonnull`/`_Nullable`,建议弃用 `__nonnull`/`__nullable`。

 

 为安全起见，Apple 制定的使用规则：

1. 通过 typedef 定义的类型的 nullability 特性通常依赖于上下文，即使是在 Audited Regions 中，也不能假定它为 `nonnull` ；
2. 对于复杂的指针类型（如 id * ）必须显式去指定是 `nonnull` 还是 `nullable`。例如，指定一个指向 `nullable` 对象的 `nonnull` 指针，可以使用 `__nullable id * __nonnul` ；
3. 我们经常使用的 `NSError **` 通常是被假定为一个指向 `nullable` NSError 对象的 `nullable` 指针。



### 使用区别

共有三种用法：

* `nonnull`，`nullable`, `null_unspecified`
* `_Nonnull`，`_Nullable`，`_Null_unspecified`，
* `__nonnull`，`__nullable`，`__null_unspecified`。

这几种用法在声明时的位置区别：

1. 单下划线和双下划线的需要放在类型定义之后。
2. 无下划线的需要放在类型定义之前。



### 使用示例：

#### 声明属性

```objectivec
@property (nonatomic, copy, nullable) NSString *name;
@property (nonatomic, copy) NSString *__nullable firstName;
@property (nonatomic, copy) NSString *_Nullable lastName;
```

#### 修饰方法返回值

```objectivec
- (nullable NSString *)method1;
- (NSString *__nullable)method2;
- (NSString *_Nullable)method3;
```

#### 修饰方法参数

```objectivec
- (void)methodWithString1:(nullable NSString *)aString;
- (void)methodWithString2:(NSString *__nullable)aString;
- (void)methodWithString3:(NSString *_Nullable)aString;
```

#### 例外情况

 ⚠️双指针类型对象、Block 的返回值、Block 的传入参数等，不能用 `nonnull`/`nullable` 修饰，只能用带下划线的 `__nonnull`/`__nullable` 或者 `_Nonnull`/`_Nullable`。

```objectivec
- (void)methodwithError1:(NSError * _Nullable * __null_unspecified)error;
- (void)methodwithError2:(NSError * nullable * null_unspecified)error;
```



#### Block 返回值

```objectivec
- (void)methodWithBlock1:(nullable void(^)(void))block;
// ⚠️ 以上 nullable 修饰的是方法传入参数，表示传入的 Block 可以为空，而不是修饰 BlocK 返回值。

- (void)methodWithBlock2:(void (^ _Nullable)(void))block;
- (void)methodWithBlock3:(void (^ __nullable)(void))block;
```



#### Block 传入参数

```objectivec
- (void)methodWithBlock21:(nullable id nonnull(^)(id nullable parameters))block;
// ⚠️ 以上 nullable 修饰的是方法传入参数，表示传入的 BlocK 可以为空。而 __nonnull 用于修饰 Block 返回值 id 不能为空。

- (void)methodWithBlock22:(id  nonnull(^ nullable)(id _Nullable parameters))block;
- (void)methodWithBlock23:(id  _Nonnull (^ _Nonnull)(id _Nullable parameters))block;
```



### 宏 NS_ASSUME_NONNULL_BEGIN 与 NS_ASSUME_NONNULL_END

```objectivec
#import <Foundation/Foundation.h>

// 在这两个宏之间的代码，所有简单指针对象都被假定为 nonnull ，因此我们只需要去指定那些 nullable 指针对象即可。
NS_ASSUME_NONNULL_BEGIN

@interface MyObject : NSObject

// 默认为 nonnull
@property (nonatomic, copy) NSString *aString;

// 方法返回值默认为 nonnull
// 方法的参数显式声明为 nullabel,表示入参可以为空
- (id)methodWithString:(nullable NSString *)string;

@end

NS_ASSUME_NONNULL_END
```





### 参考
* [Apple Blog:Nullability and Objective-C](https://developer.apple.com/swift/blog/?id=25)
* [Difference between nullable, __nullable and _Nullable in Objective-C](https://stackoverflow.com/questions/32452889/difference-between-nullable-nullable-and-nullable-in-objective-c)
* [nshipster: nil / Nil / NULL / NSNull](http://nshipster.cn/nil/)
