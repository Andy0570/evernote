选中Storyboard上的指定ViewController，设置identity inspector（身份检查器）中的 Storyboard ID 值。

![](http://upload-images.jianshu.io/upload_images/2648731-1481ac3ecd312c25.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
//        HQLLoginViewController *loginViewController = [[HQLLoginViewController alloc] init];
//        [self.navigationController pushViewController:loginViewController animated:YES];

UIStoryboard *storyboard = [UIStoryboard storyboardWithName:@"Main" bundle:[NSBundle mainBundle]];
HQLLoginViewController *loginViewController = [storyboard instantiateViewControllerWithIdentifier:@"loginViewController"];
[self.navigationController pushViewController:loginViewController animated:YES];
```

跳转到 **Segue** 连接的下一个视图控制器：
```Objective-C
[self performSegueWithIdentifier:@"ID_step2" sender:self];
```

### 参考
* [用代码创建并实例化在storyboard中声明的ViewController  ](http://blog.csdn.net/kyfxbl/article/details/17687265)
* [IOS开发－使用Storyboard进行界面跳转及传值](http://www.jianshu.com/p/88d6173665f4)
