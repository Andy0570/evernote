```objective-c
#ifndef _OBJC_NSOBJECT_H_
#define _OBJC_NSOBJECT_H_
 
#if __OBJC__
 
#include <objc/objc.h>
#include <objc/NSObjCRuntime.h>
 
@class NSString, NSMethodSignature, NSInvocation;
 
#pragma mark - 协议部分
 
@protocol NSObject
 
// 判断两个对象是否相等, 如相等返回YES, 否则返回NO
- (BOOL)isEqual:(id)object;
// 获取对象 hash 值, 两对象相等 hash 值也相等
@property (readonly) NSUInteger hash;
 
// 获取对象的父类
@property (readonly) Class superclass;
// ❇️ 获取当前对象的类
- (Class)class OBJC_SWIFT_UNAVAILABLE("use 'type(of: anObject)' instead");
// 获取当前对象
- (instancetype)self;
 
// 发送指定的消息给对象, 返回消息执行结果(相当于方法调用)
- (id)performSelector:(SEL)aSelector;
// 发送带一个参数的消息给对象, 返回消息执行结果(相当于方法调用)
- (id)performSelector:(SEL)aSelector withObject:(id)object;
// 发送带两个参数的消息给对象, 返回消息执行结果(相当于方法调用)
- (id)performSelector:(SEL)aSelector withObject:(id)object1 withObject:(id)object2;
 
//  判断对象是否继承 NSObject
- (BOOL)isProxy;
 
// ❇️ 判断对象是否是给定类或给定类子类的实例
- (BOOL)isKindOfClass:(Class)aClass;
// ❇️ 判断对象是否是给定类的实例（不包括子类）
- (BOOL)isMemberOfClass:(Class)aClass;
// ❇️ 检查对象是否实现了指定协议类的方法
- (BOOL)conformsToProtocol:(Protocol *)aProtocol;
 
// ❇️ 检查对象能否响应指定的消息
- (BOOL)respondsToSelector:(SEL)aSelector;
 
// 对象引用计数加1, 在 MRC 下使用
- (instancetype)retain OBJC_ARC_UNAVAILABLE;
// 对象引用计数减1, 在 MR C下使用
- (oneway void)release OBJC_ARC_UNAVAILABLE;
// 对象引用计数以推迟方式自动减1, 在 MRC 下使用
- (instancetype)autorelease OBJC_ARC_UNAVAILABLE;
// 获取对象引用计数, 在 MRC 下使用
- (NSUInteger)retainCount OBJC_ARC_UNAVAILABLE;
// 获取对象存储空间, 在 MRC 下使用
- (struct _NSZone *)zone OBJC_ARC_UNAVAILABLE;
 
// 获取对象描述信息
@property (readonly, copy) NSString *description;
@optional
// 获取对象在调试器中的描述信息
@property (readonly, copy) NSString *debugDescription;
 
@end
 
#pragma mark - 类部分
 
OBJC_AVAILABLE(10.0, 2.0, 9.0, 1.0)
OBJC_ROOT_CLASS
OBJC_EXPORT
@interface NSObject <NSObject> {
Class isa OBJC_ISA_AVAILABILITY;
}
 
// 运行时加载类或分类调用该方法, 每个类只会调用一次
+ (void)load;
// 类实例化使用前需要先初始化, 一个类调用一次, 如果子类没有实现该方法则会调用父类方法
+ (void)initialize;
// 初始化对象
- (instancetype)init
#if NS_ENFORCE_NSOBJECT_DESIGNATED_INITIALIZER
NS_DESIGNATED_INITIALIZER
#endif
;
 
// 为新对象分配内存空间并初始化, 等于 [[NSObject alloc] init]
+ (instancetype)new OBJC_SWIFT_UNAVAILABLE("use object initializers instead");
// 为新对象分配内存空间, 参数传 nil
+ (instancetype)allocWithZone:(struct _NSZone *)zone OBJC_SWIFT_UNAVAILABLE("use object initializers instead");
// 为新对象分配内存空间
+ (instancetype)alloc OBJC_SWIFT_UNAVAILABLE("use object initializers instead");
// 释放对象, 当对象的引用计数为0时会调用此方法
- (void)dealloc OBJC_SWIFT_UNAVAILABLE("use 'deinit' to define a de-initializer");
// 垃圾回收器调用此方法前处理它所使用的内存
- (void)finalize OBJC_DEPRECATED("Objective-C garbage collection is no longer supported");
 
// 复制为不可变对象
- (id)copy;
// 复制为可变对象
- (id)mutableCopy;
 
// 在指定的内存空间上复制为不可变对象, 在 MRC 下使用
+ (id)copyWithZone:(struct _NSZone *)zone OBJC_ARC_UNAVAILABLE;
// 在指定的内存空间上复制为可变对象, 在 MRC 下使用
+ (id)mutableCopyWithZone:(struct _NSZone *)zone OBJC_ARC_UNAVAILABLE;
 
// 判断实例是否能够调用给定的方法
+ (BOOL)instancesRespondToSelector:(SEL)aSelector;
// 判断类是否遵从给定的协议
+ (BOOL)conformsToProtocol:(Protocol *)protocol;
// ❇️ 获取指定方法实现的 IMP 指针
- (IMP)methodForSelector:(SEL)aSelector;
// 获取指向实例方法实现IMP的指针
+ (IMP)instanceMethodForSelector:(SEL)aSelector;
// 找不到函数实现的将调用此方法抛出异常
- (void)doesNotRecognizeSelector:(SEL)aSelector;
 
// 返回消息被第一个转发的对象, 对象没有找到SEL的IML时就会执行调用该方法
- (id)forwardingTargetForSelector:(SEL)aSelector OBJC_AVAILABLE(10.5, 2.0, 9.0, 1.0);
// methodSignatureForSelector:返回不为nil则调用该方法, 可以重写该方法将SEL转发给另一个对象
- (void)forwardInvocation:(NSInvocation *)anInvocation OBJC_SWIFT_UNAVAILABLE("");
// 获取方法签名, 对象没有找到SEL的IML时就会执行调用该方法, 可以重写该方法抛出一个函数的签名，再由forwardInvocation:去执行
- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector OBJC_SWIFT_UNAVAILABLE("");
 
// 获取实例方法签名
+ (NSMethodSignature *)instanceMethodSignatureForSelector:(SEL)aSelector OBJC_SWIFT_UNAVAILABLE("");
 
// 允许弱引用标量, 对于所有allowsWeakReference方法返回NO的类都绝对不能使用__weak修饰符
- (BOOL)allowsWeakReference UNAVAILABLE_ATTRIBUTE;
// 保留弱引用变量, 在使用__weak修饰符的变量时, 当被赋值对象的retainWeakReference方法返回NO的情况下, 该变量将使用“nil”
- (BOOL)retainWeakReference UNAVAILABLE_ATTRIBUTE;
 
// 判断是否是另一个类的子类
+ (BOOL)isSubclassOfClass:(Class)aClass;
 
// 动态解析一个类方法
+ (BOOL)resolveClassMethod:(SEL)sel OBJC_AVAILABLE(10.5, 2.0, 9.0, 1.0);
// 动态解析一个实例方法, 对象没有找到SEL的IML时就会执行调用该方法, 可以重写该方法给对象添加所需的SEL
+ (BOOL)resolveInstanceMethod:(SEL)sel OBJC_AVAILABLE(10.5, 2.0, 9.0, 1.0);
 
// 获取对象 hash 值, 两对象相等 hash 值也相等
+ (NSUInteger)hash;
// 获取对象的父类
+ (Class)superclass;
// 获取类
+ (Class)class OBJC_SWIFT_UNAVAILABLE("use 'aClass.self' instead");
// 获取对象描述信息
+ (NSString *)description;
// 获取对象在调试器中的描述信息
+ (NSString *)debugDescription;
 
@end
 
#endif
 
#endif
```

