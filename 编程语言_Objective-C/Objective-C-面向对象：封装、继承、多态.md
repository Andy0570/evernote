### 面向对象语言的三大特征
1. 封装 (Encapsulation) ；
2. 继承 (Inheritance)；
3. 多态 (Polymorphism)；

### 封装、权限修饰符（@public、@private、@protected）
* 隐藏属性、方法或者实现细节的过程称之为**封装**。   
* **权限修饰符**用来修饰**实例变量**，用来控制实例变量的访问权限。
* 在语法上，不存在私有方法。但约定：在 .m 文件中实现，但在 .h 文件中没有声明的方法称之为**私有方法**。

```objc
#import <Foundation/Foundation.h>
@interface Car : NSObject {
    /*    属性的封装
     *   1.使类的属性不让外部直接访问
     */  2.外部使用这个类时，不需要关心这个类具有哪些属性
    
    //@public 修饰的成员变量，外部可以直接访问
    //不建议直接使用，因为破坏了属性的封装性
@public
    NSString *_name;    //名称
    NSString *_color;   //颜色
    
    //@private 是私有属性，只能在类的内部访问
    // 子类不能访问private修饰的属性
@private
    float width;    //车宽
    
    //@protected 受保护的属性，
    //类的内部，子类都可以访问，类的外部不能访问
@protected  //默认的修饰
    float _capcity; //耗油量
    
    @package  //修饰的变量只能在当前所在的框架中访问
    
}

/*方法的封装
 1.对功能实现的细节隐藏于封装
 2.保护属性
 */
- (void)run;

//封装还可以保护属性值的有效性
- (void)setCapicity:(float)capicity;

@end
```
	//.m 文件实现中
	//调用方法时可以对属性值的有效性进行判断
	- (void)setCapicity:(float)capicity{
    if (capicity < 0) {
        //给出错误处理
        return 0;
    }
    _capcity = capicity;
	}

	
### 	继承
* 子类具有父类的一般特性（包括属性和行为），以及自身特殊的特性。
* 定义一个通用的类，它有基本的实例变量。子类继承了该类，就可以拥有这些实例变量。子类也可以定义自己的实例变量。
* **被继承的类叫超类或父类（`superclass`），继承超类的类叫子类或派生类（`subclass`）**。
* OC中继承的语法规则为: `@interface 子类 : 父类`
* 子类自动继承父类的属性和方法。
* OC语言的类都是**单继承**，即一个子类只能有一个父类。（*多继承即一个子类可以有多个父类，它继承了多个父类的特性*）


**父类：**
```objc
@interface ClassA : NSObject
{
int x;
} 
- (void) run; 
@end
```

**子类：**
```objc
@interface ClassB : ClassA  
- (void) printVar;
@end
```

### 方法重写

* 通过方法重写，子类可以改变从父类继承的行为。
* 被重写的方法定义必须与父类中的方法完全一样。
*  方法重写必须满足两个条件：
    1. 必须要有继承关系；
    2. 重写方法的方法名、参数列表必须都一致；   
例如：重写 init 初始化方法。

### 多态
* 多态是指同一种类型，具有多种表现形态。
* 多态的条件： 
  1. 必须存在继承关系。
  2. 子类重写父类的方法。
  3. 父类声明的变量指向子类对象。


* **多态**能够使来自不同类的对象定义相同名称的方法（不同的类可以有相同的方法名）。
* **动态类型**能使程序直到执行时才确定对象所属的类。
* **动态绑定**能使程序直到执行时才确定实际要调用的对象方法。

**Objective-C 系统总是跟踪对象所属的类**。
释义：先判定对象所属的类，然后在**运行时**确定需要动态调用的方法，而不是在编译的时候。

![](http://upload-images.jianshu.io/upload_images/2648731-842d1057e0081625.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


