![](http://upload-images.jianshu.io/upload_images/2648731-a87cc05949b9baf5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

官网地址：http://zipzapmac.com/Go2Shell

**Go2Shell** 可以在 Finder 中打开当前目录的终端窗口，是一个对开发者来说非常有用的App。

**特点**：软件开发工具、小巧（大概只有2M）、免费、功能简单易用。



## App Store 版本（1.2.2版本） 

‼️首先说明：不建议下载 App Store 上的版本，因为版本太旧，而且打不开 iTerm2（3.1.2版本）。

此版本的目的是演示新旧版本的不同设置



### 打开Go2Shell的配置页面

终端输入：

```shell
open -a Go2Shell --args config
```

就可以打开配置界面：

![](http://upload-images.jianshu.io/upload_images/2648731-e790bfb03db45652.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 将 Go2Shell 放到 Finder 工具栏上

1. 在应用程序中找到 Go2Shell.app，选中它。
2. 按住 command 键，鼠标左键拖动它到工具栏直到有➕号显示即可。

![](http://upload-images.jianshu.io/upload_images/2648731-45fcd29299daaf05.gif?imageMogr2/auto-orient/strip)

3. 移除：按住 command 键，选中工具栏上的小图标往上托出窗口即可。



### 将Go2Shell固定到Dock栏。 

这个很简单，固定到Dock栏之后，配合 **Manico** 快捷键使用打开终端快得飞起。



## 重点，官网下载版本（2.3版本）

之前也说过，App Store 上下载的旧版本无法打开 iTerm 2（3.1.2版本），所以我们要去[官网](http://zipzapmac.com/Go2Shell)下载最新的版本。

新版本下载安装步骤：

1. 安装后启动 Go2Shell，出现配置界面如下：

	![](http://upload-images.jianshu.io/upload_images/2648731-95a7127a40caf3b7.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 选择你喜欢的终端（当然是选择iTerm2啦）

3. 点击安装 Go2Shell 到 Finder，你会发现图标和旧版本不一样，这个是白色的。

   ![](http://upload-images.jianshu.io/upload_images/2648731-5b0db203712c3359.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 将 Go2Shell 放到 Finder 工具栏上

2.3 版本的 Go2Shell 图标拖到 Dock 栏上是打不开终端的，它只会默认打开配置窗口。

你要去路径为：`/Applications/Go2Shell.app/Contents/MacOS` 将名为 Go2ShellHelper.app 的图标拖到 Dock 栏上。
