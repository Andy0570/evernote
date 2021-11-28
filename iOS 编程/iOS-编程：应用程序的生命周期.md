### 应用程序的生命周期

#### iOS应用程序拥有的5种状态:

1. 【Not running】 应用还没启动或正在运行但是中途被系统停止；
2. 【Inactive】 应用正在前台运行(不接收事件)；
3. 【Active】 应用正在前台运行(接收事件)；
4. 【Background 】应用处于后台运行(还在执行代码)；
5. 【Suspended】 应用处于后台运行(停止执行代码)。

应用的各种状态：

| 	    状态     |    界面是否可见   |   是否能接受事件 | 是否能执行代码  |
|:------------- |:---------------:| -------------:| -------------:|
| 未运行状态     | 		否			 |         否    |       否      |
| 激活状态       | 		是         |         是    |        是     |
| 未激活状态     | 	 大部分        |         否    |        是     |
| 后台运行状态   | 	   否         |         否    |        是     |
| 挂起状态      | 	   否         |         否    |        否     |

![应用程序的生命周期](http://upload-images.jianshu.io/upload_images/1519620-8559ccb07b5a0cc7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



###  UIApplicationDelegate
当应用状态发生变化时，**UIApplication**对象的委托对象会收到相应的消息。
**UIApplicationDelegate**协议中声明的部分消息：

* **启动时间**
	
	* application: willFinishLaunchingWithOptions:
	
		> 告诉AppDelegate启动进程已经开始，但是状态恢复还没有完成
	* application: didFinishLaunchingWithOptions:
	
		> 告诉AppDelegate，启动过程几乎完成，应用程序几乎准备好运行。
* **转换到前台激活状态**	
	* applicationDidBecomeActive:
	
		> 激活装填
* **转换到非活跃状态（未激活状态）**
	* applicationWillResignActive: (前台状态——>后台运行状态。)

		> 告诉AppDelegate该应用程序即将变为非活动状态。
	* applicationWillEnterForeground: (后台运行状态——>前台时调用)

		> 告诉AppDelegate应用程序即将进入前台。
* **转换到后台运行状态**
	* applicationDidEnterBackground：
	
		> 后台运行装填
* **终止运行**
	* applicationWillTerminate: 

	> 告诉AppDelegate应用程序即将终止，仅当应用程序正在运行时调用。 如果应用程序已暂停，则不会调用此方法。


```objective-c
/**
 
 应用载入完成后调用
 
 你可以把程序载入后需要执行的代码，写在程序完成加载的方法里
 
 */
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    NSLog(@"%s",__func__);
    return YES;
}


/**
 
 应用从【前台活动状态】——>【前台非活动状态】时调用  (在此期间，程序不接受消息或事件)
 
 这可能发生在某些临时中断类型中（例如来电或SMS消息）或用户退出应用程序时，应用开始转换到后台状态时。
 使用此方法暂停正在进行的任务，禁用计时器，并使渲染回调无效。 游戏类应用应该使用这种方法暂停游戏。
 
 */
- (void)applicationWillResignActive:(UIApplication *)application {
    NSLog(@"%s",__func__);
}


/**
 
 应用进入【后台运行状态】时调用          (设置后台继续运行)

 使用此方法释放共享资源，保存用户数据，使计时器无效，并存储足够的应用程序状态信息以将应用程序恢复到其当前状态，以备日后应用终止。
 如果您的应用程序支持后台执行，则调用此方法添加要继续执行的代码，而不是调用当用户退出时的方法 applicationWillTerminate：。
 
 */
- (void)applicationDidEnterBackground:(UIApplication *)application {
    NSLog(@"%s",__func__);
}


/**
 
 应用从【后台运行状态】——>【前台活动状态】时调用

 这里你可以撤消许多进入【后台运行状态】后应用所做的改变。
 
 */
- (void)applicationWillEnterForeground:(UIApplication *)application {
    NSLog(@"%s",__func__);
}


/**
 
 应用进入【前台活动状态】后调用    (接收消息或事件)
 
 重新启动已暂停（或尚未启动）的那些当应用进入【前台非活动状态】的任何任务。 如果应用程序以前在后台运行，则可选择刷新用户界面。

 */
- (void)applicationDidBecomeActive:(UIApplication *)application {
    NSLog(@"%s",__func__);
}


/**
 应用将要退出调用 (保存数据,退出前清理)
 
 在应用程序即将要终止运行时调用。 保存数据（如果合适）。

 */
- (void)applicationWillTerminate:(UIApplication *)application {
    NSLog(@"%s",__func__);
}


/**
 
 当应用程序从系统收到内存警告时通知代理。
 
 此方法的实现应通过清除可以以后重新创建（或从磁盘重新加载）的缓存数据对象释放尽可能多的内存。 
 您可以结合UIViewController类的didReceiveMemoryWarning和UIApplicationDidReceiveMemoryWarningNotification通知使用此方法，以释放整个应用程序的内存。
 强烈建议您实现此方法。 如果您的应用程序在低内存条件下没有释放足够的内存，系统可能会立即终止。
 
 */
- (void)applicationDidReceiveMemoryWarning:(UIApplication *)application {
    NSLog(@"%s",__func__);
}


/**
 
 应用打开URL时调用
 
 如果应用程序由于某种原因无法打开，则返回NO

 */
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey, id> *)options {
    NSLog(@"%s",__func__);
    return YES;
}

```



###  测试

> **开启应用程序：**
> **[AppDelegate application:didFinishLaunchingWithOptions:]**
> **[AppDelegate applicationDidBecomeActive:]**
>
> 
> **点击Home键退出应用：**
>  **[AppDelegate applicationWillResignActive:]**
> **[AppDelegate applicationDidEnterBackground:]**
>
> 
> **点击Icon图标再次打开应用：**
> **[AppDelegate applicationWillEnterForeground:]**
> **[AppDelegate applicationDidBecomeActive:]**
>
>
> **点击Lock按钮锁定屏幕：**
> **[AppDelegate applicationWillResignActive:]**
> **[AppDelegate applicationDidEnterBackground:]**
> 
>
> **解锁屏幕：**
> **[AppDelegate applicationWillEnterForeground:]**
> **[AppDelegate applicationDidBecomeActive:]**
> 
>
> **双击Home键**： 
> **[AppDelegate applicationWillResignActive:]**
> **手势上划清理应用：**
> **[AppDelegate applicationDidEnterBackground:]**
> **[AppDelegate applicationWillTerminate:]**

### 参考文章
* [iOS从入门到精通](http://www.jianshu.com/p/3dcb1f7f14e5)
