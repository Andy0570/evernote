### GCD 函数

```objective-c
// ===============================================================
// 异步后台执行
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_HIGH, 0), ^{
    // do something
});


// ===============================================================
// 异步主线程执行
dispatch_async(dispatch_get_main_queue(), ^{
   // do something
});


// ===============================================================
// 一次性执行:单例
static dispatch_once_t onceToken;
dispatch_once(&onceToken, ^{
    // code to be executed once
});


// ===============================================================
// 延迟2秒执行
double delayInSeconds = 2.0;
dispatch_time_t popTime = dispatch_time(DISPATCH_TIME_NOW, (int64_t)(delayInSeconds * NSEC_PER_SEC));
dispatch_after(popTime, dispatch_get_main_queue(), ^{
    // code to be executed on the main queue after delay
});


// ===============================================================
// 使用 dispatch_queue_create() 自定义 dispatch_queue_t,
// 第一个参数是反向DNS样式命名惯例;
// 第二个参数指定你的队列是串行还是并发;
// 注意：当你在网上搜索例子时，你会经常看人们传递 0 或者 NULL 给 dispatch_queue_create 的第二个参数。这是一个创建串行队列的过时方式；明确你的参数总是更好。
dispatch_queue_t urls_quene = dispatch_queue_create("com.selander.GooglyPuff.photoQueue", NULL);
dispatch_async(urls_quene, ^{
    // your code
});


// ===============================================================
// 并行执行线程
dispatch_group_t group = dispatch_group_create();
dispatch_group_async(group, dispatch_get_global_queue(0, 0), ^{
    // 并行执行的线程一
});
dispatch_group_async(group, dispatch_get_global_queue(0, 0), ^{
    // 并行执行的线程二
});
dispatch_group_notify(group, dispatch_get_global_queue(0, 0), ^{
    // 汇总结果
});

// ===============================================================
// 每秒执行
-(void)startTime{
    
    __block int timeout = 60; // 倒计时时间
    NSTimeInterval intervalInSeconds = 1.0; // 执行时间间隔
    
    //获取后台队列
    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    
    // 以下代码可以使用代码片段库： dispatch_source_t timer
    // 创建一个timer放到队列中
    dispatch_source_t timer = dispatch_source_create(DISPATCH_SOURCE_TYPE_TIMER, 0, 0, queue);
    // 设置timer的首次执行时间、执行时间间隔、精确度
    dispatch_source_set_timer(timer, DISPATCH_TIME_NOW, intervalInSeconds * NSEC_PER_SEC, 0 * NSEC_PER_SEC);
    dispatch_source_set_event_handler(timer, ^{
        // 设置timer执行的事件
        if (timeout <= 0) {
            dispatch_source_cancel(timer); // 关闭timer
            dispatch_async(dispatch_get_main_queue(), ^{
                // 倒计时结束，回到主线程更新UI
            });
        }else {
            dispatch_async(dispatch_get_main_queue(), ^{
                // 倒计时进行中，更新UI
                NSLog(@"===%ds===",timeout);
            });
        }
        timeout --;
    });
    // 激活timer
    dispatch_resume(timer);
}
```



### 队列的四个优先级

```objective-c
 *  - DISPATCH_QUEUE_PRIORITY_HIGH:         QOS_CLASS_USER_INITIATED
 *  - DISPATCH_QUEUE_PRIORITY_DEFAULT:      QOS_CLASS_DEFAULT
 *  - DISPATCH_QUEUE_PRIORITY_LOW:          QOS_CLASS_UTILITY
 *  - DISPATCH_QUEUE_PRIORITY_BACKGROUND:   QOS_CLASS_BACKGROUND
```



### 引用

下面是一个关于在 `dispatch_async` 上如何以及何时使用不同的队列类型的快速指导：

- 自定义串行队列：当你想串行执行后台任务并追踪它时就是一个好选择。这消除了资源争用，因为你知道一次只有一个任务在执行。注意若你需要来自某个方法的数据，你必须内联另一个 Block 来找回它或考虑使用 `dispatch_sync`。
- 主队列（串行）：这是在一个并发队列上完成任务后更新 UI 的共同选择。要这样做，你将在一个 Block 内部编写另一个 Block 。以及，如果你在主队列调用 `dispatch_async` 到主队列，你能确保这个新任务将在当前方法完成后的某个时间执行。
- 并发队列：这是在后台执行非 UI 工作的共同选择。



不知道何时适合使用 `dispatch_after` ？

- 自定义串行队列：在一个自定义串行队列上使用 `dispatch_after` 要小心。你最好坚持使用主队列。
- 主队列（串行）：是使用 `dispatch_after` 的好选择；Xcode 提供了一个不错的自动完成模版。
- 并发队列：在并发队列上使用 `dispatch_after` 也要小心；你会这样做就比较罕见。还是在主队列做这些操作吧。



下面是你何时会——和不会——使用障碍函数 `Dispatch barriers` 的情况：

- 自定义串行队列：一个很坏的选择；障碍不会有任何帮助，因为不管怎样，一个串行队列一次都只执行一个操作。
- 全局并发队列：要小心；这可能不是最好的主意，因为其它系统可能在使用队列而且你不能垄断它们只为你自己的目的。
- 自定义并发队列：这对于原子或临界区代码来说是极佳的选择。任何你在设置或实例化的需要线程安全的事物都是使用障碍的最佳候选。



下面是一个快速总览，关于在何时以及何处使用 `dispatch_sync` ：

- 自定义串行队列：在这个状况下要非常小心！如果你正运行在一个队列并调用 `dispatch_sync` 放在同一个队列，那你就百分百地创建了一个死锁。
- 主队列（串行）：同上面的理由一样，必须非常小心！这个状况同样有潜在的导致死锁的情况。
- 并发队列：这才是做同步工作的好选择，不论是通过调度障碍，或者需要等待一个任务完成才能执行进一步处理的情况。





### Dispatch Groups

关于何时以及怎样使用有着不同的队列类型的 Dispatch Group ：

- 自定义串行队列：它很适合当一组任务完成时发出通知。
- 主队列（串行）：它也很适合这样的情况。但如果你要同步地等待所有工作地完成，那你就不应该使用它，因为你不能阻塞主线程。然而，异步模型是一个很有吸引力的能用于在几个较长任务（例如网络调用）完成后更新 UI 的方式。
- 并发队列：它也很适合 Dispatch Group 和完成时通知。



#### ① `dispatch_group_wait`

```objective-c
- (void)downloadPhotosWithCompletionBlock:(BatchPhotoDownloadingCompletionBlock)completionBlock
{
    //  因为你在使用的是同步的 dispatch_group_wait ，它会阻塞当前线程，所以你要用 dispatch_async 将整个方法放入后台队列以避免阻塞主线程。
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_HIGH, 0), ^{ // 1
        
        __block NSError *error;
        //  创建一个新的 Dispatch Group，它的作用就像一个用于未完成任务的计数器。
        dispatch_group_t downloadGroup = dispatch_group_create(); // 2
        
        for (NSInteger i = 0; i < 3; i++) {
            NSURL *url;
            switch (i) {
                case 0:
                    url = [NSURL URLWithString:kOverlyAttachedGirlfriendURLString];
                    break;
                case 1:
                    url = [NSURL URLWithString:kSuccessKidURLString];
                    break;
                case 2:
                    url = [NSURL URLWithString:kLotsOfFacesURLString];
                    break;
                default:
                    break;
            }
            //  dispatch_group_enter 手动通知 Dispatch Group 任务已经开始。你必须保证 dispatch_group_enter 和 dispatch_group_leave 成对出现，否则你可能会遇到诡异的崩溃问题。
            dispatch_group_enter(downloadGroup); // 3
            Photo *photo = [[Photo alloc] initwithURL:url
                                  withCompletionBlock:^(UIImage *image, NSError *_error) {
                                      if (_error) {
                                          error = _error;
                                      }
                                      //    手动通知 Group 它的工作已经完成。再次说明，你必须要确保进入 Group 的次数和离开 Group 的次数相等。
                                      dispatch_group_leave(downloadGroup); // 4
                                  }];
            
            [[PhotoManager sharedManager] addPhoto:photo];
        }
        // dispatch_group_wait 会一直等待，直到任务全部完成或者超时。如果在所有任务完成前超时了，该函数会返回一个非零值。你可以对此返回值做条件判断以确定是否超出等待周期；然而，你在这里用 DISPATCH_TIME_FOREVER 让它永远等待。它的意思，勿庸置疑就是，永－远－等－待！这样很好，因为图片的创建工作总是会完成的。
        dispatch_group_wait(downloadGroup, DISPATCH_TIME_FOREVER); // 5
        //  此时此刻，你已经确保了，要么所有的图片任务都已完成，要么发生了超时。然后，你在主线程上运行 completionBlock 回调。这会将工作放到主线程上，并在稍后执行。
        dispatch_async(dispatch_get_main_queue(), ^{ // 6
            // 最后，检查 completionBlock 是否为 nil，如果不是，那就运行它。
            if (completionBlock) { // 7
                completionBlock(error);
            }
        });
    });
}
```



#### ② `dispatch_group_notify`

```objective-c
- (void)downloadPhotosWithCompletionBlock:(BatchPhotoDownloadingCompletionBlock)completionBlock
{
    // 1
    //  在新的实现里，因为你没有阻塞主线程，所以你并不需要将方法包裹在 async 调用中。
    __block NSError *error;
    dispatch_group_t downloadGroup = dispatch_group_create();
    
    for (NSInteger i = 0; i < 3; i++) {
        NSURL *url;
        switch (i) {
            case 0:
                url = [NSURL URLWithString:kOverlyAttachedGirlfriendURLString];
                break;
            case 1:
                url = [NSURL URLWithString:kSuccessKidURLString];
                break;
            case 2:
                url = [NSURL URLWithString:kLotsOfFacesURLString];
                break;
            default:
                break;
        }
        
        // 同样的 enter 方法，没做任何修改。
        dispatch_group_enter(downloadGroup); // 2
        Photo *photo = [[Photo alloc] initwithURL:url
                              withCompletionBlock:^(UIImage *image, NSError *_error) {
                                  if (_error) {
                                      error = _error;
                                  }
                                  // 同样的 leave 方法，也没做任何修改。
                                  dispatch_group_leave(downloadGroup); // 3
                              }];
        
        [[PhotoManager sharedManager] addPhoto:photo];
    }
    
    //  dispatch_group_notify 以异步的方式工作。当 Dispatch Group 中没有任何任务时，它就会执行其代码，那么 completionBlock 便会运行。你还指定了运行 completionBlock 的队列，此处，主队列就是你所需要的。
    dispatch_group_notify(downloadGroup, dispatch_get_main_queue(), ^{ // 4
        if (completionBlock) {
            completionBlock(error);
        }
    });
}
```





那何时才适合用 `dispatch_apply` 呢？

- 自定义串行队列：串行队列会完全抵消 `dispatch_apply` 的功能；你还不如直接使用普通的 `for` 循环。
- 主队列（串行）：与上面一样，在串行队列上不适合使用 `dispatch_apply` 。还是用普通的 `for` 循环吧。
- 并发队列：对于并发循环来说是很好选择，特别是当你需要追踪任务的进度时。

```objective-c
- (void)downloadPhotosWithCompletionBlock:(BatchPhotoDownloadingCompletionBlock)completionBlock
{
    __block NSError *error;
    dispatch_group_t downloadGroup = dispatch_group_create();
 
    dispatch_apply(3, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_HIGH, 0), ^(size_t i) {
 
        NSURL *url;
        switch (i) {
            case 0:
                url = [NSURL URLWithString:kOverlyAttachedGirlfriendURLString];
                break;
            case 1:
                url = [NSURL URLWithString:kSuccessKidURLString];
                break;
            case 2:
                url = [NSURL URLWithString:kLotsOfFacesURLString];
                break;
            default:
                break;
        }
 
        dispatch_group_enter(downloadGroup);
        Photo *photo = [[Photo alloc] initwithURL:url
                              withCompletionBlock:^(UIImage *image, NSError *_error) {
                                  if (_error) {
                                      error = _error;
                                  }
                                  dispatch_group_leave(downloadGroup);
                              }];
 
        [[PhotoManager sharedManager] addPhoto:photo];
    });
 
    dispatch_group_notify(downloadGroup, dispatch_get_main_queue(), ^{
        if (completionBlock) {
            completionBlock(error);
        }
    });
}
```





### 参考

* [使用GCD @唐巧](http://blog.devtang.com/2012/02/22/use-gcd/)
* [Multithreading and Grand Central Dispatch on iOS for Beginners Tutorial](https://www.raywenderlich.com/4295/multithreading-and-grand-central-dispatch-on-ios-for-beginners-tutorial)
* [如何使用 NSOperations 和 NSOperationQueues](https://justinyan.me/post/2077)
* [GCD 深入理解：第一部分](https://github.com/nixzhu/dev-blog/blob/master/2014-04-19-grand-central-dispatch-in-depth-part-1.md)
* [GCD 深入理解：第二部分](https://github.com/nixzhu/dev-blog/blob/master/2014-05-14-grand-central-dispatch-in-depth-part-2.md)
* [ios多线程操作（六）—— GCD全局队列与主队列](http://subscribe.mail.10086.cn/subscribe/readAll.do?columnId=563&itemId=3130310)
