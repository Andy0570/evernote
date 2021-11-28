![](http://upload-images.jianshu.io/upload_images/2648731-30062a72b4270bc7.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 第2章 对象、消息、运行期

**对象**：用 **Objective-C** 等面向对象语言编程时，“对象”（object) 是 “基本构造单元"（building block)，开发者可以通过对象来存储并传递数据。

**消息传递**（Messaging)：在对象之间传递数据并执行任务的过程。

**Objective-C 运行期环境**：当应用程序运行起来以后，为其提供相关支持的代码叫做“Objective-C 运行期环境” (Objective-C runtime)，它提供了一些使得对象之间能够传递消息的重要函数，并且包含创建类实例所用的全部逻辑。



### 第6条：理解“属性”这一概念

#### @property：属性

**属性**（property）用于封装对象中的数据。而 Objective-C 对象通常会把其所需要的数据保存为各种实例变量。

**实例变量**通过**存取方法**（access method）来访问：

* **获取方法**（getter）用于读取变量值。
* **设置方法**（setter）用于写入变量值。

编写 Objective-C 代码时很少**定义实例变量的作用域**。因为：对象布局在编译期就已经固定了，访问实例变量时，编译器会把其替换为硬编码（hardcode）的偏移量（offset）。表示该变量距离存放对象的内存区域的起始地址有多远。

访问属性，可以使用**点语法**。

```objective-c
EOCPerson *person = [[EOCPerson alloc] init];
person.firstName = @"Chester"; //Same as
[person setFirstName:@"Chester"];

NSString *lastName = person.lastName; //Same as
NSString *lastName = [person lastName];
```

**自动合成**（autosynthesis)：使用属性时，编译器会自动编写访问属性所需的方法，自动向类中添加适当类型的实例变量。

#### @synthesize：指定实例变量的名字

```objective-c
// 将生成的实例变量命名为_myFirstName与_myLastName
@implementation EOCPerson
@synthesize firstName = _myFirstName;
@synthesize lastName  = _myLastName;
@end
```

#### @dynamic

若不想令编译器自动合成存取方法，则可以自己实现。如果你只实现了其中一个存取方法，那么另外一个还是会由编译器来合成。还有一种办法能阻止编译器自动合成存取方法，就是使用 `@dynamic` 关键字，它会告诉编译器：不要自动创建实现属性所用的实例变量，也不要为其创建存取方法。

#### 属性特质

```objective-c
@property (nonatomic, copy, readonly) NSString *firstName;
```

##### 原子性

* **atomic**（默认）
* **nonatomic**：非原子性、不使用同步锁

> atomic 与 nonatomic 的区别是什么呢？
>
> 具备 atomic 特质的获取方法会通过锁定机制来确保其操作的原子性。这也就是说，如果两个线程读写同一属性，那么不论何时，总能看到有效的属性值。若是不加锁的话（或者说使用 nonatomic 语义)，那么当其中一个线程正在改写某属性值时，另外一个线程也许会突然闯入，把尚未修改好的属性值读取出来。 发生这种情况时，线程读到的属性值可能不对。
>
> 如果开发过 iOS 程序，你就会发现，其中所有属性都声明为 nonatomic。这样做的历史原因是：在 iOS 中使用同步锁的开销较大，这会带来性能问题。一般情况下并不要求属性必须是“原子的”，因为这并不能保证“线程安全"（thread safety)，若要实现“线程安全”的操作，还需采用更为深层的锁定机制才行。例如，一个线程在连续多次读取某属性值的过程中有别的线程在同时改写该值，那么即便将属性声明为 atomic，也还是会读到不同的属性值。 因此，开发 iOS 程序时一般都会使用 nonatomic 属性。但是在开发 Mac OS X 程序时，使用 atomic 属性通常都不会有性能瓶颈。

##### 读写性

* **readwrite**：自动生成**获取方法**（getter）与**设置方法**（setter）。
* **readonly**：只生成**获取方法**（getter）。

##### 内存管理语义，该特质只影响设置方法。

* **assign**：设置方法只会执行针对“纯量类型”（scalar type，例如 CGFloat 或 NSInteger 等）的简单赋值操作。
* **strong**：此特质表明该属性定义了一种“拥有关系”（owning relationship)。为这种属性设置新值时，设置方法会先保留新值，并释放旧值，然后再将新值设置上去。
* **weak**：此特质表明该属性定义了一种“非拥有关系”（nonowning relationship)。为这种属性设置新值时，设置方法既不保留新值，也不释放旧值。此特质同 **assign** 类似, 然而在属性所指的对象遭到摧毁时，属性值也会清空（nil out)。
* **unsafe_unretained**：此特质的语义和 **assign** 相同，但是它适用于“对象类型”（object type),该特质表达一种“非拥有关系”（“不保留”，unretained),当目标对象遭到摧毁 时，属性值不会自动清空（“不安全”，unsafe)，这一点与 **weak** 有区别。
* **copy**：此特质所表达的所属关系与 **strong** 类似。然而设置方法并不保留新值，而是将其拷贝（copy）。当属性类型为 `NSString *` 时，经常用此特质来保护其封装性，以返回不可变对象。涉及到深拷贝与浅拷贝的问题。

##### 方法名

指定存取方法的方法名

* `getter=<name>`

  指定“获取方法”的方法名，如：

  ```objective-c
  @property (nonatomic, getter=isOn) BOOL on;
  ```

* `setter=<name>`

  指定“设置方法”的方法名。这种用法不太常见。

#### 要点

- 可以通过 **@property** 语法来定义对象中所封装的数据。
- 通过 “特质” 来指定存储数据所需的正确语义。
- 在设置属性所对应的实例变量时，一定要遵从该属性所声明的语义。
- 开发iOS程序时，应该使用 **nonatomic** 属性，因为 **atomic** 属性会严重影响性能。




### 第7条：在对象内部尽量直接访问实例变量

⭐️ **合理的方案**：在**写入**实例变量时，通过其**设置方法**来做。而**读取**实例变量时，则**直接访问**之。

**优点**：既能提高读取操作的速度，又能控制对属性的写入操作。

**通过设置方法来写入实例变量的原因**：能够确保相关属性的“内存管理语义”得以贯彻。

**选用该方案的注意点**：

* **直接访问实例变量**不会经过 Objective-C 的方法派发（method dispatch）、不会调用其设置方法（setter）、不会触发键值观测（KVO）。
* 在初始化方法中，总是应该直接访问实例变量，因为子类可能会**覆写（override）**设置（setter）方法。
* 使用**惰性初始化（lazy initialization）**技术时，必须通过**获取方法**来**访问属性**。


#### 要点

- 在对象内部读取数据时，应该直接通过实例变量来读，而写入数据时，应该通过属性来写。
- 在初始化方法及 `dealloc` 方法中，总是应该直接通过实例变量来读写数据。
- 有时会使用惰性初始化技术配置某份数据，这种情况下，需要通过属性来读取数据。




### 第8条：理解“对象等同性”这一概念

* `==` 操作比较的是两个指针本身。
* 应该用 NSObject 协议中的 `isEqual`  方法判断两个对象的等同性。

NSObject 协议中判断两个对象的等同性的关键方法：

```objective-c
// 默认实现：当且仅当其“指针值"完全相等时，这两个对象才相等。
- (BOOL)isEqual:(id)object;
@property (readonly) NSUInteger hash;
```

对象等同性约定：

1. 如果 `isEqual:` 方法判定两个对象相等，那么其 `hash` 方法也必须返回同一个值。
2. 但是，如果两个对象的 `hash` 方法返回同一个值，其 `isEqual:` 方法**未必会**认为两者相等。

#### 覆写 isEqual：

```objective-c
- (BOOL)isEqual:(id)object {
    // 1.判断两个指针是否相等，即两个指针指向同一个对象
    if (self == object) return YES;
    // 2.判断两个对象是否属于同一个类
    //   还应考虑到继承体系中的等同性问题
    if ( [self class] != [object class]) return NO;
    
    // 3.判断每个属性是否都相等
    EOCPerson *otherPerson = (EOCPerson *)object;
    if (![_firstName isEqualToString:otherPerson.firstName]) {
        return NO;
    }
    if (![_lastName isEqualToString:otherPerson.lastName]) {
        return NO;
    }
    if (_age != otherPerson.age) {
        return NO;
    }
    return YES;
}
```

#### 覆写 hash

若两对象相等，则其哈希码 (hash) 也相等，但是两个哈希码相同的对象却未必相等。

```objective-c
/*
 在 collection 中使用这种对象将产生性能问题。
 
 collection 在检索哈希表（hash table）时，会用对象的哈希码做索引。假如某个 collection 是用 set 实现的，
 那么 set 可能会根据哈希码把对象分装到不同的数组中。在向 set 中添加新对象时，要根据其哈希码找到与之相关的那个
 数组，依次检査其中各个元素，看数组中已有的对象是否和将要添加的新对象相等。如果相等，那就说明要添加的对象已经在
  set 里面了。由此可知，如果令每个对象都返回相同的哈希码，那么在 set 中已有 1000000 个对象的情况下，若是继
 续向其中添加对象，则需将这 1000000 个对象全部扫描一遍。
 */
- (NSUInteger)hash {
    return 1337;
}

/*
 这样做还需负担创建字符串的开销，所以比返回单一值要慢。
 把这种对象添加到collection中时，也会产生性能问题
 */
- (NSUInteger)hash {
    NSString *stringToHash =
        [NSString stringWithFormat:@"%@:%@:%lu",
            _firstName, _lastName, _age];
    return [stringToHash hash];
}


/*
 此方法既能保持较高效率，又能使生成的哈希码至少位于一定范围之内，而不会过于频繁地重复。
 */
- (NSUInteger)hash {
    NSUInteger firstNameHash = [_firstName hash];
    NSUInteger lastNameHash  = [_lastName hash];
    NSUInteger ageHash       = _age;
    return firstNameHash ^ lastNameHash ^ ageHash;
}
```

#### 特定类所具有的等同性判断方法

```objective-c
/** NSString */
- (BOOL)isEqualToString:(NSString *)aString;

/** NSArray */
- (BOOL)isEqualToArray:(NSArray<ObjectType> *)otherArray;

/** NSDictionary */
- (BOOL)isEqualToDictionary:(NSDictionary<KeyType, ObjectType> *)otherDictionary;

/** NSSet */
- (BOOL)isEqualToSet:(NSSet<ObjectType> *)otherSet;
```

#### 创建自定义等同性判定方法的原因：

1. 无须检测参数类型，能提升检测速度；
2. 代码更美观、更易读，类似于 `isEqualToString:`。



`isEqual:` 方法常见实现方式为：如果受测的参数与接收该消息的对象都属于同一个类，那么就调用自已编写的判定方法，否则就交由超类来判断。

示例：

```objective-c
- (BOOL)isEqualToPerson:(EOCPerson *)otherPerson {
    // 1.1 判断两个指针是否相等，即两个指针指向同一个对象
    if (self == otherPerson) return YES;
    
    // 1.2 判断每个属性是否都相等
    if (![_firstName isEqualToString:otherPerson.firstName]) {
        return NO;
    }
    if (![_lastName isEqualToString:otherPerson.lastName]) {
        return NO;
    }
    if (_age != otherPerson.age) {
        return NO;
    }
    return YES;
}

- (BOOL)isEqual:(id)object {
    
    // 判断两个对象是否属于同一个类
    if ( [self class] == [object class]) {
        // 1.同类，调用自定义判定方法
        return [self isEqualToPerson:(EOCPerson *)object];
    }else {
        // 2.否则，交由超类处理
        return [super isEqual:object];
    }
}
```

#### 等同性判断的执行深度

创建等同性判定方法时，需要决定是根据整个对象来判断等同性，还是仅根据其中几个字段来判断。

* 深度等同性判断（deep equality）;
* 根据自定义类的 identifier 标识符判定。

#### 容器中可变类的等同性

把某个对象放入 collection 之后，就不应再改变其（这个对象）哈希码了。因为如果把某对象放入 set 之后又修改其内容，那么后面的行为将很难预料。

#### 要点

- 若想检测对象的等同性，请提供 `isEqual:` 与 `hash` 方法。
- 相同的对象必须具有相同的hash码，但是两个hash码相同的对象却未必相同。
- 不要盲目的逐个检测每条属性，而是应该依照具体需求来制定检测方案。
- 编写 `hash` 方法时，应该使用计算速度快而且哈希码碰撞几率低的算法。



### 第9条：以“类族模式”隐藏实现细节

**类族/类别方法**（class cluster）：可以隐藏“抽象基类"背后的实现细节。

例如：

```objective-c
+ (instancetype)buttonWithType:(UIButtonType)buttonType;
```

#### 创建类族

```objective-c
#import <Foundation/Foundation.h>

/** 定义抽象基类 */
typedef NS_ENUM(NSUInteger, EOCEmployeeType) {
    EOCEmployeeTypeDeveloper,
    EOCEmployeeTypeDesigner,
    EOCEmployeeTypeFinance,
};

@interface EOCEmployee : NSObject

@property (nonatomic, copy) NSString *name;
@property (nonatomic, assign) NSUInteger age;

+ (EOCEmployee *)employeeWithType:(EOCEmployeeType)type;
- (void)doADayWork;

@end

@implementation EOCEmployee

+ (EOCEmployee *)employeeWithType:(EOCEmployeeType)type {
    switch (type) {
        case EOCEmployeeTypeDeveloper:
            return [EOCEmployeeDeveloper new];
            break;
        case EOCEmployeeTypeDesigner:
            return [EOCEmployeeDesigner new];
            break;
        case EOCEmployeeTypeFinance:
            return [EOCEmployeeFinance new];
            break;
    }
}

- (void)doADayWork {
    // Subclasses implement this
}

@end
```

其中，每个实体子类都从基类继承而来

```objective-c
@interface EOCEmployeeDeveloper : EOCEmployee
@end

@implementation EOCEmployeeDeveloper

- (void)doADayWork {
    [self writeCode];
}

@end
```

#### Cocoa 里的类族

大部分 collection 类都是类族，如 **NSArray** 及其可变版本 **NSMutableArray**。

判断某个对象是否位于类族中，使用 `isKindOfClass:` 方法。

#### 要点

- 类族模式可以把实现细节隐藏在一套简单的公共接口后面。
- 系统框架中经常使用类族。
- 从类族的公共抽象基类中继承子类时要当心，若有开发文档，则应首先阅读。




### 第10条：在既有类中使用关联对象存放自定义数据

关联对象（Associated Object）：

1. 可以给某对象关联许多其他对象，这些对象通过**键<key>**来区分。
2. 存储对象值的时候，可以指明 **存储策略**（storage policy），用以维护相应的“内存管理语义”。存储策略由名为 `objc_AssociationPolicy` 的枚举所定义：

   ```objective-c
   typedef enum objc_AssociationPolicy : uintptr_t {
       OBJC_ASSOCIATION_ASSIGN = 0,
       OBJC_ASSOCIATION_RETAIN_NONATOMIC = 1,
       OBJC_ASSOCIATION_COPY_NONATOMIC = 3,
       OBJC_ASSOCIATION_RETAIN = 01401,
       OBJC_ASSOCIATION_COPY = 01403
   } objc_AssociationPolicy;
   ```

   | 关联类型                              | 等效的 @property 属性 |
   | :-------------------------------- | :--------------- |
   | OBJC_ASSOCIATION_ASSIGN           | assign           |
   | OBJC_ASSOCIATION_RETAIN_NONATOMIC | nonatomic，retain |
   | OBJC_ASSOCIATION_COPY_NONATOMIC   | nonatomic，copy   |
   | OBJC_ASSOCIATION_RETAIN           | retain           |
   | OBJC_ASSOCIATION_COPY             | copy             |



管理关联对象的方法：

```objective-c
// 以给定的键和策略为某对象设置关联对象值。
void objc_setAssociatedObject(id object, const void *key, id value, objc_AssociationPolicy policy);

// 根据给定的键从某对象中获取相应的关联对象值。
id objc_getAssociatedObject(id object, const void *key);

// 移除指定对象的全部关联对象。
void objc_removeAssociatedObjects(id object);
```

> 我们可以把某对象想象成`NSDictionary`字典，把关联到该对象的值理解为字典中的条目，于是，存取关联对象的值就相当于在 `NSDictionary` 对象实例上调用 `[object setObject:value forKey:key]` 与 `[object objectForKey:key]` 方法。
>
> 然而两者之间有个重要差别：设置关联对象时用的键（key）是个**不透明的指针**（opaque pointer）。如果在两个键上调用 `isEqual:` 方法的返回值是 YES，那么 **NSDictionary** 就认为二者相等；然而在设置关联对象值时，若想令两键匹配到同一个值，则二者必须是完全相同的指针才行。鉴于此，**在设置关联对象值时， 通常使用静态全局变量做键。**


#### 要点

- 可以通过“关联对象”机制来把两个对象连起来。
- 定义关联对象时可指定内存管理语义，用以模仿定义属性时所采用的“拥有关系”与“非拥有关系”。
- 只有在其他做法不可行时才应选用关联对象，因为这种做法通常会引入难于查找的 bug。




### 第11条：理解 objc_msgSend 的作用

> 运行期系统如何査找并调用某方法的实现代码?

* 在 Objective-C 对象上调用方法称之为**传递消息**。
* 消息有**名称**（name）或**选择子**（selector），可以接受参数、传递返回值。
* **C**语言使用**静态绑定**的函数调用方式。即**在编译期就能决定运行时所应调用的函数**。
* **动态绑定**：需要调用的函数直到运行期才能确定。

在 Objective-C 中，如果向某对象传递消息，那就会使用动态绑定机制来决定需要调用的方法。在底层，所有方法都是普通的 C 语言函数，然而对象收到消息之后，究竟该调用哪个方法则完全于运行期决定，甚至可以在程序运行时改变，这些特性使得 Objective-C 成为一门真正的动态语言。

```objective-c
/*
 给对象发送消息

 接收者(receiver): someObject
 选择子(selector): messageName
 消息(message): 选择子 + 参数
 */
id returnValue = [someObject messageName:parameter];

// 底层执行的 C 语言调用函数
id returnValue = objc_msgSend(someObject,
                              @selector(messageName:),
                              parameters);

// 编译器看到上面这条消息后，将其转换为一条标准的 C 语言函数调用，
// 所调用的函数是消息传递机制中的核心函数，叫做 objc_msgSend
void objc_msgSend(void /* id self, SEL op, ... */ )

// 消息派发；
// 方法列表；
// 消息转发机制；
// 快速映射表，用于缓存匹配的方法名结果。
```

Objective-C 运行环境的其他函数：

```objective-c
/*
  如果待发送的消息要返回结构体，那么可交由此函数处理。

  只有当CPU的寄存器能够容纳得下消息返回类型时，这个函数才能处理此消息。
  若是返回值无法容纳于CPU寄存器中（比如说返回的结构体太大了)，那么就由另一个函数执行派发。
  此时，那个函数会通过分配在栈上的某个变量来处理消息所返回的结构体。
 */
void objc_msgSend_stret(id self, SEL op, ...);
```

```objective-c
/*
 如果消息返回的是浮点数，那么可交由此函数处理。
 
 在某些架构的 CPU 中调用函数时，需要对 ”浮点数寄存器“（floating-point register)做特殊处理。
 也就是说，通常所用的 objc_msgSend 在这种情况下并不合适。
 这个函数是为了处理 x86 等架构 CPU 中某些令人稍觉惊讶的奇怪状况。
 */
long double objc_msgSend_fpret(id self, SEL op, ...);
```

```objective-c
/*
 如果要给超类发消息，例如 [super message:parameter],那么就交由此函数处理。
 
 也有另外两个与 objc_msgSend_stret 和 objc_msgSend_fpret 等效的函数，
 用于处理发给 super 的相应消息。
 */
id objc_msgSendSuper(struct objc_super *super, SEL op, ...);
```


#### 要点

- 消息由接受者，selector及参数构成。给某对象“发送消息”也就相当于在该对象上调用方法。
- 发给某对象的全部消息都要由“动态消息派发系统”来处理，该系统会查出对应的方法，并执行其代码。




### 第12条：理解消息转发机制

> 消息转发的原理，如果类无法立即响应某个选择子, 那么就会启动消息转发流程。

#### 消息转发机制

> 在编译期向类发送了其无法解读的消息并不会报错，因为在运行期可以继续向类中添加方法，所以编译器在编译时还无法确知类中到底会不会有某个方法实现。
>
> 当对象接收到无法解读的消息后，就会启动**消息转发**（message forwarding）机制，程序员可经由此过程告诉对象应该如何处理未知消息。

消息转发的两个阶段：

第一阶段，**动态方法解析**（dynamic method resolution）：先征询接收者，所属的类，看其是否能动态添加方法，以处理当前这个**未知的选择子**（unknown selector）。

第二阶段，涉及**完整的消息转发机制**（full forwarding mechanism)。如果运行期系统已经把第一阶段执行完了，那么接收者自己就无法再以动态新增方法的手段来响应包含该选择子的消息了。此时，运行期系统会请求接收者以其他手段来处理与消息相关的方法调用。细分为两小步：

* 首先，请接收者看看有没有其他对象能处理这条消息。若有，则运行期系统会把消息转给那个对象，于是消息转发过程结束，一切如常。
* 若没有**备援的接收者**（replacement receiver)，则启动完整的消息转发机制，运行期系统会把与消息有关的全部细节都封装到 `NSInvocation` 对象中，再给接收者最后一次机会，令其设法解决当前 还未处理的这条消息。

#### 动态方法解析

对象在收到无法解读的消息后，首先将调用其所属类的下列类方法：

```objective-c
+ (BOOL)resolveInstanceMethod:(SEL)sel;
```

该方法的参数就是那个未知的选择子，其返回值为 `Boolean` 类型，表示这个类是否能新增一个实例方法用以处理此选择子。在继续往下执行转发机制之前，本类有机会新增一个处理此选择子的方法。假如尚未实现的方法不是**实例方法**而是**类方法**，那么运行期系统就会调 用另外一个方法，该方法与 `resolvelnstanceMethod:` 类似，叫做 `resolveClassMethod:`。

**使用这种办法的前提**：相关方法的实现代码已经写好，只等着运行的时候动态插在类里面就可以了。此方案常用来实现 `@dynamic` 属性：

```objective-c
id autoDictioanryGetter(id self, SEL _cmd);
void autoDictionarySetter(id self, SEL _cmd, id value);

+ (BOOL)resolveInstanceMethod:(SEL)sel {
    NSString *selectorString = NSStringFromSelector(sel);
    if ( /** selector is from a @dynamic property */ ) {
        if ([selectorString hasPrefix:@"set"]) {
            class_addMethod(self,
                            sel,
                            (IMP)autoDictionarySetter,
                            "v@:@");
        }else {
            class_addMethod(self,
                            sel,
                            (IMP)autoDictioanryGetter,
                            "@@:");
        }
        return YES;
    }
    return [super resolveInstanceMethod:sel];
}
```

#### 备援接收者

当前接收者还有第二次机会能处理未知的选择子，在这一步中，运行期系统会问它：能不能把这条消息转给其他接收者来处理。

```objective-c
- (id)forwardingTargetForSelector:(SEL)aSelector;
```

该方法参数代表未知的选择子，若当前接收者能找到备援对象，则将其返回，若找不到， 就返回nil。通过此方案，我们可以用 “组合”（composition）来模拟出 “多重继承”（multiple inheritance）的某些特性。在一个对象内部，可能还有一系列其他对象，该对象可经由此方法将能够处理某选择子的相关内部对象返回，这样的话，在外界看来，好像是该对象亲自处理了这些消息似的。

#### 完整的消息转发机制

首先创建 **NSInvocation** 对象，把与尚未处理的那条消息有关的全部细节都封于其中。 此对象包含选择子、目标（target）及参数。在触发 `NSInvocation` 对象时，“消息派发系统” (message-dispatch system）将亲自出马，把消息指派给目标对象。

```objective-c
- (void)forwardInvocation:(NSInvocation *)anInvocation;
```

这个方法可以实现得很简单：只需改变调用目标，使消息在新目标上得以调用即可。然而这样实现出来的方法与“备援接收者”方案所实现的方法等效，所以很少有人采用这么简单的实现方式。

比较有用的实现方式为：在触发消息前，先以某种方式改变消息内容，比如追加另外一个参数，或是改换选择子，等等。

#### 消息转发全流程

![](http://upload-images.jianshu.io/upload_images/2648731-10ed9ec668fbef9e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接收者在每一步中均有机会处理消息。步骤越往后，处理消息的代价就越大。最好能在第一步就处理完，这样的话，运行期系统就可以将此方法缓存起来了。如果这个类的实例稍后还收到同名选择子，那么根本无须启动消息转发流程。若想在第三步里把消息转给备援的接收者，那还不如把转发操作提前到第二步。因为第三步只是修改了调用目标，这项改动放在第二步执行会更为简单，不然的话，还得创建并处理完整的 **NSInvocation** 。

#### 以完整的例子演示动态方法解析

```objective-c
// *************************************************************
//  EOCAutoDictionary.h
#import <Foundation/Foundation.h>

/**
 以动态方法解析实现 @dynamic 属性
 
 编写一个类似于“字典”的对象，它里面可以容纳其他对象，只不过开发者要直接通过属性来存取其中的数据。
 设计思路是：由开发者来添加属性定义，并将其声明为 @dynamic，而类则会自动处理相关属性值的存放与获取操作。
 */
@interface EOCAutoDictionary : NSObject
@property (nonatomic, strong) NSString *string;
@property (nonatomic, strong) NSNumber *number;
@property (nonatomic, strong) NSDate *date;
@property (nonatomic, strong) id opaqueObject;
@end

// *************************************************************
//  EOCAutoDictionary.m
#import "EOCAutoDictionary.h"
#import <objc/runtime.h>

@interface EOCAutoDictionary ()
/** 在类的内部，每个属性的值都存放在字典里 */
@property (nonatomic, strong) NSMutableDictionary *backingStore;
@end

/** getter 函数 */
id autoDictioanryGetter(id self, SEL _cmd) {
    // 从对象中获取存储器
    EOCAutoDictionary *typeSelf = (EOCAutoDictionary *)self;
    NSMutableDictionary *backingStore = typeSelf.backingStore;
    
    // 字典的 key 是 selector 的名字
    NSString *key = NSStringFromSelector(_cmd);
    
    // Return the value
    return [backingStore objectForKey:key];
}

/** Setter 函数 */
void autoDictionarySetter(id self, SEL _cmd, id value) {
    // 从对象中获取存储器
    EOCAutoDictionary *typeSelf = (EOCAutoDictionary *)self;
    NSMutableDictionary *backingStore =  typeSelf.backingStore;
    
    /** 
     *  selector 的名称格式像这样："setOpaqueObject:"
     *  我们需要移除 "set"，":"，并且将首字母小写
     */
    NSString *selectorString = NSStringFromSelector(_cmd);
    NSMutableString *key = [selectorString mutableCopy];
    
    // 移除末尾的 “:”
    [key deleteCharactersInRange:NSMakeRange(key.length - 1, 1)];
    
    // 移除 “set” 前缀
    [key deleteCharactersInRange:NSMakeRange(0, 3)];
    
    // 首字母小写
    NSString *lowercaseFirstChar = [[key substringToIndex:1] lowercaseString];
    [key replaceCharactersInRange:NSMakeRange(0, 1)
                       withString:lowercaseFirstChar];
    
    if (value) {
        [backingStore setObject:value forKey:key];
    }else {
        [backingStore removeObjectForKey:key];
    }
}

@implementation EOCAutoDictionary
/** 禁止编译器自动生成实例变量和存取方法 */
@dynamic string, number, date, opaqueObject;

- (instancetype)init {
    if (self = [super init]) {
        _backingStore = [NSMutableDictionary new];
    }
    return self;
}

+ (BOOL)resolveInstanceMethod:(SEL)sel {
    NSString *selectorString = NSStringFromSelector(sel);
    if ([selectorString hasPrefix:@"set"]) {
        // 向类中动态地添加方法，用以处理给定的选择子
        // 第三个参数为函数指针，指向待添加的方法
        // 最后一个参数表示待添加方法的类型编码
        class_addMethod(self,
                        sel,
                        (IMP)autoDictionarySetter,
                        "v@:@");
    }else {
        class_addMethod(self,
                        sel,
                        (IMP)autoDictioanryGetter,
                        "@@:");
    }
    return YES;
}

@end
```

使用 **EOCAutoDictionary**:

```objective-c
EOCAutoDictionary *dict = [EOCAutoDictionary new];
dict.date = [NSDate dateWithTimeIntervalSince1970:475372800];
NSLog(@"dict.date = %@",dict.date);
// dict.date = Thu Jan 24 08:00:00 1985
```

> **CoreAnimation** 框架中的 **CALayer** 类使用了与本例相似的实现方式，这使得 **CALayer** 成为 **兼容于键值编码的**（key-value-coding-compliant）容器类， 也就等于说，能够向里面随意添加属性，然后以键值对的形式来访问。于是，开发者就可以向其中新增自定义的属性了，这些属性值的存储工作由基类直接负责，我们只需在 **CALayer** 的子类中定义新属性即可。

#### 要点

- 若对象无法响应某个 selector，则进入消息转发流程。
- 通过运行期的动态方法解析功能，我们可以在需要用到某个方法时再将其加入类中。
- 对象可以将其无法解读的某些 selector 转交给其他对象处理。
- 经过上述两步后，如果还是没办法处理 selector，那就启动完整的消息转发机制。




### 第13条：用“方法调配技术”调试“黑盒方法”

> Objective-C 对象收到消息之后，究竟会调用何种方法需要在运行期才能解析出来。
>
> **方法调配**（method swizzling）技术所利用的底层特性：与给定的选择子名称相对应的方法也可以在运行期改变。
>
> 此特性的优势：
>
> 1. 既不需要源代码，也不需要通过继承子类来覆写方法就能改变这个类本身的功能。
> 2. 新的功能将在本类的所有实例中生效，而不是仅限于覆写了相关方法的那些子类实例。

#### 原理

类的方法列表会把选择子的名称映射到相关的方法实现之上，使得 **动态消息派发系统** 能够据此找到应该调用的方法。这些方法均以函数指针的形式来表示，这种指针叫做 IMP。

![](http://upload-images.jianshu.io/upload_images/2648731-ce52a126501763e8.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

而 Objective-C 运行期系统提供的方法可以操作这张方法列表。开发者可以向其中新增选择子，也可以改变某选择子所对应的方法实现，还可以交换两个选择子所映射到的指针。

![](http://upload-images.jianshu.io/upload_images/2648731-2e77df73ca622d08.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```objective-c 
// 获得方法实现
Method class_getInstanceMethod(Class cls, SEL name);

// 互换两个方法实现
void method_exchangeImplementations(Method m1, Method m2);
```

示例一，互换两个方法实现：
```objective-c
Method originalMethod =
    class_getClassMethod([NSString class],
                         @selector(lowercaseString));
Method swappedMethod =
    class_getClassMethod([NSString class],
                         @selector(uppercaseString));
method_exchangeImplementations(originalMethod, swappedMethod);
```

示例二，为既有的方法实现增添新功能：

```objective-c
// *************************************************************
//  NSString+EOCMyAdditions.h
#import <Foundation/Foundation.h>

@interface NSString (EOCMyAdditions)
- (NSString *)eoc_myLowercaseString;
@end

// *************************************************************
//  NSString+EOCMyAdditions.m
#import "NSString+EOCMyAdditions.h"

@implementation NSString (EOCMyAdditions)
- (NSString *)eoc_myLowercaseString {
    NSString *lowercase = [self eoc_myLowercaseString];
    NSLog(@"%@ => %@", self, lowercase);
    return lowercase;
}
@end

// *************************************************************
// 在运行期，eoc_myLowercaseString 择子实际上对应于原有的 owercaseString 法实现。
Method originalMethod =
class_getClassMethod([NSString class],
                     @selector(lowercaseString));
Method swappedMethod =
class_getClassMethod([NSString class],
                     @selector(eoc_myLowercaseString));
method_exchangeImplementations(originalMethod, swappedMethod);
```

> 通过此方案，开发者可以为那些 **完全不知道其具体实现的**（completely opaque, “完全不透明的”）黑盒方法增加日志记录功能，这非常有助于程序调试。
>
> 然而，此做法只在调试程序时有用。很少有人在调试程序之外的场合用上述方法调配技术来永久改动某个类的功能。
>
> **不能仅仅因为 Objective-C 语言里有这个特性就一定要用它。若是滥用，反而会令代码变得不易读懂且难于维护**。

#### 要点

* 在 runtime 运行期，可以向类中新增或替换选择子所对应的方法实现。
* 使用另一份实现来替换原有的方法实现，这道工序叫做方法调配（method swizzling），开发者常用此技术向原有实现中添加功能。
* 一般来说，只有调试程序的时候才需要在 runtime 中修改方法实现，这种做法不宜滥用。




### 第14条：理解“类对象”的用意

* 对象类型并非在编译期就绑定好了，而是要在运行期査找。
* **id** 是特殊类型，它能指代任意的 Objective-C 对象类型。
* **在运行期检视对象类型** 这一操作也叫做 **类型信息査询**（introspection，内省）。
* 在程序中不要直接比较对象所属的类，明智的做法是调用“类型信息査询方法”。

```objective-c
// 此结构体存放类的元数据
struct objc_class {
    Class isa  OBJC_ISA_AVAILABILITY;

#if !__OBJC2__
    // 定义本类的超类
    Class super_class                                        OBJC2_UNAVAILABLE;
    const char *name                                         OBJC2_UNAVAILABLE;
    long version                                             OBJC2_UNAVAILABLE;
    long info                                                OBJC2_UNAVAILABLE;
    long instance_size                                       OBJC2_UNAVAILABLE;
    struct objc_ivar_list *ivars                             OBJC2_UNAVAILABLE;
    struct objc_method_list **methodLists                    OBJC2_UNAVAILABLE;
    struct objc_cache *cache                                 OBJC2_UNAVAILABLE;
    struct objc_protocol_list *protocols                     OBJC2_UNAVAILABLE;
#endif

} OBJC2_UNAVAILABLE;
```

* 每个对象结构体的首个成员是 Class 类的变量。该变量定义了对象所属的类, 通常称为 `is a` 指针。
* `superclass` 指针确立了继承关系，而 `isa` 指针描述了实例所属的类。

#### 在类继承体系中查询类型信息

类型信息査询方法：

```objective-c
// 对象是否为某类或其派生类的实例
- (BOOL)isKindOfClass:(Class)aClass;

// 对象是否为某个特定类的实例
- (BOOL)isMemberOfClass:(Class)aClass;
```

示例：

```objective-c
NSMutableDictionary *dict = [NSMutableDictionary new];

[dict isMemberOfClass:[NSDictionary class]]; // NO
[dict isMemberOfClass:[NSMutableDictionary class]]; // NO,类族模式‼️
[dict isKindOfClass:[NSDictionary class]]; // YES
[dict isKindOfClass:[NSMutableDictionary class]]; // YES
[dict isKindOfClass:[NSArray class]]; // NO
```

两者的区别：`isMemberOfClass:` 不能检测任何的类都是基于 **NSObject** 类这一事实，而 `isKindOfClass:` 可以。

示例二：

```objective-c
// 根据数组中存储的对象生成以逗号分隔的字符串
- (NSString *)commaSeparatedStringFromObjects:(NSArray *)array {
    NSMutableString *string = [NSMutableString new];
    [array enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
        if ([obj isKindOfClass:[NSString class]]) {
            [string appendFormat:@"%@,", obj];
        }else if ([obj isKindOfClass:[NSNumber class]]) {
            [string appendFormat:@"%d,", [obj intValue]];
        }else if ([obj isKindOfClass:[NSData class]]) {
            NSString *base64Encoded = [obj base64EncodedStringWithOptions:NSDataBase64Encoding64CharacterLineLength];
            [string appendFormat:@"%@,", base64Encoded];
        }else {
            // Type not support
        }
    }];
    return string;
}
```

#### 类型信息查询方法 & `class` 方法

```
// 使用 class 方法也可以精确判断出对象是否为某类实例
id object;
if ([object class] == [NSString class]) {
    // object is an instance of NSString
}
```

类型信息查询方法可以正确处理那些使用了消息传递机制的对象。

> 某个对象可能会把其收到的所有选择子都转发给另外一个对象。这样的对象叫做**代理** （proxy），此种对象均以 `NSProxy` 为根类。
>
> 通常情况下，如果在此种代理对象上调用 `class` 方法，那么返回的是代理对象本身（此类是 `NSProxy` 的子类)，而非接受的代理的对象所属的类。
>
> 然而，若是改用 `isKindOfClass:` 这样的类型信息査询方法，那么代理对象就会把这条消息转给 **接受代理的对象**（proxied object)。也就是说，这条消息的返回值与直接在接受代理的对象上面査询其类型所得的结果相同。因此，这样査出来的类对象与通过 `class` 方法所返回的那个类对象不同，`class` 方法所返回的类表示发起代理的对象，而非接受代理的对象。



#### 要点

- 每个实例都有一个指向 Class 对象的指针，用以表明其类型，而这些 Class 对象则构成了类的继承体系。
- 如果对象类型无法在编译期确定，那么就应该使用类型信息查询方法来探知。
- 尽量使用类型信息查询方法来确定对象类型，而不要直接比较类对象，因为某些对象可能实现了消息转发功能。
