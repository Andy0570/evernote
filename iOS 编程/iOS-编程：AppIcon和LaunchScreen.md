# AppIcon
在XCode8之前，设置应用程序icon我都是用的一款非常好用的第三方插件RTImageAssets，[gitHub地址](https://github.com/NSLog-YuHaitao/Xcode-Plugins-4)，但是自从更新XCode8之后第三方插件便无法正常使用。于是找到了一个解决办法，[解决Xcode8第三方插件失效问题](http://www.jianshu.com/p/86dcccaec4c3),亲测可用。

### 使用 RTImageAssets 插件生成 AppIcon
如果还没有安装该插件可以看这里，[iOS开发中,推荐几款好用的Xcode插件](http://www.jianshu.com/p/3b0ea8901bc7),或者自行Google安装。

* 当APP没有使用Icon而在模拟器上调试运行时，系统会生成默认的ICON。像这样：   
![](http://upload-images.jianshu.io/upload_images/2648731-59807e34c365c6f0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)

* 找到添加AppIcon的位置,①选中项目名称，②选择general;③下拉找到App Icons and Launch Images;④点击“→” 

![](http://upload-images.jianshu.io/upload_images/2648731-c17b0c7e60c9bedb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)

* 接着界面会显示放置AppIcon的地方，如果没有的话可以移动鼠标在如下导航栏空白位置鼠标右击新建一个。
![](http://upload-images.jianshu.io/upload_images/2648731-7585d192b21d7625.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)

* 在项目中导入一个尺寸为1024×1024的图片，这里随便找一张来自网络的butterfly.png，然后返回到App Icon set 页面，在菜单栏打开 File ——> ImageAssets ——> AppIcons.
![](http://upload-images.jianshu.io/upload_images/2648731-0565d724b207aa3a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)

* 然后系统会弹出设置Icon页面，只需要将符合要求的butterfly.png图标拖入页面内，点击 Generate，关闭该窗口。
![](http://upload-images.jianshu.io/upload_images/2648731-f0d5854e203d7c7e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)

* 此时所有需要的AppIcon已经自动生成了。
![](http://upload-images.jianshu.io/upload_images/2648731-37bce5bddaeb7c88.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)

* cmd+R 编译运行验证Icon设置成功。   
![](http://upload-images.jianshu.io/upload_images/2648731-f0a32f9876b93ac7.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)

# LaunchScreen

启动图片的设置也很容易，设置方法网上可以搜到一箩筐，下面是一些可能有用的解决方案。
LaunchImage 尺寸要求：
* [https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/launch-screen/](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/launch-screen/)
* [Xcode LaunchImage Size](https://medium.com/@furkancelik/xcode-launchimage-size-47589835859)

LaunchImage 设置方法：
* [iOS适配启动页](http://www.jianshu.com/p/19a3136043dd)
* [https://qiita.com/TD3P/items/fabfdf7e39551ee89e59](https://qiita.com/TD3P/items/fabfdf7e39551ee89e59)
* [https://blog.jonslow.com/ios-launchimage-size-using-assets/](https://blog.jonslow.com/ios-launchimage-size-using-assets/)


### 参考

* [iOS开发添加静态启动页,icon图标及APP名字](http://www.jianshu.com/p/49affaef8368)   
* [设置应用程序图标和启动背景图](http://www.jianshu.com/p/f69c8dbc0ae8)
* [通过LaunchScreen自定义启动动画](http://www.jianshu.com/p/2f1149269cd0) 
* [iOS启动图和开屏广告图，类似网易](http://www.jianshu.com/p/e52806516139)  
