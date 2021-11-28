### 框架

* **框架**是由许多类、方法、函数组成的一个类库。以便于开发者更方便的开发程序。
* 许多框架的集合构成了**SDK（开发工具包）**。
* Foundation 框架是 Objective-C 语言的基础类库。此框架中的类都以 “NS” 为前缀，便于与其他类区分开。
* UIKit 框架是 UI 界面的基础库。   

Foundation中的大部分类都提供了两种方式创建对象:
* alloc 创建：`NSNumber *number1 = [[NSNumber alloc] initWithInt:100];`
* 类方法创建：`NSNumber *number2 = [NSNumber numberWithInt:100];` 
 
### NSObject 类、延迟调用
**NSObject**类是一切类的根类(基类)，它没有父类，它是所有对象的“生命”方法，也是对象生命周期的“终结”。

#### 常用方法
* 比较两个指针是否指向同一个对象(指针是否指向同一块内存区域)   
	`- (BOOL)isEqual:(id)object;`
* 调用一个方法   
	`- (id)performSelector:(SEL)aSelector; `
* 调用一个方法,并且传递一个参数   
	`- (id)performSelector:(SEL)aSelector withObject:(id)object;`
* 调用一个方法,并且传递2个参数   
	`- (id)performSelector:(SEL)aSelector withObject:(id)object1 withObject:(id)object2;`
* 某一个对象是否派生或属于某一类   
	`- (BOOL)isKindOfClass:(Class)aClass;`
* 某一个对象是否属于某类   
	`- (BOOL)isMemberOfClass:(Class)aClass;`
* 某对象是否响应指定的方法   
	`- (BOOL)respondsToSelector:(SEL)aSelector;`
* 返回指定对象的父类和本类   
	`- (Class)superclass;  `
        `- (Class)class;`

例：假设 demo1 是一个私有方法（在 .m 文件中实现，但在 .h 文件中没有声明的方法）
	
```objectivec 
//创建了一个方法选择器，封装demo1方法
SEL selector1 = @selector(demo1);

//在performSelector方法中调用selector1方法选择器，也就是demo1
[super performSelector:selector1];
[self performSelector:selector1];  //同上

//perform调用带1个参数的方法，@"hello"传给了demo2:的形参
[self performSelector:@selector(demo2:) withObject:@"hello"];

//调用带2个参数的方法
[self performSelector:(SEL) withObject:(id) withObject:(id)];

//延迟调用
[self demo1];  //没有延迟，立马调用demo1方法

//延迟调用demo1，但是代码执行到此处时，不会停在此处3秒钟
[self performSelector:@selector(demo1) withObject:nil afterDelay:3.0];
```

---  
### NSString
* [第16章——NSString](http://www.jianshu.com/p/c4eadf54cd25)
 
---
### NSMutableString
* [第16章——NSMutableString](http://www.jianshu.com/p/b628c48be542)

---
### NSArray
* [第17章——NSArray](http://www.jianshu.com/p/aaa89e070f84)

---
### NSMutableArray
* [第17章——NSMutableArray](http://www.jianshu.com/p/64443aecc4dd)

---
### Collection 类
* [第24章——Collection 类](http://www.jianshu.com/p/d84d570fe6a9)

---
### NSNumber
* [NSNumber](http://www.jianshu.com/p/6fa0926dd478)

--- 
### NSValue
* [NSValue](http://www.jianshu.com/p/928502aba37a)
    
---
### NSNull
不能在集合(collection) 中存储 `nil` 值，因为字典和数组中 `nil` 都有特殊的含义，但有时候你确实需要存储一个表示“什么都没有”的值。那就用 `NSNull` 代替，表示什么都不是，什么都没有。

```objectivec
// [zhangsan,lisi,  ,zhaoliu];
NSNull *null = [NSNull null];
NSArray *array = @[@"zhagnsan",@"lisi",null,@"zhaoliu"];
```

#### 线上的一个实际例子：
本意：如果用户已经成功登录，就获取存储在磁盘上的用户名：
 `NSDictionary *dictionary = @{@"username":_username};`
实际生产环境中，发生了这样的情况：判断用户已经登录成功，但是无法获取用户名导致应用 crash。

```objectivec
attachDatas:
Exception:{
    Name = NSInvalidArgumentException;
    Reason = "*** -[__NSPlaceholderDictionary initWithObjects:forKeys:count:]: attempt to insert nil object from objects[0]";
}
```

最初的修复方法是：获取用户名之前判断是否为空？判断为 `nil`，就传入一个空字符串 `@""`：
`NSDictionary *dictionary = @{@"username":_username?:@""};`
规范的方法是传入一个空对象： 
 `NSDictionary *dictionary = @{@"username":_username?:[NSNull null]};`
参考：[nshipster: nil / Nil / NULL / NSNull](http://nshipster.cn/nil/)
