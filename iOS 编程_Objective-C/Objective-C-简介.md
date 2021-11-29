### 简介
Objective-C 通常写作 objc 或 OC，是根据 C 语言所衍生出来的语言，继承了 C 语言的特性，是扩充 C 语言的面向对象编程语言。
Objective-C 是**面向对象**语言。而 C 语言是**面向过程**语言。
Objective-C 是 MAC OSX 和 iOS 开发的基础语言。

### 优点及缺点
* Objective-C 是非常“实际”的语言。它使用一个用 C 写成的，很小的运行库，OC 写成的程序通常不会比其原始码大很多。
* Objective-C 的最初版本并不支持垃圾回收。
* Objective-C 不包括命名空间机制（namespace mechanism）。
* 虽然 Objective-C 是 C 的母集，但它也不视 C 的基本型别为第一级的对象。
* 和 C++ 不同，Objective-C 不支持运算子重载（它不支持 ad-hoc 多型）。
* Object-C 仅支持单一父类继承，不支持多重继承。

### 和 C++ 的比较
1. 目前只有 **Apple** 使用 Objective-C 作为其支持的语言。
2. 与 C++ 的不同之处有：
	* O-C 中所有的类都必须直接或间接继承自 NSObject;
	* O-C 中所有对象都是指针的形式;
	* O-C 用 `self` 代替 `this`;
	* O-C 使用 `id` 代替 `void`;
	* O-C 使用 `nil` 表示 `NULL`;
	* O-C 只支持单继承;
	* O-C 使用 `YES`/`NO` 表示 `TRUE`/`FALSE`;
	* O-C 使用 `#import` 代替 `#include`;
	* O-C 中用消息表示类的方法，并采用 `[aInstance method:argv]` 调用形式;
	* O-C 支持反射机制;
	* O-C 支持Dynamic Typing, Dynamic Binding和Dynamic Loading。

3. 与C++的相同之处有：
	* 与C共享的部分一致;
	* 可以使用```assert(BOOL)```, 一般用```NSCParameterAssert(BOOL)```代替。

4. O-C中的命名前缀说明：
	* NS-：NextStep;
	* CF-：Core Foundation;
	* CA-：Core Animation;
	* CG-：Core Graphics;
	* UI-：User Interface;

5. O-C中的消息特殊性：
	* 调用消息的类可以不知道如何响应这个消息。如果它不知道如何处理这个消息，它会自动将这个消息传递给其他的类，比如它的父类。
	* 调用消息的类可以是 `nil`。在 C++ 中，在使用类方法之前，我们都需要检查对象是否为空，所以在实现析构函数的时候，常会有如下的代码，如 `if (var) { delete var; } ` 但是在 Objective-C 中，我们就可以直接写 `[var release];` 即使 `var = nil` 也不会有问题。

6. O-C中的函数声明格式有：
  ```objective-c
  -/+ (return type)function_name;
  -/+ (return type)function_name:(parameter type)parameter;
  -/+ (return type)function_name:(parameter type)parameter1 otherParameter:(parameter_type)parameter2;
  ```
以上参数说明：-表示一般函数，+表示静态函数。otherParameter是参数的别名(第一个参数的别名省略)，在函数调用时方便指定。

7. O-C中的构造/析构函数:
	* O-C中的 `init()/release()` 对应于C++的构造/析构函数。`alloc()/dealloc()`也就对应于C++的 `new` 和 `delete`，其中的 `dealloc()` 由于引用计数的自动调用而不用手动调用。
	* O-C中父类的 `init()/release()` 函数需要子类的手动调用。而且每次都必须调用。不同于C++的自动调用。
	* 构造函数 `(- (id) init)` 调用形如: `CSample* pSample=[[CSample alloc] init]`; 其中 alloc 是继承来的 static 函数，init 是继承来的一般函数，如重写一般函数时，则相当于 C++ 的覆盖(不带参数)或重载(带参数)。
	* 析构函数(`- (void)release`)将引用计数减1，当=0时父类的 release() 会自动调用 dealloc;

8. 当O-C没有数据成员时，可省略{},建议保留。

9. 继承下来的方法，如：`-(id)init` 可以头文件中省略，建议保留。

10. 0-C中只有数据成员的访问限制，没有方法的访问限制。
	* 同 C++ 一样，数据成员有三种访问限制 public, protected, private，默认是 protected。   
	示例：
```objc
@interface AccessExample: NSObject {    

@public
int publicVar;

@protected
int protectedVar;

@private
int privateVar; 
} 
@end  
``` 

方法的访问限制可通过Category（类别）实现，示例代码：   
```objc
// MyClass 类
@interface MyClass   
-(void) sayHello    
{   
NSLog(@"Hello");   
}   
@end
// MyClass  的扩展类
@interface MyClass(Private)   
-(void) kissGoodbye;   
@end   
```

11. O-C中没有类的静态变量，只有全局变量。

12. O-C中的数组 NSArray 可以保存不同类型的数据。

13. O-C也支持 run-time 时的类型检查：
```- (BOOL) isKindOfClass: classObj```   用于判断该对象是否属于某个类或者它的子类。
```- (BOOL) isMemberOfClass: classObj```   用于判断该对象是否属于某个类（这里不包括子类) 。
```- (BOOL) respondsToSelector: selector```   用于判断该对象是否能响应某个消息。这里，我们可以将@selector后面带的参数理解为C++中的函数指针。   
注意:1）不要忘了@    
　　 2）@selector后面用的是()，而不是[]。    
　　3）要在消息名称后面跟：，无论这个消息是否带参数。    
　　	如：```[pSquare    respondsToSelector:@selector(Set: andHeight:)]。```   
```+ (BOOL) instancesRespondToSelector: selector```   用于判断该类是否能响应某个消息。这是一个静态函数。   
```-(id) performSelector: selector ：```   调用对象的selector方法。   
```conformsToProtocol``` 类似于```respondsToSelector ```，用于动态检查某个对象是否遵守某个协议。

14. Category：范畴/扩展类：在没有源代码的情况下，为一个已经存在的类添加一些新的功能。
	* 只能添加新的方法，不能添加新的数据成员；
	* Category的名字必须是唯一的。

15. Protocol：协议类：相当于C++中的纯虚类   
```
//创建协议
#import <Foundation/Foundation.h>
@protocol MyProtocol <NSObject>

//规则1
//规则2

@required    //表示修饰的协议方法必须实现
//完成作业的协议方法
- (void)finishTask;

//不能迟到的协议方法
-(void)dontLate;

@optional   //表示修饰的协议可选,可实现/可不实现
//穿戴整洁
- (void)wearNeat;

@end
```

```
//  类签订协议:<协议名>
@interface Student: NSObject < MyProtocol > {

//实现协议方法
- (void)finishTask{...}
-(void)dontLate{...}

 } @end
```   
	
```Objective-C
// 使用：
Student * student1 = [[Student alloc] init];
id< MyProtocol > student1 = dat;
```   
* 说明：我们首先声明了MyProtocol协议，任何遵守这个协议的类，都必须实现@required中的方法。在Objective-C 中，我们通过<>来表示遵守某个协议。当某个类声明要遵守某个协议之后，它就必须在.m文件中实现这个协议中的所有方法。使用id< MyProtocol > 作为类型，而不是像C++中的MyProtocol* var。 
 
16. IBOutlet, IBAction: 告诉Xcode之后会使用Interface Builder关联该插座变量、动作。如果你希望在Interface Builder中能看到这个控件对象，那么在定义的时候前面加上IBOutlet，在IB里就能看到这个对象的outlet，如果你希望在Interface Builder里控制某个对象执行某些动作，就在方法前面加上IBAction。
形如：
```
// 声明一个属性 questionLabel，可以指向 UILabel 对象
@property(nonatomic, weak) IBOutlet UILabel *questionLabel;

//方法：实现 Botton 动作
- (IBAction)showQuestion:(id)sender{
  //...
}
```

17. 尽量避免在一行语句中进行两层以上的嵌套。

18. 消息转发：`- (void) forwardInvocation: (NSInvocation *)anInvocation;`


### 扩展的关键字

#### @interface
类型声明，类似于 c++ 中的 class，区别在于OC中的声明与实现是强制分开的，**@interface存放于.h文件中，而@implementation存放于.m文件中。**@interface关键字用于类型的声明，包括数据成员、方法声明、属性等。方法的参数传递采用中缀符的形式，利用“`:`”分割参数名和被传递参数，类型的声明以 `@interface` 开头，以 `@end` 结束，通常一个类型的声明采用下面的结构：
```
@class someOtherObject //外部类型声明   
@interface NewClassName : ParentClassName //类型声明 子类：父类   
{  
//    定义属性   
//    全局变量，成员变量，实例变量   
    NSInteger _age;  //年龄   
    NSString *_name;  //姓名   
    float _weight;  //体重     
}  
//方法定义   
-(void)shopping；//不带参数的方法   
-(void)goshopping:(float)price; //带参数的方法       
-(id)someMethod:(int)someArg someOtherArgName:(int)someOtherArg;     
//类方法,使用类名调用的方法 [类名 方法]   
//以'+'开头    
+(id)someMethod:(int)someArg;     
-(id)init; //初始化方法     
@property int num; //属性,为属性自动生成set和get方法   
@end   
```

#### @implementation
对应于@interface的类型声明，@implementation表示一个类型的实现过程，存放于‘.m’文件中，同样以@end结束，实现的格式通常如下：
```
//预编译指令，只编译 .m 文件，而 .h 文件导入到此处处理。   
#import "Person.h"   
@implementation NewClassName     
-(id)someMethod:(int)someArg someOtherArgName:(int)someOtherArg   
{   
//实现代码   
}    
+(id)someMethod:(int)someArg    
{   
//类方法实现代码    
//与实例方法的区别：类方法中不能使用属性    
 }   
@synthesize num=i; //将属性与变量进行对应   
@end
```

#### new、alloc

Object-C中的方法调用形式采用消息发送的方式，通常调用的形式如：
```objc
[someObject someMethod:firstArg someOtherArgName:otherArg];
```  

实例的初始化也采用消息发送的形式，可以简单的调用类型的new方法来获取一个实例对象，简单实例化的方法通常是：   
```Objective-C
someObject *obj = [someObject new]; //类的实例化
```    

**new** 方法的实际过程是调用 **alloc** 和 **init** 方法，因此如果需要采用自定义的方法来初始化实例，则需要自己重写 `init` 方法，通常的初始化方式为：  
```Objective-C
someObject *obj = [[someObject alloc] init]; //采用无参数的init实例化   
someObject *obj = [[someObject alloc] initWithArg:Arg]; //采用参数的实例化 
```

#### @class
**@class**是一个前向引用声明，类似于C++中的friend友元声明，其作用是告诉编译器其后面的字段代表一个类型名称，尽管不知道类型的具体实现，但是只需要将其作为一个类型名称处理即可。通常在使用复合的结构时可以采用**@class**来减少头文件的相互引用，如果出现循环依赖，则需要依靠**@class**来避免引用的死循环。   
* @class 可以解决 #import 引起的相互导入而导致的循环引用问题。
* 在头文件里要使用其他类,一般用@class, 在实现文件用#import导入类文件。   
通常使用形式为：   
```
@class someOtherObject;      
@interface someObject:NSObject   
{   
someOtherObject *obj;   
}   
@end   
```

#### #import
类似于C语言中的 **#include**，导入一个头文件,获取该头文件中类的定义—方法和属性。

#### @property
* 尽管可以使用 obj->arr 的形式去强制读取对象的成员变量，但是良好的编程形式是对外界提供成员变量的读写接口。**@property**关键字提供了外界对成员变量的访问接口，**其本质是自动为某一个属性生成set和get方法**。
* 根据不同的需要，可以添加 **readonly**（只读，相当于只添加get不添加set方法）或者 **readwrite**（读写，如果不添加则为默认）；
* 还有三种赋值方式可选 ：**assign**（直接赋值，通常用于基本类型），**retain**（释放旧值，增加新的retaincount），**copy**（常用于字符串，生成一个新的拷贝）.      

 **格式**：
 
	@property(原子性©,赋值©,读写性©)NSString *XXX;  
          1.原子性    
            * atomic：多线程环境下，存在线程保护；   
            * nonatomic：多线程环境下，不存在线程保护；   
          2.赋值     
            * assign：直接赋值，默认；(对象之外的类型使用)   
            * retain：保留对象；（所有的对象，都使用 retain）   
            * copy：拷贝对象；NSString 的对象都使用 copy    
          3.读写性     
            * readwrite：生成getter、setter方法，默认；   
            * readonly：只生成getter方法   

 **作用**：   
 1.生成一个 _XXX 属性，此属性是 private ,不能被继承；   
 2.自动为该属性生成 set 和 get 方法。   
 通常使用的方式如下： 
```  
@interface someObject:NSObject   
{   
int i; //成员变量    
}   
@property (assign,readonly) int num; //属性(注意无需下划线)   
@end
```

#### @synthesize
与 **@property** 对应，将一个外在属性与成员变量相关联，定义在 **@implementation** 中，如果属性名与变量名一致则可以省略变量名。常用方法：

	@implementation someObject 
	  
	@synthesize num=i;//如果属性名也为i，则可以直接写为 @synthesize i 
	
	//更改属性名，将默认的 _username 改为 _name
	@synthesize username = _name;  
	@end   

#### 内存管理
* 内存管理是关于如何管理 **对象** 生命周期的编程原则。
* Object-C采用**引用计数**的方式进行内存管理，由于所有的对象都继承于 **NSObject**，因此所有的对象都可以接受 **NSObject** 的三个方法：  
``` 
-(id)retain;   //retain方法将对象的引用计数加1并返回该对象   
-(void)release; //release将引用计数减1   
-(unsigned)retainCount; //retainCount方法返回对象当前的引用计数  
``` 
* 当采用 `new`、`alloc`、`copy` 方法创建一个对象时，它的引用计数被置为1，如果程序中对该对象进行操作，则应根据需要，通过调用 `retain` 和 `release` 方法来保证该对象在不需要的时候被清除。当一个对象的引用计数被置为0后，系统会自动向对象发送一个 `dealloc` 消息，将其占有的资源释放。通常情况下，如果一个对象的初始化过程调用了其他资源，则应该重写该对象的 `dealloc` 过程，保证在对象的销毁期正确释放这些资源。
* Object-C进行内存管理的3条规则是：   
1. 如果使用 `new`、`alloc`、`copy` 操作获得一个对象，则该对象的保留计数器值为1.      
2. 如果通过任何其他方法获得一个对象，则假设该对象的保留计数器值为1，而且已经被设置为自动释放。
* 如果保留了某个对象，则必须保持 `retain` 方法和 `release` 方法的使用次数相等。

* **retainCount**：内存管理默认为系统自动.   
内存管理改为手动方法：选中Project -> Build settings -> 搜索框搜索“arc”,将 Object-C Automatic Reference Counting值设置为No；   
*手动内存管理(MRC)黄金法则:*如果对一个对象使用了 **alloc**,**[mutable]copy**,**retain**, 那么你必须使用相应的 **release** 或者 **autorelease** 释放.

* 为了更加方便的进行能存管理，cocoa中提供了一个自动释放池（**autorelease pool**）的概念，每一个类都继承了一个**autorelease**方法，当调用对象的**autorelease**方法时，该对象会被加入到开始创建的自动释放池中。当程序进行到不再需要自动释放池中的对象时，将自动释放池释放的时候会向池中的所有对象发送一个 `release` 消息，从而保证不再需要的对象被正确的释放。通常的用法如下：

```
	NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];   
	someObject * obj = [[someObject alloc] init];   
	[obj autorelease]; //将 obj 对象加入自动释放池中，引用计数不会-1.   
	//其他代码  
	[pool release]; //执行该语句时，系统会向池内所有的对象发送 release 消息；
```	
在这个例子中，如果对obj进行的其他 `retain` 操作和 `release` 操作保持一致的话，则会将obj的引用计数变为0，从而调用它的 `dealloc` 方法进行资源释放.

#### ARC
* ARC自动引用计数(automatic reference counting)，提供自动管理内存的功能。
* 不需要手动管理引用计数,不需要也不允许 `retain`、`release`、`autorelease`。
* 注意版本的支持是在iOS4(不支持弱引用)、iOS5上。

#### 内存中的区域划分
* **栈**:栈区(stack)由系统自动分配和释放 ,存放局部变量的值等。
* **堆**:一般由程序员分配和释放,如果不释放,则出现内存泄露。程序退出时, 系统会回收你的内存。特点:无序、速度慢、容量大。
* **静态存储区**:全局变量(外部变量)和静态变量都存放在静态区域。当程序结束时,系统回收。
* **常量区**:存放常量的内存区域,程序结束时,系统回收。
* **代码区**:存放二进制代码的区域。

##### 数组内存管理
* 如果一个对象被添加到数组,那么这个对象的引用计数会被 **retain** 而 +1.
* 数组销毁或者 **removeAllObjects**,会给每一个元素发送 **release** 消息。
	
```
//对数组中的每一个元素发送release消息  
[array removeAllObjects]; 
	
//移除下标为1的元素,同时向它发送release消息  
[array removeObjectAtIndex:1];
``` 

#### 类方法创建的内存管理
* **Foundation** 中的类可以通过 **alloc** 创建和 **类方法** 创建,区别在于内存管理不一样。
* **类方法** 创建的对象是加入了自动释放池。
* 新语法（简便写法）创建的对象和类方法创建的对象相同,由自动释放池管理。

#### Category （类别、类目）
* 类目是为现有的类提供一个新的方法的方法，即使没有一个类的源代码，仍然可以向类中添加一个方法以方便使用。
* 通过类目扩展的方法,子类也能继承下来。
* 类目不能为原始类增加属性。
* 无法调用原始类的方法。
* 类目的主要目的有3个：将类的实现分散到多个不同的文件或框架中，创建对私有方法的前向引用，向对象添加非正式协议。
* 类目的定义和类的定义相似。在.h文件里声明,.m文件里实现方法。
* 定义类目的文件命名规则: 类名+类目名称,如“NSString+Revert”。     
   
**类目的创建：** 菜单选File -> New -> File -> Object-C File ->选择File Type为 Category

类别的声明方法：

	@interface NSString (Extension)
	{
	//        NSString *s = @"hello";  错误！类目不能创建属性！！！
	}

	// 要扩展的方法 
	-(id)someMethod:(int)someArg; 

	//覆写父类方法
	- (NSArray<NSString *> *)componentsSeparatedByString:(NSString *)separator;

	@end 
    
类别的实现方法：

	@implementation NSString (Extension)

	-(id)someMethod:(int)someArg; {
    //实现代码
	}

	- (NSArray<NSString *> *)componentsSeparatedByString:(NSString *)separator{
    //无法调用原始类的方法
    //实现代码
    return expression;
	}
	@end  
 
####  延展
在类的实现文件.m中定义类目,叫**延展**。

	//延展：使用类目声明方法，在实现类中实现此方法
	//延展可以在.h中声明,也可以在.m中声明
	@interface Persion (p2)
	
	-(id)someMethod:(int)someArg;
	
	@end

#### @protocol （协议）
* Object-C中的协议类似于java中的接口，通过**@protocol**关键字定义一个或多个需要遵从协议的对象实现的方法。
* 协议本身不是类，它定义了一组方法，让其他类来实现。
* 在类实现时需要将协议中规定的方法都予以实现。Object C 2.0增加了2个新的协议修饰符**@optional**和**@required**，可以规定协议中的方法是否为必须实现的方法。   
* **创建方式：**菜单选File -> New -> File -> Object-C File ->自定义协议名，选择File Type为 Protocl。
协议定义的方法：
```
@protocol MyProtocol < NSObject >

@required //表示修饰的协议方法必须实现    
-(void)someMethod1; //协议方法

@optional //表示修饰的协议可选，可实现/可不实现      
-(void)someMethod2;     

@end
```

采用协议的方法需在类声明时使用尖括号注明其需要使用的协议：   
`@interface someObject:NSObject <MyProtocol>`   
然后在 .m 文件中添加协议中需要实现的方法。

#### self 和 super
* self指的是类对象本身;
* super是父类对象本身;
* self用来调用本类对象的方法;
* self关键字先从本类中查找是否有此方法，如果没有，再从父类中调用此方法;
* super调用从父类继承下来的方法;
* super关键字直接调用父类中定义的方法.


### 参考
* [百度百科：Objective-C](http://baike.baidu.com/link?url=da9znr6i-KHqdfvWMs1YVSHwjL009DT8hwE_ETIm8iaEXxcHo8tblkzLd4fxZkHF-EEIAjDHbxNLLjhYxutiwK)
* [简书：iOS 整体框架类图值得收藏](https://www.jianshu.com/p/c255ad8259b2)
