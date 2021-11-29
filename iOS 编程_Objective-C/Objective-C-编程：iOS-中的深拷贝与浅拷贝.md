# 导读

最近经常在关注的公众号或者技术网站看到关于 iOS 深拷贝与浅拷贝的话题。看到一篇技术文章，点击收藏或者只是读过一遍，并不会让你深刻领悟其中的技术原理，只有照着源码敲一遍，运行调试一通，你才会深谙其道，说不定还会有新的发现。所以，这篇文章就是阅读了前人的文章，自己又敲了一遍，加深一下印象，然后还有一点点新的感悟和理解。

# 一、概念与总结

## 1. 浅拷贝

浅拷贝就是对内存地址的复制，让【目标对象指针】和【源对象指针】指向同一块内存空间，当内存被销毁时，指向这块内存的所有指针需要重新定义才可以使用，要不然会成为野指针。

![](http://upload-images.jianshu.io/upload_images/2648731-ee63ca9b3d6a73ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/400)

**浅拷贝就是拷贝【指向原来对象的】指针，使原对象的引用计数+1，可以理解为创建了一个指向原对象的新指针，并没有创建一个全新的对象。**

## 2. 深拷贝

深拷贝是指拷贝对象的具体内容，而内存地址是自主分配的，拷贝结束之后，两个对象虽然存的值是相同的，但是内存地址不一样，两个对象也互不影响，互不干涉。

![](http://upload-images.jianshu.io/upload_images/2648731-8bd7b8d1f8763dbf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/400)

**深拷贝就是创建【值与原对象相同，但是内存地址不同的】新对象，创建后的对象和原对象没有任何关系。**

## 总结

浅拷贝是指针拷贝，深拷贝是内容拷贝。本质区别在于：
* 是否影响内存地址的引用计数；
* 是否创建新的内存地址。


# 二、示例分析

在 iOS 中，深拷贝与浅拷贝要更加的复杂，涉及到容器与非容器、可变与不可变对象的 `copy` 与 `mutableCopy`，还有 **@property** 属性中的内存管理。

##  1. 非集合对象的 copy 与 mutableCopy

### 1.1 不可变对象 NSString → NSMutableString

```objectivec
NSString *string1 = @"Hello";

// 引用计数+1。
NSString *string2 = string1;

// NSString 的 copy 是浅拷贝，且 copy 返回的对象是不可变对象。
NSMutableString *string3 = [string1 copy];
// copy 返回的是不可变对象，string3 不能被修改，因此程序会 crash。
// [string3 appendString:@" World"];

// NSString 的 mutableCopy 是深拷贝。
NSMutableString *string4 = [string1 mutableCopy];
[string4 appendString:@" World"];

NSLog(@"string1:<%p : %@> \n",string1,string1);
NSLog(@"string2:<%p : %@> \n",string2,string2);
NSLog(@"string3:<%p : %@> \n",string3,string3);
NSLog(@"string4:<%p : %@> \n",string4,string4);
```

打印结果：

```objectivec
string1:<0x100001040 : Hello>
string2:<0x100001040 : Hello>
string3:<0x100001040 : Hello>
string4:<0x1003004a0 : Hello World>
```

结论：

* **NSString** 的 `copy` 是浅拷贝，且 `copy` 返回的对象是不可变对象；
* **NSString** 的 `mutableCopy` 是深拷贝。



### 1.2 可变对象 NSMutableString → NSMutableString

```objectivec
NSMutableString *mStr1 = [NSMutableString stringWithString:@"Hello"];

// 引用计数+1。
NSMutableString *mStr2 = mStr1;

// NSMutableString 的 copy 是深拷贝，且 copy 返回的对象是不可变对象。
NSMutableString *mStr3 = [mStr1 copy];
// copy 返回的是不可变对象，mStr3 不能被修改，因此程序会 crash。
// [mStr3 appendString:@" World"];

// NSMutableString 的 mutableCopy 是深拷贝。
NSMutableString *mStr4 = [mStr1 mutableCopy];
[mStr4 appendString:@" World"];

NSLog(@"mStr1:<%p : %@> \n",mStr1,mStr1);
NSLog(@"mStr2:<%p : %@> \n",mStr2,mStr2);
NSLog(@"mStr3:<%p : %@> \n",mStr3,mStr3);
NSLog(@"mStr4:<%p : %@> \n",mStr4,mStr4);
```

打印结果：

```objectivec
mStr1:<0x100200f10 : Hello>
mStr2:<0x100200f10 : Hello>
mStr3:<0x6f6c6c654855 : Hello>
mStr4:<0x100200ba0 : Hello World>
```

结论：

* **NSMutableString** 的 `copy` 是深拷贝，且 `copy` 返回的对象是不可变对象；
* **NSMutableString** 的 `mutableCopy` 是深拷贝；
* **NSMutableString** 的 `copy`  和 `mutableCopy` 都是深拷贝；
* ⚠️ 虽然 **NSMutableString** 的 `copy` 是深拷贝，但  `copy` 返回的对象是不可变对象，也就是说：如果对「用 `copy` 声明过的 **NSMutableString** 类型的」值进行修改会导致程序 crash。这下明白 **@property** 属性声明 **NSMutableString** 不使用 `copy` 的原因了吧：


  ```objectivec
  @property (nonatomic, copy) NSString *string;
  @property (nonatomic, strong) NSMutableString *mutableString;
  ```

  
### 1.3 可变对象 NSMutableString → NSString 

💡 引用这个示例的原因在后面【项目应用】中会讲到。

```objectivec
NSMutableString *mStr1 = [NSMutableString stringWithString:@"Hello"];

// 引用计数+1。
NSString *str2 = mStr1;

// NSMutableString 的 copy 是深拷贝。
NSString *str3 = [mStr1 copy];

// NSMutableString 的 mutableCopy 是深拷贝。
NSString *str4 = [mStr1 mutableCopy];

NSLog(@"mStr1:<%p : %@> \n",mStr1,mStr1);
NSLog(@"str2 :<%p : %@> \n",str2,str2);
NSLog(@"str3 :<%p : %@> \n",str3,str3);
NSLog(@"str4 :<%p : %@> \n",str4,str4);
```

打印结果：

```objectivec
mStr1:<0x10030a240 : Hello>
str2 :<0x10030a240 : Hello>
str3 :<0x6f6c6c654855 : Hello>
str4 :<0x100309fb0 : Hello>
```



## 2.集合对象的 copy 与 mutableCopy

### 2.1 不可变对象 NSArray

```objectivec
NSArray *array1 = @[@"Hello",@"World"];

// NSArray 的 copy 是浅拷贝，且 copy 返回的对象是不可变对象。
NSArray *array2 = [array1 copy];

// NSArray 的 mutableCopy 是深拷贝。
NSArray *array3 = [array1 mutableCopy];

NSLog(@"array1:<%p : %@> \n",array1,array1);
NSLog(@"array2:<%p : %@> \n",array2,array2);
NSLog(@"array3:<%p : %@> \n",array3,array3);
```

打印结果：

```objectivec
array1:<0x1003065d0 : (
    Hello,
    World
)>
array2:<0x1003065d0 : (
    Hello,
    World
)>
array3:<0x100306bd0 : (
    Hello,
    World
)>
```

结论：

* **NSArray** 的 `copy` 是浅拷贝，且 `copy` 返回的对象是不可变对象;
* **NSArray** 的 `mutableCopy` 是深拷贝。





### 2.2 可变对象 NSMutableArray 

```objectivec
NSMutableArray *mArray1 = [NSMutableArray arrayWithObjects:@"Hello",@"World", nil];

// NSMutableArray 的 copy 是深拷贝，且 copy 返回的对象是不可变对象。
NSMutableArray *mArray2 = [mArray1 copy];

// NSMutableArray 的 mutableCopy 是深拷贝。
NSMutableArray *mArray3 = [mArray1 mutableCopy];

NSLog(@"mArray1:<%p : %@> \n",mArray1,mArray1);
NSLog(@"mArray2:<%p : %@> \n",mArray2,mArray2);
NSLog(@"mArray3:<%p : %@> \n",mArray3,mArray3);
```

打印结果：

```objectivec
mArray1:<0x100203da0 : (
    Hello,
    World
)>
mArray2:<0x100204030 : (
    Hello,
    World
)>
mArray3:<0x1002040b0 : (
    Hello,
    World
)>
```

结论：

* **NSMutableArray** 的 `copy` 是深拷贝，且 `copy` 返回的对象是不可变对象；
* **NSMutableArray** 的 `mutableCopy` 是深拷贝。
* 重要：同样地，**@property** 属性声明：


  ```objectivec
  @property (nonatomic, copy) NSArray *array;
  @property (nonatomic, strong) NSMutableArray *mutableArray;
  ```


### 2.3 单层深拷贝

**特别注意的是**：对于集合类的可变对象来说，深拷贝并非严格意义上的深复制，只能算是**单层深复制**，即虽然新开辟了内存地址，但是存放在内存上的值（也就是数组里的元素仍然指向原数组元素值，并没有另外复制一份），这就叫做 **单层深复制**。

示例代码：

```objectivec
NSMutableString *mString1 = [NSMutableString stringWithString:@"Hello"];
NSMutableString *mString2 = [NSMutableString stringWithString:@"World"];
NSMutableArray *mArray1 = [NSMutableArray arrayWithObjects:mString1,mString2, nil];

NSMutableArray *mArray2 = [mArray1 copy];
NSMutableArray *mArray3 = [mArray1 mutableCopy];

NSLog(@"mArray1:<%p : %@> \n",mArray1,mArray1);
NSLog(@"mArray2:<%p : %@> \n",mArray2,mArray2);
NSLog(@"mArray3:<%p : %@> \n",mArray3,mArray3);
NSLog(@"数组元素地址：<value1:%p>,<value2:%p> \n",mArray1[0],mArray1[1]);
NSLog(@"数组元素地址：<value1:%p>,<value2:%p> \n",mArray2[0],mArray2[1]);
NSLog(@"数组元素地址：<value1:%p>,<value2:%p> \n",mArray3[0],mArray3[1]);

NSLog(@"-----------修改原值后---------");
[mString1 appendString:@"ios"];

NSLog(@"mArray1:<%p : %@> \n",mArray1,mArray1);
NSLog(@"mArray2:<%p : %@> \n",mArray2,mArray2);
NSLog(@"mArray3:<%p : %@> \n",mArray3,mArray3);
NSLog(@"数组元素地址：<value1:%p>,<value2:%p> \n",mArray1[0],mArray1[1]);
NSLog(@"数组元素地址：<value1:%p>,<value2:%p> \n",mArray2[0],mArray2[1]);
NSLog(@"数组元素地址：<value1:%p>,<value2:%p> \n",mArray3[0],mArray3[1]);
```

打印结果：

```objectivec
mArray1:<0x100306bc0 : (
    Hello,
    World
)>
mArray2:<0x100306d10 : (
    Hello,
    World
)>
mArray3:<0x100306dc0 : (
    Hello,
    World
)>
数组元素地址：<value1:0x100306620>,<value2:0x100306a00> 
数组元素地址：<value1:0x100306620>,<value2:0x100306a00> 
数组元素地址：<value1:0x100306620>,<value2:0x100306a00> 

 -----------修改原值后---------
mArray1:<0x100306bc0 : (
    Helloios,
    World
)>
mArray2:<0x100306d10 : (
    Helloios,
    World
)>
mArray3:<0x100306dc0 : (
    Helloios,
    World
)>
数组元素地址：<value1:0x100306620>,<value2:0x100306a00> 
数组元素地址：<value1:0x100306620>,<value2:0x100306a00> 
数组元素地址：<value1:0x100306620>,<value2:0x100306a00> 
```

结论：

* mArray1、mArray2、mArray3 的地址都不一样，开辟了新的内存空间，证明 **NSMutableArray** 的 `copy` 和 `mutableCopy` 的确都是深拷贝。
* 所有数组中值的地址是一样的，并没有开辟新的内存，而是指针指向了原地址。因此说它是**单层深拷贝**。
* 修改原数组 mArray1 中的值后，mArray2 和 mArray3 数组中的值也跟着发生了变化。也证明了它是**单层深拷贝**。




### 2.4 集合对象的完全深拷贝

2.2 中提到的  **NSMutableArray** 的拷贝只是单层深拷贝，下面的方法可以实现完全深拷贝：

#### （1）归档解档法

```objectivec
NSMutableString *mString1 = [NSMutableString stringWithString:@"Hello"];
NSMutableString *mString2 = [NSMutableString stringWithString:@"World"];
NSMutableArray *mArray1 = [NSMutableArray arrayWithObjects:mString1,mString2, nil];

// 归档
NSData *data = [NSKeyedArchiver archivedDataWithRootObject:mArray1];
// 解档
NSArray *array = [NSKeyedUnarchiver unarchiveTopLevelObjectWithData:data error:nil];

NSLog(@"mArray1:<%p : %@> \n",mArray1,mArray1);
NSLog(@"array:<%p : %@> \n",array,array);
NSLog(@"数组元素地址：<value1:%p>,<value2:%p> \n",mArray1[0],mArray1[1]);
NSLog(@"数组元素地址：<value1:%p>,<value2:%p> \n",array[0],array[1]);
```

打印结果：

```objectivec
mArray1:<0x100303270 : (
    Hello,
    World
)>
array:<0x100305400 : (
    Hello,
    World
)>
数组元素地址：<value1:0x100302f30>,<value2:0x1003030d0>
数组元素地址：<value1:0x1003053c0>,<value2:0x100305990>
```

结论：

* 两个数组的内存地址不同，数组中元素的内存地址也不同，实现了完全深拷贝。

#### (2) `- (instancetype)initWithArray:(NSArray<ObjectType> *)array copyItems:(BOOL)flag;`

```objectivec
NSMutableString *mString1 = [NSMutableString stringWithString:@"Hello"];
NSMutableString *mString2 = [NSMutableString stringWithString:@"World"];
NSMutableArray *mArray1 = [NSMutableArray arrayWithObjects:mString1,mString2, nil];

NSArray *array = [[NSArray alloc] initWithArray:mArray1 copyItems:YES];

NSLog(@"mArray1:<%p : %@> \n",mArray1,mArray1);
NSLog(@"array:<%p : %@> \n",array,array);
NSLog(@"数组元素地址：<value1:%p>,<value2:%p> \n",mArray1[0],mArray1[1]);
NSLog(@"数组元素地址：<value1:%p>,<value2:%p> \n",array[0],array[1]);
```

打印结果：

```objectivec
mArray1:<0x100403270 : (
    Hello,
    World
)>
array:<0x1004034d0 : (
    Hello,
    World
)>
数组元素地址：<value1:0x100402f30>,<value2:0x1004030d0>
数组元素地址：<value1:0x6f6c6c654855>,<value2:0x646c726f5755>
```

结论

* 两个数组的内存地址不同，数组中元素的内存地址也不同，实现了完全深拷贝。（那之前的**归档解档法**是不是有点旁门左道，或者说有点杂技代码（hack）的嫌疑🤔）



# 三、准则

1. 不可变对象的 `copy` 方法是浅拷贝，`mutableCopy`  方法是深拷贝。
2. 可变对象的 `copy` 和 `mutableCopy` 方法都是深拷贝（区别完全深拷贝与单层深拷贝） 。
3. 无论是可变对象还是不可变对象， `copy` 方法返回的对象都是不可变对象。

![](http://upload-images.jianshu.io/upload_images/2648731-8aa646f4e6320f13.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)


## 最佳实践

### 自定义指定初始化方法

#### 推荐：

```objectivec
- (instancetype)initWithUsername:(NSString *)username
                        password:(NSString *)password {
    if (self) {
        _username = [username copy];
        _password = [password copy];
    }
    return self;
}
```

#### 反对：

```objectivec
- (instancetype)initWithUsername:(NSString *)username
                        password:(NSString *)password {
    if (self) {
        _username = username;
        _password = password;
    }
    return self;
}
```

#### 原因：

如果传入的 **NSString** 对象类型是 **NSMutableString** 类型，一旦对原数据修改，会引起不必要的麻烦。如果使用 `copy` 方法，不管它是 **NSString** 对象的`copy`浅拷贝还是 **NSMutableString** 对象的`copy`深拷贝，得到的总是不可变对象。

示例代码：

```objectivec
// 1.3 扩展
NSMutableString *mStr1 = [NSMutableString stringWithString:@"Hello"];

// 引用计数+1
NSString *str2 = mStr1;
NSString *str3 = [mStr1 copy];

NSLog(@"mStr1:<%p : %@> \n",mStr1,mStr1);
NSLog(@"str2 :<%p : %@> \n",str2,str2);
NSLog(@"str3 :<%p : %@> \n",str3,str3);

NSLog(@"-----------修改原值后---------");
[mStr1 appendString:@" World"];

NSLog(@"mStr1:<%p : %@> \n",mStr1,mStr1);
NSLog(@"str2 :<%p : %@> \n",str2,str2);
NSLog(@"str3 :<%p : %@> \n",str3,str3);
```

打印结果：

```objectivec
mStr1:<0x100304b00 : Hello>
str2 :<0x100304b00 : Hello>
str3 :<0x6f6c6c654855 : Hello>
-----------修改原值后---------
mStr1:<0x100304b00 : Hello World>
str2 :<0x100304b00 : Hello World>
str3 :<0x6f6c6c654855 : Hello>
```

###  setter方法

```objectivec
@property (nonatomic, copy) NSString *str;

- (void)setStr:(NSString *)str {
    // _str = str; 不要这样写
    _str = [str copy];
}
```

## 参考

* [iOS 图文并茂的带你了解深拷贝与浅拷贝 @贝克的飞机](http://www.cnblogs.com/beckwang0912/p/7212075.html)
* [集合深浅拷贝以及经常遇到的坑（面试常问）@时间已静止](http://www.jianshu.com/p/8a35e30ddcc1)
* [程序底层——程序如何在RAM ROM运行，内存分配与分区](http://www.jianshu.com/p/d85a5e56c505)
