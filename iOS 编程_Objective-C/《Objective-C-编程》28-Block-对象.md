**Block** 对象是一段代码。先给出一个 **Block** 对象的示例：

```objectivec
^{
  NSLog(@"This is an instruction within a block.")
}
```
看上去和 C 函数类似，但是它没有函数名。`^` 表示这段代码是一个 **Block** 对象。

和函数一样，**Block** 对象也可以有实参和返回值：

```objectivec
^(double dividend, double divisor) {
  double quotient = dividend / divisor
  return quotient;
}
```
这段代码中的 **Block** 对象有两个 `double` 类型的实参，一个 `double` 类型的返回值。**Block** 对象可以被当成一个实参来传递给可以接收 block 的方法。

## 使用 Block 对象

iOS SDK 4.0以后，随着 Block 的加入，很多特性也随之添加或者发生了升级。Apple 所推荐的 Block 使用范围包括以下几个方面：
* 枚举——通过 block 获取枚举对象或控制枚举进程；
* View 动画——简单明了的方式规定动画；
* 排序——在 Block 内写排序算法；
* 通知——当某事件发生后执行 Block 内的代码；
* 错误处理——当错误发生时执行 Block 代码；
* 完成处理——当方法执行完毕后执行 Block 代码；
* GCD 多线程——多线程控制。

Block 对象可以用变量保存。声明一个 Block 变量：
```objectivec
void (^devowelizer)(id, NSUInteger, BOOL *);
```

* **Block** 变量的名称（如devowelizer）是写在括号中，`^` 字符后面的。
* **Block** 对象的返回类型：`void`。
* **Block** 对象的实参类型：`id`, `NSUInteger`, `BOOL *`。
* **Block** 变量的类型：有三个参数（`id`, `NSUInteger`, `BOOL *`），并且没有返回值的 **Block** 对象。
* 注意到，声明 **Block** 变量时参数类型没有写名称，而 **Block** 对象中的实参类型是有名称的。

编写一个 Block 对象，并将它赋给之前声明的 Block 变量：
```objectivec
// 声明 Block 变量
void (^devowelizer)(id, NSUInteger, BOOL *);

// 将Block 对象赋给变量
devowelizer = ^(id string, NSUInteger i, BOOL *stop) {
    NSMutableString *newString = [NSMutableString stringWithString:string];
    
    // 枚举数组中的字符串，将所有出现的元音字母替换成空字符串
    for (NSString *s in vowels) {
        NSRange fullRange = NSMakeRange(0, newString.length);
        [newString replaceOccurrencesOfString:s
                                   withString:@""
                                      options:NSCaseInsensitiveSearch
                                        range:fullRange];
    }
    [devowelizedStrings addObject:newString];
}; // Block 变量赋值结束
```

和其他变量一样，你也可以将 **Block** 对象的声明和赋值写在一起：
```objectivec
void (^devowelizer2)(id, NSUInteger, BOOL *) = ^(id string, NSUInteger i, BOOL *stop) {
  
    NSMutableString *newString = [NSMutableString stringWithString:string];

    // 枚举数组中的字符串，将所有出现的元音字母替换成空字符串
    for (NSString *s in vowels) {
        NSRange fullRange = NSMakeRange(0, [newString length]);
        [newString replaceOccurrencesOfString:s
                                   withString:@""
                                      options:NSCaseInsensitiveSearch
                                        range:fullRange];
    }
    [devoweLizedStrings addObject:newString];
};
```

完整示例：

使用 **Block** 对象枚举数组中的字符串，并移除字符串中所有的元音字母（a、e、i、o、u）。

```objectivec
// 创建两个数组对象，分别用于保存最初的字符串对象和去除元音字母后的版本
NSArray *originalStrings = @[@"Sauerkuaut",
                             @"Raygun",
                             @"Big Nerd Ranch",
                             @"Mississippi"];
NSLog(@"最初的字符串：%@", originalStrings);

NSMutableArray *devowelizedStrings = [NSMutableArray array];

// 创建数组对象，保存需要从字符串中移除的元音字符
NSArray *vowels = @[@"a", @"e", @"i", @"o", @"u"];

/**
 *  声明 Block 变量
 *
 *  Block 变量的名称（如devowelizer）是写在括号中，^字符后面的。
 *  Block 对象的返回类型：void。
 *  Block 对象的实参类型：id, NSUInteger, BOOL *。
 *  Block变量的类型：有三个参数（id, NSUInteger, BOOL *），并且没有返回值的 Block 对象
 */
void (^devowelizer)(id, NSUInteger, BOOL *);

// 将Block 对象赋给变量
devowelizer = ^(id string, NSUInteger i, BOOL *stop) {
    NSMutableString *newString = [NSMutableString stringWithString:string];
    
    // 枚举数组中的字符串，将所有出现的元音字母替换成空字符串
    for (NSString *s in vowels) {
        NSRange fullRange = NSMakeRange(0, newString.length);
        [newString replaceOccurrencesOfString:s
                                   withString:@""
                                      options:NSCaseInsensitiveSearch
                                        range:fullRange];
    }
    [devowelizedStrings addObject:newString];
}; // Block 变量赋值结束

// 枚举数组对象，针对每个数组中的对象，执行 Block 对象 devowelizer
[originalStrings enumerateObjectsUsingBlock:devowelizer];
NSLog(@"新的字符串：%@", devowelizedStrings);
```
运行代码，控制台输出如下：
```
2020-03-20 22:39:05.843135+0800 VowelMovement[42325:1778877] 最初的字符串：(
    Sauerkuaut,
    Raygun,
    "Big Nerd Ranch",
    Mississippi
)
2020-03-20 22:39:05.843971+0800 VowelMovement[42325:1778877] 新的字符串：(
    Srkt,
    Rygn,
    "Bg Nrd Rnch",
    Msssspp
)
Program ended with exit code: 0
```

`enumerateObjectsUsingBlock:` 是一个枚举对象方法，完整的声明如下：

```objectivec
- (void)enumerateObjectsUsingBlock:(void (NS_NOESCAPE ^)(ObjectType obj, NSUInteger idx, BOOL *stop))block NS_AVAILABLE(10_6, 4_0);
```

该方法要求传入的 **Block** 对象的三个实参类型是固定的：

* 第一个实参是对象指针，指向当前（枚举）的对象。该指针的类型是 `id`，所以无论数组包含的是什么类型的对象，都可以将地址赋值给该指针。
* 第二个实参的类型是 `NSUInterger`，其值是当前对象在数组中的索引。
* 第三个实参是指向 `BOOL` 变量的指针，该变量的默认值是 NO。如果将该值设置为 YES，那么数组对象会在**执行完当前**的 **Block** 对象后终止枚举过程。

下面不妨修改示例中 **Block** 对象的代码，检查字符串是否包含字符y（包含大小写），如果有，则设置指针指向YES（会阻止 **Block** 对象进行枚举），终止枚举：

```objectivec
// 创建两个数组对象，分别用于保存最初的字符串对象和去除元音字母后的版本
NSArray *originalStrings = @[@"Sauerkuaut",
                             @"Raygun",
                             @"Big Nerd Ranch",
                             @"Mississippi"];
NSLog(@"最初的字符串：%@", originalStrings);

NSMutableArray *devowelizedStrings = [NSMutableArray array];

// 创建数组对象，保存需要从字符串中移除的元音字符
NSArray *vowels = @[@"a", @"e", @"i", @"o", @"u"];

// 声明 Block 变量
void (^devowelizer)(id, NSUInteger, BOOL *);

// 将Block 对象赋给变量
devowelizer = ^(id string, NSUInteger i, BOOL *stop) {
    
    NSRange yRange = [string rangeOfString:@"y" options:NSCaseInsensitiveSearch];
    
    // 是否包含字符“y”
    if (yRange.location != NSNotFound) {
        *stop = YES; // 执行完当前的 Block 对象后终止枚举过程
        return; // 结束当前正在执行的 Block 对象
    }
    
    NSMutableString *newString = [NSMutableString stringWithString:string];
    
    // 枚举数组中的字符串，将所有出现的元音字母替换成空字符串
    for (NSString *s in vowels) {
        NSRange fullRange = NSMakeRange(0, newString.length);
        [newString replaceOccurrencesOfString:s
                                   withString:@""
                                      options:NSCaseInsensitiveSearch
                                        range:fullRange];
    }
    [devowelizedStrings addObject:newString];
}; // Block 变量赋值结束

// 枚举数组对象，针对每个数组中的对象，执行 Block 对象 devowelizer
[originalStrings enumerateObjectsUsingBlock:devowelizer];
NSLog(@"新的字符串：%@", devowelizedStrings);
```

运行代码，控制台输出如下：
```
2020-03-20 22:47:45.832963+0800 VowelMovement[42398:1783080] 最初的字符串：(
    Sauerkuaut,
    Raygun,
    "Big Nerd Ranch",
    Mississippi
)
2020-03-20 22:47:45.833589+0800 VowelMovement[42398:1783080] 新的字符串：(
    Srkt
)
Program ended with exit code: 0
```

## typedef

通过 `typedef` 关键字，可以将某个 **Block** 对象类型定义为一个新类型，以方便使用。需要注意的是，不能在方法的实现代码中使用 `typedef`。也就是说，**应该在实现文件的顶部，或者头文件内使用 `typedef` 关键字来定义 Block 对象**。

语法：
```objectivec
typedef <#returnType#>(^<#name#>)(<#arguments#>);
```

修改示例代码:

```objectivec
#import <Foundation/Foundation.h>

/**
 *  使用 typedef 关键字将某个 Block 对象定义为一个 新的类型
 *
 *  定义位置：实现文件的顶部，或是头文件内使用 typedef
 *  这里只是声明了 Block 对象的实参和返回类型，并没有实现真正的 Block 对象
 */
typedef void(^ArrayEnumerationBlock)(id, NSUInteger, BOOL *);

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        
        // 创建两个数组对象，分别用于保存最初的字符串对象和去除元音字母后的版本
        NSArray *originalStrings = @[@"SagurrKraut",@"Raygun",@"Big Nerd Ranch",@"Mississippi"];
        NSLog(@"%@",originalStrings);
        NSMutableArray *devoweLizedStrings = [NSMutableArray array];
        // 创建数组对象，保存需要从字符串中移除的字符
        NSArray *vowels = @[@"a",@"e",@"i",@"o",@"u"];
        
        // 声明 Block 变量
        ArrayEnumerationBlock devowelizer;
        
        // 将 Block 对象赋予变量
        devowelizer = ^(id string, NSUInteger i, BOOL *stop) {
            
            // 检查字符串是否包含字符y(包含大小写)，如果有，则设置指针指向YES（会阻止 Block 对象进行枚举），终止枚举。
            NSRange yRange = [string rangeOfString:@"y"
                                           options:NSCaseInsensitiveSearch];
            // 是否包含字符y?
            if (yRange.location != NSNotFound) {
                *stop = YES;    // 执行完当前的 Block 对象后终止枚举过程
                return;         // 结束当前正在执行的 Block 对象
            }
          
            NSMutableString *newString = [NSMutableString stringWithString:string];
            
            // 枚举数组中的字符串，将所有出现的元音字母替换成空字符串
            for (NSString *s in vowels) {
                NSRange fullRange = NSMakeRange(0, [newString length]);
                [newString replaceOccurrencesOfString:s
                                           withString:@""
                                              options:NSCaseInsensitiveSearch
                                                range:fullRange];
            }
            [devoweLizedStrings addObject:newString];
            
        };  // Block 编码赋值结束
        
        // 枚举数组对象，针对每个数组中的对象，执行 Block 对象 devowelizer
        [originalStrings enumerateObjectsUsingBlock:devowelizer];
        NSLog(@"new strings:%@",devoweLizedStrings);
      
    }
    return 0;
}
```



## Block 对象 vs. 其他回调

通过委托机制（delegation）和通知中心（notification）这两种回调机制，程序能够在特定事件发生时调用指定的方法。虽然这两种回调机制能够很好地完成任务，但**缺点就是回调的设置代码和回调方法的具体实现无法写在同一行代码中**。而且这两段代码经常会间隔很远，甚至出现在不同的文件中：

```objectivec
// 将 BNRLogger 实例注册为观察者，使之在系统的时区设置发生变化时能够收到相应的通知。
[[NSNotificationCenter defaultCenter]
                         addObserver:logger
                         selector:@selector(zoneChange:)
                         name:NSSystemTimeZoneDidChangeNotification
                         object:nil];
```
这里的 `zoneChange:` 方法可能在别的类文件中被实现了，开发者对此方法的实现一无所知。

然而，使用 **Block** 对象可以将与回调相关的代码写在同一行代码中:

```objectivec
[[NSNotificationCenter defaultCenter]
                        addObserverForName:NSSystemTimeZoneDidChangeNotification
                                    object:nil
                                     queue:nil
                                usingBlock:^(NSNotification * _Nonnull note) {
    NSLog(@"The system time zone has changes!");
}];
```

## 返回值

对于有返回值的 **Block** 对象，可以像调用函数那样调用 **Block** 对象，然后使用其返回值。

```objectivec
// 声明 divBlock 变量
double (^divBlock)(double, double);

// 将 Block 对象赋给变量
divBlock = ^(double dividend, double divisor) {
  double quotient = dividend / divisor;
  return quitient;
}

// 调用 Block 对象时，设置一个变量来接收返回值
double myQuotient = divBlock(42.0, 12.5);
```

## 匿名 Block 对象

匿名的 **Block** 对象是可以传递给方法的 Block 对象的，而不需要先赋值给变量。用匿名 **Block** 对象修改示例代码：

```objectivec
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        
        // 创建两个数组对象，分别用于保存最初的字符串对象和去除元音字母后的版本
        NSArray *originalStrings = @[@"SagurrKraut",@"Raygun",@"Big Nerd Ranch",@"Mississippi"];
        NSLog(@"%@",originalStrings);
        NSMutableArray *devoweLizedStrings = [NSMutableArray array];
        // 创建数组对象，保存需要从字符串中移除的字符
        NSArray *vowels = @[@"a",@"e",@"i",@"o",@"u"];
        
        [originalStrings enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {

            // 检查字符串是否包含字符y(包含大小写)，如果有，则设置指针指向YES（会阻止 Block 对象进行枚举），终止枚举。
            NSRange yRange = [obj rangeOfString:@"y"
                                           options:NSCaseInsensitiveSearch];
            // 是否包含字符y?
            if (yRange.location != NSNotFound) {
                *stop = YES;    // 执行完当前的 Block 对象后终止枚举过程
                return;         // 结束当前正在执行的 Block 对象
            }
            
            NSMutableString *newString = [NSMutableString stringWithString:obj];
            
            // 枚举数组中的字符串，将所有出现的元音字母替换成空字符串
            for (NSString *s in vowels) {
                NSRange fullRange = NSMakeRange(0, [newString length]);
                [newString replaceOccurrencesOfString:s
                                           withString:@""
                                              options:NSCaseInsensitiveSearch
                                                range:fullRange];
            }
            [devoweLizedStrings addObject:newString];
        }];
        
        NSLog(@"new strings:%@",devoweLizedStrings);
    }
    return 0;
}
```

## 外部变量

**外部变量**：在 **Block** 对象中使用外部创建的其它变量。

* 基本类型的变量：**Block** 对象通过拷贝变量的值，并用 **Block** 对象内的局部变量保存。
* 指针类型的变量：**Block** 对象使用**强引用**。

## 修改外部变量

正如上面所说，在 **Block** 对象，被捕获的变量是拷贝而获得的常数，外部变量的值在 **Block** 对象内部是无法被修改的。如果需要在 **Block** 对象内部修改某个外部变量，使用 `__block` 关键字修饰声明的外部变量。

```objectivec
__block int counter = 0;
```

## 在 Block 对象中使用 self

使用 self 可能会导致强引用循环的问题：

- 如果【block内部】使用【外部声明的强引用】访问【对象A】, 那么【block内部】会自动产生一个【强引用】指向【对象A】
- 如果【block内部】使用【外部声明的弱引用】访问【对象A】, 那么【block内部】会自动产生一个【弱引用】指向【对象A】

```objectivec
__weak __typeof(self) weakSelf = self;
dispatch_block_t block = ^{
    [weakSelf doSomething]; // weakSelf != nil
    // preemption, weakSelf turned nil
    [weakSelf doSomethingElse]; // weakSelf == nil
};
```

💡💡💡最佳实践：
```
__weak __typeof(self) weakSelf = self;

myObj.myBlock = ^{
    __strong __typeof(self) strongSelf = weakSelf;
    if (strongSelf) {
        [strongSelf doSomething]; // strongSelf != nil
        // preemption, strongSelf still not nil（抢占的时候，strongSelf 还是非 nil 的)
        [strongSelf doSomethingElse]; // strongSelf != nil
    } else {
        // Probably nothing... return;
    }
};
```

## 在 Block 对象中无意使用 self

如果直接在 **Block** 对象中使用实例变量，那么 **Block** 会捕获 `self`，而不会捕获实例变量。因此，**不要直接存取实例变量，使用存取方法**：

```objectivec
// Block中,不要直接存取实例变量
NSLog(@"%@", _employeeID); // 不推荐

// 要使用存取方法
NSLog(@"%@", weakSelf.employeeID);
```
以上代码中，编译器会把 `_employeeID` 看成是 `self->_employeeID`，`self` 就会被 Block 对象无意中捕获了。这样就会造成强引用循环问题！


## 参考

* [Objective-C中的Block @onevcat](https://onevcat.com/2011/11/objc-block/)
* [iOS中书写代码规范35条小建议](http://www.cocoachina.com/ios/20170324/18950.html)
* [谈Objective-C block的实现 @唐巧](http://blog.devtang.com/2013/07/28/a-look-inside-blocks/)
* [iOS OC 语言: Block 底层实现原理](https://www.jianshu.com/p/e23078c11518)
* [iOS闭包循环引用精讲](http://www.cocoachina.com/articles/15349)
* [有一种 Block 叫 Callback，有一种 Callback 叫 CompletionHandler](https://github.com/ChenYilong/iOSBlog/blob/master/Tips/%E5%9F%BA%E4%BA%8EWebsocket%E7%9A%84IM%E5%8D%B3%E6%97%B6%E9%80%9A%E8%AE%AF%E6%8A%80%E6%9C%AF/%E6%9C%89%E4%B8%80%E7%A7%8D%20Block%20%E5%8F%AB%20Callback%EF%BC%8C%E6%9C%89%E4%B8%80%E7%A7%8D%20Callback%20%E5%81%9A%20CompletionHandler.md)
