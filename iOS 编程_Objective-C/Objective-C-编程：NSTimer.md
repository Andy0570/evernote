**定时器**：让程序定时执行某一个方法。

## 用 NSTimer 封装的定时器示例

### MyTimeCounter.h 文件

```objective-c
#import <Foundation/Foundation.h>

@protocol MyTimeCounterDelegate <NSObject>
// 外部调用这个类需要实现的代理方法，用于返回当前计时的时间
-(void)currentTime:(NSUInteger)time;
@end

// 自定义的定时器类
@interface MyTimeCounter : NSObject

@property (nonatomic, strong) id<PATimeCounterDelegate>delegate;

- (void)start;
- (void)stop;
- (void)pause;
- (void)continue;

@end
```

### MyTimeCounter.m 文件

```objective-c
#import "MyTimeCounter.h"

@interface MyTimeCounter()

@property (nonatomic, assign) NSUInteger count; // 全局属性，用于记录当前的秒数
@property (nonatomic, strong) NSTimer *timer;

@end

@implementation MyTimeCounter

// 开始计时
- (void)start {
    [self start]; // 重置计时器
    
    /* 方法作用：每隔1秒执行指定方法
     *
     * 参数说明：
     * TimeInterval : 执行之前等待的时间。比如设置成 1.0，就代表 1 秒后执行方法。
     * target : 需要执行方法的对象。
     * selector : 需要执行的方法。
     * userInfo : 用户信息
     * repeats : 是否需要循环
     */
    self.timer = [NSTimer scheduledTimerWithTimeInterval:1.0f
                                                  target:self
                                                selector:@selector(repeatShowTime:)
                                                userInfo:nil
                                                 repeats:YES];
}

- (void)repeatShowTime:(NSTimer *)tempTimer {
    _count ++;
    
    // 返回当前记录的时间
    if (_delegate && [_delegate respondsToSelector:@selector(currentTime:)]) {
        [_delegate currentTime:_count];
    }
}

// 停止计时
- (void)stop {
    // 如果已经存在 timer 对象，则释放
    if (self.timer) {
        [self.timer invalidate]; // 释放计时器
        self.timer = nil;
    }
    _count = 0;
}

// 暂停
- (void)pause {
    [self.timer setFireDate:[NSDate distantFuture]];
}

// 继续
- (void)continue {
    [self.timer setFireDate:[NSDate date]];
}

// 销毁
- (void)dealloc {
    if (self.timer) {
        [self.timer invalidate];
        self.timer = nil;
    }
}

@end
```

### 外部调用

```objective-c
#import "MyTimeCounter.h"

// 默认倒计时 15s
static int const KCountDown = 15;

//...

@property (nonatomic, strong) MyTimeCounter *timeManager; // 倒计时管理器

//...

// Lazy loading
- (PATimeCounter *)timeManager{
    if (!_timeManager) {
        _timeManager = [[PATimeCounter alloc] init];
        _timeManager.delegate = self;
    }
    return _timeManager;
}

- (void)myFunction {
  [self.timeManager start]; // 开始倒计时
  
  // ...
  
  [self.timeManager stop]; // 结束倒计时
}

#pragma mark - MyTimeCounterDelegate

// 倒计时管理器
- (void)currentTime:(NSUInteger)time {
    if (time != KCountDown) {
        // 主线程更新 UI
        __weak __typeof(self)weakSelf = self;
        dispatch_async(dispatch_get_main_queue(), ^{
            weakSelf.timeLabel.text = [NSString stringWithFormat:@"%d", KCountDown - time];
        });
    } else {
        _isTimeOut = YES;
    }
}
```



## 通过 GCD 实现的 60s 倒计时示例

```objective-c
// 重复执行事件
__block int timeout = 60; // 倒计时时间
NSTimeInterval intervalInSeconds = 1.0; // 执行时间间隔，1秒

// 全局队列
dispatch_queue_t queue = dispatch_get_global_queue(QOS_CLASS_DEFAULT, 0);

// 计时器
dispatch_source_t timer = dispatch_source_create(DISPATCH_SOURCE_TYPE_TIMER, 0, 0, queue);
dispatch_source_set_timer(timer, DISPATCH_TIME_NOW, intervalInSeconds * NSEC_PER_SEC, 0 * NSEC_PER_SEC);
dispatch_source_set_event_handler(timer, ^{
    // 执行的事件
    if (timeout <= 0) {
        dispatch_source_cancel(timer);
        dispatch_async(dispatch_get_main_queue(), ^{
            // 主队列执行
            
        });
    } else {
        dispatch_async(dispatch_get_main_queue(), ^{
            // 主队列执行
            
        });
    }
    timeout --;
});
dispatch_resume(timer);
```



## 开源的第三方框架

参考 GitHub：<https://github.com/search?q=iOS+Countdown>



## 参考

* [掘金：iOS 倒计时的探究与选择](https://juejin.im/post/5b1a4b11f265da6e2e4ac483)
* [iOS 开发 简单实现倒计时的方法](http://www.voidcn.com/article/p-yogzdokx-bpb.html)
* [MrPeak 杂货铺：iOS 关于时间的处理](http://mrpeak.cn/blog/ios-time/)
* [iOS 经典讲解之 NSTimer、CADisplayLink、GCD 三种定时器的使用](https://blog.csdn.net/loving_ios/article/details/50800992)
* [更可靠和高精度的 iOS定时器](http://blog.lessfun.com/blog/2016/08/05/reliable-timer-in-ios/) ⭐️⭐️⭐️
* [深入理解 RunLoop](https://blog.ibireme.com/2015/05/18/runloop/) ⭐️⭐️⭐️
