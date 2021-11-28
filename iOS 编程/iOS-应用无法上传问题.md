### 问题描述

无法将打包编译好到 iOS 应用通过 Xcode 或 Transporter 上传到 App Store。

* 当我通过 Xcode（Version：11.2.1） 内置功能上传应用时，会无限卡顿在 **Uploading** 的 **Authenticating with the App Store...** 这一步骤。
* 我又尝试使用 Transporter（Version：1.1）上传 IPA 包的方式交付应用，会无限卡顿在 **正在验证APP，正在通过+APP+Store+进行认证** 这一步骤。

一开始我以为是网络延迟问题，于是我尝试了使用公司的 WiFi、手机 4G 热点、家里的 1000M WiFi、通过虚拟私有网络上传...我甚至还尝试了关闭电脑防火墙并上传，无一奏效。

### 解决方法

查看 stackoverflow 的这个[问题](https://stackoverflow.com/questions/22443425/application-loader-stuck-at-authenticating-with-the-itunes-store-when-uploadin)似乎可以得到答案。
解决方法是让我们更新一个名为 `.itmstransporter` 的文件。
因此，终端下执行以下命令：
```bash
$ cd ~
$ mv .itmstransporter/ .old_itmstransporter/
$ "/Applications/Xcode.app/Contents/Applications/Application Loader.app/Contents/itms/bin/iTMSTransporter"

# 上述最后一条命令改为如下
"/Applications/Application Loader.app/Contents/MacOS/itms/bin/iTMSTransporter"
```

但是我发现由于我的 Xcode 版本是 11.2.1 的，Xcode 下并没有「Application Loader」这个开发工具。因此执行最后一条命令会报错。

![Xcode Developer Tool](https://upload-images.jianshu.io/upload_images/2648731-4806ff64f88cdb88.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/400)

幸好「Application Loader」这个应用之前额外下载了，在应用程序目录下，因此，我尝试修改以上的命令如下：

```bash
$ cd ~
$ mv .itmstransporter/ .old_itmstransporter/
# 最后一条命令改为如下
$ "/Applications/Application Loader.app/Contents/MacOS/itms/bin/iTMSTransporter"
```
执行以上最后一条命令后，控制台输出：

```bash
[2019-11-22 13:31:30 CST] <main>  INFO: Transporter is searching for updated software components.
[2019-11-22 13:31:35 CST] <main>  INFO: Transporter has detected that new software components exist; updating.
...
[略]
...
[2019-11-22 13:48:34 CST] <main> DEBUG:   ... updated bundle successfully retrieved.
[2019-11-22 13:48:34 CST] <main>  INFO: Transporter has been successfully updated. The updated components will be available the next time Transporter runs.
usage: iTMSTransporter [-help <arg> | -info | -m <arg> | -version]   [-o <arg>] [-v
       <arg>]  [-WONoPause <arg>] [-Xmx1024m]
iTMSTransporter : iTunes Store Transporter 1.8.2
```

之后关闭 Transporter，重新尝试，还是不行 🤯🤯🤯
电脑关机，通过 Xcode 内置工具尝试，成功上传 🤔

