[移动弹窗基础知识浅析——IOS 弹窗体系
](https://www.jianshu.com/p/8a93dd9555e0)
主流 APP 上的半透明弹窗效果：
![image](http://upload-images.jianshu.io/upload_images/2648731-a5bdac848e92b3b0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### GitHub 第三方框架

类似样式的第三方框架

* [Custom iOS AlertView](https://github.com/wimagguc/ios-custom-alertview)
* [SCLAlertView](https://github.com/dogo/SCLAlertView)



### 原生实现

如何在当前页面显示一个半透明的弹窗视图？

新建一个带 Nib 的 **UIViewController** 子类对象（本实例类名为 **HQLVersionUpdateViewController**），接着设置根视图的不透明度（Opacity），这里的值设置为60%：

![image](http://upload-images.jianshu.io/upload_images/2648731-a0ba0f0c9c12f57b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接着构建你想要显示的弹窗视图：

![image](http://upload-images.jianshu.io/upload_images/2648731-8cc8d6795da345af.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

最后添加在当前页面显示的代码：

```objective-c
// 显示版本更新弹窗
HQLVersionUpdateViewController *versionUpdateVC = [[HQLVersionUpdateViewController alloc] init];
versionUpdateVC.modalTransitionStyle = UIModalTransitionStyleCrossDissolve; // 设置弹出方式
versionUpdateVC.providesPresentationContextTransitionStyle = YES;
versionUpdateVC.definesPresentationContext = YES;
versionUpdateVC.modalPresentationStyle = UIModalPresentationOverFullScreen; // 设置全屏显示
[self presentViewController:versionUpdateVC animated:YES completion:nil];
```



### 参考

* [iOS 创建半透明 ViewController](http://miketech.it/ios-transparent-viewcontroller/)
* [iOS 利用模态视图转换弹出半透明视图](http://blog.sina.com.cn/s/blog_ce804acc0102vyrz.html)
