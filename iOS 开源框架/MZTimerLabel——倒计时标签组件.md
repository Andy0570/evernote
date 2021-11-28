

* [GitHub：**MZTimerLabel**](https://github.com/mineschan/MZTimerLabel)
* star:1300+



MZTimerLabel
============


![image](http://upload-images.jianshu.io/upload_images/2648731-87f72200d2bb9070.gif?imageMogr2/auto-orient/strip)

### 目的

MZTimerLabel 是 UILabel 的子类，它提供了使用 UILabel 作为倒计时器或者秒表（类似于Apple 的时钟App）的便捷方法，而且最少只需要**2行代码**。MZTimerLabel 也提供倒计时结束时的协议方法以供调用。

### 简单示例

To use MZTimerLabel as a stopwatch and counter, you need only __2 lines__.
 ```objective-c
MZTimerLabel *stopwatch = [[MZTimerLabel alloc] initWithLabel:aUILabel];
[stopwatch start];
 ```

Easy? If you are looking for a timer, things is just similar.
 ```objective-c
MZTimerLabel *timer = [[MZTimerLabel alloc] initWithLabel:aUILabel andTimerType:MZTimerLabelTypeTimer];
[timer setCountDownTime:60];
[timer start];
 ```

Now the timer will start counting from 60 to 0 ;)

### 自定义外观

As MZTimerLabel is a UILabel subclass, you can directly allocate it as a normal UILabel and customize `timeLabel` property just like usual.

 ```objective-c
MZTimerLabel *redStopwatch = [[MZTimerLabel alloc] init];
redStopwatch.frame = CGRectMake(100,50,100,20);
redStopwatch.timeLabel.font = [UIFont systemFontOfSize:20.0f];
redStopwatch.timeLabel.textColor = [UIColor redColor];
[self.view addSubview:redStopwatch];
[redStopwatch start];
 ```

MZTimerLabel uses `00:00:00 (HH:mm:ss)` as time format, if you prefer using another format such as including milliseconds.Your can set your time format like below.

`timerExample4.timeFormat = @"HH:mm:ss SS";`

 

### Control the timer

You can start,pause,reset your timer with your custom control, set your control up and call these methods:

```objective-c
-(void)start; // 开始 
-(void)pause; // 暂停
-(void)reset; // 重设
```

#### Getter and Setters

You may control the time value and behaviours at the begining or during runtime with these properties and methods

通过以下这些方法，你可以在倒计时一开始或者进行中控制时间值和行为。

```objective-c
@property (assign) BOOL shouldCountBeyondHHLimit;   //see example #12
@property (assign) BOOL resetTimerAfterFinish;      //see example #7

-(void)setCountDownTime:(NSTimeInterval)time;
-(void)setStopWatchTime:(NSTimeInterval)time;
-(void)setCountDownToDate:(NSDate*)date;
-(void)addTimeCountedByTime:(NSTimeInterval)timeToAdd; //see example #10, #11
```

And if you want to have information of the timer, here is how.

```objective-c
@property (assign,readonly) BOOL counting;  //see example #4-7

- (NSTimeInterval)getTimeCounted;    //see example #3
- (NSTimeInterval)getTimeRemaining;  //see example #3
- (NSTimeInterval)getCountDownTime;  
```

### Timer Finish Handling

Usually when you need a timer, you need to deal with it after it finished. Following are 2 examples showing how to do it using `delegate` and `block` methods.

#### 代理方法

First, set the delegate of the timer label.

`timer.delegate = self;`

And then implement `MZTimerLabelDelegate` protocol in your dedicated class

`@interface ViewController : UIViewController<MZTimerLabelDelegate>`

Finally, implement the delegate method `timerLabel:finshedCountDownTimerWithTimeWithTime:`

 ```objective-c
 -(void)timerLabel:(MZTimerLabel*)timerLabel finshedCountDownTimerWithTime:(NSTimeInterval)countTime{
    //time is up, what should I do master?
 }
 ```

#### Blocks 方法

 Block is a very convenient way to handle the callbacks, MZTimerLabel makes your life even easier.

 ```objective-c
 MZTimerLabel *timer = [[MZTimerLabel alloc] initWithLabel:aUILabel andTimerType:MZTimerLabelTypeTimer];
[timer3 setCountDownTime:60]; 
[timer startWithEndingBlock:^(NSTimeInterval countTime) {
    //oh my gosh, it's awesome!!
}];
 ```

 Or set it seperately

 ```objective-c
[timer3 setCountDownTime:60]; 
timer.endedBlock = ^(NSTimeInterval countTime) {
    //oh my gosh, it's awesome!!
};
[timer start];
 ```
