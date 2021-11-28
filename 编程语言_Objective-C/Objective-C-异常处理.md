**异常**：当程序执行时，发生的某种特殊状况，打断了程序正常的运转流程。

Objective-C 的异常处理语法与 Java 和 C++ 类似。通过使用 `NSException`, `NSError` 以及自定义的异常处理类来处理这些异常。
Objective-C 对异常的支持包括四个编译器指令: `@try`、`@catch`、`@throw` 以及 `@finally`。使用规则如下: 

* 如果这句或这部分代码有可能出现问题,就把它放在`@try{}`中.
* `@catch` 捕获异常,出现了问题后,会执行到这里,然后你就可以对错误进行另外的处理,比如记录日志或者提醒用户哪错了。
* `@finally` 无论是否会抛出异常,这个块中的代码都会执行。 
* `@throw `指令允许你抛出自己的异常。可以使用该指令抛出特定的异常。

异常分类：
1. 数组越界;
2. 不能识别的方法选择器;
3. 野指针调用：指针指向销毁的对象;

异常示例代码：
```objectivec
@try {

    // @try 块中写有可能出错的代码段
    NSArray *array = [NSArray array];
    [array objectAtIndex:1];  //数组越界

} @catch (NSException *exception) {

    // @catch 块内处理异常代码
    NSLog(@"捕获到异常：%@%@",exception.name, exception.reason);

} @finally {

    //可选项，不管是否出现异常，都会执行此代码段
    NSLog(@"始终执行的代码！");

}

NSLog(@"Hello, World!");
```

输出结果：
> Demo[17328:3652315] 捕获到异常：NSRangeException*** -[__NSArray0 objectAtIndex:]: index 1 beyond bounds for empty NSArray
Demo[17328:3652315] 始终执行的代码！
Demo[17328:3652315] Hello, World!

