`#define NSAssert(condition, desc, ...)`

如果给定条件为假，则生成一个断言。

参数：
* `condition`：表达式，计算结果为 `YES` 或者 `NO`。
* `desc`：`NSString` 对象类型的字符串，其中包含一个 printf-style 样式的字符串，该字符串中包含一条描述失败条件和占位符参数的错误消息。

断言（Assertion）是指在开发期间使用的、让程序在运行时进行自检的代码（通常是一个子程序或宏）。断言为真，则表明程序运行正常，而断言为假，则意味着它已经在代码中发现了意料之外的错误。断言对于大型的复杂程序或可靠性要求极高的程序来说尤其有用。

Assert 用于在代码中强制执行语义约定（编写“防御性代码”），示例代码：

```objectivec
- (void)doSomethingWithString:(NSString *)string {
    NSAssert(string != nil, @"parameters should not be nil");
    
    // ... 执行正常流程
}
```

因此，它不仅可以安全防范潜在的不良输入，而且还可以以一种有用的标准方式记录它们。


断言是为了确保一个值是它所应该的值。如果断言失败了，就意味着哪里出了问题，因此程序会崩溃退出。使用 Assert 的一个原因是，如果你有一个函数，如果传递给它的参数不是某个值（或一个值的范围），你可以使用 Assert 来确保这个值是你所期望的值，如果不是，那就意味着真的出了问题，所以应用程序会崩溃退出。Assert 对于调试/单元测试非常有用，当你对外提供框架来阻止用户做 "邪恶 "的事情时，Assert 也是非常有用的。

`NSAssert` 宏对断言条件进行评估，并作为断言处理程序的前端。

每个线程都有自己的断言处理程序，它是 `NSAssertionHandler` 对象类型。当被调用时，断言处理程序会打印一条错误消息，其中包括方法和类名（或函数名）。然后它会引发一个 `NSInternalInconsistencyException` 异常。如果条件值为 `NO`，`NSAssert()` 宏会在当前线程的断言处理程序上调用 `handleFailureInMethod:object:file:lineNumber:description:` 方法，并传递 `desc` 参数作为描述字符串。

`NSAssert()` 宏只能在 Objective-C 语言的方法中使用。

如果定义了预处理程序宏 `NS_BLOCK_ASSERTIONS` 或者 Xcode 编译设置（build setting）中的 `ENABLE_NS_ASSERTIONS` 项被禁用了，那么断言将被禁用。

> [!IMPORTANT]
>
> 不要在这个宏的条件参数中调用带有副作用的函数（避免把「需要执行有效操作的代码」放到断言中）。当断言被禁用时，条件参数不会被评估，所以如果你调用了带有副作用的函数，当你在非调试配置中构建项目时，这些函数可能永远不会被调用。

> [!IMPORTANT]
>
> 从 Xcode 4.2 开始，`release` 环境默认关闭了断言。也就是当编译发布版时，任何调用 `NSAssert()` 的地方都被空行替换。所以，不要在 `NSAssert()` 内执行任何有效操作。

> [!NOTE]
>
> 并非所有版本的配置都会默认禁用断言。

如果想要在发布版中使用 `NSAssert()`，可以在 **Build Setting** 中的 **Enable Foundation Assertions** 中修改（不建议！！！）。

![](https://upload-images.jianshu.io/upload_images/2648731-4de2ad1c50c1faa0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 总结

对来源于系统内部的可靠数据使用断言，即用断言来处理绝不应该发生的情况。不要对外部不可靠数据（如用户输入、文件、网络读取等）使用断言，即对于外部不可靠数据或预期会发生的情况应当使用错误处理。同时要避免把需要执行的代码放到断言中，断言可以看成可执行的注释。

来源于系统外部的数据都是不可信的，需要严格检查（通常是错误处理）才能放行到系统内部，这相当于一个守卫。而对于系统内部的交互（如调用其他方法），如果每次也都要去处理输入的数据，也就相当于系统没有可信的边界，会让代码变的臃肿。事实上，在系统内部传递给方法正确的参数是调用者的责任，调用者应该确保传递给所调用方法的数据是符合要求的。这样就隔离了不可靠的外部环境和可靠的内部环境，降低复杂度。

但在开发阶段，代码极可能存在缺陷，有可能是处理外部数据的逻辑不周全或调用内部方法的代码存在错误，最终造成调用失败。这个时候，断言就可以发挥作用，用来确诊到底哪一部分问题导致程序出错。在清理了所有缺陷后，内外有别的信用体系就建立起来了。等到发行版的时候，这些断言就没有存在的必要了。

单元测试可能是一个更好的方法，但有些情况下（如复杂的算法过程中），我们希望在代码中执行检查，这时断言将更有效。


## 参考

* [Apple: NSAssert](https://developer.apple.com/documentation/foundation/nsassert?language=occ)
* [stackoverflow：What is NSAssert1?](https://stackoverflow.com/questions/5496378/what-is-nsassert1)
* [stackoverflow：What's the point of NSAssert, actually?](https://stackoverflow.com/questions/1375786/whats-the-point-of-nsassert-actually)
* [简书：断言 NSAssert 的使用](https://www.jianshu.com/p/3f16e7aaf7ef)
* [CSDN：iOS 开发中断言的使用 —NSAssert ()](https://blog.csdn.net/univcore/article/details/16859263)
