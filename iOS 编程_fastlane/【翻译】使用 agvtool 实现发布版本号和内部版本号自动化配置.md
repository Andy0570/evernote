原文链接：<https://developer.apple.com/library/content/qa/qa1827/_index.html>

## 问：如何使用 agvtool 工具实现自动增加构建版本号和发布版本号？

答：发布版本号（version number）和构建版本号（build number）分别表示应用程序的发布版本号和内部/构建版本号。agvtool 是一个命令行工具，允许您自动将这些版本号数字增加到下一个最高数字或特定数字。本文档提供了使用 agvtool 更新构建版本号和发布版本号的详细说明。“Xcode” 和 “Command Line” 章节分别表示在 Xcode 和命令行中执行的步骤。

> 注：
> 
> **发布版本号（version number）**，是给应用程序的使用者（用户）看的版本号，用于标识应用程序的已发布版本。它存储在你的应用程序的 `Info.plist` 文件的 `CFBundleShortVersionString` （简称为 Bundle versions string, short）字段中。
> **构建版本号（build number）**，用于标识未发布或已发布版本的应用程序版本。它存储在你的应用程序的 `Info.plist` 文件的 `CFBundleVersion` （简称为 Bundle versions）字段中。

有关显示 App Store 应用程序的发布版本号和构建版本号的示例，请参见图 1，它们分别为 1.3 和 201.4。

![图 1](https://developer.apple.com/library/archive/qa/qa1827/Art/QA1827_AppStoreBuildNumber.png)

> 重要提示：当您的应用包含多个 targets 目标时，agvtool 会将所有 targets 的版本号设置为相同的数字。


## Xcode

你必须在 Xcode 项目中完成以下步骤。

### 1.开启 agvtool

导航到 targets 的 **Build Settings** 窗口，然后更新所有的构建配置，如下所示：

* 将 Versioning 列表下的 `Current Project Version` 值设定为你选择的值。

  你的 Xcode 项目的数据文件 `project.pbxproj` 包含一个 `CURRENT_PROJECT_VERSION`（当前项目版本）构建设置，该设置值用于指定项目的当前版本号。
  agvtool 会在 `project.pbxproj` 中搜索 `CURRENT_PROJECT_VERSION` 值。如果 `CURRENT_PROJECT_VERSION` 值存在则继续运行，否则停止运行。这个值用于更新内部版本号。
  
  > 注：`Current Project Version` 的值必须是整数或浮点数（例如 34.6），对于新创建的项目，这个值应该设置为 1。

* 设置 `Versioning System` 值为 `Apple Generic`。
  
  默认情况下，Xcode 不会使用任何版本控制系统。将 `Versioning System` 设置为 `Apple Generic` 可确保 Xcode 在项目中包含所有 agvtool 生成的版本信息。

![图2](https://developer.apple.com/library/archive/qa/qa1827/Art/QA1827_Versioning.png)


### 2.设置发布版本和内部版本号

agvtool 会在项目的 `Info.plist` 文件中搜索发布版本号和内部版本号。如果它们存在则更新它们，否则不执行任何操作。请确保你的 `Info.plist` 文件中存在 `CFBundleVersion` （Bundle versions，即**内部版本号**）和 `CFBundleShortVersionString` （Bundle versions string，即**发布版本号**）这两个值。

![图 3](https://developer.apple.com/library/archive/qa/qa1827/Art/QA1827_InfoPaneInXcode.png)

> 注：`DYLIB_CURRENT_VERSION`（Current Library Version，当前库版本）构建设置用于指定库或框架的当前构建版本。
如果你需要构建库或者框架，请确保按照上述步骤操作，并确保 Targets 下的 “Build Settings” 中包含此设置。


## 命令行

退出 Xcode 应用，然后在运行以下任何命令之前，在终端下导航到应用程序中包含 `.xcodeproj` 文件的目录下。
`.xcodeproj` 项目文件包含 `project.pbxproj` 文件，该文件被用于 agvtool。

### 更新发布版本号

要将发布版本号更新为指定版本号，请运行：

```bash
agvtool new-marketing-version <your_specific_version>
```
其中 `<your_specific_version>` 是你想要更新到的版本号数值，如清单 1 所示。

清单 1：将发布版本号更新为 2.0

```bash
$ xcrun agvtool new-marketing-version 2.0
Setting CFBundleShortVersionString of project MyProject to:
    2.0.
 
Updating CFBundleShortVersionString in Info.plist(s)...
 
Updated CFBundleShortVersionString in "MyProject.xcodeproj/../MyProject/MyProject-Info.plist" to 2.0
```

### 更新内部版本号

* 要自动增加内部版本号，请运行：

    ```bash
    agvtool next-version -all
    
    # 或者
    agvtool bump -all
    ```

    清单 2：自动递增内部版本号

    ```bash
    $ xcrun agvtool next-version -all
    Setting version of project MyProject to:
        2.

    Also setting CFBundleVersion key (assuming it exists)

    Updating CFBundleVersion in Info.plist(s)...

    Updated CFBundleVersion in "MyProject.xcodeproj/../MyProject/MyProject-Info.plist" to 2
    ```

    > 注：运行 `agvtool next-version -all` 命令会将你的版本号增加到下一个最高的整数值。
例如，它将更新 2 到 3 ，更新 1.3 到 2。


* 要将应用程序的内部版本号设置为特定版本，运行：

    ```bash
    agvtool new-version -all <your_specific_version>
    ```

    其中 `<your_specific_version>` 是你想要更新到的版本号数值。


    清单 3：设置内部版本号为 2.6.9

    ```bash
    $ xcrun agvtool new-version -all 2.6.9
    Setting version of project MyProject to:
        2.6.9

    Also setting CFBundleVersion key (assuming it exists)

    Updating CFBundleVersion in Info.plist(s)...

    Updated CFBundleVersion in "MyProject.xcodeproj/../MyProject/MyProject-Info.plist" to 2.6.9
    ```

### 查看版本号

* 要查看当前发布版本号，请运行：

    ```bash
    agvtool what-marketing-version
    
    # 或者
    agvtool mvers
    ```
    
    清单 4：查看当前发布版本号
    
    
    ```bash
    $ xcrun agvtool what-marketing-version
    No marketing version number (CFBundleShortVersionString) found for Jambase targets.

    Looking for marketing version in native targets...
    Looking for marketing version (CFBundleShortVersionString) in native targets...

    Found CFBundleShortVersionString of "2.0" in "MyProject.xcodeproj/../MyProject/MyProject-Info.plist"
    ```
    
* 要查看当前内部版本号，请运行：

    ```bash
    agvtool what-version
    
    # 或者
    agvtool vers
    ```

    清单 5：查看当前内部版本号

    ```bash
    $ xcrun agvtool what-version
    Current version of project MyProject is:
        2.2
    ```

### 查看帮助命令

```bash
$ agvtool help

agvtool - Apple-generic versioning tool for Xcode projects
  usage:
    agvtool help
    agvtool what-version | vers [-terse]
    agvtool [-noscm | -usecvs | -usesvn] next-version | bump [-all]
    agvtool [-noscm | -usecvs | -usesvn] new-version [-all] <versNum>
    agvtool [-noscm | -usecvs | -usesvn] tag [-force | -F] [-noupdatecheck | -Q] [-baseurlfortag]
    agvtool what-marketing-version | mvers [-terse | -terse1]
    agvtool [-noscm | -usecvs | -usesvn] new-marketing-version <versString>


For more information type "man agvtool".
```


### 速记表

最后，整理了一张 agvtool 命令行的速记表，方便参考：

![速记表 @Carbonize](https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/Carbonize%202019-08-02%20at%202.09.50%20PM.png)



### 参考

* [agvtool: Automating iOS Build and Version Numbers](https://medium.com/xcblog/agvtool-automating-ios-build-and-version-numbers-454cab6f1bbe)
* [使用 agvtool 更改 app version／build](https://addicechan.github.io/agvtool/)
* [satckoverflow: How to set CURRENT_PROJECT_VERSION in xcode 8
](https://stackoverflow.com/questions/39673280/how-to-set-current-project-version-in-xcode-8/55525399#55525399)