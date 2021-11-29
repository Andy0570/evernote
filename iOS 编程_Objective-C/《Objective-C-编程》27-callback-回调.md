回调（callback） 就是将一段可执行的代码和一个特定的事件绑定起来。当特定的事件发生时，就会执行这段代码。

## 实现回调的四种途径

1. **目标—动作对（target-action）**：在程序开始等待前，要求“当事件发生时，向指定的对象发送某个特定的消息”。这里接收消息的对象是目标（target），消息的选择器（selector）是动作（action）。
2. **辅助对象（helper objects）**：程序开始等待前，要求“当事件发生时，向遵守相应协议的辅助对象发送消息”。**委托对象**（delegate）和**数据源**（data source）是常见的辅助对象。
3. **通知（notification）**：苹果公司提供了一种称为通知中心（notification center）的对象。在程序开始等待前，可以告知通知中心“某个对象正在等待某些特定的通知”。当事件发生时，相关的对象会向通知中心发布通知，然后再由通知中心将通知转发给正在等待该通知的对象。
4. **Block 对象（Blocks）**： **Block** 是一段可执行的代码。在程序开始等待前，声明一个 **Block** 对象，当事件发生时，执行这段 **Block** 对象。

## 目标 - 动作对

💡💡💡当要向一个对象发送一个回调时，使用目标—动作对。

每隔两秒，**NSTimer** 对象会向其目标 （**BNRLogger** 实例对象）发送指定的动作消息 ```updateLastTime:```。

* **main.m** 文件

```objectivec
#import <Foundation/Foundation.h>
#import "BNRLogger.h"

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        
        BNRLogger *logger = [[BNRLogger alloc] init];
        
        /**
         *  目标-动作对
         *
         *  接收消息的对象是目标（target）
         *  消息的选择器(selector)是动作(action)
         *	__unused 修饰符用于消除"没有使用的变量"警告
         */
        __unused NSTimer *timer = [NSTimer scheduledTimerWithTimeInterval:2.0
                                       target:logger
                                     selector:@selector(updateLastTime:)
                                     userInfo:nil
                                      repeats:YES];
      
        // NSRunLoop 实例会持续等待着，当特定的事件发生时，就会向相应的对象发送消息。
        [[NSRunLoop currentRunLoop] run];

    }
    return 0;
}
```

* **BNRLogger.h** 文件

```objectivec
#import <Foundation/Foundation.h>

@interface BNRLogger : NSObject
  
@property (nonatomic) NSDate *lastTime;

- (NSString *)lastTimeString;
- (void)updateLastTime:(NSTimer *)t;

@end
```

* **BNRLogger.m** 文件

```objectivec
#import "BNRLogger.h"

@implementation BNRLogger
  
- (NSString *)lastTimeString {
    static NSDateFormatter *dateFormatter = nil;
    if (!dateFormatter) {
        dateFormatter = [[NSDateFormatter alloc] init];
        [dateFormatter setTimeStyle:NSDateFormatterMediumStyle];
        [dateFormatter setDateStyle:NSDateFormatterMediumStyle];
        NSLog(@"create dateFormatter");
    }
    return [dateFormatter stringFromDate:self.lastTime];
}

// 动作方法总是有一个实参，它是(传入发送动作消息的)那个对象
- (void)updateLastTime:(NSTimer *)t {
    self.lastTime = [NSDate date];
    NSLog(@"Just set time to %@",self.lastTimeString);
}

@end
```



## 辅助对象

💡💡💡当要向一个对象发送多个回调时，使用符合相应协议的 **辅助对象**。根据用途，辅助对象常被称为委托对象或数据源（data source）。

示例：我们知道，数据同步传输是会阻塞主线程的，也就是说，在获取数据时，用户界面会失去响应。接下来我们使用异步的方式传输网络请求数据。而且要在数据传输过程中实现回调。

* **main.m** 文件

```objectivec
#import <Foundation/Foundation.h>
#import "BNRLogger.h"

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        
        BNRLogger *logger = [[BNRLogger alloc] init];
        
        NSString *string = @"http://bit.ly/nsurlsession-test";
        NSURL *url = [NSURL URLWithString:string];
        NSURLRequest *request = [NSURLRequest requestWithURL:url];
        
        /**
         *  首先创建一个 NSURLSessionConfiguration 对象。
         *
         *  NSURLSessionConfiguration 对象有一些诸如 allowsCellularAccess 、
         *  HTTPAdditionalHeaders 等属性。
         */
        NSURLSessionConfiguration *config = [NSURLSessionConfiguration defaultSessionConfiguration];
        
        /**
         *  然后创建一个 NSURLSession 对象。
         *
         *  在真正面向对象的程序中，这将是您创建的一些对象的属性，而不仅仅是一个局部变量。
         *  任何关于iOS开发的书（包括我们的）都将显示使用 NSURLSession 的更为正确的例子。
         *
         */
        NSURLSession *session = [NSURLSession sessionWithConfiguration:config
                                                              delegate:logger
                                                         delegateQueue:nil];
        
        /**
         *  你创建的最后一个对象是一个 NSURLSessionTask - 这与 NSURLConnection 是类似的。
         *
         *  不同之处在于，每个独立的 NSURLConnection 对象都具有独立的委托对象和属性。
         *  而所有的 NSURLSessionTask 对象都被 Session 对象所拥有，
         *  该对象只拥有一个委托对象以处理所有任务的回调。
         */
        NSURLSessionTask *task = [session dataTaskWithRequest:request];

        // 你通过发送 - resume 来告诉 task 开始工作
        // 然后 task 将根据需要将实现委托的工作返回到 Session 对象。
        [task resume];
        
        
        // Don't forget that this last bit is a hack;
        //
        // 你永远不会在生产应用程序中使用它。
        // 你在这里看到它只是因为它要强制命令行应用程序保持运行。
        // 通常 main（）函数返回时应用终止运行。
        [[NSRunLoop mainRunLoop] run];
        
    }
    return 0;
}
```

* **BNRLogger.h** 文件

```objectivec
#import <Foundation/Foundation.h>

// 声明 BNRLogger 会实现 NSURLSessionDelegate, NSURLSessionDataDelegate 协议
@interface BNRLogger : NSObject <NSURLSessionDelegate, NSURLSessionDataDelegate>

@end
```

* **BNRLogger.m** 文件

```objectivec
#import "BNRLogger.h"

/**
 *  我决定使用类扩展（class extension）的方式添加可变数据对象，因为这是非常流行的做法。
 *
 *  你应该逐渐偏向于使用属性（@property）来代替实例变量，使用访问器而不是直接读取状态，
 *  会让你的代码更安全，并且如果当前类中的属性或实例变量不需要暴露给应用程序中的其他类，
 *  那么你就应该使用类扩展（class extension）来隐藏它。
 */
@interface BNRLogger ()
@property (nonatomic) NSMutableData *downloadedData;
@end

@implementation BNRLogger

// 除了预期的方法之外，这里没有太多变化
// 另外，我使用了属性符号。
- (void)URLSession:(NSURLSession *)session
          dataTask:(NSURLSessionDataTask *)dataTask
    didReceiveData:(NSData *)data
{
    NSLog(@"received %lu bytes", data.length);
    
    // 如果 NSMutableData 对象还不存在，就创建它
    if (!self.downloadedData) {
        self.downloadedData = [[NSMutableData alloc] init];
    }
    
    // 将新数据附加到现有的数据堆中。
    [self.downloadedData appendData:data];
}

// Rather than having two methods for completion (one for success, one for failure),
// the session will call this method in either case, providing an NSError only if
// the task failed.
- (void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task didCompleteWithError:(NSError *)error
{
    // 正常情况下，就像“读取文件”章节那样，我们通常会检查数据而不是检查错误来知道是否有问题
    // 但是在这里，如果不检查错误的话，你是无法知道你收集到的数据是否完整的
    if(!error) {
        // We have all the data!
        NSLog(@"Finished! Total size is %lu bytes.", self.downloadedData.length);
        NSString *str = [[NSString alloc] initWithData:self.downloadedData
                                              encoding:NSUTF8StringEncoding];
        NSLog(@"Here you go!\n%@",str);
        NSLog(@"Printed %lu characters",str.length);
        
        
    } else {
        NSLog(@"Encountered an error: \(error)");
        self.downloadedData = nil;
    }
}
@end
```

## 通知

💡💡💡处理要发送给**多个对象**的回调时，使用通知。

当用户修改 Mac 系统的时区设置时，程序中的很多对象可能需要知道系统发生的这一变化。这些对象都可以通过通知中心将自己注册成为观察者（observer）。当系统的时区设置发生变化时，会向通知中心发布 `NSSystemTimeZoneDidChangeNotification` 通知，然后通知中心会将该通知转发给相应的观察者。

* **main.m** 文件

```objectivec
#import <Foundation/Foundation.h>
#import "BNRLogger.h"

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        
        BNRLogger *logger = [[BNRLogger alloc] init];
        
        // 将 BNRLogger 实例注册为观察者，使之在系统的时区设置发生变化时能够收到相应的通知。
        [[NSNotificationCenter defaultCenter]
                                 addObserver:logger
                                 selector:@selector(zoneChange:)
                                 name:NSSystemTimeZoneDidChangeNotification
                                 object:nil];
        
        [[NSRunLoop mainRunLoop] run];
        
    }
    return 0;
}
```
以上代码中注册了一个通知事件，当通知中心接收到 `NSSystemTimeZoneDidChangeNotification` 消息时，就会向 `BNRLogger` 实例发送 `zoneChange:` 消息。


* **BNRLogger.h** 文件

```objectivec
#import <Foundation/Foundation.h>

@interface BNRLogger : NSObject 

- (void) zoneChange:(NSNotification *)note;

@end
```

* **BNRLogger.m** 文件

```objectivec
#import "BNRLogger.h"

@implementation BNRLogger

- (void) zoneChange:(NSNotification *)note {
    NSLog(@"The system time zone has changes!");
}

@end
```

构建并运行程序，当程序处于运行状态时，打开 **System Preference**（系统偏好设置），修改系统的时区设置。这时 `zoneChange:`方法应该会被调用。


## 如何选择回调机制

* 对于只做一件事情的对象（例如 `NSTimer`），使用 **目标—动作对**。
  当要向一个对象发送一个回调时，使用目标—动作对。
* 对于功能更复杂的对象（例如 `NSURLSession`），使用 **辅助对象**。最常见的辅助对象是委托对象。
  当要向一个对象发送多个回调时，使用符合相应协议的辅助对象。根据用途，辅助对象常被称为委托对象或数据源（data source）。
* 对于要触发多个（其他对象中的）回调的对象（例如 `NSTimeZone`），使用 **通知**。
  处理要发送给多个对象的回调时，使用通知。


## 回调与对象所有权

为了避免回调时产生强引用循环的风险（例如：创建的对象拥有一个指向回调对象的指针，而这个回调对象的指针指向你创建的对象），编写代码时，应遵守以下规则：

* **通知中心不拥有观察者**。如果将某个对象注册为观察者，那么通常应该在释放该对象时将其移出通知中心：

  ```objectivec
  - (void)dealloc {
    [[NSNotificationCenter defalutCenter] removeObserver:self];
  }
  ```

* **对象不拥有委托对象或数据源对象**。如果某个新创建的对象是另一个对象的委托对象或数据源对象，那么该对象应该在其 `dealloc` 方法中取消响应的关联：

  ```objectivec
  - (void)dealloc {
    [windowThatBossesMeAround setDelegate:nil];
    [tableViewThatBegsForData setDataSource:nil];
  }
  ```

* **对象不拥有目标**。如果某个新创建的对象是另一个对象的目标，那么该对象应该在其 `dealloc`  方法中将相应的目标指针赋为 **nil**:

  ```objectivec
  - (void)dealloc {
    [buttonThatKeepsSendingMeMessage setTarget:nil];
  }
  ```

## 选择器的工作机制

当某个对象收到消息时，会向该对象的类进行查询，检查是否有与消息名称相匹配的方法。该查询过程会沿着继承层次结构向上，直到某个类回应“我有与消息相匹配的方法”。

方法的查询非常快速。如果使用方法的实际名称（可能会很长）进行查询，那么查询的速度会很慢。**为了提速，编译器会为每个其接触过的方法附上一个唯一的数字**。运行时，程序使用的是这个数字，而不是方法名。

以上提到的代表特定方法名的唯一数字称为 **选择器**（selector）。当一个方法需要一个选择器作为实参（就像`scheduledTimerWithTimeInterval:target:selector:userInfo: repeats:`）时，它实际就是需要这个数字。通过编译指令 `@selector`，可以得到与方法名相对应的选择器。
