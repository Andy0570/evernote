# iOS 单元测试&UI测试

## Unit Tests

参考

- [iOS单元测试UnitTest简单使用](http://www.jianshu.com/p/1a3772de126b)
- [iOS-使用Xcode自带单元测试UnitTest](http://www.jianshu.com/p/009844a0b9ed)



#### 新建单元测试类

**File** —> **New** —> **Target** —> **iOS Unit Testing Bundle**

```objective-c
#import <XCTest/XCTest.h>
#import "Person.h" // 导入需要被测试的类

@interface PassValueDemoTests : XCTestCase

@end

@implementation PassValueDemoTests

// 一次单元测试开始前的工作，可以设置全局变量
// 此方法会在调用类的测试方法被调用之前被调用
- (void)setUp {
    [super setUp];
    // Put setup code here. This method is called before the invocation of each test method in the class.
    
}

// 一次单元测试结束前的销毁工作
// 此方法会在调用类的测试方法被调用之后被调用
- (void)tearDown {
    // Put teardown code here. This method is called after the invocation of each test method in the class.
    [super tearDown];
}

// 功能测试
- (void)testExample {
    // This is an example of a functional test case.
    // Use XCTAssert and related functions to verify your tests produce the correct results.
}

// 性能测试
- (void)testPerformanceExample {
    // This is an example of a performance test case.
    [self measureBlock:^{
        // Put the code you want to measure the time of here.
        // 将要测量执行时间的代码放在此处
        
        NSTimeInterval start = CACurrentMediaTime();
        for (int i = 0; i < 1000 ; i++) {
            NSLog(@"i = %d",i);
        }
        NSLog(@"%f",CACurrentMediaTime() - start);
    }];
}

// 自定义测试方法，需要以 test 开头
- (void)testNewPerson {
    [self checkPersionDict:@{@"name":@"张三",@"age":@20}];
    [self checkPersionDict:@{@"name":@"Tom"}];
    [self checkPersionDict:@{}];
//    [self checkPersionDict:@{@"name":@"张三",@"age":@20,@"title":@"box"}];
}

// 检查字典所建的 Person 信息
- (void)checkPersionDict:(NSDictionary *)dict {
    Person *person = [[Person alloc] initWithDictionary:dict];
    NSString *name = dict[@"name"];
    NSUInteger age = [dict[@"age"] unsignedIntegerValue];
    
    // 1.检查名称
    XCTAssert([name isEqualToString:person.name] || person.name == nil, @"姓名不一致");
    XCTAssert(age == person.age, @"年龄不一致");
}
@end
```

### 单元测试断言方法

```objective-c
XCTFail(format…)  //生成一个失败的测试；
XCTAssertNil(a1, format...)  //为空判断，a1为空时通过，反之不通过；
XCTAssertNotNil(a1, format…) //不为空判断，a1不为空时通过，反之不通过；
XCTAssert(expression, format...) //当expression求值为TRUE时通过；
XCTAssertTrue(expression, format...) //当expression求值为TRUE时通过；
XCTAssertFalse(expression, format...)  //当expression求值为False时通过；
XCTAssertEqualObjects(a1, a2, format...)  //判断相等，[a1 isEqual:a2]值为TRUE时通过，其中一个不为空时，不通过；
XCTAssertNotEqualObjects(a1, a2, format...)  //判断不等，[a1 isEqual:a2]值为False时通过；
XCTAssertEqual(a1, a2, format...)  //判断相等（当a1和a2是 C语言标量、结构体或联合体时使用,实际测试发现NSString也可以）；
XCTAssertNotEqual(a1, a2, format...)  //判断不等（当a1和a2是 C语言标量、结构体或联合体时使用）；
XCTAssertEqualWithAccuracy(a1, a2, accuracy, format...)  //判断相等，（double或float类型）提供一个误差范围，当在误差范围（+/-accuracy）以内相等时通过测试；
XCTAssertNotEqualWithAccuracy(a1, a2, accuracy, format...)   //判断不等，（double或float类型）提供一个误差范围，当在误差范围以内不等时通过测试；
XCTAssertThrows(expression, format...)  //异常测试，当expression发生异常时通过；反之不通过；
XCTAssertThrowsSpecific(expression, specificException, format...)  //异常测试，当expression发生specificException异常时通过；反之发生其他异常或不发生异常均不通过；
XCTAssertThrowsSpecificNamed(expression, specificException, exception_name, format...)  //异常测试，当expression发生具体异常、具体异常名称的异常时通过测试，反之不通过；
XCTAssertNoThrow(expression, format…)  //异常测试，当expression没有发生异常时通过测试；
XCTAssertNoThrowSpecific(expression, specificException, format...)  //异常测试，当expression没有发生具体异常、具体异常名称的异常时通过测试，反之不通过；
XCTAssertNoThrowSpecificNamed(expression, specificException, exception_name, format...)  //异常测试，当expression没有发生具体异常、具体异常名称的异常时通过测试，反之不通过。
```



# UI Tests

参考
- [UI 测试](http://www.jianshu.com/p/877f2a1dcabc)
