### 方法调用
OC语言中采用特定的语言调用类或者实例(对象)的方法称为**发送消息**或**方法调用**。   
```objectivec
// 方法调用格式：[对象 方法名];
[Person1 shopping];   
// 调用带参数的方法 
[Person2 goshopping:200.0]; 
```  

### set、get 方法
set、get 方法用来访问和修改对象的属性值。

* set 方法是对某一个属性赋值。
* get 方法是获取某一个属性的值。
```objectivec
//1.定义设置器方法   
//年龄的设置器方法：给对象的年龄设置的   
-(void)setAge:(NSInteger)age;   
-(void)setName:(NSString *)name;   
-(void)setWeight:(float)weight;

//2.定义多个参数的设置器方法   
//方法名：setName:setAge:setweight:   
-(void)setAge:(NSInteger)age setName:(NSString *)name setWeight:(float)weight;

//3.定义访问器方法   
//访问器方法：用于访问对象的属性值   
//OC里的命名规范:get方法名不需要加get前缀   
-(NSInteger)age;   
-(NSString *)name;
```

### 调用
在类的外部如：*main.m* 文件中想要给对象设置属性，要通过调用相应的设置器方法：
```objectivec
// 格式：[对象 方法名]
// set 方法
[Person setAge:34];   
[Person setName:@"张三"];   
[Person setWeight:80];<br />    

// get 方法  
// 用age变量接收调用的方法返回值   
NSInteger age = [Person age ];   
NSLog(@"person的年龄：%ld",age);   
NSString *name = [Person name];   
NSLog(@"person的名字：%@",name);   
    
// 点语法
//点语法只能调用 set 和 get 方法
Person.age = 34;   //等效于 [Person setAge:34]; 
NSInteger age = Person.age; //等效于 NSInteger age = [Person age ]; 
```
    
* 字符串   
	OC中字符串都是以 `@` 开头的,比如 `@"Hello"` 是OC中的字符串。
* 控制台打印   
	 C语言中使用`printf("Hello")`打印字符串, OC中使用NSLog函数打印字符串:`NSLog(@"Hello")`;
* 把一个对象使用 `%@` 打印，实际上是调用了此对象的 `description` 方法，然后取的此方法的返回值打印。   
```objectivec
NSLog(@"%@",book);     
//与下面的方法等价       
NSString *desc = [book description];        
NSLog(@"%@",desc); 
```  
 
    
###   初始化方法
* 对象必须先创建,然后初始化,才能使用。如：`NSObject *object = [[NSObject alloc] init];`；
* OC中的对象通过指针来声明。如：`Person *p` ；
* 初始化方法，是创建对象 `alloc` 之后调用，并且只能调用一次；
* 方法名必须以 `init` 开头；
* 返回值类型是 `id` 类型；
```objectivec
//自定义初始化方法，将姓名传入   
- (id)initWithName:(NSString *)name;
//多个参数的初始化方法的定义   
- (id)initWithName:(NSString *)name WithAge:(NSInteger)age;
``` 


#### 自定义初始化方法的实现
在 `init` 方法中，若要父类完成所需的一次性初始化，需要调用 `[super init]` 方法，`init` 方法返回的值，描述了被初始化的对象。   
将 `[super init]` 的结果赋给 `self` 是**Objective-C**的标准惯例，这么做是为了防止父类在初始化过程中返回的对象不同于原先创建的对象。

```objectivec
- (id)initWithName:(NSString *)name{
    //使用super可以调用父类中定义的方法
    self = [super init];
    if (self) {
        //赋值
        _name = name;
    } 
    return self;
}
```

####调用初始化方法

```objectivec
Person *p1 = [[Person alloc] init];
Person *p2 = [[Person alloc] initWithName:@"jack"];
Person *p3 = [[Person alloc] initWithName:@"Tom" WithAge:22];
```    

`initWithCString:`：C 语言的字符串转换成 Objective-C 的字符串：
```objectivec
NSString *outputValue = [[NSString alloc] initWithCString:inputValue encoding:NSUTF8StringEncoding];
```
