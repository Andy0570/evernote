## KVC 
### 键值编码的基本概念

1. **KVC** 是 **KeyValue Coding** 的简称，它是一种可以直接通过字符串的名字(key)来访问属性的机制。使用该机制不需要调用存取方法和变量实例就可访问对象属性。本质上讲，键-值编码定义了你的程序存取方法需要实现的样式及方法签名。
2. 在应用程序中实现键-值编码兼容性是一项重要的设计原则。存取方法可以加强合适的数据封装，而键-值编码方法在多数情况下可简化程序代码。
3. 键-值编码方法在 Objective-C 非标准协议（类目）**NSKeyValueCoding**中被声明，默认的实现方法由 **NSObject** 提供，所以凡是继承自 **NSObject** 的类都具备 KVC 功能。
4. 键-值编码支持带有对象值的属性，同时也支持纯数值类型和结构。非对象参数和返回类型会被识别并自动封装/解封。
  	
### 设置和访问

键/值编码中的基本调用包括 `-valueForKey：` 和 `-setValue：forkey：` 这两个方法，它们以字符串的形式向对象发送消息，字符串是我们关注属性的关键。

示例1：
```objectivec
BNRAppliance *a = [[BNRAppliance alloc] init];

[a setProductName:@"Washing Machine"];
// 使用 KVC 重写以上代码
[a setValue:@"Washing Machine" forKey:@"productName"];
        
[a setVoltage:240];
// 使用 KVC 重写以上代码
[a setValue:[NSNumber numberWithInt:240] forKey:@"voltage"];
        
 NSLog(@"a is %@",a);
 // 使用 KVC 重写以上代码
 NSLog(@"the product name is %@",[a valueForKey:@"productName"]);
``` 

示例2：
```objectivec
Person *jack = [[Person alloc] init];
NSMutableString *name = [[NSMutableString alloc] initWithFormat:@"jack"];
[jack setValue:name forKey:@"name"];
NSLog(@"jack name : %@", [jack valueForKey:@"name"]);
```

* 使用 KVC，编译器会查找是否存在 **setter**、**getter** 方法，如果不存在，它将在内部查找名为 **_key** 或 **key** 的实例变量。通过 KVC，可以获取不存在**getter**方法的对象值，无需通过对象指针直接访问。也就是说，**KVC 能够在没有存取方法的情况下直接存取实例变量**。
* **KVC 只对对象类型有效**。当我们通过 `setValue：forKey：` 设置对象的值，或通过 `valueForKey` 来获取对象的值时，如果对象的实例变量为基本数据类型（`char`、`int`、`float`、`BOOL`）时，我们需要使用 `NSNumber` 对象对数据进行**封装**。

### key路径

* 除了通过键设置值外，键/值编码还支持指定路径，像文件系统一样，用“点”号隔开。
* 使用 key path 可以一次性遍历复杂的对象表。
* 注意顺序，第一个想要遍历的对象放在第一个。

💡💡💡你可以这样理解：KVC 支持类似于「链式语法」的特性！

示例：
`BNRDepartment. manager` 指向 `BNREmployee. emergencyContact` 指向 `BNRPerson. phoneNumber`

```objectivec
BNRDepartment *sales = ...;
BNREmployee *sickEmployee = [sales valueForKey:@"manager"];
BNRPerson *personToCall = [sickEmployee valueForKey:@"emergencyContact"];
[personToCall setValue:@"555-606-0842" forKey:@"phoneNumber"];

// 使用 Key路径 重写以上代码
BNRDepartment *sales = ...;
[personToCall setValue:@"555-606-0842"
            forKeyPath:@"manager.emergencyContact.phoneNumber"];
```

#### 一对多的关系

如果向 `NSArray` 请求一个键值，它实际上会查询数组中的每个对象来查找这个键值，然后将查询结果打包到另一个数组中并返回给你。

```objectivec
NSArray *booksArray = [NSArray arrayWithObjects:book1, book2, nil];
[book1 release];
[book2 release];
[book setValue:booksArray forKey:@"relativeBooks"];
NSLog(@"books 2: %@", [book valueForKeyPath:@"relativeBooks.price"]);
```

#### 实现简单的运算

```objectivec
NSString *count = [book valueForKeyPath:@"relativeBooks.@count"];
NSLog(@"count : %@", count);
NSString *sum = [book valueForKeyPath:@"relativeBooks.@sum._price"];
NSLog(@"sum : %@", sum);
NSString *avg = [book valueForKeyPath:@"relativeBooks.@avg._price"];
NSLog(@"avg : %@", avg);
NSString *min = [book valueForKeyPath:@"relativeBooks.@min._price"];
NSLog(@"min : %@", min);
NSString *max = [book valueForKeyPath:@"relativeBooks.@max._price"];
NSLog(@"max : %@", max);
```

## KVO 
### 键值观察的基本概念

1. **Key Value Observing**，直译为：基于键值的观察者。它提供一种机制，**当指定的对象的属性被修改后，则对象就会接受到通知**。简单的说，就是每次指定的被观察对象的属性被修改后，KVO 就会自动通知相应的观察者。
2. 与 `NSNotification` 不同，键-值观察中并没有所谓的中心对象来为所有观察者提供变化通知。取而代之地，当有变化发生时，通知被直接发送至处于观察状态的对象。`NSObject` 提供这种基础的键-值观察实现方法。   
3. 你可以观察任意对象属性，包括简单属性，对一或是对多关系。对多关系的观察者将会被告知发生变化的类型-也就是任意发生变化的对象。
4. 键-值观察为所有对象提供自动观察兼容性。你可以通过禁用自动观察通知并实现手动通知来筛选通知。

### 注册观察者

为了正确接收属性的变更通知，观察对象必须首先发送一个`addObserver:forKeyPath:options:context:`消息至被观察对象，用以传送观察对象和需要观察的属性的关键路径，以便与其注册。选项参数指定了发送变更通知时提供给观察者的信息。 使用`NSKeyValueObservingOptionOld` 选项可以将初始对象值以变更字典中的一个项的形式提供给观察者。指定`NSKeyValueObservingOptionNew` 选项可以将新的值以一个项的形式添加至变更字典。你可以使用逐位“|”这两个常量来指定接收上述两种类型的值。   

```objectivec
BNRLogger *logger = [[BNRLogger alloc] init];
__unused NSTimer *timer =
            [NSTimer scheduledTimerWithTimeInterval:2.0
                                             target:logger
                                           selector:@selector(updateLastTime:)
                                           userInfo:nil
                                            repeats:YES];

BNRObserver *observer = [[BNRObserver alloc] init];
// 让 BNRObserver 实例观察 BNRLogger 的 lastTime 属性！
// 无论 lastTime 何时发生变化，都要通知我它改变的新值以及改变之前的旧值
[logger addObserver:observer
         forKeyPath:@"lastTime"
            options:NSKeyValueObservingOptionNew | NSKeyValueObservingOptionOld
            context:nil];

[[NSRunLoop mainRunLoop] run];
```

### 接受变更后通知

当被观察对象的一个被观察的属性发生变动时，观察者会收到 `observeValueForKeyPath:ofObject:change:context:` 消息。所有观察者都必须实现这一方法。触发观察通知的对象和键路径、包含变更细节的字典，以及观察者注册时提交的上下文指针均被提交给观察者，`context` 可以为任意类型参数。

```objectivec
- (void)observeValueForKeyPath:(NSString *)keyPath
                      ofObject:(id)object
                        change:(NSDictionary<NSKeyValueChangeKey,id> *)change
                       context:(void *)context {
    NSString *oldValue = [change objectForKey:NSKeyValueChangeOldKey];
    NSString *newValue = [change objectForKey:NSKeyValueChangeNewKey];
    NSLog(@"Observed:%@ of %@ was changed from %@ to %@",
          keyPath,object,oldValue,newValue);
} 
```

注意，当在代码中将某个对象注册为观察者时，你需要传递**指针**作为 `context`。当接收变化的通知时，`context` 会随通知一起发送。`context` 可以用来回答：“这真的是我需要的通知吗？”（你可以理解为 `context` 可以作为此观察事件的唯一标识符）。例如，你的父类可能使用 KVO，如果覆盖了 `observeValueForKeyPath:ofObject:change:context:` 方法，如何知道该将哪条消息转发给父类的实现？可以创建一个单独的指针，在开始观察的时候将它作为 `context`，每次收到通知的时候将它和 `context` 进行对比。**静态变量的地址**可以很好地工作。因此，如果子类化某个使用了 KVO 的类时，可以编写如下代码：

```objectivec
static int contextForKVO;

[petOwner addObserver:self
           forKeyPath:@"fido"
              options:NSKeyValueObservingOptionNew
              context:&contextForKVO];
...

- (void)observeValueForKeyPath:(NSString *)keyPath
                      ofObject:(id)object
                        change:(NSDictionary<NSKeyValueChangeKey,id> *)change
                       context:(void *)context
{
    // 判断这是不是我的快递？
    if (context !=&contextForKVO) {
        // 不，这是我爹（父类）的快递
        [super observeValueForKeyPath:keyPath
                             ofObject:object
                               change:change
                              context:context];
    } else {
        // 这是我的快递，处理变化
    }
}
```

### 显式触发通知

**如果使用存取方法来设置属性，那么系统会自动通知观察者**。但如果处于某些原因，你选择不使用存取方法呢？比如，直接存取实例变量。这时可以通过 `willChangeValueForKey:` 和 `didChangeValueForKey:` 方法通知系统某个属性的值即将/已经发生变化。

```objectivec
- (void)updateLastTime:(NSTimer *)timer {
    NSDate *now = [NSDate date];
    [self willChangeValueForKey:@"lastTime"];
    _lastTime = now;
    [self didChangeValueForKey:@"lastTime"];
}
```

### 独立的属性

如果你不想观察 `_lastTime` 而想观察  `_lastTimeString`，即：
```objectivec
BNRObserver *observer = [[BNRObserver alloc] init];
[logger addObserver:observer
         forKeyPath:@"lastTimeString"
            options:NSKeyValueObservingOptionNew | NSKeyValueObservingOptionOld
            context:nil];
```
但是系统不知道当 `_lastTime` 属性发生变化时，`_lastTimeString` 也会随之发生变化。为了修复这个问题，你可以通过实现一个类方法显式的告诉系统，`_lastTime` 会影响  `_lastTimeString`：
```objectivec
+ (NSSet *)keyPathsForValuesAffectingLastTimeString {
    return [NSSet setWithObject:@"lastTime"];
}
```
注意这个方法的名字，它是 `keyPathsForValuesAffecting` 加上**首字母大写的键**的名字。另外，没有必要在类的头文件中声明这个方法，系统会在运行时找到它。


### 移除观察者身份

你可以发送一条指定观察方对象和键路径的 `removeObserver:forKeyPath:` 消息至被观察的对象，来移除一个键-值观察者（当我们达到目的时）。

```objectivec
[child removeObserver:self forKeyPath:@"key"];
```

